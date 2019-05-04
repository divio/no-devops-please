Deploying to the cloud
============================================================

If you've made a successful transition towards containerised ways of thinking in the local environment, let's look at
how they apply in deployment.

The same rules and principles apply, and most important of all, this one: your instructions are precious, but the end
result is disposable.

We have our instructions locally, which we amended to install Django Axes. So, let's push those instructions to the
cloud, where they can be executed there.

Use Git to commit the changed files (``requirements.in``, ``settings.py``) and push them. On Divio Cloud, your
project's dashboard will show that you have an undeployed commit.

You can deploy using the Control Panel, or by running::

    divio project deploy

You can also push site *content* to the cloud if you need to, with::

    divio project push media  # for media files
    divio project push db     # for the database

That will take care of the Test server; for Live, you'd do ``divio project deploy live``.

Other platforms will follow a similar process for deployment. The details will be different, but the principles are the
same.

And that's it.


Why is it so easy?
--------------------------------

If your server is a precious work of art, that you must labour over carefully, you become used to deployments being
tricky, risky, error-prone, potentially disastrous events. Deployments become the time when you (or somebody) have to
work hard or watch nervously (or both) in a DevOps role, working hard on to make sure it's successful and ready to to
jump in if it's not.

And no wonder that a single ``deploy`` command might seem excessively easy.

This is what containerisation and Platform-as-a-service systems can save you from, because it doesn't need to be that
way. Those responsibilities can be handed over to someone else.

**Your** part of the process is to stop being concerned with the precious work of art that will be built, to
concentrate on getting the instructions perfectly right, and to get used to using them to make your automated builds
over and over again, locally, and on the cloud.
