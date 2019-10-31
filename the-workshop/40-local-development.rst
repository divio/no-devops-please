Install a new Python package
============================================================

..  admonition:: Introducing

    * the ``requirements.in`` file


Next, we're going to install a new package, `Django Axes <https://github.com/jazzband/django-axes>`_, into the project
(Django Axes keeps track of log-in attempts).

Maybe you are used to using pip to install Python packages into your environment. You can try it - launch a bash shell
in a container as you did previously, and run

..  code-block:: bash

    pip install django-axes==3.0.3

It will work - but what do you think will happen after you exit the container and launch it again? Will ``django-axes``
still be there? You can check with:

..  code-block:: bash

    docker-compose run web pip list

As you will realise, pip installation (just like other operations on the environment) doesn't survive the statelessness
of Docker containers.


Build the pip installation into your Docker image
--------------------------------------------------

You have already used the ``RUN`` command in the ``Dockerfile``, so you could do the same again, adding:

..  code-block:: Dockerfile

    RUN pip install django-axes==3.0.3

However, this is a bit crude and won't look very nice when we have to add many packages. In fact, there are already
some lines in the ``Dockerfile`` that take care of pip installation for you:

..  code-block:: Dockerfile

    ENV PIP_INDEX_URL=${PIP_INDEX_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/+simple/} \
        WHEELSPROXY_URL=${WHEELSPROXY_URL:-https://wheels.aldryn.net/v1/aldryn-extras+pypi/${WHEELS_PLATFORM:-aldryn-baseproject-py3}/}
    COPY requirements.* /app/
    COPY addons-dev /app/addons-dev/
    RUN pip-reqs compile && \
        pip-reqs resolve && \
        pip install \
            --no-index --no-deps \
            --requirement requirements.urls

These lines process the ``requirements.in`` file, in which you will already find some packages listed. Add::

    django-axes==3.0.3

right at the end of the file. Now you will need to rebuild the project because you have made a change. Run::

    docker-compose build

Now the *image* for the project contains the new requirement - this time you *will* see it if you run::

    docker-compose run web pip list

because (like the WebP support in the previous step) you have baked it into the image, and it will be available in
any environment created from that image, from now on.


Configure the Django application for django-axes
----------------------------------------------------

The only configuration required for Django Axes is to add it to the ``INSTALLED_APPS`` in ``settings.py``. This project
uses Divio's optional Django project with auto-configured settings, so the way to do it is with:

..  code-block:: python
    :emphasize-lines: 4

    # all django settings can be altered here

    INSTALLED_APPS.extend([
        "axes",
    ])

You will need to run migrations::

    docker-compose run web python manage.py migrate

And here's Django Axes in the admin, which you will see if you start the project up again (``docker-compose up``):

.. image:: /images/axes.png
   :alt: 'Django Axes in the admin'
   :width: 663
