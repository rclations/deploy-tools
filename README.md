# INN Deployment Tools

A box of tools for deploying INN's WordPress sites to WPEngine. Based on Chicago Tribune's [deploy-tools](https://github.com/newsapps/deploy-tools).

[Read documentation for all included commands here.](https://github.com/INN/deploy-tools/blob/master/COMMANDS.md)

## Prerequisites

You'll need Python (versions 2.5 to 2.7) and [pip](https://pip.pypa.io/en/latest/installing.html) to get started with these tools. [Virtualenv](https://virtualenv.pypa.io/en/latest/virtualenv.html) and [virtualenvwrapper](https://pypi.python.org/pypi/virtualenvwrapper/3.4) are not required, but using them is a good practice, so we'll use them here.

Set up python dev environment:

    $ sudo easy_install pip
    $ sudo pip install virtualenv
    $ sudo pip install virtualenvwrapper
    $ echo 'source /usr/local/bin/virtualenvwrapper.sh' >> ~/.zshrc

Change "~/.zshrc" to the appropriate rc file for your shell. If you're using bash: "~/.bashrc".

Open a new terminal window or tab and create a virtual environment for your project:

    $ mkvirtualenv projectnamegoeshere --no-site-packages
    $ workon projectnamegoeshere

## Setup

To use these tools, add this repo as a git submodule in the root of your project:

	$ git submodule add https://github.com/INN/deploy-tools.git tools

Then pull the example files in to the root of your project:

	$ cp -r tools/examples/* ./

Install the required libraries with pip:

Note: if you're using OS X Mavericks (10.9), you might need to set some compiler flags for Fabric and its dependencies to install correctly:

    $ export CFLAGS=-Qunused-arguments
    $ export CPPFLAGS=-Qunused-arguments

Then:

    $ workon projectnamegoeshere
    $ pip install -r requirements.txt
    $ fab wp.verify_prerequisites

The `wp.verify_prerequisites` command will notify you of any issues that might prevent you from using the deploy tools in their entirety.

Now edit the example `fabfile.py` to adjust the settings for your project:

    env.project_name = ''   # name for the project

You'll also want to supply the ssh environment variables for `production` and `staging` (or any other enviornments).

    env.hosts       = []    # ssh host for production.
    env.user        = ''    # ssh user for production.
    env.password    = ''    # ssh password for production.

    env.hosts       = []    # ssh host for staging.
    env.user        = ''    # ssh user for staging.
    env.password    = ''    # ssh password for staging.

By default, the deploy tools will use git for deployment to WPEngine. If you'd rather use sftp to deploy (recommended), you can do so by specifying `sftp_deploy` in your `fabfile.py`.

    env.sftp_deploy = True

After setting `env.sftp_deploy` to `True`, make sure you run `fab wp.verify_prerequisites` to ensure you have the required software installed.

If your version of curl does not support sftp and you wish to use the tools in this repository to deploy, you will have to use a version of curl that does support it. For OSX users, the verification script uses brew to take care of that problem. For users of other operating systems, check your online support communities. Ubuntu users may have success in following [this guide](http://zeroset.mnim.org/2013/03/14/sftp-support-for-curl-in-ubuntu-12-10-quantal-quetzal-and-later/).


## Usage

### Deployment

These tools use [Fabric](http://www.fabfile.org/).

To see a list of available commands:

    $ fab -l

To deploy to your staging environment:

    $ fab staging master deploy

And production:

    $ fab production master deploy

To switch to a different branch and deploy

    $ fab staging branch:newfeaturebranchname deploy

### Local development

#### The Basics

These tools assume that you already have a local server environment setup for deveopment.

For instructions and information on setting up a local server environment on your computer, consult the Largo documentation on [Setting up a complete Largo dev environment](http://largo.readthedocs.io/developers/setup.html).

#### Database commands

These tools include a few commands to ease database setup and manipulation. [Read about them here](https://github.com/INN/deploy-tools/blob/master/COMMANDS.md).

### Installing and/or upgrading WordPress

In setting up your dev environment, you'll want to pull in all the necessary WordPress files if they are not included in the project repository. To do this, use the command:

    $ fab wp.install:"4.5.3"

Where "4.5.3" identifies the [tagged version of the WordPress repository](https://github.com/WordPress/WordPress/tags) that you want to use.

Fabric will download the release .zip file from Github and extract its contents to the project root.

The `.gitignore` file included in the examples directory is a good starter for WordPress projects destined for deployment to WPEngine. It will help keep your project repo tidy by ignoring all WordPress core files that are unnecessary for deployment.
