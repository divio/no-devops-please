Explore ``docker-compose.yml``
============================================================

Open the entire project in your preferred text editor, and let's start by looking at the
``docker-compose.yml`` file.

.. image:: /images/project-files.png
   :alt: 'Your local project'

When you run your project (with ``docker-compose up``, ``divio project up`` or the **Start** button in the Divio app),
Docker Compose will use this file to orchestrate its components.

Below we've highlighted the relevant parts of the file - you don't need to go through it all right now, but you will
probably want to come back to it later. But before you go on, please explore :ref:`some key Docker Compose
operations <key-docker-compose-operations>` at the bottom of this page.


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


.. _key-docker-compose-operations:

Key Docker Compose operations
-----------------------------

You already know ``docker-compose up``, which runs your project. If you hit ``control-c`` in the console, it will stop it.

Another way to stop it is by executing ``docker-compose stop`` in the same directory. ``stop`` is the opposite of
``up`` (don't ask).

Suppose you made a change to the project (perhaps to its requirements or ``Dockerfile``). Then you'd need to rebuild
it: ``docker-compose build``.

``docker-compose build`` is interesting. Look at the output::

    ➜  docker-compose build
    db uses an image, skipping
    Building web
    [...]

It builds the ``web`` container, but not the ``db`` container. That's because it doesn't need to. The ``db`` container,
as you can see from the ``docker-compose.yml`` file above, uses an off-the-shelf image. In Docker, images can be
built and saved for re-use, saving time and making things repeatable.

..  admonition:: Divio Cloud developer handbook links

    * `docker-compose.yml in Divio Cloud projects <http://docs.divio.com/en/latest/reference/docker-docker-compose.html>`_
