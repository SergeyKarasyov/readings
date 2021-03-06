# https://livebook.manning.com/book/typescript-quickly



# 1 Getting familiar with TypeScript
* typescript is transplited to javascript
* typescript is superset of ecma script(aka javascript), i.e. .js can be renamed to .ts(if there is no problem related to dynamic typization all should be ok)
* official page:  www.typescriptlang.org. 
* online typescript compliler to js: http://www.typescriptlang.org/play/index.html
* types in TypeScript are optional
* ts allows to use the latest ECMAScript syntax (e.g. async and await)
* list of languages that compiles to js: https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js
* TypeScript follows the latest specifications of ECMAScript and adds to them types, interfaces, decorators, class member variables (fields), generics, enums, the keywords public, protected, and private and more.
* TS roadmap: https://github.com/Microsoft/TypeScript/wiki/Roadmap
## deploying app written in ts
* Ts files (a.ts, b.ts) -> complied > Js files(a.js, b.js) -> bundle -> Js file(main.js) ->deploy -> Js engine(main.js)
* Bundlers like Webpack or Rollup, only concatenate multiple JavaScript files, but can optimize the code and remove unused code (a.k.a. perform tree-shaking). 
* js code may not be rewritten to ts,e definition files with the name extension .d.ts can be used for type checking

## Using TypeScript compiler
```
npm -v
npm install -g typescript
tse -v
```
* create a file
```
function getFinalPrice(price: number, discount: number) {
  return price - price/discount;
}
console.log(getFinalPrice(100, 10));
console.log(getFinalPrice(100, "10%"));
```
* run this code, note that js code would be generated even even with error being printed
```
tsc main
```
* or run with compile option that will block generation of .js file
```
tsc main --noEmitOnError true
```
* you can use --t option to choose JS syntax
```
tsc --t ES5 main
```
* it is possible to preconfigure the compilation(source, target, directories), need to create the file tsconfiguration.json
```javascript
{
  "compilerOptions": {
      "baseUrl": "src",
      "outDir": "./dist",
      "noEmitOnError": true,
      "target": "es5"
  }
}
```
* to start project(actually to create tsconfiguration.json file)
```
tsc --init
```
* in tsconfiguration.json it is possible to inherit configuration using ```extends``` control word
* REPL(Read-Evaluate-Print-Loop) executes your code in interactive console mode
* Good to install in vscode
TSLint - integrated the TypeScript linter,
Prettier - code formatter,
Path Intellisense - auto-completes file paths.
* online ide based on vscode - https://stackblitz.com/



#  2. Basic and custom types
* The type can be assigned to a variable either explicitly by a software developer or implicitly (a.k.a. inferred types) 
## 2.1.1  Basic type annotations
* add a colon and a type annotation to specify the variable type
```
let firstName: string;
let age: number;
```
## Types:
* string - for textual data
* boolean - for true/false values
* number - for numeric values
* symbol - unique value created by calling the Symbol constructor
* any - for variables that can hold values of various types, which may be unknown when you write code
* unknown - a counterpart of any but no operations are permitted on an unknown without first asserting or narrowing to a more specific type.
* never - for representing an unreachable code (we’ll provide an example shortly)
* void - an absence of a value
* Symbol - immutable, and unique. Sym1 is not eq sym2
```
const sym1 = Symbol("orderID");
const sym2 = Symbol("orderID");
```
```
const ord = Symbol('orderID');

const myOrder = {
    ord: "123"
};

console.log(myOrder['ord']);
```
* Typescript also has null and undefined(not assigned or function that do not return value) values
* function that returns string or null
```
function getName(): string | null
{

};
```
* any type, to this variable can assign numeric, textual, boolean or custom type, but that is not an optimal behaviour
* never type - assigned to function that never returns or just throws an error
```
const logger = () =>
{
  ...
};
```

* typescript may specify type from the 'rvalue', hence type specification is redundant here
```typescript
let name1: string = 'something';
```
* it is possible to infer type/value from the literal. The variable name3 will only allow one value: John Smith and an attempt to assign another value to a variable of a literal type would result in type checker’s error:
```typescript
let name3: 'Name';
name3 = 'Mary Lou';  // error: Type '"Mary Lou"' is not assignable to type '"John Smith"'
```

* if declare variable without initialization -> typescript will infer its type as ```any```. In javascript it would such uninitialized value is *undefined*
* infering of the type is called *type widening*
* Type annotations are used not only for declaring variable types, but also for declaring types of function arguments and their return values,
### Types in function declarations
* example of functions in javascript vs typescript
```javascript
//javascript
function calcTax(state, income, dependents){
  if(state = "NY"){
    return income * 0.006 - dependents * 500;
  }
  else if(state == 'NJ'){
    return income * 0.006 - dependents * 300;
  }
}
```

```typescript
//typescript 
function calcTax(state: string, income: number, dependents: number) : number{
  if(state = "NY"){
    return income * 0.006 - dependents * 500;
  }
  else if(state == 'NJ'){
    return income * 0.006 - dependents * 300;
  }
}
```
### the union type
* allow a value to be of several types
```
let padding: string | number;
```
* check built-in type of the variable:
```
    if (typeof padding === "number") {
        return Array(padding + 1).join(" ") + value;
    }
```
* if the type is a custom one, need to use ```instanceof```
```
if (person instanceof Person) {...}
```
### define custome type
* use keyword ```type``` to declare class or an interface(or use ```enum```)
```
type Foot = number;
type Pound = number;


type Patient = {
  name: string;
  height: Foot;
  weight: Pound;
}


let patient: Patient = {
  name: 'Joe Smith',
  height: 5,
  weight: 100
}
```
* function examples
```
function myAdd(x: number, y: number): number {  return x + y; }
let myAdd                                   = function(x: number, y: number): number { return x + y; };
let myAdd: (x: number, y: number) => number = function(x: number, y: number): number { return x + y; };

```
* if during initialization to forget about one of properties(e.g. weight) typescript will complain that initialized type is not the one we are creating, some property is missing
* it is possible to create optional properties
```
type Patient = {
        name: string;
        height: Height;
        weight?: Weight;
}

let patient: Patient = {
        name: 'Joe Smith',
        height: 5
}
```

* the type keyword for declaring a type alias to a function signature, in this case an object can have properties of any type:
```
type ValidatorFn = (c: FormControl) => { [key: string]: any }| null
```
function accepts object of FormControl, and returns either an object describing error or null.  
```
class FormControl {
constructor (initialValue: string, validator: ValidatorFn | null) {...}
}
```
* typescripts can declare classes, javascript cannot declare properties in class
```
//typescript
class Person{
  firstName: string;
  lastName: string;
  age: number;
}


//javascript
"use strict"
class Person{
}

//initialization is same in both cases
const p = new Person();
p.firstName = "john";
p.lastName = "Smith";
p.age = 25;
```
* class constructors(allows set all properties in one line). There are level qualifiers ```public``, ```private```, ```protected```
```typescript
//typescript
class Person{
  constructor(public firstName: string, 
              public lastName: string,
              public age: nummber) {};
}              
//javascript ES6
class Person{
  constructor(firstName, lastName, age) {
  this.firstName = firstName;
  this.lastName = lastName;
  this.age = age;
};
};

//initialization is the same in both cases
const p = new Person("A", "B", 25);
//possible but stupid, why to explicitly set type to Person if that is the const and we know the type we are setting here :)
const p: Person = new Person("John", "Smith", 25);
```
* possible to mark properties of the class as readonly(can be initialized at declaration, or in class constructor). const cannot be used with class properties
```typescript
class Block {
  readonly nonce: number;
  readonly hash: string;

  constructor (
    readonly index: number,
    readonly previousHash: string,
    readonly timestamp: number,
    readonly data: string
  ) {
    const { nonce, hash } = this.mine();
    this.nonce = nonce;
    this.hash = hash;
  }
  // The rest of the code is omitted for brevity
}
```
### interfaces
*  JavaScript doesn’t support interfaces but TypeScript does.
* TypeScript includes the keywords ```interface``` and ```implements```
```typescript
interface Person{
  firstName: string;
  lastName: string;
  age: number;
}
```
* if declare class with ```class``` it's instance van be used as a value or be instantiated via ```new```. While ```interface``` class is not an object, it would be just used by tpscript compiler checks, and would not be present in javascript code. 
* if two types has the same members, they are compatible. as typescript has structural type system(not nominal)
```
class Person {
        name: string;
}

class Customer {
        name: string;
}

const cust: Customer = new Person();
```
of course access modifiers can influence this
* what to choose ```type```, ```class```, ```interface```: if type doesn't need to be used for instantiating objects -> user interface, otherwirse class. For type safety use interface or type(neither is available in javascript), Interfaces cannot be used in unions or intersections(types can be used here)
* assignable only that class that has more or same properties
* union of custom types
```
export class SearchAction {
  actionType = "SEARCH";

  constructor(readonly payload: {searchQuery: string}) {}
}

export class SearchSuccessAction {
  actionType = "SEARCH_SUCCESS";

  constructor(public payload: {searchResults: string[]}) {}
}

export class SearchFailedAction {
  actionType = "SEARCH_FAILED";
}

export type SearchActions = SearchAction | SearchSuccessAction | SearchFailedAction;
```
* discriminated union, in this case it is ```Shape```
```
interface Rectangle {
    kind: "rectangle";
    width: number;
    height: number;
}
interface Circle {
    kind: "circle";
    radius: number;
}

type Shape = Rectangle | Circle;
function area(shape: Shape): number {
  switch (shape.kind) {
    case "rectangle": return shape.height * shape.width;
    case "circle": return Math.PI * shape.radius ** 2;
  }
}

const myRectangle: Rectangle = { kind: "rectangle", width: 10, height: 20 };
console.log(`Rectangle's area is ${area(myRectangle)}`);

const myCircle: Circle = { kind: "circle", radius: 10};
console.log(`Circle's area is ${area(myCircle)}`);
```
* the ```in``` guard, acts as a narrowing expression for types.
```
interface A { a: number };
interface B { b: string };

function foo(x: A | B) {
    if ("a" in x) {
        return x.a;
    }
    return x.b;
}
```
* type ```unknown``` was introduced in TypeScript 3.0. If you declare a variable of type unknown, the compiler will force you to narrow its type down before accessing its properties. 
* diff any vs unknown
```
type Person = {
  address: string;
}
let person1: any;
person1 = JSON.parse('{ "adress": "25 Broadway" }');
console.log(person1.address);  //misspelled address, will print undefined
```
```
let person2: unknown;
person2 = JSON.parse('{ "adress": "25 Broadway" }');
console.log(person2.address);//compilation failure, as misspelled.
```
* custom guards
```
const isPerson = (object: any): object is Person => "address" in object;

if (isPerson(person2)) {
    console.log(person2.address);
} else {
    console.log("person2 is not a Person");
}

const isPerson = (object: any): object is Person => !!object && "address" in object; // better as checks if object is truthy
```
# 3. Object-oriented programming with classes and interfaces. 
##  3.1  Working with classes 
* inheritance
```typescript
class Person {
  firstName: string;
  lastName: string;
  age: number;
  
  sayHello(): string{
    return `My name is ${this.firstName} ${this.secondName}`;
}

class Employee extends Person {
  department: string;
}

const emp = new Employee();
```
* 

Under the hood, JavaScript supports prototypal object-based inheritance, where one object can be assigned to another object as its prototype, and this happens during the runtime. During transpiling TypeScript to JavaScript, the generated code uses the syntax of the prototypal inheritance.
* if a method(s) is declared in a superclass, it will be inherited by the subclass unless the method was declared with the access qualified private
* publick methods or properties can be accessed internally by class or externally from other classes, scripts
* public is a default access modifier.
* Class members marked as protected can be accessed either from the internal class code or from class descendants.
* in javascript private, protected, publick keywords are absent, they are only for a convinience during development.
```
class Person {
    public firstName: string;
    public lastName: string;
    private age: number;
    
    protected sayHello(): string{
      return `My name is ${this.firstName} ${this.lastName}`;
  }
  
  class Employee extends Person {
    department: string;
    
    reviewPerformance(): void{
      this.sayHello();
      this.increasePay(5);
    }
    increasePay(percent: number): void{
    //   return percent*1000* 0.22 * this.age();//it will not compile as age is 
    }
  }
  
  const emp = new Employee();
  console.log(emp.increasePay(5));
```
* better way to declare class properties:
instead of   
```
class Person {
    public firstName: string;
    public lastName: string;
    private age: number;
    
    constructor(firstName: string, lastName: string, age: number){
      this.firstName = firstName;
      this.lastName = lastName;
      this.age = age;
}
```
use this one   
```
class Person {  
    constructor(public firstName: string, public lastName: string, public age: number){
}
```
* static variables(in javascript from ES6)
```
class Stat {
static stat_value = 100;

use(){
    Stat.stat_value--;
    console.log(`value is : ${Stat.stat_value}`);
    }
}
const s1 = new Stat();
s1.use();
const s2 = new Stat();
s1.use();
```
* Static class members are not shared by subclasses. 
* Singleton, constuctor is private, getInstance is static
```
class AppState {
    counter = 0;
    private static instanceRef: AppState;
    private constructor(){}
    static getInstance(): AppState{
        if (AppState.instanceRef === undefined){
            AppState.instanceRef = new AppState();
        }
        return AppState.instanceRef;
    }
}

const appState1 = AppState.getInstance();
const appState2 = AppState.getInstance();
```
* method```super()``` and keyword ```super``` need to specify from whom the method would be called(superclass or subclass)
* If both the superclass and the subclass have constructors, the one from the subclass must invoke the constructor of the superclass using the method super() as seen in listing 3.3.
```
class Person {
	constructor(public firstName: string, public lastName: string, private age: number) {}
}

class Employee extends Person {
	constructor(firstName: string, lastName: string, age: number, public department: string) {
		super(firstName, lastName, age);
	}
}
const emp = new Employee('Joe', 'Smith', 29, 'Accounting');
```
*  If a method in a subclass wants to invoke a method with the same name defined in the superclass, it needs to use keyword super instead of this when referencing the superclass method.
* abstract classes. Class that cannot be instantiated. Still class can include methods that are implemented, as well as only declared methods. 
```
abstract class Person {
	constructor(public name: string) {}

	changeAddress(newAddress: string) {
		console.log(`changeAddress ${newAddress}`);
	}
	giveDayOff() {
		console.log(`giveDayOff`);
	}

	promote(percent: number) {
		this.giveDayOff();
		this.increasePay(percent);
		console.log(`promote ${percent}`);
	}

	abstract increasePay(percent: number);
}

class Employee extends Person{
    increasePay(percent: number){
        console.log(`Increasing the salary of ${this.name} by ${percent}%`);
    }
}

class Contractor extends Person{
    increasePay(percent: number){
        console.log(`Increasing the hourly rate of ${this.name} by ${percent}%`);    }
}


const workers: Person[] = [];
workers[0] = new Employee('John');
workers[1] = new Contractor('Mary');
workers.forEach(worker => worker.promote(5));
```
* Since the descendants of Person don’t declare their own constructors, the constructor of the ancestor will be invoked automatically when we instantiate Employee and Contractor. If any of the descendants declared its own constructor, we’d have to use super() to ensure that the constructor of the Person is invoked.
* how to overload method:
    * arguments
```
class ProductService {
    getProducts();
    getProducts(id: number);
    getProducts(id?: number) {
        if (typeof id === 'number') {
          console.log(`Getting the product info for ${id}`);
        } else {
          console.log(`Getting all products`);
        }
    }
}
const prodService = new ProductService();
prodService.getProducts(123);
prodService.getProducts();
```
   * arguments and return type
```
interface Product {
  id: number;
  description: string;
}

class ProductService {

    getProducts(description: string): Product[];
    getProducts(id: number): Product;
    getProducts(product: number | string): Product[] | Product{
        if (typeof product === "number") {
          console.log(`Getting the product info for id ${product}`);
                    return { id: product, description: 'great product' };
        } else if (typeof product === "string") {
          console.log(`Getting product with description ${product}`);
          return [{ id: 123, description: 'blue jeans' },
                  { id: 789, description: 'blue jeans' }];
        } else {
           return null;
        }
    }
}

const prodService = new ProductService();

console.log(prodService.getProducts(123));

console.log(prodService.getProducts('blue jeans'));
```
* overloading constructor
```
class Product {
  id: number;
  description: string;

  constructor();
  constructor(id: number);
  constructor(id: number, description: string);
  constructor(id?: number, description?: string) {
    // Constructor implementation goes here
  }
}
```
* or similar solution using interface	
```
interface ProductProperties {
    id?: number;
    description?: string;
}

class Product {
  id: number;
  description: string;

  constructor(properties?: ProductProperties ) {
    // Constructor implementation goes here
  }
}
```
* interface example
```
interface MotorVehicle {
    startEngine(): boolean;
    stopEngine(): boolean;
    brake(): boolean;
    accelerate(speed: number);
    honk(howLong: number): void;
}
class Car implements MotorVehicle {
  startEngine(): boolean {
    return true;
  }
  stopEngine(): boolean{
    return true;
  }
  brake(): boolean {
    return true;
  }
  accelerate(speed: number) {
    console.log(`Driving faster`);
  }

  honk(howLong: number): void {
    console.log(`Beep beep yeah!`);
  }
}

const car = new Car();
car.startEngine();
```
* class need to implement each and every methods from the interface
* it is possible to set different types for the object
```
const car : Car = new Car();//if class Car has more methods than MotorVehicle, this object can invoke all of them
or 
const car : MotorVehicle = new Car();// this object can invoke only methods from the interface
```
* implementing several interfaces
```
interface Flyable {
  fly(howHigh: number);
  land();
}

interface Swimmable {
  swim(howFar: number);
}

class Car implements MotorVehicle, Flyable, Swimmable {
  // Implement all the methods from three
  // interfaces here
}
or 
class SecretServiceCar extends Car implements Flyable, Swimmable {
  // Implement all the methods from two
  // interfaces here
}
```
* extending interfaces
```
interface Flyable extends MotorVehicle{
	fly(howHigh: number);
	land();
}

class SpecialCar implements Flyable{
//to implement here all from MotorVehicle and all from Flyable
}
```
* Mantra: "Program to interfaces, not implementations"
* in TypeScript, you can declare a class that implements another class,(interface would be all  public stuff)
```
class MockProductService implements ProductService {
  // implementation goes here
}
```
* Name conventions: We named the interface IProductService starting with the capital I, while the class name was ProductService. Some people prefer using the suffix Impl with concrete implementations, e.g. ProductServiceImpl and the interface would be simply named ProductService.
* factory function may have part of business logic and return the proper instance based on conditions(tests, production)
```
function getProductService(isProduction: boolean): IProductService {
  if (isProduction) {
     return new ProductService();
  } else {
     return new MockProductService();
  }
}
const productService: IProductService;
const isProd = true;
productService = getProductService(isProd);
const products[] = productService.getProducts();
```
### Conclusion
*  You can create a class using another one as a base. We call it class inheritance.
* A subclass can use public or protected properties of a superclass.
* If a class property is declared as private, it can be used only within this class.
* You can create a class that can only instantiated once by using a private constructor.
* If a method with the same signature exists in the superclass and a subclass, we call it method overriding. Class constructors can be overridden as well. The keyword super and the method super() allow a subclass invoke the class members of a superclass.
* You can declare several signatures for a method, and this is called method overloading.
* Interfaces can declare method signatures, but can’t contain their implementations.
* You can inherit one interface from another.
* While implementing a class, see if there are certain methods that can be declared in a separate interface. Then your class has to implement that interface. This approach provide a clean way to separate declaring the functionality from implementing it.

# 4. Enums and generics
* by default starts with zero, possible to set the first value, all other would be calculated automatically
```
enum Weekdays {
  Monday = 1,
  Tuesday = 2,
  Wednesday = 3,
  Thursday = 4,
  Friday = 5,
  Saturday = 6,
  Sunday = 7
}
let dayOff = Weekdays.Tuesday;

console.log(Weekdays[3])
```
* string enums
```
enum Direction {
    Up = "UP",
    Down = "DOWN",
    Left = "LEFT",
    Right = "RIGHT",
}
```
* Redux framework(state machine) requires apps to emit state, good to be done via enums
* String enums are not reversible, and you can’t find the member’s name if you know its value.
* const enum in js is just a variable(not a function like the simple enum), hence cannot run search of member by value.

### 4.2  Generics
* TypeScript generics allow to write a function that can work with a variety of types. In other words, you can declare a function that works with a generic type(s), and the concrete type(s) can be specified later by the caller of this function.
```
let lotteryNumbers: Array<number>;
```
*  Specify the type of the array element followed by []:
```
const someValues: number[];
```
* Use a generic Array followed by the type parameter in angle brackets:
```
const someValues: Array<number>;
```
* in typescript,it is possible to add to array objects that have properties in common with the mentioned type
```
class Person {
    name: string;
}

class Employee extends Person {
    department: number;
}

class Animal {
    name: string;
    breed: string;
}

const workers: Array<Person> = [];

workers[0] = new Person();
workers[1] = new Employee();
workers[2] = new Animal();  // no errors
```
* When all elements of the array have the same type, use the syntax as in declaration of values1 - it’s just easier to read and write. But if an array can store elements of different types, you can use generics to restrict the types allowed in the array. 
```
const values1: string[] = ["Mary", "Joe"];
const values2: Array<string> = ["Mary", "Joe"];
const values4: Array<string | number> = ["Joe", 123, 567]; // no errors
```
* 4.2.2  Creating your own generic types
* programming to interfaces you think differently: "Today, I need to compare rectangles, and tomorrow they’ll ask me to compare other objects. Let me play smart and declare an interface with a function compare(). Then the class Rectangle as well as any other class in the future can implement this interface. The algorithms for comparing rectangles will be different than comparing, say, triangles, but at least they’ll have something in common and the method signature of compareTo() will look the same".
```
//bad use case, as can be used the unexpected type
interface Comparator {
  compareTo(value: any): number;
}

class Rectangle implements Comparator {

  compareTo(value: any): number {
    // the algorithm of comparing rectangles goes here
  }
}

class Triangle implements Comparator {

  compareTo(value: any): number {
    // the algorithm of comparing triangles goes here
  }
}
```

```
//good example of generic usage

interface Comparator<T> {
  compareTo(value: T): number;
}

class Rectangle implements Comparator<Rectangle>{
  compareTo(value: Rectangle): number {
    // the algorithm of comparing rectangles goes here
  }
}

class Triangle implements Comparator<Triangle>{
  compareTo(value: Triangle): number {
    // the algorithm of comparing rectangles goes here
  }
}

rectangle1.compareTo(triangle1);//typescript will report problem
rectangle1.compareTo(rectangle2); //good!!!
```
* default values of generic types
```
class A <T>{
	value: T;
}

class B extends A{//!!!!!!!!!!!transpile error
}
class C extends A<any>{
}
```
 * or
```
class A <T = any>{
	value: T;
}
//or 
class A < T = {} >{
}
class B extends A{
}
```
*  4.2.3  Creating generic functions
```
function printMe<T> (content: T): T {
    console.log(content);
    return content;
}


const printMe = <T> (content: T): T => {
    console.log(content);
    return content;
}

const a = printMe("Hello");

class Person{
  constructor(public name: string) { }
}

const b = printMe(new Person("Joe")); 
```
* comparison with generic pair
```
class Pair<K, V> {
  constructor(public key: K, public value: V) {}
}

function compare <K,V> (pair1: Pair<K,V>, pair2: Pair<K,V>): boolean {
    return pair1.key === pair2.key &&
           pair1.value === pair2.value;
}

let p1: Pair<number, string> = new Pair(1, "Apple");

let p2 = new Pair(1, "Orange");

// Comparing apples to oranges
console.log(compare<number, string>(p1, p2));

let p3 = new Pair("first", "Apple");

let p4 = new Pair("first", "Apple");

// Comparing apples to apples
console.log(compare(p3, p4));
```
* If a function can receive a function as argument or return another function, we call it a higher order function.
* higher order function that returns function with signature ```(c: number) => number```
```
(someValue: number) => (multiplier: number) => someValue * multiplier;
```
```
const outerFunct = (someValue: number) => (multiplier: number) => someValue * multiplier;
const innerFunct = outerFunct(10);
let result = innerFunc(5);
console.log(result)
```
*  
Let’s start with declaring the generic function that can take a generic type T but returns the function (c: number) ⇒ number:

```
type numFunc<T> = (arg: T) => (c: number) => number;
```
```
const noArgFunc: numFunc<void> = () => (c: number) => c + 5;
const numArgFunc: numFunc<number> = (someValue: number) => (multiplier: number) => someValue * multiplier;
const stringArgFunc: numFunc<string> = (someText: string) => (padding: number) => someText.length + padding;
const createSumString: numFunc<number> = () => (x: number) => 'Hello';
```
#  5. Decorators and advanced types
* a decorator is "a special kind of declaration that can be attached to a class declaration, method, accessor, property, or parameter. Decorators use the form @expression, where expression must evaluate to a function that will be called at runtime with information about the decorated declaration."
* Say you have class A {…} and there is a magic decorator called @Injectable() that knows how to instantiate classes and inject their instances into other objects.
```
@Injectable() class A {}
```
* Angular framework decorator(a built-in decorator @Component() for the class and @Input() for the property )
```
@Component({
	selector: 'order-processor',
	template: 'Buying {{quantity}} items'
})
export class OrderComponent{
	@Input() quantity: number;
}
```
* TypeScript doesn’t come with any built-in decorators, but you can create your own or use the ones provided by the framework or a library of your choice.
* in order to use decorators in ts:
```
--experimentalDecorators
```
```
"experimentalDecorators": true
```
### class decorator
* A class decorator is applied to the class, and the decorator function is executed when the constructor executes. The class decorator requires one parameter - a constructor function for the class.
```
function whoAmI (target: Function): void{
	console.log(`You are : \n ${target}`)
}


@whoAmI
class Friend {
	constructor (private name: string, private age: number){}
}
```
* In JavaScript, a mixin is a class that implements a certain behavior.Mixins are not meant to be used alone, but their behavior can be added to other classes. While JavaScript doesn’t support multiple inheritance, you can compose behaviors from multiple classes using mixins.
```
type constructorMixin = { new(...args: any[]): {} };

function <T extends constructorMixin> (target: T) {
   // the decorator is implemented here
}
```
* We want to create a decorator, that can accept a salutation parameter, and add to the class a new property message concatenating the given salutation and name. Also, we want to replace the code of the method sayHello() to print the message.
```
function useSalutation(salutation: string) {
  return function <T extends constructorMixin> (target: T) {
    return class extends target {
     name: string;
     private message = 'Hello ' + salutation + this.name;

     sayHello(){console.log(`${this.message}`);}
    }
  }
}

@useSalutation("Mr. ")
class Greeter {

  constructor(public name: string) { }
  sayHello() { console.log(`Hello ${this.name} `) }
}

const grt = new Greeter('Smith');
grt.sayHello();

```
### method decorator
* add logger for every function call
```
function logTrade(target, key, descriptor) {

    const originalCode = descriptor.value;

    descriptor.value = function () {

      console.log(`Invoked ${key} providing:`, arguments);
      return originalCode.apply(this, arguments);
    };

   return descriptor;
}

class Trade {
  @logTrade
  placeOrder(stockName: string, quantity: number, operation: string, traderID: number) {

     // the method implementation goes here
    }
  // other methods go here
}



const trade = new Trade();
trade.placeOrder('IBM', 100, 'Buy', 123);
```
* readonly mapped types
```
interface Person {
  name: string;
  age: number;
}

interface ReadonlyPerson {
  readonly name: string;
  readonly age: number;
}
const worker: ReadonlyPerson = {name: "John", age: 22};
//or
const worker: Person = {name: "John", age: 22};

function doStuff(person: Readonly<Person>) {

    person.age = 25;
}
```
### keyof and a lookup type. index type query keyof and a lookup type.
* Readonly declaration in lib.es5.d.ts
```
type Readonly<T> = {
  readonly [P in keyof T]: T[P];
};
```
* Usually, the letter T in generics represents type, K - key, V - value, P - property et al.
* keyof is called index type query, and it represents a union of allowed property names (the keys) of the given type. If the type Person would be our T, then keyof T would represent the union of name and age. 
```
type propNames = keyof Person; //i.e. "name"|"age"
```
* The keyof type query can be used only in type declarations.
* Readonly \<T\>
```typescript 
interface Person{
	name: string;
	age: number;
}

Readonly<Person>// will create smth like: 
interface Person{
	readonly name: string;
	readonly age: number;
}
```
* keyof usage
```
	function filterBy<T, P extends keyof T>(
	    property: P,
	    value: T[P],
	    array: T[]) {
	    return array.filter(item => item[property] === value);
	}
```
* creating opposite to Readonly -> Modifiable:
```
type Modifiable<T> = {
	-readonly[P in keyof T]: T[P];
}

```
* The minus sign in front of the readonly qualifier removes it from all properties of the given type.
```
interface Person {
  readonly name: string;
  readonly age: number;
}

const worker1: Person = {name: "John", age: 25};

worker1.age = 27;

const worker2: Modifiable<Person> = {name: "John", age: 25};

worker2.age = 27;
```

* creating a type with all properties optional(with question mark at the end):
```
type Partial<T> = {
	[P in keyof T]?: T[P];
}
```
* creating a type with all properties mandatory
```
type Required<T> = {
	[P in keyof T]-?: T[P];
}
```
* create a type from properties of another type
```
type Pick<T, K extends keyof T> {
	[P in K] : T[P];
}

interface Person {
	name: string;
	age: number;
	address: string; 
}

type PersonNameAddress<T, K> = Pick<Person, 'name'|'address'>;
```
* function that returns different types based on condition
```
function getProducts<T>(id?: T):
  T extends number ? Product : Product[]
```
* type that excludes types that are assignable to U
```
type Exclude<T, U> = T extends U ? never : T;
```
* type will contain all the properties of T except those that belong to the given type K.
```
type RemoveProps<T, K> = Exclude<keyof T, K>;
```
### the keyword infer
* Within the extends clause of a conditional type, it is now possible to have infer declarations that introduce a type variable to be inferred. Such inferred type variables may be referenced in the true branch of the conditional type. It is possible to have multiple infer locations for the same type variable.

For example, the following extracts the return type of a function type:
```
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : any;
```
* todo: replace return types with another ones.e.g. wrap it with Promise.
```
interface SyncService {
    baseUrl: string;
    getA(): string;
}
```
```
class AsyncService implements Promisify<SyncService> {
    baseUrl: string;

    getA(): Promise<string> {
        return Promise.resolve('');
    }
```
# 6. Tooling
* When you install TypeScript, its bin directory includes two files: tsc and tsserver. The latter is the TypeScript Language Service that IDEs use to support these productivity features. When you type the TypeScript code, the IDEs communicate with tsserver that compiles the code in memory
### Source maps
* Source map files have extensions .map, and they contain json-formatted data that map the corresponding code fragments in the generated JavaScript to the original language, which in our case is TypeScript.
```
tsc greeter.ts --sourceMap true
//
tsc greeter.ts --sourceMap true --inlineSources true // With this option, the .js.map file will also include the TypeScript source code of your app
```
```
<!DOCTYPE html>
<html>
  <body>
    <script src="greeter.js"/>
  </body>
</html>
```
```
npm install -g live-server
```
```
Settings -> more tools -> developer tools -> sources -> put a breakpoint in ts file ->F5
```
### The linter TSLint
* create a new folder and run here
```
npm init -y
```
* install typescript and tslint in the folder
```
npm install typescript tslint
```
* create configuration for tslint
```
./node_modules/.bin/tslint --init
```
* or use similar tool that runs local binaries by default from node_modules
```
npx tslint --init
```
* default rules
```
node_modules/tslint/lib/configs/recommended.js
```
### Bundling js code with Webpack
```
npm install webpack webpack-cli -g
```
create a project
```
.:
package.json  package-lock.json  src  webpack.config.js

./src:
index.js
```
```
npm install
```
```
npm run bundleup
```
```
node dist/index.bundle.js
# or
npm install webpack-dev-server -D
```
### Bundling js code with Webpack
package.json(ts-loader -> transplit ts to js)
```
"devDependencies": {
    "ts-loader": "^5.3.2",
    "typescript": "^3.2.2",
    "webpack": "^4.28.3",
    "webpack-cli": "^3.1.2"
}
```
### Babel
Babel is a free and open-source JavaScript compiler that is mainly used to convert ECMAScript 2015+ (ES6+) code into a backwards compatible version of JavaScript that can be run by older JavaScript engines. Babel is popular tool for using the newest features of the JavaScript programming language.
### Deno
a secure run-time environment that’s built on top of the V8 engine (just like Node) and has a built-in TypeScript compiler.

### ncc
It’s a command line interface for compiling a Node.js module into a single file, together with all its dependencies. This tool can be used by TypeScript developers who write the apps that run on the server side.


# 7 Using Typescript and Javascript together
### type definition files
The purpose of type definition files is to let the TypeScript compiler know the types expected by the APIs of a specific JavaScript library. Type definition files just include the names of the variables (with types) and function signatures (with types) used by a particular JavaScript library
* The suffix of any definition filename is d.ts, and you can find these files for more than 6000 JavaScript libraries at www.npmjs.com/~types. 
* how to install javascript types(e.g. types for js ajax):
```
npm install @types/jquery -D
```
* type definition files are for: use the existing JavaScript libraries while enjoying the benefits of a strongly-typed language.
### shims
* A shim is a library that intercepts API calls and transforms the code so the old environment (e.g. IE 11) can support newer API (e.g. ES6). Example
```
npm install @types/es6-shim -D
```
### Creating your own type definition files
* js hello.js
```
function greeting(name){
	console.log("hello " + name);
}
```
* file ./src/typings.d.ts
```
declare function greeting(name: string): void;
```
* reference directive on top of your ts file that uses function
```
///<reference path="src/typings.d.ts" />
```
## Introducing TypeScript in your JavaScript project
* write ts code even in js, but add in ts conf
```
 "allowJs": true.
```
* when code is ready, opt in to type checking by adding the tsc compiler’s option 
```
"checkJs": true
```
* ignore separate places in JS code
```
//@ts-ignore 
```
* the static type analyzer can check JS code for validity
```
//@ts-check 
```
* if tsc cannot infer the variable type based on how it’s used, the compiler silently defaults the type to any
```
"noImplicitAny": false 
```
* to help tsc with type inference add the JSDoc annotations (e.g. @param, @return)

# 8. Developing your own blockchain
* The very first block in a chain is called a genesis block.

# 9. Developing a browser-based blockchain node
* The lib directory implements the blockchain creation and block mining. It also has a universal function for generating hashes for both the browser and Node.js environments.
* The browser directory contains the code that implements the UI of our blockchain app that. This code uses the code from the directory lib.
* The node directory is a small demo of how you can run the app that also uses the code from the directory lib.
* package for webserver 'serve'
* to bundle css and htmls in a dist folder, need to use package 'copyfiles'
```
    "compileDeploy": "tsc && npm run deploy",
    "deploy": "copyfiles -f src/browser/*.html src/browser/*.css dist"
```
* after cloning code, need just to run
```
npm run compileDeploy
npm start
in browser go to dist folder
```
### web client part
* index.html, main.ts, styles.css
* html imports js code via:
```
<head>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <script type="module" src="dist/browser/main.js"></script>
</head>
```
* ts/js code reads data from html using getElementById API:
```
const amountEl              = document.getElementById('amount') as HTMLInputElement;
```
### how to debug in browser
* ```CTRL``` + ```SHIFT``` + ```I``` and go to source code(```Sources```)find code and add breakpoint. To check what is in variable, add it to ```Watch```

# 10. Client-server communications using Node.js, TypeScript, and WebSockets
### several useful packages/tools
* ws - a Node.js library that supports WebSocket protocol(should be added as dependency in package.json)
* press - a small Node.js framework offering HTTP support
* demon - a tool that restarts node.js-based apps when the script file changes are detected
* t-html - HTML templates in JavaScript for rendering to the browser’s DOM
### longest chain rule
* imagine latest approved block is #100, there are two miners with #101(1) and #101(2). It would be approved that one, after which somebody mined more blocks. 
### project structure
* public - html and css
* src/client - client impl + main.ts, tsconfig.client.json
* src/server - server impl + main.ts, tsconfig.server.json, message-server.ts, blockchain-server.ts
* ser/shared - shared data types definitions
* nodemon.json - config for nodemon tool
* tsconfig.json - common configuration of compiler for both client and server
### custom tsconfig.json
* If you introduce tsc configuration files named other than tsconfig.json, you need to use the -p option and specify the path to the file you want to use. 
### start node.js with ts
```
ts-node myScript.ts
```
### brief intro in websockets
*  protocol allows bidirectional message-oriented streaming of text and binary data between browsers and web servers. 
* In contrast to HTTP, WebSocket is not a request-response based protocol, and both the server and the client apps can initiate the data push to the other party as soon as the data becomes available, in real time.
* there is a server (or a device) that may need to send an immediate notification to the user because some important event happened elsewhere. This is different from the use case when the user decides to send a request for fresh data to the server.
### compare websockets to http
* http - half duplex vs websockets full duplex
* ~300 bytes of overhead of http vs couple of bytes by websockets
* http/https vs ws/wss

### how to check sample server
```
npm install
npm run build:server
node build/server/simple-websocket-server.js
firefox  localhost:8000
```
### websocket states(readyState)
0 - connecting - socket has been created. connection is not up
1 - open - the connection is open and ready to communicate
2 - closing the connection is closng 
3 - closed the connection is closed 

### how to debug
```
1) ctrl + I 
network -> ws -> messages
```
# 11. Developing Angular apps with TypeScript
* the main players in the market for developing web apps (besides super-popular jQuery) are Angular and React.js
* Vue.js is getting more and more traction every month
* Angular is a framework. 
* React.js is a library that does one thing really well: rendering of the UI in the browser’s DOM.
### angular components/functionalities
 * Dependency injection support
* Angular Material - a library of modern-looking UI components
* The router for arranging user’s navigation in the app
* A module to communicate with the HTTP servers
* Means for splitting the app into deployable modules that can be loaded either eagerly or lazily
* Powerful Forms support
* A library of reactive extensions (RxJS) to handle data streams
* A development web server that supports live code reloads
* The build tools for optimizing and bundling for deployment
* The command-line interface to quickly scaffold an app, a library, or smaller artifacts like components, modules, services, et al.
### install angular
```
npm install @angular/cli -g
```
```
ng version
```
### create new angular project (it maybe that you need to install/update nvm)
```
ng new project_name --minimal
```
### run angular project in the browser
```
ng serve -o
#or
ng server --prod
```
### angular project 
* src/app/app.component.ts - component,  a class decorated with @Component().It has properties selector, template, styles etc. Where selector contains value that can be used in html files. E.g.
```
  selector: 'app-root',
```
```
<body>
  <app-root></app-root>
</body>
```

* src/app/app.module.ts - module.  class decorated with the @NgModule() decorator.
* Angular allows you to separate HTML from the TypeScript code, and if if you prefer to keep HTML in a separate file, use the property templateURL instead of template, for example: templateUrl: "app.component.html"
* '''{}'''' in '''Welcome to {{title}}!''' is interpolation. Where '''title''' is a property of class AppComponent. 
* it is possible to add css files via styles array in app.components.ts
* in angular it is necessary to have at least one module. 
* The @NgModule() decorator requires you to list all components and modules used in the app. 
```
import { BrowserModule } from '@angular/platform-browser';
import { NgModule } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
declarations: [ AppComponent, CustomerComponent, OrderComponent ],
  imports: [
    BrowserModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```
* The bootstrap property names the top level component that the module must load first.
* main.ts  
```
import { enableProdMode } from '@angular/core';
import { platformBrowserDynamic } from '@angular/platform-browser-dynamic';

import { AppModule } from './app/app.module';
import { environment } from './environments/environment';

if (environment.production) {
  enableProdMode();
}

platformBrowserDynamic()
  .bootstrapModule(AppModule)
  .catch(err => console.error(err));
```
* platformBrowserDynamic() API creates a platform, which is an entry point to the web app.
* The ng serve command builds and rebuild the bundles in memory to speed up the development process, but if you’d like to see the actual files, run the ng build command, and it’ll create the file index.html and all the bundles in the directory dist.
*  The CLI-generated file angular.json contains all project configurations, and you can change the output directory as well as many other default options there.
* ng new hello-world --minimal. If we wouldn’t use the --minimal option, CLI would also generate some boilerplate code for unit and end-to-end testing. 
* component is a class with UI,
* a service is just a class that implements the business logic of your app. Services don’t have UI, and Angular will instantiate and inject a service into our application’s component(s) or another service.
### dependency injection
```
var product = new Product();
createShipment(product);
```

* createShipment() function depends on the existence of an instance of the Product object. In other words, the createShipment() function has a dependency: Product.
* But the function itself doesn’t know how to create Product. The calling script should somehow create and give (think inject) this object as an argument to the function.
* This is what the Dependency Injection pattern is about: If object A depends on an object identified by a token (a unique ID) B, object A won’t explicitly use the new operator to instantiate the object that B points at. Rather, it will have B injected from the operational environment.
* Let’s ask Angular CLI to generate the class ProductService in our hello-world project
```
ng generate service product --skip-tests
```

*  DI allows you to decouple application components and services by sparing them from knowing how to create their dependencies.
*  token, which is an arbitrary key representing an object to be injected.
*  You map tokens to values for DI by specifying providers. A provider is an instruction to Angular about how to create an instance of an object for future injection into a target component or another service.
*  a provider can be specified in the module declaration (if you need a singleton service), 
* or via injection
```
@Component({
  providers: [ProductService]
  # or
  # providers:[{provide: ProductService, useClass: ProductService}]
  # or
  # providers: [{provide: ProductService, useClass: AnotherProductService}]
})
class ProductComponent {
  product: Product;

  constructor(productService: ProductService) {

    this.product = productService.getProduct();
  }
}
```
* For declaring custom types, it’s better to use interfaces instead of classes if possible.
```
ng generate component product --t --s --skip-tests
```
* In Angular, asynchronous HTTP are implemented using a special observable object offered by the RxJS library that comes with Angular.
* If your app requires HTTP communications, you need to add HttpClientModule to the imports section of the @NgModule() decorator. After that, you can use the injectable service HttpClient for invoking get(), post(), put(), delete(), and other requests. Each of these requests returns an Observable object.
* In the context of client-server communications, you can think of Observable as a stream of data that can be pushed to your web app by the server. 
```
interface Product {
    id: number,
    title: string
}
...
class ProductService {
constructor(private httpClient: HttpClient) { }

ngOnInit() {
  this.httpClient.get<Product>('/product/123')
      .subscribe(
        data => console.log(`id: ${data.id} title: ${data.title}`),
        (err: HttpErrorResponse) => console.log(`Got error: ${err}`)
      );
  }
}
```
* hook method ngOnInit() is invoked by Angular when a component is instantiated and all its properties are initialized.
* By default, HttpClient expects the data in JSON format, and the data is automatically converted into JavaScript objects. If you expect non-JSON data, use the responseType option.

* input forms in typescript. 
```
myFormModel: FormGroup;

  constructor() {
    this.myFormModel = new FormGroup({
      username: new FormControl(''),
      ssn: new FormControl('')
    });
  }
```
* FormControl attaching one or more built-in or custom validators, which can be attached to a form control or to the enire form. add two built-in Angular validators to a form control.
```
city = new FormControl('New York',
                [Validators.required,
                 Validators.minLength(2)]);
```
* The injectable service FormBuilder is one of the way creation of form models.
```
constructor(fb: FormBuilder) {
  this.myFormModel = fb.group({
    username: [''],
    ssn: [''],
    passwordsGroup: fb.group({
      password: [''],
      pconfirm': ['']
    })
  });
}
```
* The formGroup directive binds an instance of the FormGroup class that represents the entire form model to a top-level form’s DOM element, usually a <form>. In the component template, use formGroup with a lowercase f, and in TypeScript, create an instance of the class FormGroup with a capital F
```
<form [formGroup]="myFormModel">
  <div formGroupName="dateRange">
    <input type="date" formControlName="from">
    <input type="date" formControlName="to">
  </div>
</form>
	
```
* In a single-page application (SPA), the web page won’t be reloaded, but its parts may change
* the content area of the page (known as router outlet). 
* Every application has one router object, and to arrange navigation, you need to configure the routes of your app. Angular includes many classes supporting navigation — for example, Router, Route, Routes, ActivatedRoute, and others. You configure routes in an array of objects of type Route, as in listing 11.27:

```
const routes: Routes = [
    {path: '',        component: HomeComponent},
    {path: 'product', component: ProductDetailComponent}
];
...
import { BrowserModule } from '@angular/platform-browser';
import { RouterModule } from '@angular/router';
...
@NgModule({
  imports: [BrowserModule, 
            RouterModule.forRoot(routes)],
    ...
})
```
# 13. Developing React.js apps with TypeScript
* it’s the second-most popular JavaScript library (jQuery remains the most broadly used library). React is not a framework but a library responsible for rendering views in the browser (think of the letter V in the MVC design pattern). 
* main elements in React are components. 
* react give an opportun. to control even smallest page element(e.g. div), and everything else can be written in another framework
* you can develop react in both js and ts, deploy via babel or webpack
### simplest application in react
```<!DOCTYPE html>
<html>
   <head>
     <meta charset="utf-8">
     <script 
 crossorigin src="https://unpkg.com/react@16/umd/react.development.js">
      </script>
     <script 
 crossorigin src="https://unpkg.com/react-dom@16/umd/
 react-dom.development.js">
      </script>
   </head>
   <body>
     <div id="root"></div>
 
     <script >
         const element = React.createElement('h1',
                                              null,
                                              'Hello World');
          ReactDOM.render(element,
                         document.getElementById('root'));
       </script>
 
   </body>
</html>
```
* declaring the page content (React.createElement())
* rendering page to the browser’s DOM (ReactDOM.render()) 
* In React, UI elements are represented as a tree of components that always has a single root element.
* This web page has a <div> with the ID root that serves as such an element for the content rendered by React
* downloading of the react packages are done over cdn
```
<script crossorigin src="https://unpkg.com/react@16/umd/react.development.js"></script>
```
# generate react application using the create-react-app
```
npm install create-react-app -g
```
* it automatically installs react, react-dom, react-scripts
```
create-react-app hello-world --typescript
```
* start application
```
npm start
//or 
react-scripts start
```
* main files to pay attention
```
public/index.html
src/App.test.tsx
src/App.tsx
src/index.tsx
```
* The serviceWorker.ts file is generated just in case you want to develop a progressive web app (PWA) that can be started offline using cached assets	
* JSX is an XML-like syntax extension to ECMAScript without any defined semantics. It’s NOT intended to be implemented by engines or browsers.
* JSX stands for JavaScript XML. It defines a set of XML tags that can be embedded inside JavaScript code. These tags can be parsed and turned into regular HTML tags for rendering by the browser, and React includes such a parser. 
* in order tsc to understand tsx files:
```
"jsx": "react"
```
* Every React app has at least one component, the root component. 
* A React component can be declared either as a function or a class. A functional (function-based) component is implemented as a function and is structured as follows.
```
const MyComponent = (props) => {
 
  return (
    <div>...</div>
  )
 
  // other functions may go here
}
 
export default MyComponent;
```
###  Managing a component’s state 
* A component’s state is a datastore that contains data that should be rendered by the component. The data in the component’s state is preserved even if React re-renders the component. If you have a Search component, its state could store the last search criteria and the last search result. 	
* for class-based components use ```this.setState(...). ```
```
interface State {
  userName: string;
  imageUrl: string;
}
+
export default class App extends Component {
 
  state: State = { userName: 'Rashid',
           imageUrl: 'https://picsum.photos/600/150' };
 
  render() {
    return (
      <div>
        <h1>{this.state.userName}</h1>
        <img src={this.state.imageUrl} alt=""/>
       </div
    );
  }
}

```
### hooks
* hooks allow you to “attach” behavior to a functional component without the need to write classes, create wrappers, or use inheritance. Example, useState()
```
const [userName, setUserName] = useState('John');
```
* example 
```
import React, {useState} from 'react';
 
interface State {
  userName: string;
  imageUrl: string;
}
 
const App: React.FC = () => {
 
  const [state, setState] = useState<State>({
    userName: 'John',
    imageUrl: 'https://picsum.photos/600/150'
  });
 
  return (
    <div>
      <h1>{state.userName}</h1>
      <img src={state.imageUrl} alt=""/>
    </div>
  );
}
 
export default App;

```
*  In React apps, the most popular state-management Javascript library is Redux (you could also choose to use MobX, another popular library). Redux is based on the following three principles:
	* Single source of truth— There is a single data store, which contains the state of your app.
    	* State is read-only— When an action is emitted, the reducer function clones the current state and updates the cloned object based on the action.
    	* State changes are made with pure functions— Reducer functions take an action and the current state object, and they return a new state.
 
*  In Redux, the data flow is unidirectional:
    	* The app component dispatches the action on the store.
    	* The reducer (a pure function) takes the current state object and then clones, updates, and returns it.
   	* The app component subscribes to the store, receives the new state object, and updates the UI accordingly.
* The reducer does—it’s a pure function that specifies how the app state should be changed. The reducer never changes the current state, but creates a new version and returns a new reference to it. The reducer function doesn’t implement any app logic that requires working with external services.
* useeffects is triggered after the component is added to the DOM, or each time it is re-rendered
* For making Ajax requests - use the browser’s Fetch API (http://mng.bz/Xp4a). The fetch() function returns a Promise,need async and await keywords 
* the async and await keywords for asynchronous code is equivalent to promises with chained .then() invocations 
* third-party libraries for handling HTTP requests Axios (www.npmjs.com/package/axios). 
* hook to be trigerred only if some exact state is changing
```
useEffect(() => console.log("useEffect() was invoked"),[]);
vs
useEffect(() => console.log("useEffect() was invoked"),['city']);
```
*  A React app is a tree of components: container components, presentation ones. A container (a.k.a. smart) component contains application logic, communicates with external data providers, and passes data to its child components. Typically, container components are stateful and have little or no markup. A presentation (a.k.a. dumb) component just receives data from its parent and displays it.A presentation component gets the data to be displayed via its props JavaScript object. 
* React uses props for data exchange between parent and child components 
* Each component has a property called props, which can be an arbitrary JavaScript object with properties specific to your app
* props are immutable, and a component can’t modify the original data received via props. 

###  Virtual DOm
*  layer between the component and the browser’s DOM. Each component consists of UI elements, and Virtual DOM optimizes the process of rendering these elements to the browser’s DOM
* if the DOM is changed by javascript, react checks diffs and send it browser dom

# Vue.js
* Vue is a component-based library that is focused on View(from MVC)
* it is possible to attach a vue instance to any html element
* uses virtual dom
### simplest vue.js example
```
<!DOCTYPE html>
  <body>
    <div id="one"></div>
    <div id="two"></div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js></script>
  </body>
</html>
```
* The constructor of the Vue object requires an argument of type Component-Options
* and now example with vue changing the div one
```
<!DOCTYPE html>
  <body>
    <div id="one">
        <h1>{{greeting}}</h1>
    </div>
    <div id="two">
        <h1>{{greeting}}</h1>
    </div>
 
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
 
    <script type="text/javascript">
      const myApp = new Vue({
          el: "#one",
          data: {
              greeting: "Hello World"
          }
      })
    </script>
  </body>
</html>
```
* it is possible to provide render object instead of string literals:
```
new Vue({
  render: h => h(App) // App is a top-level component
})
```
* h - stands for hyperscript(a script that generates html structures)
* as in example it can <div> element to play the role of UI component, it is still possible to make such Vue compoment. It consists of :
	* a declarative template
	* a script
	* styles
	
### vue cli
```
npm install @vue/cli -g
vue create hello-world
cd hello-world
npm run serve
```
* Installing Vue using npm gives TypeScript type declaration files,
* invoking the $mount('#app') method starts the mounting process and attaches the Vue instance to the DOM element with the app
* To support TypeScript decorators, the compiler’s experimentalDecorators option must be set to true in tsconfig.json.
* exclamation mark near msg is to request no notification on msg to be null or undefined:
```
<script lang="ts">
import { Component, Prop, Vue } from 'vue-property-decorator';
 
@Component
export default class HelloWorld extends Vue {
  @Prop() private msg!: string;
}
</script>
```
* Vue CLI generated the code with a @Prop() property-level decorator to declare that the HelloWorld component takes one property, msg.
* props are used to send data from parent to child. 
* emit() are used to send data from child to parent.
```
//child
this.$emit("place-order", orderData);
...
//parent
<order-component @place-order = "processOrder">
 
...
 
processOrder(payload) {
 
// handle the payload, i.e. the orderData received from the order component
 
}
```
