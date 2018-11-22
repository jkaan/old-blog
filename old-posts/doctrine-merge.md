---
title: "Doctrine Merge"
date: 2014-08-19 20:56:00
tags: [doctrine, orm, php]
---

This will be a very short post about the merge method that exists on the entity manager of Doctrine.
I have encountered this issue when working on a project at work.

It's very easy to forget about the internals of Doctrine since it's such an easy ORM to work with.

When you set the user on the token to pass to the security context that token will be saved in the session. When you retrieve the security context and hereby the user the entity isn't known to Doctrine to be managed. You would however think it was. The reason for this is because the object retrieved is stored in the session and therefore doesn't act like a normal Doctrine entity. To overcome this issue you use the merge method of Doctrine's entity manager.

The merge method takes one parameter, the object to merge with an object from the database. The merge method compares the entities inside the database, will merge them if the correct one is found and then return the entity. This returned entity is now managed by Doctrine which allows you to continue as you would otherwise.

So remember, if you are ever working with some application which would save an entity to a session. Be sure to, after retrieving this entity from the session, always call the merge method by passing the entity as a parameter.
