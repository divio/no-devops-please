.. _explanation-project-creation:

What happens when a project is created
======================================

When a new project is created, you will see someting like this on the project dashboard:

.. image:: /images/project-undeployed.png
   :alt: 'New project in the Dashboard'

As implied by the *6 undeployed commits* message, the new project has a repository. This is what the infrastructure
does:

A repository is created
-----------------------

The system creates a new repository. As an *application developer*, you could in fact have created that repository and
everything in it yourself, but in things case, it's created for you. You might even prefer to, and on some platforms you
would be required to. For now, since the Divio Control Panel can do this for us, let's work with it and consider
alternatives later.

This Git repository contains all the instructions needed to build a *Docker image* for the project. It contains a
``Dockerfile``, describing the commands that must be run to build it, a ``requirements.in`` file, listing the Python
packages that will need to be installed, and so on.

This repository defines the *application*, the part that you as an application developer will be responsible for and
will want to get your hands on. To the greatest extent possible, this application needs to be *platform-independent*.
It should not care whether it is to be deployed on Divio or on some other platform (just like your skills, this
application should be transferable with minimum extra work).

..  admonition:: Divio Cloud developer handbook links

    * `Version control for your Divio projects <http://docs.divio.com/en/latest/background/version-control.html>`_
    * `How to configure a Git remote for your project
      <http://docs.divio.com/en/latest/how-to/resources-configure-git.html#configure-version-control>`_


Services are provisioned
------------------------

The Control Panel has provisioned services for the new application. (As an application developer who doesn't want to be
doing DevOps, you should be thrilled to discover the Cloud Platform has taken care of this for you.)

The database, media storage, web server and other services have not only been set up for you, your project is ready
to use them right away.

It has done this for two *environments*, *Test* and *Live*. On Divio, your *Test* server is a private staging
environment, while *Live* is the public server.

Other Cloud providers may offer a similar arrangement, but in any case the important point is that given a *single
respository*, you can deploy it in multiple environments, and all Cloud hosting providers will make this easy.


Environment variables are configured
------------------------------------

You may be used to including things like database credentials in settings files, or perhaps in multiple settings
files for different environments. It's a good idea to keep those things **out** of your respositories. They aren't
*code*, they are *configuration*, and they belong to *environments*.

So, we store them in environment variables, and each environment has its own set of variables. All the repository
contains is a way to look for the variables and turn them into the appropriate Django settings.

..  admonition:: Divio developer handbook links

    `Divio CLI reference <http://docs.divio.com/en/latest/reference/divio-cli.html>`_
