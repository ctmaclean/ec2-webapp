##TL;DR for deploying your node app...

## 1) hack locally and push to your github repo
`ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update`

## 2) hack on your server, change code locally to fix bugs, push to github repo, stash/rollback server changes  (really not recommended)
`ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update`

##Detailed instructions

## Workflow

Here we outline two different workflows -- how to hack on and deploy your app.

## Workflow 1: Hack locally, git pull to deploy

This is the recommended workflow as it is robust and every change you make is backed up (in your git repository) and can be reverted at any time.

- Work in your own, local clone of the repository.
- No need to log in to or modify files on the server
- Use the `update` script to deploy changes (update the server)

### Examples of using the update script

The update script usage: `update [restart|noop [<refspec>]]`

Make some changes and push them live, but don't restart services:

    cd src/your_node_project
    git commit ...
    ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update
    # some status output here

Make some changes and push them live, also restarting services (Node.js server):

    cd src/your_node_project
    git commit ...
    ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update restart

Revert to an older version on the server:

    ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update restart v0.1.2

> Note: `v0.1.2` in this example is a tag but can be replaced by any git refspec, like a commit hash or branch name.

You can simplify the "update" call by creating an alias in your `~/.bash_rc` (or other shell startup file):

    alias your_node_project-update='ssh -i ~/.ssh/your_node_project.pem ubuntu@your_node_project.com:/var/your_node_project/update'

Then, the above example could instead be executed as:

    your_node_project-update restart v0.1.2

## Workflow 2: Live hack (what happens the first few times you deploy or bugs you can't replicate locally.)

- Hack directly on the server in `/var/your_node_project`
- No git involved and thus no backup or ability to revert from a b0rked server

Simply make a copy of this repo into `/var/your_node_project` on your server and follow the guide in `INSTALL.md` with exception of checking out your repo.

Example:

    cd /var/your_node_project
    # hack hack hack
    sudo initctl your_node_project restart
    # it probably works, let's have a beer and relax

> Note: Next time you want to run your update script you'll have to either stash or revert your changes for update to run.  To do that - `git -- checkout master` [TODO: Confirm this is the exact command], then run the update script.
