Ways of thinking
================

We're going to take a little break from *doing* things, and stop to introduce a few ideas that you will need to keep in
mind as we continue.

.. _traditional-deployment:

In 'traditional' development and deployment, you're building a system that you will eventually deploy. This is your
work of art, your precious construction. It's the expensive final end of your labours, that you've worked hard for. You
started with the basics, added on to them, made multiple refinements, until finally your creation was complete and
ready to face the world.

In containerised deployment, your final product is cheap and disposable. It's not precious at all, it's just an
instance, infinitely reproducible, of an image, and what's precious are the *instructions* that are followed to
assemble that image.

What matters is not the thing we build, but the instructions we buid it with.

So: **stop caring about the things you build**.

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

With these thoughts in mind, let's continue to discover their consequences.
