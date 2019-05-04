Deploy your project
============================================================

The project exists, but isn't actually running yet. Let's deploy it: hit **Deploy** for the test server.

This will take about three or four minutes.

What happens during the deployment process
-------------------------------------------

This is a two-part process; first an **image is built**; then, a **container is deployed** (more on images and
containers later).


Building the image
~~~~~~~~~~~~~~~~~~

* The Control Panel clones the Git repository.
* Docker follows the instructions in the ``Dockerfile`` to build the *image*:

  * it uses a pre-built Docker image to install system components (DevOps work, in other words)
  * it uses pip to install the Python requirements
  * it runs the Django ``collectstatic`` command to copy static files to the right place.


Deploying the container
~~~~~~~~~~~~~~~~~~~~~~~

* The image is now built, and the Control Panel takes over again. It creates a Docker *container* (an instance of the
  image), and provides it with the required environment variables
* Inside the container:

  * it launches Django
  * runs any database migrations that need to be run.
* Then the Control Panel checks that the new container is running as it should be, and if so it declares the deployment
  successful.

The site is now available, connected to its web server, database and media storage.


Deployment on other platforms
-----------------------------

On different platforms, the deployment process can be a little different, but the basic principle is the same: the
system builds an image from the repository, creates a container from the image and launches it in an environment, wired
up to its backing services.


Visit the *Test* site
-----------------------

You can use the link in the Control Panel to access the *Test* server of your project.
