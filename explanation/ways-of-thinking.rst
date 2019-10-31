.. _explanation-thinking:

Ways of thinking
================

While you have been following the tutorial steps, you will have encountered some key aspects of containerised
development and deployment that make a difference to how you need to work.

In short, when you install something into a system, say using ``apt`` or ``pip``, it will persist - remain installed -
for the lifetime of the system. That could be indefinitely.

At the same time, one rarely rebuilds a server - or even a virtual server or a virtualenv - from scratch. Once it
has been created, it is added to, maintained, upgraded and so on.

However, containers are by their nature short-lived and impermanent, **and** they are recreated from scratch very often.

Here I will recapitulate some of the implications of this and introduce some new ways to help think about them.


Persistence and non-persistence
-------------------------------------------

The environment of containerised project, unlike that of a virtualenv for example, does not *persist*.

If you activate a virtualenv, install something in it, and come back to activate it again later, you will find your
installed package still in it, because the environment persists.

The changes will last forever.

The changes in a containerised environment *do not persist*. If you activate the environment, install something
in it and come back to activate it again later, you will find that what you installed is nowhere to be seen.

The changes to a container last only as long as the lifetime of that particular container; as soon as the container is
shut down, the changes will be lost.

**Understanding this is absolutely key to understanding how to work with containerisation.**


.. _traditional-deployment:

Traditional and containerised development and deployment
---------------------------------------------------------------------------

In 'traditional' development and deployment, you're building a system that you will eventually deploy. This is your
work of art, your precious construction. It's the expensive final end of your labours, that you've worked hard for. You
started with the basics, added on to them, made multiple refinements, until finally your creation was complete and
ready to face the world.

In containerised deployment, your final product is cheap and disposable. It's not precious at all, it's just an
instance, infinitely reproducible, of an image, and what's precious are the *instructions* that are followed to
assemble that image.

What matters is *not the thing we build*, but *the instructions we build it with*.

So: **stop working on the construction; work on instruction**.

In a way, this is the central lesson of this workshop, and once you have internalised it, you will find working with
containerisation much easier.


.. _cheap-disposable:

Containers are cheap
--------------------

Containers are:

* cheap: it costs almost nothing (time, computing resources, money) to create a new one
* disposable: throw them away when you finish using them, as soon as you finish using them; don't keep them around
* reproducible: if you can build one, you can build a thousand new ones with the same effort, and they'll all be
  exactly the same
* unchanging: once created, they stay the same until they are destroyed; no important changes are made to them or in
  them that must be retained for the future


Instructions are expensive
--------------------------

On the other hand, whereas otherwise you could start with the basics and then refine and massage them into shape - you
can't take that attitude any more. What you start with, when you build your containers, must be **perfect**. You must
come up with the perfect set of instructions, because once you set the build process in motion, what you get will be
what you asked for, and you won't have the opportunity to fix things up as you go along.

It's the difference between preparing for a night camping at the bottom of your own garden, and getting a rocket ready
for a trip to the moon.

One bit of good news is that Platform-as-a-service systems provide most of the instructions (well-tested,
expertly-written) for you - you only need to make your amendments.


Advantages and disadvantages for the developer
----------------------------------------------

You may feel that this way of working doesn't only bring advantages, and that's true - you do lose something.

*The disadvantage*: this is a slower and less streamlined, less interactive way to work. There's initially more
overhead from Docker and its set-up.

*The advantage*: you'll quickly gain back the little extra effort you spend, and more.

In fact you very quickly get to used to not thinking:

    I will make the changes on the Test server [or perhaps, in the development environment].

and instead start thinking:

    I'll write the change to the project repository and rebuild.


..  admonition:: Practical implications

    Until you are fully used to these ways of thinking, some of their implications will occasionally take you by
    surprise.

    For some of them, see :ref:`explanation-practical-implications`
