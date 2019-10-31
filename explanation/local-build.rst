.. _explanation-local-build:

What happens during the local set-up process
=============================================


What happens when you set up the project locally is almost the same as what happens when it's :ref:`deployed on the
Cloud <explanation-project-deployment>`, and you can watch it happening in the console.

* The Divio CLI uses Git to clone the repository.
* Docker uses the instructions in the ``Dockerfile`` to build the *image*:

  * it uses a pre-built Docker image to install system components
  * it uses pip to install the Python requirements
  * it runs the Django ``collectstatic`` command to copy static files to the right place.

* Finally, the Divio CLI will pull down the database and media from your Test server.

The image is now built, and is waiting to be used.


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
