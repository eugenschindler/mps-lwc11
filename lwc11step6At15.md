# Task #

1.5 Integrating manually written code (again in Java, C# or C++).

Integrate derived attributes to entities.

# Code #

http://mps-lwc11.googlecode.com/git/lwc11/archives/lwc11-6-at15.zip


# Details #

Step 6, according to plan, is 1.2, implementing a type system. I have taken the freedom to change the ordering of the tasks, and we're going to do task 1.5 now. This is because implementing a type system is more interesting if we do it in a Java world.

Notice that the techniques used for implementing the type system would be the same if we did 1.2 now, without Java involvement. But doing it with Java is more interesting.

So this is task 1.5, providing support for manual code, exemplified by the derived attributes. In MPS, there is no real "manually written code", since you will do the coding in the same environment as the "modeling". Consequently, we have decided to implement the derived attributes as expressions directly in the entity declaration.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-result.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-result.png)

There are, of course, all kinds of other ways of where to put the code: in sepate constructs, as statement lists inside the entity etc. The example is the simplest way to do it, while still demonstrating the integration with Java.

## Language Dependencies ##

Since we now want to use Java constructs in our own language, we have make the entities language extend Java -- which is called jetbrains.mps.baseLanguage in MPS. So we add this language in the Extended Languages section in the language properties of entities.

## Adapting Attribute Types ##

In the entities language we had developed our own type stuff: Type, PrimitiveType, StringType, IntType, BoolType and EntityType. BaseLanguage comes with its own representation for all of these (except the EntityType, of course). We could somehow integrate our own types with the types in BaseLanguage. But it is much more pragmatic to throw our own types away.

So, in our entities language, we delete Type, PrimitiveType, StringType, IntType and BoolType. You can simply select the corresponding concept structure nodes and delete them. In the dialog do not select "Safe delete". Of course various dependent things are now broken. You can run Check Model on the entities language to see all the problems:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-modelChecker.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-modelChecker.png)

The errors are obvious: editors now reference non-existing concepts; you can simply delete the editors. Also, in the generator, the reduction rules for those concepts are of course broken. Delete them as well. The problems in the structure are more interesting.

For one, our EntityType, which extends Type, is broken, because Type doesn't exist anymore. In our new world we want our EntityType to extend baseLanguage's type -- just select the respective Type concept from the code completion menu.

A similar change has to be performed in the EntityAttribute: its type property must now use BaseLanguage's Type.

Notice that you can now use any Java type as the type of the attributes, try to press ctrl-space in the type slot of an EntityAttribute. This is not what we wanted! We want to use only the primitive types. We can achieve this by using the Can Be Parent constraint we mentioned before. Note how we use the link/../ and concept/../ concepts to access link and concept literals.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-canBeParent.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-canBeParent.png)

## Adapting Instance Expressions ##

A similar change should be performed in the instances language. The EntityAttributeValue uses its own expressions (EAVExpression, IntConstantExpression, ...) to put on the right side of an EntityAttributeValue. We delete all this expression stuff and change the expr property of EntityAttributeValue to Expression from BaseLanguage.

So we can now write models/code similar to what we had before:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-anotherExample.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-anotherExample.png)

But the types are now Java primitive types, and the expressions (right side of =) are now Java expressions. This means we can automatically do this:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-yetAnotherExample.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-yetAnotherExample.png)

Nice :-)

Now you're back to where we were before, but based on Java.


## Derived Attributes ##

Now let's get to the derived attributes. We could use several approaches, for example, using a subconcept (DerivedAttribute extends EntityAttribute). An alternative solution is the one we implement here: each EntityAttribute has a 0..1 child of type Expression called derivationExpression.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-derivationExpression.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-derivationExpression.png)

The convention is that whenever this expression is not null, then we have a derived attribute.

So the first consequence is for the editor: it must show the keyword "derived" if the expression is not null. Similarly, the editor has to show the actual derivationExpression if it is not null. Here is the new editor definition:

TODO: is missing

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-conditionalEditor.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-conditionalEditor.png)

The interesting things is the two conditional cells, marked up by the question mark. If you click on the "derived" cell and take a look at the inspector, you can see the Show If condition:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-condCell.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-condCell.png)

It determines when the cell is shown -- so, it only shows if the expression is not null. We do the same thing for the equals sign and the derivationExpression itself. We've put the equals sign and the expression cells into an additional indent collection, so we only need one condition.

Now, the question is: how can this expression become non-null if you don't even show it, so nobody can enter something into it? This is a good case for using an intention. You can press alt-enter on any attribute and get:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-intentionExample.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-intentionExample.png)

Selecting the intention gives you:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-intentionExample2.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-intentionExample2.png)

You can now enter an expression.

Let's see how to do this. On the language, use the context menu to create an Intentions aspect for the entities language. On it, use the context menu to create a new Intention Declaration.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-emptyIntention.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-emptyIntention.png)

An intention has a name, it is associated (i.e. accessible from) an concept, it has an expression that is used as the label, it has a condition that determines whether it is applicable in a given context, and it has the body which is run when the intention is executed. Below is the code for the intention at hand. The comments should explain what's going on.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-derivationIntention.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-derivationIntention.png)


## Referencing Attributes ##

So far so good. We can now add any kind of valid Java expression for derived attributes. However, we cannot reference existing attributes of the same entity in these expression -- rendering them entirely useless in the current form :-)  Let's fix this.

We use the same approach we've used before, basically a use of GoF's Adapter pattern. We define a subconcept of Java's expression called EARefExpr that references an EntityAttribute:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttr.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttr.png)

Whenever we have a reference, we also need to define a scope. Here it is:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttrScope.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttrScope.png)

From the expression, we climb up the tree until we find an Entity (the plus in the ancestor function says that we should include the immediate parent in the search). And then we return its attributes, without the one we're currently defining the expression for (no recursive definitions!).

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttrScope2.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefAttrScope2.png)

Regenerate the language and play with it. It should work.

## Adapting the Generator ##

We first have to adapt the existing generator in the following way:

  * generate a private member only for non-derived attributes
  * generate a setter only for non-derived attributes
  * in the getter, embed the derivation expression in case of derived attributes

To make this simpler, we first create a utility behaviour on the EntityAttribute:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-isDerived.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-isDerived.png)

We can then adapt the generator to only generate members and setters for non-derived attributes:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-membersForNonDerived.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-membersForNonDerived.png)

The interesting place is the getter, where we need different bodies depending on whether the thing is derived or not. I have implemented this with two conditional generation rules:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-conditionalGenerator.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-conditionalGenerator.png)

In the first alternative, where the condition in the if returns true if the attribute is derived,  we embed the actual expression (more on this below). The second one returns the field in case it is non derived.

Let's look at how we embed the derivationExpression in this method. They we do this is first by returning "something", in this case we use the string literal "12". We then use a COPY\_SRC macro that replaces this string literal with the derivationExpression. Here is the code in  the body of the COPY\_SRC macro:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-inspectorForCopySrc.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-inspectorForCopySrc.png)

It simply returns (a copy of) the derivationExpression **and** applies the reduction rules defined for the expression (elements) on the fly.

So we're almost done. One step is missing. We have to deal with the references to other attributes, these EARefExpr. We have to transform them to calls to the respective getter. Here is the template, and I guess it warrants some explanation.

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefExprRedRule.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-eaRefExprRedRule.png)

First of all: the red <TF  ... TF> thingies mark the so-called template fragment. In other words, only this part used to replace the input node when the reduction rule is executed. So why do we have all this other stuff?? The reason is that MPS templates have to be structurally correct. And since we want to be able to call another method (the getter!), we have find a way to put this "other method" somewhere so we can call it from withing the template fragment code. If you look at the code in the template fragment, it calls this (dummy) method called "setter". We then use the reference macro (->$) to replace it with a reference to the getter method generated from  the attribute our current EARefExpr references. Here is the code for the reference macro:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-refMacro.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-refMacro.png)

Here we simply construct the name of the target getter (yes, we should externalize this into a behaviour and call it from here and from the place where we generate the actual getter).

Note also that we make use of the "trick" to return the name of the target element. Alternatively we could also use mapping labels. However, in many cases the name trick is less work.

## Testing the Example ##

If we generate code from this model:

![http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-exampleAgain.png](http://mps-lwc11.googlecode.com/git/lwc11/wiki-images/lwc11step6At15-exampleAgain.png)

here is the generated code (the getGreeting() method is the interesting part):

```
public class Person {
  private String name;
  private String fistname;
  private String gender;
  private Car car;

  public Person() {
  }

  public Person(String name, String fistname, String gender, Car car) {
    this.name = name;
  }

  public void setName(String value) {
    this.name = value;
  }

  public void setFistname(String value) {
    this.fistname = value;
  }

  public void setGender(String value) {
    this.gender = value;
  }

  public void setCar(Car value) {
    this.car = value;
  }

  public String getName() {
    return name;
  }

  public String getFistname() {
    return fistname;
  }

  public String getGender() {
    return gender;
  }

  public String getGreeting() {
    return (this.getGender() == "male" ?
      "Hello Mr. " + this.getName() :
      "Hello Ms. " + this.getName()
    );
  }

  public Car getCar() {
    return car;
  }
}

```