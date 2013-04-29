% Static Typing

I wasn't comfortable with the html file that Nic sent out, so I started work in in MD. I left numbers in just to indicate what I was thinking of including on each slide. Clearly we'll need to move things around and modify them - and delete the numbers.

1
Classes: The New Sugar in JavaScript
====================================

By Nicandro Flores, Megan Greening, and Brian McWilliams
--------------------------------------------------------

2
How did we get here?
====================

*ECMAScript is a specification for scripting languages
*TC39 is tasked with writing the new spec, called Harmony or ECMAScript 6 (ES6)
*Classes will be added in Harmony
* Programmers are divided on whether or not this is a good thing

The JavaScript Specification

 + ECMAScript is the JS specifiaction
   http://www.ecma-international.org/ecma-262/5.1/
              
 + TC39: group in charge of writing ECMAScript6
   http://www.ecma-international.org/memento/TC39.htm

 + The new spec will be out in late 2013


3
The Prototype Way
==================


*Classes can be simulated in JS since objects are highly versatile
*"Prototype" is the property that allows programmers to simluate protoype-based classes

Constructor Functions
---------------------

~~~~javascript
    > function Person(name) {
      this.name = name;
      }
    > Person.hasOwnProperty('name') // => true
    > Person.prototype              // => {}
    > Person.prototype.sayName = function() { 
        return "Hi I am a Person and my name is: " + this.name; 
      };
    > Person.prototype              // => { sayName: [Function] }
    > function Employee(name,title,company) {
        Person.call(this,name);
        //Person.apply(this,['name']);
        this.title = title;
        this.company = company;
      }
~~~~


Prototype Inheritance
----------------------

~~~~javascript
    > function Employee(name,title,company) {
        Person.call(this,name);
        //Person.apply(this,['name']);
        this.title = title;
        this.company = company;
      }
    > Employee.prototype = new Person('John Doe','TBD','Unemployed');
    > Employee.prototype.constructor = Employee;
    > Employee.prototype.describe = function() {
        return Person.prototype.sayName.call(this,this.name) 
               + ", I am a " + this.title + " at " 
               + this.company + "!";
      }
~~~~

Now let make some instances of these objects/classes
----------------------------------------------------

~~~~javascript
    > var me = new Person('Nic');
    > var prof = new Employee('Jim Baker','code slinging Ninja','Rackspace');
    > console.log(me.sayName());
      Hi I am a Person and my name is: Nic
    > console.log(prof.sayName());
      Hi I am a Person and my name is: Jim Baker
    > console.log(prof.describe());
      Hi I am a Person and my name is: Jim Baker, 
      I am a code slinging Ninja at Rackspace!
~~~~


4
Code Example of Prototypes
==========================
*Some or all of the employee example? This might be two slides' worth.

5
More Code to Complete Protype Discussion
========================================

6
Maximally Minimal Classes - What Do Classes Need?
=================================================
*A Class keyword 
*A body that includes the constructor function and instance methods
*Cand declare a class as a subclass of another class using extends
*Super is available from any of the methods or the constructor

7
Formal Class Grammar
====================
<Note/speaking> When we began gathering resources for this topic, we were suprised to be able to apply what we just learned.  The task group was describing the new feature in Backus–Naur Form.   Here we see the top level Class definition.  It is a little harder to make out than what we did, but we can see that ClassDeclaration returns a Binding Identifier and a Class Tail, then a ClassTail.
<End speaking><p>
<center>Class BNF</center>
<pre>
ClassDeclaration: class BindingIdentifier ClassTail
ClassExpression: class BindingIdentifieropt ClassTail
ClassTail: ClassHeritageopt{ ClassBodyopt}
ClassHeritage : extends AssignmentExpression 
ClassBody: ClassElementList 
ClassElementList: 
    ClassElement 
    ClassElementList ClassElement 
ClassElement:
    MethodDefinition;
MethodDefinition :
    PropertyName(FormalParameterList) {FunctionBody}
    *PropertyName(FormalParameterList){FunctionBody}
    get PropertyName ( ){FunctionBody}
    set PropertyName(PropertySetParameterList) {FunctionBody}
</pre>
<align = right>
July 26, 2012 TC39 Meeting Notes Allen Wirfs-Brock http://t.co/PwuF12Y0
</align>

From Prototype to Class
=======================
*Put code here that relates to previous code, discuss differences

9
How Classes Will Be Used
========================
*Put code that shows how classes work.

10
Pros
====
*I'll leave several sections blank - we can move stuff around as we need to.

11

12

13

14

15
Main Arguments Against
======================
*JS already has "class" functionality.
*Prototypal classes are available and they work.
*No point in maximally minimal classes.

16
Classes May Add Confusion
=================================
*Adding common features of classes (inheritance, subclasses, etc) will add confusion to JS.
*There will be TWO ways of creating classes and that may lead to errors later on.

17
Prototypal Classes Work
=======================
Adapted from Dr. Axel Rauschmayer:
<pre>
    var PersonProto = {
        describe: function () {
            return "Person called "+this.name;
        },
    };
    var jim = {
        __proto__: PersonProto,
        name: "Jim",
    };
    var dana = {
        __proto__: PersonProto,
        name: "Dana",
    };
    
    console.log(jim.describe()); // Person called Jim
</pre>
October 21, 2012 - http://www.2ality.com/2011/11/javascript-classes.html

18
What's the Point of Minimal Classes?
====================================
From Rick Waldren
<pre>
Attempting to extend this proposal is likely to result in dead-lock that 
would result in the inclusion of no class definition support in “ES6”. 
</pre>
*Minimal classes are limited.
*They will not be in regular use for quite awhile.

January 18, 2013 - http://wiki.ecmascript.org/doku.php?id=strawman:maximally_minimal_classes

19
Conclusion
==========
*The decision has been made and classes are coming to JS.
*The good news is that under the hood classes just use the structure that JS already has to work: that means that, in theory, everybody can be happy!

20
Closing Thoughts/Extra Slide/More Conclusions
=============================================
