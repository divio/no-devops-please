.. But I never wanted to do DevOps! documentation master file, created by
   sphinx-quickstart on Wed May  1 10:52:58 2019.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

But I never wanted to do DevOps!
============================================================

You can be a Python web programmer and not have to do anything with DevOps, by making use of containerisation
technology and Platform-as-a-service systems.

This hands-on workshop will expand your repertoire and put new components in your developer's toolbox - and will help
ensure you never find yourself having to answer to a pager in the middle of the night.

The examples and exercises in the workshop will be based on a workflow using:

* `Docker <http://docker.com>`_, with the
* `Divio Cloud platform <http://divio.com>`_ and
* `Amazon Web Services <https://aws.amazon.com>`_.

We will also use:

* `Django <https://djangoproject.com>`_ (some basic skills required)
* `LogDNA <https://logdna.com>`_
* `Sentry <http://sentry.io>`_ (to be completed)
* `Elastic search <https://www.elastic.co/products/elasticsearch>`_ (to be completed)

... and other tools, but it's about **skills**, not **tools** - and *all the
skills are transferable*. The purpose of the workshop is to help you become
familiar woth a mindset and way of working that:

* **allows you to stop worrying about servers and deployment**
* ... **by making use of services that take care of that for you**.


Who is this for?
-----------------

This workshop has been written for the Python developer who likes programming, but would like to give the
responsibility for configuration and maintenance of hardware, operating systems, databases, web servers and other
important things that a website needs to someone else.

Containerisation is an excellent way to do that - but then you have to learn some new tricks, and abandon some old
habits and ways of working. This workshop is intended to ease you along that path.

It's what I wish I had discovered several years ago, when I was exactly in that position.


..  toctree::
    :maxdepth: 2
    :caption: Contents

    10-requirements
    20-installation
    30-create-project
    40-deploy-project
    50-local-set-up
    60-explore-local-docker-docker-compose
    70-explore-local-docker-dockerfile
    75-ways-of-thinking.rst
    80-local-development
    90-cloud-deployment
    100-adapting-to-containerisation
    110-external-services
