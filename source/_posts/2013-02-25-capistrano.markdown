---
layout: post
title: "Capistrano"
date: 2013-02-25 23:23
comments: true
categories: [ruby, gem]
---

最近研究 Capistrano 来着，在此记录心得体会。

### 什么是 Capistrano

Capistrano 是一个可在多台服务器上并行命令的工具，主要用于部署 Web 应用。

### 为什么要用 Capistrano

Capistrano 分化并自动化部署过程，让部署变得更简单，由于使用 Ruby，
所以 DevOps 实践起来也更容易。

### Capistrano 的具体使用

#### 基本概念

+ SSH

  Capistrano 通过 SSH 协议在服务器上执行命令，通过 ssh-keygen 和 ssh-copy-id
  可以更方便的使用 SSH。

+ SCM

  SCM，跟据实际情况可以是 git，svn 等，指定代码仓库，Capistrano
  部署内容将在此获取。

+ Roles

  一般的有 web，app，db，可以指定不同服务器，所以可以让指定的 task
  在特定服务器上执行。

+ Tasks

  要执行的任务，用法跟 Rake 相近。

+ Callbacks

  有 `after` `before`，可让 task 于其他 task 执行前后执行。

### 实施步骤

+ 安装 capistrano gem

+ capify .

  生成 Capfile config/deploy.rb 两个文件

+ cap deploy:migrations

  部署并启动服务

+ cap deploy

  更新代码后再次部署

+ 更多 `cap -T`

config/deploy.rb 实例

``` ruby config/deploy.rb
set :user, "deployer"
set :domain, "foobar.com"
set :application, "foobar"

require 'bundler/capistrano'

# RVM
require 'rvm/capistrano'
set :rvm_ruby_string, "1.9.3-p385"
set :rvm_type, :user

# filepath
set :repository,  "git@github.com:thisiskun/foobar.git"
set :deploy_to, "/home/#{user}/#{domain}"

role :web, domain
role :app, domain
role :db, domain, :primary => true

set :deploy_via, :remote_cache
set :scm, 'git'
set :branch, 'master'
set :scm_verbose, true
set :use_sudo, false
set :rails_env, :production

set :scm, :git

# add uploads dir
set :uploads_dirs, %w(public/uploads)
set :shared_children, fetch(:shared_children) + fetch(:uploads_dirs)

# if you want to clean up old releases on each deploy uncomment this:
after "deploy:restart", "deploy:cleanup"

namespace :deploy do

  task :start do ; end
  task :stop do ; end
  task :restart, :roles => :app, :except => { :no_release => true } do
    run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"
  end

  namespace :deploy do
    namespace :assets do
      desc "Precompile assets on local machine and upload them to the server."
      task :precompile, :roles => web, :except => {:no_release => true} do
        run_locally "bundle exec rake assets:precompile"
        find_servers_for_task(current_task).each do |server|
          run_locally "rsync -vr --exclude='.DS_Store' public/assets #{user}@#{server.host}:#{shared_path}/"
        end
      end
    end
  end

  desc "Update remote database and settings file with local copy"
  task :sync_yaml do
    run "mkdir -p #{shared_path}/config"
    run_locally("rsync config/database.yml #{user}@#{domain}:#{latest_release}/config/database.yml")
    run_locally("rsync config/settings.yml #{user}@#{domain}:#{latest_release}/config/settings.yml")
  end
end

before "bundle:install", "deploy:sync_yaml"
```

### 实际部署中遇到的问题

#### RVM

如果服务器上用 RVM 安装的 ruby，需要做如下配置

``` ruby Gemfile
gem 'rvm-capistrano'
```

``` ruby config/deploy.rb
require 'rvm/capistrano'

# RVM
require 'rvm/capistrano'
set :rvm_ruby_string, "1.9.3-p385"
set :rvm_type, :user
```

#### deploy 完代码后执行 `bundle install`

``` ruby config/deploy.rb
require 'bundler/capistrano'
```

看代码，可知是在 `deploy:update_code` 后执行的。

#### assets precompile

在整个部署过程中，最耗时间的应该就是 assets precompile 这一步了，
而且性能消耗比较高，你可能不会想在服务器上做，所以比较好的解决方案就是，

+ 本地 assets precompile 后上传服务器


``` bash .gitignore
public/assets
```

``` ruby Capfile
load 'deploy/assets'
```

覆写 assets:precompile task

``` ruby config/deploy.rb
namespace :deploy do
  namespace :assets do
    desc "Precompile assets on local machine and upload them to the server."
    task :precompile, :roles => web, :except => {:no_release => true} do
      run_locally "bundle exec rake assets:precompile"
      find_servers_for_task(current_task).each do |server|
        run_locally "rsync -vr --exclude='.DS_Store' public/assets #{user}@#{server.host}:#{shared_path}/"
      end
    end
  end
end
```

+ 提高 assets:precompile 的速度

``` ruby Gemfile
group :assets do
  gem 'turbo-sprockets-rails3'
end
```

#### config/database.yml 敏感数据问题

切忌把这些包含敏感数据的配置文件加入到版本控制工具中去

``` bash .gitignore
config/database.yml
```

定义上传的 task

``` ruby config/deploy.rb
  desc "Update remote database and settings file with local copy"
  task :sync_yaml do
    run_locally("rsync config/database.yml #{user}@#{domain}:#{latest_release}/config/database.yml")
    run_locally("rsync config/settings.yml #{user}@#{domain}:#{latest_release}/config/settings.yml")
  end

  before "bundle:install", "deploy:sync_yaml"
```

#### 上传文件问题

``` bash .gitignore
# where your uploaded files localed
public/uploads
```

``` ruby config/deploy.rb
# add uploads dir
set :uploads_dirs, %w(public/uploads)
set :shared_children, fetch(:shared_children) + fetch(:uploads_dirs)
```

因为修改了 `shared_dirs`，所以要先执行 deploy:setup。

关于 `shared_dirs`，这里实际上放置了一些各个 releases 之间共享的文件，
像是系统生成的 log，pid 等，Capistrano 会在更新代码后生成 soft link，
生成的 assets 文件也是放在这里的，避免之前的版本不能访问的问题。
所以上传文件目录放在共享下也是比较合理的。

---------------------------------------

参考：

+ [Agile Web Development with Rails](http://pragprog.com/book/rails4/agile-web-development-with-rails)
+ [一次搞懂 Assets Pipeline](http://pragprog.com/book/rails4/agile-web-development-with-rails)
+ [stackoverflow Carrierwave files with Capistrano](http://stackoverflow.com/a/9710542/1115359)
