Edit the ``Dockerfile``
=======================

..  admonition:: Introducing

    * the ``Dockerfile``
    * using ``RUN`` in the ``Dockerfile``

Your ``Dockerfile`` will look much like this:

..  code-block:: Dockerfile
    :emphasize-lines: 15

    FROM divio/base:4.15-py3.6-slim-stretch

    ENV PIP_INDEX_URL=${PIP_INDEX_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/+simple/} \
        WHEELSPROXY_URL=${WHEELSPROXY_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/}
    COPY requirements.* /app/
    COPY addons-dev /app/addons-dev/
    RUN pip-reqs compile && \
        pip-reqs resolve && \
        pip install \
            --no-index --no-deps \
            --requirement requirements.urls

    COPY . /app

    RUN DJANGO_MODE=build python manage.py collectstatic --noinput

It contains a series of instructions that are used to build the project's image. For example, the highlighted line
above runs a Django command to collect static files.


Use ``RUN`` to install a package in the image
---------------------------------------------

We want to add the commands we used (``apt-get update`` and ``apt-get install libwebp-dev`` to install ``libwebp-dev``.
Add two lines as indicated:

..  code-block::
    :emphasize-lines: 3-4

    FROM divio/base:4.15-py3.6-slim-stretch

    RUN apt-get update
    RUN apt-get install libwebp-dev -y

    ENV PIP_INDEX_URL=${PIP_INDEX_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/+simple/} \

By making a change to the ``Dockerfile``, you've made a change to the instructions that build it. To use these new
instructions, you will need to rebuild the project::

    docker-compose build

And now you can try once more to open a Python shell and open a WebP image as you did in the previous section. In fact
you can launch a Python shell directly with:

..  code-block:: bash

    docker-compose bash

and then run:

..  code-block:: pycon

    >>> from PIL import Image
    >>> Image.open("test.webp")

And this time it will work - because you have baked support for WebP into the environment.

..  admonition:: Explanation

    Further reading: :ref:`Understanding the Dockerfile <dockerfile>`

