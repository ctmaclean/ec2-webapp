# EC2 web app template modified from rsms/ec2-webapp (and using the excellent project layout)

This is a template we use at Cardinal as a base for our Node.js EC2 servers.  More web services focused then the original project (pretty damn slim actually)..

As with the (excellent) original:

- Less than 15 minutes from start to finish
- Eligible/compatible with the ["AWS Free Usage Tier"](http://aws.amazon.com/free/) (Highly recommended with Node - so damn cheap!)
- Ubuntu Linux
- Git-based deployment

Tweaked out a bit:

- Modified to use Upstart as per a bit more current initialization of services in Ubuntu
- Modified node to produce a .pid file for monit to keep an eye on
- Added monit to allow for monitoring and restart of app
- modified scripts to use a (rudimentary) version of Amazon's 'user data' option on instance launch for app config
- stripped out a lot of the scripting that didn't apply to our useage pattern.
- removed nginx (we don't server any static files)

This template (still) enables a very smooth, simple workflow but is more intended as a base server to be imaged for 1+n failover servers

It should also be noted that we build our servers and then image them from the AWS console, versioning the server to make it easy to deploy N servers for failover.  Our servers typically exit process on any type of serious error and we use monit and failover servers to pick up the slack.  

**Aight.  Lets start ** Head over to [INSTALL.md](https://github.com/ctmaclean/ec2-webapp/blob/master/INSTALL.md#readme)
