Set your project up locally
============================================================

The next step is to set up your project on your own computer, for development purposes and also to understand better
how the system works.

On different systems there will be different ways of doing this, and some will offer more extensive local tooling than
others.

Three options for setting up Divio Cloud projects locally
---------------------------------------------------------

On Divio Cloud you have three ways of doing it - read through the options before deciding which to use:


Use the Divio app
~~~~~~~~~~~~~~~~~

(Requires you to have :ref:`installed the Divio app <installation>`.)

Select your project, and hit **Set up project**.

.. image:: /images/divio-app-set-up.png
   :alt: 'The Divio app'
   :width: 358


Use the Divio shell
~~~~~~~~~~~~~~~~~~~

(Requires you to have :ref:`installed the Divio app <installation>`.)

Hit **Open shell** in the Divio app, and then run::

  divio project setup <project slug>

You can get the project slug from the Control Panel:

.. image:: /images/project-slug.png
   :alt: 'The project *slug*
   :width: 668

The Divio shell is itself a bit of Docker magic. It's a shell running *inside* a Docker environment, on your own
computer. This environment is configured with things like keys and other credentials so that it is automatically logged
in to your Divio account, and can communicate with our Git server.


Use your own shell
~~~~~~~~~~~~~~~~~~~

The two methods above are quick ways to get started, because they do some configuration for you. However, you may find
it more convenient to interact with your projects from your own familiar local environment if you're going to be using
this more, in which case you need to do the configuration yourself:

* install the Divio CLI: ``pip install divio-cli``
* run ``divio login``, which will fetch a token from https://control.divio.com/account/desktop-app/access-token/
* upload your public key to https://control.divio.com/account/ssh-keys/

Then run ``divio project setup <project slug>``.


The local build process
~~~~~~~~~~~~~~~~~~~~~~~

What happens when you set up the project locally is almost the same as what happens on the Cloud, and you can
watch it happening in the console.

* The Divio CLI (even the Divio app uses the CLI under the bonnet) uses Git to clone the repository.
* Docker uses the instructions in the ``Dockerfile`` to build the *image*:

  * it uses a pre-built Docker image to install system components
  * it uses pip to install the Python requirements
  * it runs the Django ``collectstatic`` command to copy static files to the right place.

The image is now built, and is waiting to be used.

You can build or rebuild a project's image(s) at any time, by running::

  docker-compose build

We will look at ``docker-compose`` shortly.

(In a Divio project, you can also use *Rebuild* in the Divio app.)

Finally, the Divio CLI will pull down the database and media from your Test server.


Running the local site
~~~~~~~~~~~~~~~~~~~~~~

We won't talk about *local deployment* here, it's not really the right term. Instead, what we will do is get the local
site up and running. This includes some steps that are *in some ways* the analogue of what happens in a Cloud
deployment, but there are also some important differences.

To get the site up and running, you can do one of:

* Use the **Start** button in the Divio app.
* Run ``divio project up``.
* Run ``docker-compose up`` - choose *this way of doing it for now*.

The reasons for using ``docker-compose``:

* It's not a Divio-specific command, and you will be using it with other Docker-based platforms.
* It shows its output in a console, which is always useful.

You should be able to log in to your site at http://localhost:8000.


Docker Compose and ``docker-compose.yml``
-----------------------------------------

Cloud deployments are handled (*orchestrated*) by the Control Panel. There are various orchestration systems - as the
name suggests, orchestration means co-ordinating all the various components of project in the right order so that they
add up to a functional whole.

In your local environment, there are a number of Cloud components that you **don't** have:

* a Control Panel
* a database cluster
* a media host
* a public web server and load-balancer

So we have to do things quite differently locally.

Instead of the Control Panel, Docker Compose orchestrates the deployment. Instead of a database cluster, Docker Compose
sets up a Postgres database inside another Docker container on your computer. And by running the Django project in
``DEBUG`` mode, Django itself takes care of publishing media and providing a web server (more on this later).

Let's go on to explore the local Docker set-up.
