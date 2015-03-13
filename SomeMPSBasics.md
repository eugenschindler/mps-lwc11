Working with MPS is a little bit different because it is not an ASCII text editor but rather a projectional editor. Editing operations directly change the tree. Hence the editing gestures are quite a bit different. Also, sometimes you need to know where to look in order to achieve something. This page provides a couple of hints.

# Editing #

Editing is best shown with a screencast, because one can better show and explain how things work. We will add a screencast illustrating this in the near future.

There is one important thing, though: you can only ever enter what's available in the code completion menu (unless you're entering a string value, of course). Text you type is colored red, with a light red background until it is bound (by matching something in the code completion menu). So just typing along, everything being red, will lead nowhere.

You can use shift + cursor keys to select ranges. However, in many cases it is better to select "up the tree", because this is usually the unit of delete/copy/cut. You can use ctrl-up and ctrl-down to select along the tree hierarchy.

Sometimes you have to use the intentions to achieve something. For example, making a java class abstract cannot be done by typing "abstract" before the class keyword. Rather, you have to use an intention. So it is often a good idea to try to press alt-enter and see if the intentions menu allows you to do what you wanted to do.

Also, some things (e.g. in the editor definition) must be specified in the inspector. Make sure it is always open (alt-2 or **Window->Tool Window->Inspector** or via the button labeled _Inspector_ usually placed at bottom right). It is generally a good idea to keep it open, since it always shows you the name of and provides a direct link to the concept you're currently editing or supposed to instantiate. Often you can find also attributes that are for some reason not displayed in the main editing area here, e.g. making a class "abstract" as alternative to intentions explained above.

Pressing enter in a cell adds another cell after the current one.
Pressing Insert adds one before the current one.

Pressing Ctrl-Shift-S on any element opens its concept (aka structure).
Pressing Ctrl-Shift-E on any element opens its editor.

Another very useful "trick" is the Back functionality, that incrementally goes back to previous edit locations. I have used the Settings to assign the Alt-Backspace keyboard shortcut just like in current browsers. Very useful.

# Dependencies #

To import a language or stub into a Language, Model, Generator or Solution just press ALT-Enter and go to the dependencies tab. There you can add e.g. j.m.b immediately.

A **very** usefull shortcut is to import the used part automatically as follows (two ways):
  * with Ctrl-M import the model via its name (e.g. j.m.b)
  * with Ctrl-R import the model via a arbitrary element of that package (e.g. String)

# Checking #

If something does not work, you can always use Check Model or Check Language from the respective context menus. This will give you a list of all the errors in your code.

# Debugging #

As of now, there is no real debugger that can be used within the "meta programs", except the generation trace (see below). So you have to revert to "printf-debugging".
In any base language program you can use the shortcut _sout_ to expand into a System.out.println() statement.
To see the output start the java vm with console output.
  * on Windows make sure you start MPS with java.exe, not javaw.exe. You can change this in the mps.bat file that starts MPS.
  * on MAC invoke the shellscript "msp" in /Applications/MPS/MPS 2.0.app
  * on Unix ?

# Error/Warning Trace #

You use error or warnings to flag constraint or model violations in MPS.
As an example to check name uniqueness with a "non type system rule" in the typesystem (no joke :-), then you usually output some error or warnings. To trace back from the message to the origin there are two usefull shortcuts:
  * with Ctrl-Alt-LeftClick on the message and then click on Go To Rule
  * select the complete node which caused the error (e.g. Ctrl-W Ctrl-Shift-W) the context menu "Type System" -> "Go To Rule Which Caused Error"
  * instead of the context menu also Ctrl-Alt-R is possible

# Generation Trace #

When transforming models, you may want to trace how your model is transformed into other models, possibly over several intermediate stages. You can do this by enabling the Save Transient Models flag in the Generate menu. If you then generate models, you will find a new tree node Transient Models at the very end of the explorer.

![https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-transientModels.png](https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-transientModels.png)

If you open it, you can see the various intermediate stages of translation. Double clicking a root concept opens the respective (transient) model for inspection. You can then select any element and select Generation Trace and Generation Traceback from the context menu. This will show you into what the selected element has been generated (or what it has been generated from), including the translation templates that had been involved.

The following screenshot shows this. I have opened the marked EntityResource element (in the first transient model). I have then selected a complete attribute ("string name") and commanded Generation Trace. In the Generation Trace window at the bottom left you can see, in the tree view, into what this attribute was transformed. You can double click onto each of these elements to inspect them.

![https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-traceback.png](https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-traceback.png)

# Using the Explorer #

The structure of the tree of the model is often more or less directly visible from the syntax. However, sometimes the structure is unintuitive and you still need to understand it, for example to be able to extend it with your own concepts in a sub language. In this case, you can always mark any program element in the editor and select Show In Explorer. This shows the actual tree structure of any element in the explorer window.

![https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-nodeExplorer.png](https://mps-lwc11.googlecode.com/svn/trunk/lwc11/wiki-images/someMPSBasics-nodeExplorer.png)