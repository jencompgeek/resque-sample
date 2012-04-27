resque-sample
=============

Copy of [resque demo sample](https://github.com/defunkt/resque/tree/master/examples/demo) that can be deployed on Cloud Foundry.
This app differs from the original only by addition of the Gemfile and Cloud Foundry manifest files.
Highlights CF Ruby auto-reconfiguration by automatically connecting the server and workers to a CF Redis service.

### Starting the Demo App

Here's how to deploy the Sinatra app to Cloud Foundry:

    $ bundle install
    $ bundle package
    $ vmc push --manifest server-manifest.yml
    Would you like to deploy from the current directory? [Yn]:
	Application Name: my-resque-server
	Application Deployed URL [my-resque-server.cloudfoundry.com]:
    $ open http://my-resque-server.cloudfoundry.com

Click 'Create New Job' a few times. You should see the number of
pending jobs rising.


### Starting the Demo Worker

Deploy the worker on Cloud Foundry:

    $ vmc push --manifest worker-manifest.yml
	Would you like to deploy from the current directory? [Yn]:
	Application Name: my-resque-worker
	$ vmc logs my-resque-worker

You should see the following output:

    *** Starting worker 5fa7867f-b1ce-4b01-bf64-3af799439f72:6825:default
    *** got: (Job{default} | Demo::Job | [{}])
    Processed a job!
    *** done: (Job{default} | Demo::Job | [{}])

You can also edit the command property in worker-manifest.yml to use `VVERBOSE` (very verbose) if you want to see more:

    $ VVERBOSE=true QUEUE=default rake resque:work
    *** Starting worker hostname:90399:default
    ** [05:55:09 2009-09-16] 90399: Registered signals
    ** [05:55:09 2009-09-16] 90399: Checking default
    ** [05:55:09 2009-09-16] 90399: Found job on default
    ** [05:55:09 2009-09-16] 90399: got: (Job{default} | Demo::Job | [{}])
    ** [05:55:09 2009-09-16] 90399: resque: Forked 90401 at 1253141709
    ** [05:55:09 2009-09-16] 90401: resque: Processing default since 1253141709
    Processed a job!
    ** [05:55:10 2009-09-16] 90401: done: (Job{default} | Demo::Job | [{}])

Notice that our workers `require 'job'` in our `Rakefile`. This
ensures they have our app loaded and can access the job classes.

### Scaling the Demo Worker

To add more workers:

    $ vmc instances resque-worker +2

### Starting the Resque frontend

Great, now let's check out the Resque frontend. Either click on 'View
Resque' in your web browser or run:

    $ open http://my-resque-server.cloudfoundry.com/resque/

You should see the Resque web frontend. 404 page? Don't forget the
trailing slash!

### That's it!

Click around, add some more queues, add more jobs, do whatever, have fun.