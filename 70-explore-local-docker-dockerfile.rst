Explore the ``Dockerfile``
============================================================

Now let's take a look at the ``Dockerfile``. This is the file that defines your
web application *image*. (The ``db`` image doesn't have a ``Dockerfile``
associated with it - it's a pre-built image.)

.. image:: /images/dockerfile.png
   :alt: 'The Dockerfile'


The Dockerfile in this project is defined when the project is created, using the options you selected. Because it's
one of the Divio Cloud standard Dockerfiles, it contains some Divio-specific components - but we can ignore those
completely for now, and look at ones you might use with any Docker host.

Again, at this stage you don't need to spend too much time looking at this - you can come back to it later when you
need to.


The ``FROM`` instruction
-------------------------

..  code-block:: yaml

    FROM divio/base:4.15-py3.6-slim-stretch

This is the pre-built image that our project is based on. It's getting it from the `Docker Hub
<https://hub.docker.com/r/divio/base>`_, where you can see that the codebase for this image `is published on GitHub
<https://github.com/divio/ac-base/blob/4.15-py3.6-slim-stretch/py3.6-slim-stretch/Dockerfile>`_.

Not only that, you can also see that it, in turn, is based on another image: ``python:3.6.8-slim-stretch``. These
images form the layers of the Docker project, and they go down all the way to the operating system components. It's a
very handy system for building what you need in a modular and repeating way.


Python package installation
---------------------------

First, ``ENV`` declares a couple of environment variables:

..  code-block:: yaml

    ENV PIP_INDEX_URL=${PIP_INDEX_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/+simple/} \
        WHEELSPROXY_URL=${WHEELSPROXY_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/}


Then we copy some files into a working directory within the image:

..  code-block:: yaml

    COPY requirements.* /app/
    COPY addons-dev /app/addons-dev/


And run pip instructions to install the requirements:

..  code-block:: yaml

    RUN pip-reqs compile && \
        pip-reqs resolve && \
        pip install \
            --no-index --no-deps \
            --requirement requirements.urls


Copy miscellaneous files
------------------------

Various files need to be copied into the image:

..  code-block:: yaml

    COPY . /app


Run ``collectstatic``
---------------------

``collectstatic`` will do the same for Django's static files:

..  code-block:: yaml

    RUN DJANGO_MODE=build python manage.py collectstatic --noinput

..  admonition:: Divio Cloud developer handbook links

    * `The Dockerfile in Divio Cloud projects <http://docs.divio.com/en/latest/reference/docker-dockerfile.html>`_
