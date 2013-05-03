To-Do
- Add direct quotations where possible.
- Mention community when possible.

The New Sugar In Your JavaScript
=================================
Group Members: Nicandro Flores, Megan Greening, and Brian McWilliams.

ECMAScript is an internationally standardized specification for scripting languages.  Many client side languages such as JavaScript(JS) and ActionScript are derived from it.  TC39 is a working group tasked with the responsibility of writing the new ECEMAScript 6 (ES6) specification[1], with a late 2013 target release. In approximately six months Node.js will begin embracing the newly proposed specification, browsers will begin popping up messages asking users upgrade their browsers, and JavaScript (JS) programmers will stand divided with TC39's decision to include a new class syntax into the language. 

The Prototypla Way
------------------

Despite JS being an object oriented language it does not have syntax to create classes in the conventional way programmers are used to, such as in Java. ECMAScript does specify that program state and methods are carried by objects, and that structure and behavior are both inherited. Therefore, even though class syntax such as "class" and "extends" do not exist in JS, there must be a way to simulate the idea of classes in JS. 

JS function objects are highly versatile. They are first class citizens, so they no different than data just as classes are data in other class based languages. For example functions can be used as templates to construct other objects. Templated objects are easily created using the 'new' operator. Additionally functions also come pre-equipped with hidden properties and methods. One of these properties is the 'prototype' property, with these three tools programmers can simulate prototyped-based classes.

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

It is simple matter to either augment these prototypes or completely replace them. To augment the person prototype we can do:

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

Though this seems quite elaborate, many of these steps can be easily condensed into functions. JS evangelists such as Dr. Axel Raushmayer and Douglas Crockford have written extensivly on these topics [6][7]. These ideas are common knowledge to seasoned JS programmers, but someone new to the language may find the prototypical inheritance pattern awkward and may be easily disuaded from further learning the JS language.




>> I can add a section here that talks about Proto.js and the extend(Child,Parent) function if we have to
====== let make sure to say more about what the community thinks =====




==== Insert example of a prototype based class here ====
>> Use example mentioned in the link below
>> here: http://www.2ality.com/2011/11/javascript-classes.html
>> If we need other examples use:
>> http://www.2ality.com/2012/01/js-inheritance-by-example.html
>> Point out the use of call(), may need to meniton apply() and bind() too
>> and how they are used to switch context. (not part of the topic anymore)
>> Point out the renaming of Employee.prototype.constructor to Employee which
>> examplained here:
>> http://www.2ality.com/2011/06/constructor-property.html

>>MG: If you would like I can go ahead and import the code examples we used in the presentation.

>> lead into next topic saying like:
>> "Does JS need a native syntax to shorten the above example?"

The New Class Syntax: Maximally Minimal Classes
--------------------
The concept of adding a more traditional Class structure to ES has been around in one form or another since at least ES4.  During the work on ES6, there were at least four formal proposals competing in this space, (class operator, classes, minimal classes, and classes as sugar)[2].  What these discussions highlighted was that there was no consensus on how to implement a broad range of Class semantics[3][4].  As a result, they agreed to “an absolutely minimal class declaration syntax that all interested parties may be able to easily agree upon.”

Those interested parties were able to agree that something was better than nothing, and to define a set of minimal requirements which could be extended in the future.  The Class proposal:
   *  has a declaration form that uses the class keyword and an identifier to create the class
   *	has a body that can include both the constructor function, as well as any instance (prototype) methods – including getter and setter properties
   *	can declare the class as a subclass of a another class (probably with the extends keyword)
   *	super is available from any of the methods or constructor function

Formal Class declarations and expressions:[5]
<pre>
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
</pre>

As an example, we can see the above function in the new EC6 Class format:[5]

<pre>
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
</pre>

This is how you use these classes:[5]
<pre>
    > let jane = new Employee("Jane", "CTO");
    > jane instanceof Person
    true
    > jane instanceof Employee
    true
    > jane.describe()
    Person called Jane (CTO)
</pre>

Arguments for
--------------

>> make it easy for new JS programmers
>> similar to what other seasoned programmers are used to
>> Lost of discussion here:
>> https://mail.mozilla.org/pipermail/es-discuss/2012-May/thread.html#22825
Progammers working in other languages are used to be able to work with classes in their code. By adding this functionality, JS would be brought closer into line with other languages. It seems like there is popular support in the JS community for adding classes. However, as with any new modification, there is some concern over how classes will be implemented.


Arguments against
-----------------

>> lost of arguments here:
http://www.nczonline.net/blog/2012/10/16/does-javascript-need-classes/

While it seems like the general consensus in the field is that adding classes to JS will be a good thing, there are those who do not see a need for classes. Many people argue that you do not need classes in JS because there are already ways to effectively create "classes" through the use of constructors to define custom reference types. Furthermore, they feel that common features of classes (subclasses, superclasses, inheritance, etc) tend to cause additional confusion. You can already create custom reference types and all this change will do is to add unecessary syntactic sugar. Since the addition of classes does not alter how JS works, many in the community do not see a need to add classes at all.

The addition of classes is merely a simplified way of using the features already avaialbe to JS. Many feel that you should just learn how to use the syntax already available to you. No major change is occuring - in essence, all that is happening is the the underworkings of the code are being hidden by the new class syntax. Prototypal inheritance is not that confusing once you start getting down to the details of it - and it even allows for inheritance.

>> Megan: I would like to either include a code snippet here or refer to a piece of code that we used in a different part of the paper. I have popped my own snippet in for now. We can always pare it down later! Most of this code and information comes from: http://www.2ality.com/2011/11/javascript-classes.html

In fact, there are those in the field who argue that ptrotypal inheritance is even simpler than inheritance in classes. One such individual is Dr. Axel Rauschmayer. He provides the following code as an example:

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

He goes on to explain that "jane and tarzan share the same prototype PersonProto which provides method describe() to both of them. Note how similar PersonProto is to a class. " Essentially this protypal way of handling 

JS uses constructors (also called constructor functions) to generate instances of objects or prototypes. Building from the same example as above, we have the following:

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

The constructor Person sets up a new instance of a person using "new". Any instances set up using this function share the prototype Person.prototype and can use functions the protype includes. This is the same way that inheritance works in classes in other languages, we just do not call anything a "class" in JS.

>> This gets into something that Dr. R. and others call exemplars. I don't think we should get into that - it seems beyond the scope of this paper. However, I could certainly include that kind of information if we end up needing it.

Concluson
--------- 

The new class syntax proposed by in the new ECMAScript.next hides the standard details of writing prototypical classes in JS. Is hiding the details a good thing? New programmers may not understand what is going on under the hood when things go wrong when using the new syntax. The new syntax will certainly lead to nicer symantics and new JS code produced with it will be more readable and clear. At what point do we decide we've had enough turtles, Feyman? Future generations can decide this. 

Citations
---------
[1] Draft Specification for ES.next (Ecma-262 Edition 6) [Online] Available: <br> http://wiki.ecmascript.org/doku.php?id=harmony:specification_drafts

[2] List of ECMA6 withdrawn proposals [Online] Available: <br> http://wiki.ecmascript.org/doku.php?id=strawman:withdrawn

[3] "Finding a "safety syntax" for classes" discusion thread (120 replies) [Online] Avaialbe: <br> https://mail.mozilla.org/pipermail/es-discuss/2012-March/021430.html

[4] "Using Object Literals as Classes" discussion thread (67 replies) [Online] Available: <br> https://mail.mozilla.org/pipermail/es-discuss/2012-March/021253.html

[5] "ECMAScript.next: classes" 2ality JavaScript and More Blog Dr. Axel Rauschmayer [Online] Available: <br> http://www.2ality.com/2012/07/esnext-classes.html

[6] "JavaScript quirks" 2ality JavaScript and More Blog, Dr. Axel Raushmayer [Online] Available: http://www.2ality.com/2013/04/12quirks.html

[7] "JavaScript The Good Parts" by Douglas Crockford, May 2, 2008, O'Rielly. Page 57 Section 3.5 Prototypes
