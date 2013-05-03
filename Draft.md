You can find evidence of our collaboration (for both the paper and the presentation) at https://github.com/Meglet/CSCI3155Paper

The New Sugar In Your JavaScript
=================================
Group Members: Nicandro Flores, Megan Greening, and Brian McWilliams.

ECMAScript is an internationally standardized specification for scripting languages.  Many client side languages such as JavaScript(JS) and ActionScript are derived from it.  TC39 is a working group tasked with the responsibility of writing the new ECEMAScript 6 (ES6) specification[1], with a late 2013 target release. In approximately six months Node.js will begin embracing the newly proposed specification, browsers will begin popping up messages asking users upgrade, and JavaScript programmers will stand divided with TC39's decision to include a new class syntax into the language. 

The Prototypla Way
------------------

Despite JS being an object oriented language it does not have syntax to create classes in the conventional way programmers are used to, such as in Java. ECMAScript does specify that program state and methods are carried by objects, and that structure and behavior are both inherited. Therefore, even though class syntax such as "class" and "extends" do not exist in JS, there must be a way to simulate the idea of classes in JS. 

JS function objects are highly versatile. They are first class citizens, so they are no different than data just as classes are data in other class based languages. For example functions can be used as templates to construct other objects. Templated objects are easily created using the 'new' operator. Additionally functions also come pre-equipped with hidden properties and methods. One of these properties is the 'prototype' property.  With these three tools programmers can simulate prototyped-based classes.

Starting with two constructor functions, Person and Employee, their initial prototype property is simply an empty object, "{}". 

~~~~javascript
function Person(name) {
  this.name = name;
}
function Employee(name,title,company) {
    Person.call(this,name);
    this.title = title;
    this.company = company;
}
Person.prototype // => {}
~~~~

It is simple matter to either augment these prototypes or completely replace them. To augment the person prototype we can:

~~~~javascript
Person.prototype.sayName = function() { return "My name is: " + this.name; }
Person.prototype // => [sayName: [Function]]
~~~~~

Instances of the Person constructor function will have references to Person's augmented prototype allowing to acess the to sayName() method.

~~~~javascript
var me = new Person('Joe Shmoe');
me.sayName()  // => Joe Shmoe
~~~~

In order to create the inheritance relationship between the Person and the Employee constructor functions the prototype of Employee is completely replaced with a new instance of the Person. For example:

~~~~javascript
Employee.prototype = new Person('John Doe','TBD','Unemployed');
Employee.prototype.constructor = Employee;
Employee.prototype.describe() = function() {
  Person.prototype.sayName.call(this,this.name)
  + ", I am a " + this.title + " at "
  + this.company + "!"; 
}
~~~~~

At this stage, instances of Employee will have access to properties defined in Employees.prototype as well as properties defiend in Person.prototype.

~~~~javascript
var prof = new Employee('Jim Baker','code slinging Ninga','Rackspace');
prof.sayName()   // a method in Person.prototype
prof.describe()  // a method in Employee.prototype
~~~~

Though this seems quite elaborate, many of these steps can be easily condensed into functions. JS evangelists such as Dr. Axel Raushmayer and Douglas Crockford have written extensivly on these topics [6][7]. These ideas are common knowledge to seasoned JS programmers, but someone new to the language may find the prototypical inheritance pattern awkward and may be easily dissuaded from further learning the JS language.

The New Class Syntax: Maximally Minimal Classes
--------------------
The concept of adding a more traditional Class structure to ES has been around in one form or another since at least ES4.  During the work on ES6, there were at least four formal proposals competing in this space, (class operator, classes, minimal classes, and classes as sugar)[2].  What these discussions highlighted was that there was no consensus on how to implement a broad range of Class semantics[3][4].  As a result, they agreed to “an absolutely minimal class declaration syntax that all interested parties may be able to easily agree upon.”

Those interested parties were able to agree that something was better than nothing, and to define a set of minimal requirements which could be extended in the future.  The Class proposal:
   *  has a declaration form that uses the class keyword and an identifier to create the class
   *	has a body that can include both the constructor function, as well as any instance (prototype) methods – including getter and setter properties
   *	can declare the class as a subclass of a another class (probably with the extends keyword)
   *	super is available from any of the methods or constructor function

Below we see the formal Class declaration and expression.  Just as we learned in lecture, the language is described in Backus–Naur Form.[5]

~~~~javascript
    ClassDeclaration:
        "class" BindingIdentifier ClassTail
    ClassExpression:
        "class" BindingIdentifier? ClassTail
    ClassTail:
        ClassHeritage? "{" ClassBody? "}"
    ClassHeritage:
        "extends" AssignmentExpression
    ClassBody:
        ClassElement+
    ClassElement:
        MethodDefinition
        ";"
    MethodDefinition:
        PropName "(" FormalParamList ")" "{" FuncBody "}"
        "*" PropName "(" FormalParamList ")" "{" FuncBody "}"
        "get" PropName "(" ")" "{" FuncBody "}"
        "set" PropName "(" PropSetParamList ")" "{" FuncBody "}"
~~~~

As an example, we can see the above inheritance relationship in the new EC6 Class format:[5]

~~~~javascript
    // Supertype
    class Person {
        constructor(name) {
            this.name = name;
        }
        describe() {
            return "Person called "+this.name;
        }
    }
    // Subtype
    class Employee extends Person {
        constructor(name, title) {
            super.constructor(name);
            this.title = title;
        }
        describe() {
            return super.describe() + " (" + this.title + ")";
        }
    }
~~~~

This is how you use these classes:[5]
~~~~javascript
    > let jane = new Employee("Jane", "CTO");
    > jane instanceof Person
    true
    > jane instanceof Employee
    true
    > jane.describe()
    Person called Jane (CTO)
~~~~

Parsing of Judgement Forms
-----------------
The draft ECMAScript specification[10] contains the official way statements should function.  While their document did not use formal, cryptic symbols as our labs did, the community utilizes the same skills we gained.  We can see the precedent/consequence parsing.  These statements are the equivalent of our judgement forms. 

<pre>
Static Semantics: ConstructorMethod
ClassBody : ClassElementList
  1. Let list be PrototypeMethodDefinitions of ClassElementList.
  2. For each MethodDefinition m in list, do
    a. If PropName of m is ″constructor″, return m.
  3. Return empty.

Static Semantics: Contains
ClassTail : ClassHeritageopt { ClassBody }
1. If symbol is ClassBody, return true.
2. If ClassHeritage is not present, return false.
3. If symbol is ClassHeritage, return true.
4. Return the result of Contains for ClassHeritage 
   with argument symbol.
</pre>

Arguments against
-----------------

While it seems like many in the field feel that adding Classes to JS will be a good thing, there are those who do not see a need for this feature. Many people argue that you do not need classes in JS because there are already ways to effectively create "classes" through the use of constructors to define custom reference types[6]. Furthermore, they feel that common features of classes (subclasses, superclasses, inheritance, etc) tend to cause additional confusion. You can already create custom reference types and all this change will do is to add unnecessary syntactic sugar. Since the addition of classes does not alter how JS works, many in the community do not see a need to add classes at all. As Herby Vojčík stated in a discussion about the addition of classes, "I just don't understand why you use class keyword here. [It] will work without any changes, as-is, with plain function keyword. After all, you just augment a constructor definition." [9]  This opinion is a common one in the community.

The addition of classes is merely a simplified way of using the features already available to JS. Many feel that you should just learn how to use the syntax already available to you. No major change is occurring - in essence, all that is happening is the the under workings of the code are being hidden by the new class syntax. Prototypical inheritance is not that confusing once you start getting down to the details of it - and it even allows for inheritance.

In fact, there are those in the community who argue that prototypical inheritance is even simpler than inheritance in classes. One such individual is Dr. Axel Rauschmayer[6]. He provides the following code as an example:
~~~~javascript
    var PersonProto = {
        describe: function () {
            return "Person called "+this.name;
        },
    };
    var jane = {
        __proto__: PersonProto,
        name: "Jane",
    };
    var tarzan = {
        __proto__: PersonProto,
        name: "Tarzan",
    };
    
    console.log(jane.describe()); // Person called Jane
~~~~

He goes on to explain that "jane and tarzan share the same prototype PersonProto which provides method describe() to both of them. Note how similar PersonProto is to a class."[6] Essentially this protypal way of handling classes.

JS uses constructors (also called constructor functions) to generate instances of objects or prototypes. Building from the same example as above, we have the following:

~~~~javascript
    // Constructor: set up the instance
    function Person(name) {
        this.name = name;
    }

    // Prototype: shared by all instances
    Person.prototype.describe = function () {
        return "Person called "+this.name;
    };

    var jane = new Person("Jane");
    console.log(jane instanceof Person); // true

    console.log(jane.describe()); // Person called Jane
~~~~

The constructor Person sets up a new instance of a person using "new". Any instances set up using this function share the prototype Person.prototype and can use functions the prototype includes. This is the same way that inheritance works in classes in other languages, we just do not call anything a "class" in JS.

Arguments for
--------------

When new people learn to program, they are introduced to Object Oriented Programming(OOP) as a best practice. This normally occurs in Java or C++.  JavaScript syntax however does not conform to these languages' styles.  As one JavaScript blogger, Peter Michaux puts it, "When they arrive to JavaScript their concepts of how an object-oriented language works no longer apply they learn that their big OOP investment was not complete." [8] Adding class syntax to JavaScript eases the transition.

Peter also claims that there are not many great resources for learning how to effectively use prototype based inheritance effectively.  A quick search of Amazon bears this out.  A search for "prototype inheritance JavaScript" yields 14 responses, while a search for "class inheritance" yields 3,001 resources.  

Finally, as we can see from the code comparisons for the traditional prototype way and the new class methods, code becomes shorter and more clear.  In the rational code, we produce 28 lines, 717 characters to declare and then use prototypes.  Using the new class syntax, we achieve the same functionality with 17 lines, 238 characters, an incredible 66% savings.

Conclusion
--------- 

The new class syntax proposed by ECMAScript.next changes no underlying functionality, it simply adds syntactic sugar to (arguably) make development easier.  This change encourages new blood to enter the JavaScript community, and code written under the new standard will be easier to read.  The use of the syntatic sugar may make it more challenging to understand how the language works at a deeper level later in their careers.  The continued support for traditional prototyping will provide both opportunities and challenges as code bases develop with mixed syntax.  As a minimal implementation, the community has the opportunity to accelerate or limit further changes depending on developer reaction once EC6 is complete.  "The current goal is to have ECMAScript.next finished by 2013, with parts of it making it into web browsers (especially Firefox) before then."[11]

Citations
---------
[1] Draft Specification for ES.next (Ecma-262 Edition 6) [Online] Available: <br> http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts

[2] List of ECMA6 withdrawn proposals [Online] Available: <br> http://wiki.ecmascript.org/doku.php?id=strawman:withdrawn

[3] "Finding a "safety syntax" for classes" discusion thread (120 replies) [Online] Avaialbe: <br> https://mail.mozilla.org/pipermail/es-discuss/2012-March/021430.html

[4] "Using Object Literals as Classes" discussion thread (67 replies) [Online] Available: <br> https://mail.mozilla.org/pipermail/es-discuss/2012-March/021253.html

[5] "ECMAScript.next: classes" 2ality JavaScript and More Blog Dr. Axel Rauschmayer [Online] Available: <br> http://www.2ality.com/2012/07/esnext-classes.html

[6] "JavaScript quirks" 2ality JavaScript and More Blog, Dr. Axel Raushmayer [Online] Available: <br> http://www.2ality.com/2013/04/12quirks.html

[7] "JavaScript The Good Parts" by Douglas Crockford, May 2, 2008, O'Rielly. Page 57 Section 3.5 Prototypes

[8] "Transitioning from Java Classes to JavaScript Prototypes" [Online] Available: <br> http://michaux.ca/articles/transitioning-from-java-classes-to-javascript-prototypes

[9] Discussion of ECMAScript, May 2012 [Online] <br> https://mail.mozilla.org/pipermail/es-discuss/2012-May/022813.html

[10] Current working draft for ES.next, revision 14, no markup. [Online] Available: <br> http://wiki.ecmascript.org/lib/exe/fetch.php?id=harmony%3Aspecification_drafts&cache=cache&media=harmony:working_draft_ecma-262_edition_6_03-08-13-nomarkup.pdf

[11] ECMAScript: ES.next versus ES 6 versus ES Harmony [Online] Available: <br> http://www.2ality.com/2011/06/ecmascript.html
