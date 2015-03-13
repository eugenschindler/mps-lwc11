# Task #

1.3 Show how to do a model-to-model transformation.

Define an ER meta model (Database, Table, Column) and transform the entity model into an instance of this ER meta model.

# Code #

http://mps-lwc11.googlecode.com/git/lwc11/archives/lwc11-8-at13.zip

# Details #

As we have said several times, model transformation and code generation are the same process in MPS. In this sense, we will not learn anything new in this section: you simple define a new language (consisting of Database, Table, Column, with the usual properties and Relationships) and then you write transformation templates to do the transformation. Please take a look at the source code to understand the (trivial) ER language.

However, you can also use another transformation approach: you can write (almost) everything as a script, avoiding most of the templates stuff. Let's take a look.

## Implementing a transformation script ##

As you can see when you look at the code, our new language that contains the database meta model is called entityRelationship. It contains the language definition, you might want to spend two minutes to familiarize yourself with the structure.

Our new language also contains the generator. We'll take a look at it in detail in a minute, but since it transforms entities to tables, our language has to have a dependency to the entities language. If you look at the generator, you can see that it contains two models.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-projectStructure.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-projectStructure.png)

One is stereotyped @generator; this one contains the templates. The other one is a "normal" model that contains some utility classes we will use below.

Let us first look at the generator model. It contains a mapping configuration that has a root mapping rule that maps an EntityResource with the ADatabase template. Nothing special so far.

The ADatabase template is somewhat special, since it contains only one macro:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-template.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-template.png)

This one macro, as you can see, replaces the whole database with what the createDatabase function in the Helper class returns. In other words, we have moved the whole creation of the database, based on the input EntityResource, into one helper function. Here is the  transformation code, with comments:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-trafoCode.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-trafoCode.png)

Whenever the template approach seems to cumbersome, you can always fall back to a transformation script. This script looks like "normal Java programming" on a tree..
As you can see, its also possible to start with a template and then mix it with script code.

At this point, the transformation itself is finished. We can now try it out.


## Running the transformation ##

One special thing about this transformation: there is no subsequent "code generator" that generates text from the database. So in order to see the result of the transformation, you need to switch on the Save Transient Models option in the Generate menu.

Then you need to tell your example model that it should not just generate Java code, but also engage the database generator. On the Advanced tab in model properties you can add this language to the Languages Engaged on Generation list.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-advanced.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-advanced.png)

If you now Generate Files, you can see the transient models at the end of the project explorer tree.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-transient.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step8At13-transient.png)

You can open the databases and see the result.