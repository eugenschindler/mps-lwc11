# Task #

0.3 Simple constraint checks such as name-uniqueness

For example, check the name uniqueness of the properties in the entities.

# Code #

http://mps-lwc11.googlecode.com/git/lwc11/archives/lwc11-3-at03.zip

# Details #

MPS has different kinds of constraints which we'll describe briefly here. For the 0.3 task we will only need one of them.


## Concept-specific Constraints ##

Note that all of these constraints are not "just" constraints in the sense that they check an existing model. Rather, they prevent a potentially wrong model from being built; this means, they affect code completion as well.

**Reference Constraints** define which nodes are valid targets for a reference (a pointer). The constraint returns the set of valid target nodes. These kinds of constraints are otherwise known as scopes and are demonstrated in a later step of this tutorial.

**Property Constraints** are invoked when working with properties. Remember, properties, unlike child relationships, are primitives (int, string or boolean). As part of the property constraint you can define a setter, a getter and a validation check.

Let us implement a simple check that makes sure that properties start with a capital letter. Open the Entity concept and select the constraints tab. It is probably empty, so you should click into the white area to create a new one. You can then create a new property constraint by pressing enter in the respective slot. This is how the result will look:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-propertyConstraint.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-propertyConstraint.png)

You can now choose the name property in the respective field and press enter in the "is valid" slot. Here you can now enter a boolean that determines whether the value is valid. Here is a possible implementation:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-propertyConstraintImpl.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-propertyConstraintImpl.png)

If you regenerate the language and go back to the example model, you cannot even enter a name with a lower case first character, it doesn't bind and the entered value stays red. The disadvantage is that you cannot define an error message here for your validation rule. So you might want to use the non typesystem rule approach to constraints defined below alternatively (which as also known as "NonTypesystemRule").

There are also constraints that can be used to validate the tree structure beyond what the structure definition can do. They are called "can be child", "can be parent", and "can be ancestor". Note that these constraints also influence the code completion menu! So they prevent "wrong trees from being built".

  * can be child: you get passed in the parent node of the current node (which is an instance of the concept for which you currently define the constraints), and you determine whether your current concept can potentially live below the passed in parent. For example, if you'd add a can be child constraint to Entity with the body of !(parentNode.isInstanceOf(EntityResource)); then this would prevent Entities from being added under an EntityResource. Note how this constraint is being written from the perspective of the child, i.e. you modify the child concept (by adding the constraint). For the next two, this is the other way round.
  * can be parent: you get the concept passed in you could instantiate (it's not yet a node because the constraint is evaluated before the tree is constructed!) and you determine whether this kind of child would be valid. For example, if I added the code !(childConcept == concept/Entity/); as the can be parent constraint for EntityResource, it would not be valid to add Entities below an EntityResource. Note how this constraint is written from the perspective of the parent, i.e. you modify the parent concept.
  * can be ancestor is like can be child, but it works recursively down the tree, i.e. the children don't have to be direct children. For example, if I wrote !(childConcept == concept/EntityType/); in the can be ancestor constraint for EntityResource, I could not add any EntityTypes as descendents at any nesting level below EntityResource. Note that this one is potentially much slower than the can be child, so use the latter whenever possible.



# Cross-Concept Constraints #

Cross-concept constraints are those that affect (or potentially, could affect) more than one element. You can find
all of them hosted in the typesystem language aspect.

One class of constraints is the typesystem itself. We'll talk about this later when we talk about - type systems :)

There is one odd one in the type system rules: the NonTypesystemRule :-) This is the one we use to implement the
name uniqueness.

Go to the type system aspect and use the context menu to create a NonTypesystemRule. Give it a name (such as
"checkNameUniqueness") and in the condition slot press control space to select the concept option. This leads to:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames1.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames1.png)

We then have to define, for which concept this check should be applicable. We want to write a constraint that
checks that attribute names are unique. We write this constraint from the perspective of the Attribute, by checking
if in the Attribute's parent's attributes there are more than one that have the name of the attribute for which we
run the check (actually, this very statement is easier to program than to say :-)) So we select _EntityAttribute_
as the concept and give it a reasonably short local name (the one after the "as").

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames2.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames2.png)

Now we have to implement the actual check.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames3.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step3At03-uniqueNames3.png)

Let's look at the code in detail:

```
attribute   // this is the local name of the current instance 
.parent     // predefined MPS property that returns the owner of the current element
: Entity    // colon is the node-typecast. It casts the parent down to node<Entity>
.attributes // returns all the attributes of that owning Entity
.where({~it => it.name == attribute.name; })  // select only those whose name is the name as the current node's name
.size > 1   // and then check if there's more than one element in the resulting collection
```

The error construct defines the error message to be output, as well as the node to which the error
message should be associated with. Alternatively, there is also a warning construct.

After rebuilding the language, you can try to add two attributes with the same name. If you do, these two are both
underlined red, and you also get the error messages reported in the model checker.

Also a tip: with Ctrl-Alt-LeftClick on a error message you can go back to the rule which fired/triggered this error message. Also see the [Basics](http://code.google.com/p/mps-lwc11/wiki/SomeMPSBasics) document for further tricks.