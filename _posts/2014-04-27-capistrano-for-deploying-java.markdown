---
layout: post
title:  "Capistrano & Java (or using capistrano to execute remote commands)"
date:   2014-04-27
categories: Programming
---

Assumption:
-
 Capistrano for java works if the deployment involves doing a git checkout on a server machine and building the package - a command similar to `mvn clean package`. If there are proper deployment structures in place such as a CI server, or you do not/can not build your war file in the server machine - then it doesn't really help.

Prerequisites:
-
 You need to have ruby installed in local machine, install capistano gem and have run `cap install` anywhere -it doesn't really matter if the files are in the root directory of the project or in a separate directly. But it is better to have the capistrano files along with the source code. More install instructions in the capistrano website.

How To:
-

[Capistrano](http://capistranorb.com) essentially executes commands remotely on a machine. So, we can 'in principle' use capistrano to do any arbitrary jobs that involves remote execution.

To achieve that we need to override any ruby specific tasks that gets executed by capistrano.

1. `bundle install` task gets executed on `deploy:finalize_update`, so this needs to be overridden.
2. `deploy:restart` task touches `tmp/restart.txt` - doesn't make sense in Java world.
3. The whole `deploy:finalize_update` step is not necessary.

Hence, in our `Capfile`, we override these tasks.

	namespace :deploy do
	# override ruby specific tasks
    	task :finalize_update do; end
    	task :restart do; end
  	end

  	namespace :bundle do
  	#override bundle install task
    	task :install do; end
 	end

	# create the task which will needs to be executed in remote machine. such as mvn deploy
  	namespace :tomcat do
    	task :deploy do
      	run "cd #{deploy_to}/current; mvn clean tomcat7:redeploy"
    	end
  	end

	#invoke the task after the 'create_symlink' stage in capistrano
	after 'deploy:create_symlink', 'tomcat:deploy'

`      	run "cd #{deploy_to}/current; mvn clean tomcat7:redeploy"` is where interesting part is. You should already have this code `set :deploy_to /path/to/deploy` in the cap file.
Instead of this command, we can `run` almost any command and have it executed in the remote machine, thus reusing capistrano for any other application.
