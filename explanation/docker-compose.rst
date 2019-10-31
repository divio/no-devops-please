.. _explanation-docker-compose:

The ``docker-compose.yml`` file
=================================

.. image:: /images/project-files.png
   :alt: 'Your local project'

When you run your project (with ``docker-compose up`` or other commands), Docker Compose will use this file to
orchestrate its components.

Below we've highlighted the relevant parts of the file - you don't need to go through it all right now, but you will
probably want to come back to it later.


Services or containers
----------------------

..  code-block:: yaml

    services:
      web:
        [...]
      db:
        [...]

The file defines two *services* - in effect, two containers that Docker Compose will
launch. One is the ``web`` service or container, containing your Django project. The
other is the ``db`` container, our local stand-in for the database cluster that would
be available on the Cloud.


The ``web`` service
-------------------

..  code-block:: yaml

    build: "."

Docker Compose needs to know where to find the instructions to build the image for this container - the answer is right
here, because that's where the ``Dockerfile`` is.

.. _default-web-command:

..  code-block:: yaml

    command: python manage.py runserver 0.0.0.0:80

When the container starts up, start the Django runserver on port 80.

..  code-block:: yaml

    ports:
      - "8000:80"

... and map that port 80 *inside* the container to port 8000 *outside*.

..  code-block:: yaml

    env_file: .env-local

Read the file ``.env-local`` for any environment variables.

..  code-block:: yaml

    links:
      - "db:postgres"

Resolve the hostname ``postgres`` to the ``db`` container.


The ``db`` service
------------------

..  code-block:: yaml

    image: postgres:9.6-alpine

This container is based on a pre-built, off-the-shelf image, one specifically created to run Postgres 9.6 on Alpine
Linux.

..  code-block:: yaml

    environment:
      POSTGRES_DB: "db"

When the container is launched, its environment should include the ``POSTGRES_DB`` variable with the value ``db``.
