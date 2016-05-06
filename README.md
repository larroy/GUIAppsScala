# Writing reactive user interface applications in Scala. (Part 1)

In this tutorial, I will describe a step-by-step way to create simple UI applications in Scala that
will run seamlessly between platforms, specifically the major desktop platforms such as Mac, Linux,
Windows. I have tested this approach in all of the platforms without major issues. I have read that
deploying [JavaFX](https://en.wikipedia.org/wiki/JavaFX) apps to Android is possible, but I haven't tried this. Please let me know if you
do.

## Tools
We will use the following tools to develop our application, please refer to the specific
installation instructions for each tool and your development platform:
- [Java 8 SDK](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- The interactive build tool [SBT](http://www.scala-sbt.org/)
- [Intellij Idea](https://www.jetbrains.com/idea/download/)
- [Scene Builder](http://gluonhq.com/open-source/scene-builder/) from gluon hq, as Oracle doesn't
  provide binary builds anymore.

## General approach
Our approach is based on [JavaFX](https://en.wikipedia.org/wiki/JavaFX) which replaces
[Swing](https://en.wikipedia.org/wiki/Swing_(Java)) for Java based UI applications.

We will use the [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) paradigm,
I will assume from now that you have a basic understanding of it. Our **view** would be coded in an
XML file, specifically an
[FXML](https://docs.oracle.com/javase/8/javafx/fxml-tutorial/why_use_fxml.htm) description of the UI
which can be generated with the **Scene Builder**.

Our FXML view is then stored as a
[resource](https://docs.oracle.com/javase/8/docs/technotes/guides/lang/resources.html) in our
application, under `src/main/resources` and loaded at runtime.

The widgets specified on our **FXML view** are then bound to our **controller** class in Scala.
This is done in
[Application.start](https://docs.oracle.com/javase/8/javafx/api/javafx/application/Application.html#start-javafx.stage.Stage-)

The wiring of these classes is done in two places:
- In the FXML file the [controller is
  specified](https://docs.oracle.com/javafx/2/api/javafx/fxml/doc-files/introduction_to_fxml.html#controllers)
  using `fx:controller="com.foo.ModelController"`

- Then in the start method of our class extending `Application` (our main class) we get an instance
  as: `val modelController = fXMLLoader.getController.asInstanceOf[ModelController]`

In **ModelController** we bind the variables for the widgets using `@FXML` annotations
```
@FXML
var myButton: Button = _
```

These variables are bound to the widget objects created when the FXML template is evaluated at
runtime.

With this in mind, the workflow is similar to the one used in the [FXML tutorial in
Java](http://docs.oracle.com/javafx/2/get_started/fxml_tutorial.htm), with the caveat that you might
find useful to add and call a `postInitialize` method on **ModelController** to pass the stage and
the scene to ModelController or anything else that you want to wire in your ModelController so it
can do useful things with the events coming from the GUI:

So in our overriden Application.start we will do:
```
// Get the fxml file
val fxmlUrl = getClass.getResource("/view.fxml")
val fXMLLoader: FXMLLoader = new FXMLLoader(fxmlUrl)
  ..  fXMLLoader.load()
[...]
val modelController = fXMLLoader.getController.asInstanceOf[ModelController]
modelController.postInitialize(stage, scene, ...)
```


In Part 2, we will show how to make our applications reactive and show a full example.


## Links
- [JavaFX API docs](https://docs.oracle.com/javase/8/javafx/api/toc.htm)
