---
layout: post
title: "Scala Guide Sessions"
date: 2014-03-12 13:49
comments: true
categories: scala
---

It is guide for scala quick learn sessions. All resource base on [Scala for the Impatient](http://www.amazon.com/Scala-Impatient-Cay-S-Horstmann/dp/0321774094/ref=sr_1_1?ie=UTF8&qid=1394603644&sr=8-1&keywords=scala)

Day1: A better java
===

1. The basic
---
play hello world - IDE

Using the Scala interpreter

Defining variables with var and val

```
val sum = 1 + 1
sum = 5 //error

var counter = 1
counter = counter + 3
```

• Using operators and functions

```
counter.+=(3)

1 to 3
1.to(3)
```

• Navigating Scaladoc”


2. Control Structures and Functions
---
An if expression has a value.

```
val sum = 2
val counter = if(sum == 3) 1 else 2
```

A block has a value—the value of its last expression.

```
val total = {
 val i = 2
 i + 3
}
```

The Scala for loop is like an “enhanced” Java for loop.

```
for(i <- 1 to 3) println(i)

for (i <- 1 to 3; j <- 1 to 3 if i != j) print((10 * i + j) + " ")
```

Semicolons are (mostly) optional.

The void type is Unit.
```
def hello(name: String): Unit = {
 println("hello: "+name)
}
```

Avoid using return in a function.
Beware of missing = in a function definition.
```
def hello(name: String) {
 println("hello: "+name)
}
```

Exceptions work just like in Java or C++, but you use a “pattern matching” syntax for catch.
Scala has no checked exceptions.
```
def safeSqrt(x:Int):Double = {
  if (x >= 0) { Math.sqrt(x)
  } else throw new IllegalArgumentException("x should not be negative")
}

try {
  safeSqrt(-1)
} catch {
  case ex: IllegalArgumentException => println(ex)
}
```


3. Working with Arrays
---

Use an Array if the length is fixed, and an ArrayBuffer if the length can vary.
```
val nums = new Array[Int](10)

import scala.collection.mutable.ArrayBuffer
val b = ArrayBuffer[Int]()
b += 1
b += (1, 2, 3, 5)
b ++= Array(8, 13, 21)

a filter { _ % 2 == 0 } map { 2 * _ }
```

* Don’t use new when supplying initial values.
*  Use () to access elements.
*  Use for (elem <- arr) to traverse the elements.
*  Use for (elem <- arr if . . . ) . . . yield . . . to transform into a new array.


4. Maps and Tuples
---

Scala has a pleasant syntax for creating, querying, and traversing maps.
```
val scores = Map("Alice" -> 10, "Bob" -> 3, "Cindy" -> 8)
"Alice" -> 10  === ("Alice", 10)
val bobsScore = scores("Bob")
val bobsScore = scores.getOrElse("Bob", 0)
scores("Bob") = 10
“scores += ("Bob" -> 10, "Fred" -> 7)
scores -= “Alice"
“for ((k, v) <- map) process k and v”
```

You need to choose between mutable and immutable maps.

By default, you get a hash map, but you can also get a tree map.

Tuples are useful for aggregating values.
```
val t = (1, 3.14, "Fred")
val second = t._2
val (first, second, third) = t
```

Fields in classes automatically come with getters and setters.
```
case class Person(name: String)
val world =  Person("World")
world.name
```

You can replace a field with a custom getter/setter without changing the client of a class—that is the “uniform access principle.”

Every class has a primary constructor that is “interwoven” with the class definition. Its parameters turn into the fields of the class. The primary constructor executes all statements in the body of the class.
Auxiliary constructors are optional. They are called this.

```
class Person {

  private var name = ""
  private var age = 0
  def this(name: String) {
    this()
    this.name = name
  }
  def this(name:String, age:Int){
    this(name)
    this.age = age
  }
}

new Person
new Person("World")
new Person("World",18)
```

6. Objects
---

Use objects for singletons and utility methods.
A class can have a companion object with the same name.
The class and its companion object can access each other’s private features. They must be located in the same source file.
```
class Account {
  val id = Account.newUniqueNumber()
  private var balance = 0.0
  def deposit(amount: Double) { balance += amount }

}
object Account { // The companion object
  private var lastNumber = 0
  private def newUniqueNumber() = { lastNumber += 1; lastNumber }
}
```

Objects can extend classes or traits.
```
object Hello extends App {
  println("Hello, World!")
}
```

The apply method of an object is usually used for constructing new instances of the companion cla
```
case class Account(private val id: Int,private val balance: Double)

object Account { // The companion object
  private var lastNumber = 0
  private def newUniqueNumber() = { lastNumber += 1; lastNumber }
  def apply(initialBalance: Double):Account =  {
    Account(newUniqueNumber(), initialBalance)
  }
}
```

7. Packages and Imports
---

Packages nest just like inner classes.
Package paths are not absolute.
A chain x.y.z in a package clause leaves the intermediate packages x and x.y invisible.”

Package statements without braces at the top of the file extend to the entire file.
A package object can hold functions and variables.
• Import statements can import packages, classes, and objects.
• Import statements can be anywhere.
• Import statements can rename and hide members.
• java.lang, scala, and Predef are always imported.
```
import java.lang._
import scala._
import Predef._
```

8. Inheritance
---
The extends and final keywords are as in Java.
You must use override when you override a method.

```
case class Person(name:String)
//Superclass Construction
class Employee(name:String, salary:Double) extends Person(name) {
  override def toString = getClass.getName + "[name=" + name + "], Salary is [" + salary +"]"
}

new Employee("World",0)

//Type Checks and Casts
val p:Person = new Employee("World",0)
if (p.isInstanceOf[Employee]) {
  val s = p.asInstanceOf[Employee] // s has type Employee
}
```


Only the primary constructor can call the primary superclass constructor.


You can override fields.
```
class Person(val name: String) {
  override def toString = getClass.getName + "[name=" + name + "]"
}

class SecretAgent(codename: String) extends Person(codename) {
  override val name = "secret" // Don't want to reveal name . . .
  override val toString = "secret" // . . . or class name
}
```

A def can only override another def.
A val can only override another val or a parameterless def.
A var can only override an abstract var (see Section 8.8, “Abstract Classes,” on page 91).”

In Scala, the eq method of the AnyRef class checks whether two references refer to the same object. The equals method in AnyRef calls eq. When you implement a class, you should consider overriding the equals method to provide a natural notion of equality for your situation.”
```
class Person(name: String)
val p1 = new Person("World")
val p2 = new Person("World")
p1 == p2
```

*case class*
```
case class Person(name: String)
val p1 = Person("World")
val p2 = Person("World")
p1 == p2
```


A class can implement any number of traits.
Traits can require that implementing classes have certain fields, methods, or superclasses.
Unlike Java interfaces, a Scala trait can provide implementations of methods and fields.
When you layer multiple traits, the order matters—the trait whose methods execute first goes to the back.
```
trait Account {
  var balance = 0.0
}
trait Logger {
  def log(msg:String)
}
trait ConsoleLogger extends Logger {
  def log(msg:String)=println(msg)
}
trait SavingsAccount extends Account with Logger {
  def withdraw(amount: Double) {
    if (amount > balance) log("Insufficient funds")
    else balance -= amount
  }
}
val acct = new SavingsAccount with ConsoleLogger
acct.withdraw(100)

```



Identifiers contain either alphanumeric or operator characters.
Unary and binary operators are method calls.
```
(1 to 10) == 1.to(10)
```

Operator precedence depends on the first character, associativity on the last.
In Scala, all operators are left-associative except for
• Operators that end in a colon (:)
• Assignment operators
```
2 :: Nil === Nil.::(2)
```

```
   //WARN: only for demo, be careful use this feature
   class Foo() { def ++:(n:Int) = println(2*n) }
   val foo = new Foo()
   123 ++: foo
```

The apply and update methods are called when evaluating expr(args).

```
object add {
  private var _v = 1
  def apply(v:Int):Unit = {println(_v + v)}
  def update(name:String,v:Int):Unit = {println("Hello:"+name+(_v + v))}
}

add(2)
add("world") = 3
```

****
• Extractors extract tuples or sequences of values from an input”
```
object Number {
  def unapply(input: String): Option[Int] =
    try {
      Some(Integer.parseInt(input.trim))
    } catch {
      case ex: NumberFormatException => None
    }
}

val Number(n) = "1729"
```


Day2: Functional and powerful collection

Chapter 12. Higher-Order Functions
===
Functions are “first-class citizens” in Scala, just like numbers.
```
import scala.math._
val num = 3.14
val fun = ceil _
fun(num)
```
You can create anonymous functions, usually to give them to other functions.
```
(x: Double) => 3 * x
val triple = (x: Double) => 3 * x
def triple(x: Double) = 3 * x
List(3.14, 1.42, 2.0).map((x: Double) => 3 * x)
List(3.14, 1.42, 2.0).map((x) => 3 * x)
List(3.14, 1.42, 2.0).map(x => 3 * x)
List(3.14, 1.42, 2.0).map(3 * _)
```
A function argument specifies behavior that should be executed later.
//function parameter: (parameterType) => resultType
```
def valueAtOneQuarter(f: (Double) => Double) = f(0.25)
valueAtOneQuarter(Math.sqrt _)
```

//((Double) => Double) => Double
```
def mulBy(factor : Double) = (x : Double) => factor * x
val quintuple = mulBy(5)
quintuple(20)
```

Many collection methods take function parameters, applying a function to the values of the collection.
```
(1 to 9).map(v => v + 2)
(1 to 9).reduceLeft(_ * _)
(1 to 9).filter(_ % 2 == 0) // 2, 4, 6, 8
"Mary had a little lamb".split(" ").sortWith(_.length < _.length)
```
There are syntax shortcuts that allow you to express function parameters in a way that is short and easy to read.
You can create functions that operate on blocks of code and look much like the built-in control statements.
Closures
```
def mulBy(factor : Double) = (x : Double) => factor * x
val triple = mulBy(3)
val half = mulBy(0.5)
println(triple(14) + " " + half(14))
```

Currying
```
def mulBy(x: Double)(y: Double) = x * y
val triple = mulBy(3)
val half = mulBy(0.5)
println(triple(14) + " " + half(14))
```

Control Abstractions
```
def runInThread(block: () => Unit){
  new Thread{
    override def run() { block()}
  }.start()
}
runInThread { () => println("Hi"); Thread.sleep(100); println("Bye") }
Thread.sleep(1000)
println("finished")
```

```
def runInThread(block: => Unit){
  new Thread{override def run(){block}}.start()
}

runInThread { println("Hi"); Thread.sleep(10); println("Bye") }
Thread.sleep(1000)
println("finished")
```

Chapter 13. Collections
===
http://twitter.github.io/effectivescala/index-cn.html
                    Traversable
                         |
                         |
                      Iterable
                         |
      +------------------+--------------------+
     Map                Set                  Seq
      |                  |                    |
      |             +----+----+         +-----+------+
    Sorted Map  SortedSet   BitSet   Buffer Vector LinearSeq

```
Traversable:
def foreach[U](f: A => U): Unit
def map(f: A => B)
def isEmpty: Boolean

def takeWhile(p: A => Boolean)
def dropWhile(p: A => Boolean)
def span(p: A => Boolean)
def splitAt(n: Int)

def filterNot(p: A => Boolean)

def filter(p: A => Boolean)
def find[A](p: A => Boolean):Option[A]

def collect(pf: PartialFunction[A, B])
//List(1,2,"SS").collect{case i:Int => i}

def partition(p: A => Boolean)
def groupBy[K](f: A => K): immutable.Map[K, Repr]

def forall(p: A => Boolean): Boolean
def exists(p: A => Boolean)


def head: A
def headOption: Option[A]
def tail:
def last: A
def lastOption: Option[A]
def init: Repr

def take(n: Int)
def drop(n: Int)

def tails: Iterator[Repr]

def view
def flatMap[B, That](f: A => GenTraversableOnce[B])
```

```
def foldLeft[B](z: B)(op: (B, A) => B)
def reduceLeft[B >: A](op: (B, A) => B)

```
Scala's collect is both filter and map, sorting out all the values
which have a matching case clause and returning the value of the case
clause in question.

flatMap——结合了map 和 flatten —— 要特别小心，它有着难以琢磨的威力和强大的实用性

```
val list = List(1,2,3,4,5)
def g(v:Int) = List(v-1, v, v+1)
list.map(x => g(x))
list.flatMap(x => g(x))
```

Day3: Pattern match and Type Parameters

Chapter 14. Pattern Matching and Case Classes
===
The match expression is a better switch, without fall-through.
If no pattern matches, a MatchError is thrown. Use the case _ pattern to avoid that.
A pattern can include an arbitrary condition, called a guard.

```
def choose(ch:Char) =
  ch match {
    case '+' => 1
    case '-' => -1
    case _ if Character.isDigit(ch) => Character.digit(ch, 10)
    //case _ => 0
  }
choose('-')
choose('9')
choose('x')
```

You can match on the type of an expression; prefer this over isInstanceOf/asInstanceOf.

```
def choose(obj:Any) =
obj match {
  case x: Int => x
  case s: String => Integer.parseInt(s)
  case _: BigInt => Int.MaxValue
  case _ => 0
}
choose('2')
choose(1)
```

You can match patterns of arrays, tuples, and case classes, and bind parts of the pattern to variables.

```
def choose(arr:Seq[Int]) =
arr match {
  case Nil => "0"
  case x :: y :: Nil => x + " " + y
  case 0 :: xs => "0 ..."
  case _ => "something else"
}
choose(List())
choose(List(1,3))
choose(List(0))
choose(List(0,1,2))
choose(List(1,2,3))


val pattern = "([0-9]+) ([a-z]+)".r
"99 bottles" match {
  case pattern(num, item) => num
}
```

In a for expression, nonmatches are silently skipped.
```
import scala.collection.JavaConversions.propertiesAsScalaMap
// Converts Java Properties to a Scala map—just to get an interesting example
for ((k, v) <- System.getProperties())
  println(k + " -> " + v)
```

A case class is a class for which the compiler automatically produces the methods that are needed for pattern matching.
```
trait Amount
case class Dollar(value: Double) extends Amount
case class Currency(value: Double, unit: String) extends Amount

def choose(atm:Amount) =
atm match {
  case Dollar(v) => "$" + v
  case Currency(_, u) => "Oh noes, I got " + u
  case _ => ""
}
choose(Dollar(10))
```
An apply method is provided for the companion object that lets you construct objects without new, such as Dollar(29.95) or Currency(29.95, "EUR").
An unapply method is provided that makes pattern matching work
```
object Number {
  def unapply(input: String): Option[Int] =
    try {
      Some(Integer.parseInt(input.trim))
    } catch {
      case ex: NumberFormatException => None
    }
}

val Number(n) = "1729"


case a Currency u => a
case class ::[E](head: B, tail: List[E]) extends List[E]
lst match { case h :: t => ... }  // Same as case ::(h, t), which calls ::.unapply(lst)
result match { case p ~ q => ... } // Same as case ~(p, q)
```

The common superclass in a case class hierarchy should be sealed.

```
sealed trait TrafficLightColor
case object Red extends TrafficLightColor
case object Yellow extends TrafficLightColor
case object Green extends TrafficLightColor

def choose(color: TrafficLightColor) =
color match {
  case Red => "stop"
  case Yellow => "hurry up"
  //case Green => "go"
}
```


“A set of case clauses enclosed in braces is a partial function—a function which may not be defined for all inputs.”
```
val f: PartialFunction[Char, Int] = { case '+' => 1 ; case '-' => -1 }
f('-') // Calls f.apply('-'), returns -1
"-3+4".collect { case '+' => 1 ; case '-' => -1 } // Vector(-1, 1)
f.isDefinedAt('0') // false
f('0')
```

Chapter 17. Type Parameters
===

Classes, traits, methods, and functions can have type parameters.
Place the type parameters after the name, enclosed in square brackets.
```
class Pair[T, S](val first: T, val second: S) {
    def hello:T = first
}
def getMiddle[T](a: Array[T]) = a(a.length / 2)
```

Type bounds have the form T <: UpperBound, T >: LowerBound, T <% ViewBound, T : ContextBound.
```
class Pair[T](val first: T, val second: T) {
  def smaller = if (first.compareTo(second) < 0) first else second // Error
}

class Pair[T <: Comparable[T]](val first: T, val second: T) {
  def smaller = if (first.compareTo(second) < 0) first else second // Error
  def replaceFirst[R >: T](newFirst: R) = new Pair[R](newFirst, second)
}

val p = new Pair("Fred", "Brooks")
println(p.smaller)
```

The <% relation means that T can be converted to a Comparable[T] through an implicit conversion.
```
class Pair[T <% Ordered[T]](val first: T, val second: T) {
  def smaller = if (first < second) first else second
}
new Pair(4, 2)
```

```
def makePair[T : Manifest](first: T, second: T) = {
  val r = new Array[T](2); r(0) = first; r(1) = second; r
}
makePair: [T](first: T, second: T)(implicit evidence$1: Manifest[T])Unit

makePair(4, 9)(intManifest) -> new Array(2)(intManifest)
```

• You can restrict a method with a type constraint such as (implicit ev: T <:< UpperBound).
implicit evidence parameter

```
class Pair[T](val first: T, val second: T)(implicit ev: T <:< Comparable[T])
```

```
def firstLast[A, C <: Iterable[A]](it: C) = (it.head, it.last)
def firstLast[A, C](it: C)(implicit ev: C <:< Iterable[A]) = (it.head, it.last)
```

Use +T (covariance) to indicate that a generic type’s subtype relationship is in the same direction as the parameter T, or -T (contravariance) to indicate the reverse direction.
```

class Pair[+T](val first: T, val second: T)
```
The + means that the type is covariant in T—that is, it varies in the same direction.
Since Student is a subtype of Person, a Pair[Student] is now a subtype of Pair[Person].


• Covariance is appropriate for parameters that denote outputs, such as elements in an immutable collection.
• Contravariance is appropriate for parameters that denote inputs, such as function arguments.”
