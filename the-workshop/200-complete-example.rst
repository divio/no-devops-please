A complete example workflow
===========================

To help you practise the skills you have learned, here is an example of a complete workflow, using `Wagtail
<https://wagtail.io>`_ (a very popular Django-based content management system), Azure's Cognitive Services API, OpenCV
and more.

In this exercise, you will:

* create a project
* add and deploy some custom code
* integrate OpenCV for feature and face detection in images
* use a third-party Wagtail application that includes an API for image content recognition, via Microsoft Azue.


Create the project, on the cloud and locally
--------------------------------------------

Create a new Wagtail project at https://control.divio.com/control/project/create/ (skip the Subscription page). Deploy the Test server.

Set the project up locally, with ``divio project setup <project id>``.

Check that it runs as expected: ``cd`` into the project, and run::

    docker-compose up

You can log into it at http://localhost:8000/django-admin, and see the Wagtail admin at http://localhost:8000/admin.


Build a new application
-----------------------

Create the application
~~~~~~~~~~~~~~~~~~~~~~

Next, we'll add a new "home" application to the project, by extending Wagtail's ``Page`` model.

Run:

    ``docker-compose run --rm web python manage.py startapp home``

This issues the Django startapp command, inside the web container. You will find the files for the new ``home``
application in the project.

Add the new HomePage model
~~~~~~~~~~~~~~~~~~~~~~~~~~

In the new application (at ``home/models.py``), add a new ``HomePage`` model (this example is copied straight from the
`Wagtail documentation <http://docs.wagtail.io/en/v2.5.1/getting_started/tutorial.html>`_)::

    from django.db import models

    from wagtail.core.models import Page
    from wagtail.core.fields import RichTextField
    from wagtail.admin.edit_handlers import FieldPanel


    class HomePage(Page):
        body = RichTextField(blank=True)

        content_panels = Page.content_panels + [
            FieldPanel('body', classname="full"),
        ]


Add a template for it
~~~~~~~~~~~~~~~~~~~~~~~~~~

Create a new file at ``home/templates/home/home_page.html``::

    {% extends "base.html" %}

    {% load wagtailcore_tags %}

    {% block body_class %}template-homepage{% endblock %}

    {% block content %}
        {{ page.body|richtext }}
    {% endblock %}


Add a base template for the whole project
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

As you can see, the template above ``extends "base.html"``. So, create ``templates/base.html`` in the root of the
project (not the ``home`` application)::

    <h1>{{ page.title }}</h2>

    {% block content %}{% endblock %}


Configure Django settings
-------------------------

In the project's ``settings.py``, edit the ``INSTALLED_APPS`` list, to add the ``home`` application::

    INSTALLED_APPS.extend([
        'home',
    ])


Create and run migrations
--------------------------

The project has new models, so we need to create and run migrations for them::

    docker-compose run --rm web python manage.py makemigrations home

followed by::

    docker-compose run --rm web python manage.py migrate

Check that you can now create new pages with a ``body`` field.


Deploy your changes
-------------------

Git add, commit and push your changes to the Cloud.

Then you can either deploy the Test server using the Control Panel, or run::

    divio project deploy


Implement image feature detection
---------------------------------

Upload an image to Wagtail. You will find that you can draw a 'focal point' around the important part of an image,
such as the subject's face. This is used when cropping automatically. But, Wagtail has a nice feature: automatic
detection of key areas of an image.

To enable the feature, add::

    WAGTAILIMAGES_FEATURE_DETECTION_ENABLED = True

to the ``settings.py``. Before it will work though, we need to add OpenCV to the project.


Install a package via ``requirements.in``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

In your project's ``requirements.in``, add::

      https://files.pythonhosted.org/packages/7b/d2/a2dbf83d4553ca6b3701d91d75e42fe50aea97acdc00652dca515749fb5d/opencv_python-4.1.0.25-cp36-cp36m-manylinux1_x86_64.whl

(This is from https://pypi.org/project/opencv-python/.)


Install system libraries
~~~~~~~~~~~~~~~~~~~~~~~~

We also need some system libraries for OpenCV. In the ``Dockerfile``, add::

    RUN apt-get update && apt-get install -y libsm6 libxrender1 libxext6

(after the ``# <DOCKER_FROM>...# </DOCKER_FROM>`` is a good place).


Rebuild
~~~~~~~

The changes you have made require that the project be rebuilt::

    docker-compose build

Once completed, you should be able to upload an image and see the automatic feature detection.


Implement image content detection
---------------------------------

The third-party `wagtailaltgenerator <https://pypi.org/project/wagtailaltgenerator/>`_ is a nice addon for Wagtail.

Before you use it, you need to obtain a key for `Azure cognitive services <https://azure.microsoft.com/en-gb/>`_ - you
can get a free account and key. Once you have your key:

* add ``wagtailaltgenerator`` to the ``requirements.in``
* add ``wagtailaltgenerator`` to the ``INSTALLED_APPS``

Then add key to the ``setting.py``, for example::

    COMPUTER_VISION_API_KEY = '99265265444f147baecb737f38074bca'
    COMPUTER_VISION_REGION = 'centralus'

Once again:

* docker-compose build
* test locally
* push to cloud
* deploy on cloud

and finally, once you're happy with it on the Test server, you can also deploy to Live::

    divio project deploy live
