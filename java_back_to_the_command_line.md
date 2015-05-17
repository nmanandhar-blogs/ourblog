#Java: Back to the Command Line

Introduction
============
> *It takes less time to become an expert than to remain a novice*


For those of us who started programming Java using IDE's or even build tools like Maven, the process of compiling a Java program, creating executable jar files may 
seem esoteric. Morever even though we have a vague sense of terms like classpath,sourcepath etc is, we have a vague nagging sensation that we really dont know
what they signify. The purpose of this blog is to understand these terms, create a working executable jar file forfeiting the comforts of the IDE. We will take
a scientific approach, formulate hypothesis ,design experiments and come to a conclusion. *So are you ready to go back to the command line ?*

##The Setup
We will create a folder structure similar to the folder structure of Maven.


* SimpleProject
  * src
    * main
      * java
          * com
              * mycompany
                * App.java 
                * geometry
                  * Point.java 
      * resources



![Alt Images](images\sampleImage.png)



The SimpleProject is our main project folder. Under it we have a folder src\main\java which will contain all our java source files. The src\main\resources folder will 
contain other files that should be in the classpath. For now, you can leave the resources folder empty

    com.mycompany.geometry.Point.java


    package com.mycompany.geometry;

    public class Point {
        private int x;
        private int y;

        public Point(int x, int y) {
            this.x = x;
            this.y = y;
        }

        @Override
        public String toString() {
            return String.format(" Point:(%d,%d) ", x, y);
        }
    }


    com.mycompany.App.java

    package com.mycompany;
    import  com.mycompany.geometry.Point;

    public class App {

        public static void main(String[] args) {
            Point point1 = new Point(4, 5);
            System.out.println("point1 = " + point1);
        }
    }

## Compiling the project.
In order the run the program we need to first compile it using javac. 

    $> javac -help
    Usage: javac <options> <source files>
    where possible options include:
      -verbose                   Output messages about what the compiler is doing
      -classpath <path>          Specify where to find user class files and annotation processors
      -cp <path>                 Specify where to find user class files and annotation processors
      -sourcepath <path>         Specify where to find input source files
      -d <directory>             Specify where to place generated class files
      -s <directory>             Specify where to place generated source files
      -encoding <encoding>       Specify character encoding used by source files
      -source <release>          Provide source compatibility with specified release
      -target <release>          Generate class files for specific VM version
      -help                      Print a synopsis of standard options

<br/>

    SimpleProject$>javac src\main\java\com\mycompany\App.java
    src\main\java\com\mycompany\App.java:2: error: package com.mycompany.geometry does not exist
    import  com.mycompany.geometry.Point;
                                  ^
    src\main\java\com\mycompany\App.java:7: error: cannot find symbol
            Point point1 = new Point(4, 5);
            ^
      symbol:   class Point
      location: class App
    src\main\java\com\mycompany\App.java:7: error: cannot find symbol
            Point point1 = new Point(4, 5);
                               ^
      symbol:   class Point
      location: class App
    3 errors

javac complains that the package com.mycompany.geometry does not exist and  it cannot find the symbol Point, that is used in the App class.

In order to make this work we have to pass all the source files in one invocation. Back to the command line

    SimpleProject>javac src\main\java\com\mycompany\App.java src\main\java\com\mycompany\geometry\Point.java
    The compiler does not emit any error messages, so compilation must have completed succesfully.
    Lets try running our program
    SimpleProject>java com.mycompany.App
    Error: Could not find or load main class com.mycompany.App

    SimpleProject>java -classpath src\main\java com.mycompany.App
    point1 =  Point:(4,5)

    Voila our program finally runs when we pass in the claspath.

    Let's try another experiment. Change the current directory to src\main\java
    SimpleProject> cd src\main\java
    SimpleProject\src\main\java> java com.mycompany.App
    point1 =  Point:(4,5)

Holy Moly!!!. When we run the application from src\main\java our program runs without having to provide the classpath. [Note if your program still does not run 
don't worry. You will be able to figure out why by the end of this blog]

Ok let's recap. We know that we need to pass all the java source files on the command line or javac will complain. Wait... that's not entirely true. Let's try another
experiment.

If you are not already inside src\main\java change cd to the directory.
SimpleProject\src\main\java> javac com\mycomapny\App.java

This time the program compiles again without having to provide all the java files on the command line. That's very reassuring indeed, however we would like to compile
and run our program from the Project folder and avoid having to change directory to src\main\java everytime. Also if you noticed, all the compiled classes are placed
in the same directory as the source files. We would like to put the compiled classes in a seperate folder. After all, we would put source file in a version control
while we should not put the compiled classes in version control and having them in the same directory clutters up the source directory and increases the changes
that we mistakenly commit the files.

Ok let's do it the way it should be done

    SimpleProject>mkdir target
    SimpleProject>javac -sourcepath src\main\java -d target src\main\java\com\mycompany\App.java
    SimpleProject>java -cp target com.mycompany.App
    point1 =  Point:(4,5)

