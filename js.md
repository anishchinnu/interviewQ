|Questions|
|--------- |
|[1. Difference between let & var ?](#1)|
|[2. Pure components in react ?](#2)|
|[3. Controlled & Uncontrolled components ?](#3)|
|[4. Features & why React?](#4)|
|[7. Ref & forwardRef ?](#7)|
|[9. Closures](#9)|
|[15. Higher order component](#15)|
|[28. Prototype property](#28)|
|[29. Debounce & Throttle](#29)|
|[30. bind?](#30)|
|[31. call & apply ?](#31)|
|[32. Arrow function ?](#32)|
|[33. Create Objects ?](#33)|
|[59. Anonymous Functions](#59)|
|[60. Bubbling and capturing](#60)|
|[61. Event delegation](#61)|
|[62. useMemo()](#62)|
|[63. Shallow compare](#63)|
|[64. React Lazy and Suspense](#64)|
|[65. JSX](#65)|
|[66. .env](#66)|
|[67. Array.splice() vs Array.slice()](#67)|
|[68. filter() vs find()](#68)|
|[69. JavaScript Object.assign()](#69)|
|[70. Destructuring assignment](#70)|
|[71. Spread Operator](#71)|
|[72. When to choose React & when to choose Angular](#72)|
|[73. forEach() vs map()](#73)|
|[74. Array.reduce()](#74)|
|[75. Redux](#75)|
|[76. Immutable Object](#76)|
|[77. Flatten the array](#77)|
|[78. Function Overloading](#78)|
|[79. Primitive data type](#79)|
|[80. Why Do We Write super(props)?](#80)|
|[81. Babel-Polyfill?](#81)|
|[82. Psuedo-class & Psuedo-element?](#82)|
|[83. Datalist?](#83)|
|[84. for..in and for..of?](#84)|
|[85. withRouter?](#85)|
|[86. map & flatMap?](#86)|
|[87. HTTP 2 vs HTTP 1.1?](#87)|
|[88. React.cloneElement() and createElement()?](#88)|
|[89. Execution context?](#89)|
|[90. Lexical environment](#90)|
|[91. Why hooks ?](#91)|
|[92. React diffing algorithm ?](#92)|
|[93. Prop drilling ?](#93)|
|[94. React Portal ?](#94)|

<a id="1"></a>
### 1. Difference between let & var ?
#### let is block-scoped
* A variable defined using a `var` statement is known throughout the function it is defined in, from the start of the function. (*)
* A variable defined using a `let` statement is only known in the block it is defined in, from the moment it is defined onward. (**)

```javascript
    function varTest() {
        var x = 31;
        if (true) {
            var x = 71;  // Same variable!
            console.log(x);  // 71
        }
        console.log(x);  // 71
    }

    function letTest() {
        let x = 31;
        if (true) {
            let x = 71;  // Different variable
            console.log(x);  // 71
        }
        console.log(x);  // 31
    } 
```
#### let doesn't allow to redeclare Variables
A variable declared with `var` can be redeclared again. For example,
```javascript
var a = 5; // 5
var a = 3; // 3
```
A variable declared with `let` cannot be redeclared within the same block or same scope. For example,
```javascript
let a = 5;
let a = 3; // Uncaught SyntaxError: Identifier 'a' has already been declared 
```
Redeclaring a variable with `var` in a different scope or block changes the value of the outer variable too.
```javascript
var a = 5;
console.log(a); // 5
{
    var a = 3;
    console.log(a); // 3
}
console.log(a); // 3
```
Redeclaring a variable with `let` in a different scope or block treats that variable as a different variable.
```k=javascript
let a = 5;
console.log(a); // 5
{
    let a = 3;
    console.log(a); // 3
}
console.log(a); // 5
```
#### let Doesn't Allow Hoisting
The variables declared with `var` are **hoisted** to the top of the scope of the program.
```javascript
console.log(a);
var a; // undefined (not an error)
```
The keyword `let` does not allow hoisting.
```javascript
console.log(a);
let a; // Uncaught ReferenceError: a is not defined
```

<a id="2"></a>
### 2. Pure components in react ?
1. For class components react provides `React.PureComponent` base class.
2. For Functional component react provides `React.memo` HOC (Higher Order Component).

`React.PureComponent`* is exactly the same as *`React.Component`* except that it handles the `shouldComponentUpdate()` method for you. When props or state changes, *PureComponent* will do a shallow comparison on both props and state. *Component* on the other hand won't compare current props and state to next out of the box. Thus, the component will re-render by default whenever `shouldComponentUpdate` is called.


React Components re-renders in the following scenarios:
* “setState” is called in Component
* “props” values are updated
* this.forceUpdate() is called

In the case of Pure Components, the React components do not re-render blindly without considering the updated values of React “props” and “state”. If updated values are the same as previous values, render is not triggered.

```javascript
    class ImpureComponent extends React.PureComponent {
        constructor() {
            super();
            this.state = {
            counter: 0
        }
    
        // The value of Counter is updated to same value during continues interval
    
        setInterval(() => {
            this.setState({
                counter: 0
            });
            }, 1000);
        }
  
    render() {        
        // This function wont be re-rendered in case when the new state is same as previous
        return <b>Counter Value: {this.state.counter}</b>
    }
}
```
#### React.memo
```javascript
const MyComponent = React.memo(function MyComponent(props) {
  /* render using props */
});
```
`React.memo` is a higher order component.

If your component renders the same result given the same props, you can wrap it in a call to `React.memo` for a performance boost in some cases by memoizing the result. This means that React will skip rendering the component, and reuse the last rendered result.

`React.memo` only checks for prop changes. If your function component wrapped in `React.memo` has a `useState`, `useReducer` or `useContext` Hook in its implementation, it will still rerender when state or context change.

By default it will only shallowly compare complex objects in the props object. If you want control over the comparison, you can also provide a custom comparison function as the second argument.
```javascript
function MyComponent(props) {
  /* render using props */
}
function areEqual(prevProps, nextProps) {
  /*
  return true if passing nextProps to render would return
  the same result as passing prevProps to render,
  otherwise return false
  */
}
export default React.memo(MyComponent, areEqual);
```

<a id="3"></a>
### 3. Controlled & Uncontrolled components ?
* In a controlled component, the form data is handled by the *state within the component*. The state within the component serves as *“the single source of truth”* for the input elements that are rendered by the component.
* Uncontrolled components act more like traditional HTML form elements. The data for each input element is stored in the DOM, not in the component. Instead of writing an event handler for all of your state updates, you use a `ref` to retrieve values from the DOM.
* A Controlled Component is one that takes its current value through props and notifies changes through callbacks like onChange. A parent component "controls" it by handling the callback and managing its own state and passing the new values as props to the controlled component. You could also call this a "dumb component".
* A Uncontrolled Component is one that stores its own state internally, and you query the DOM using a ref to find its current value when you need it. This is a bit more like traditional HTML.
Most native React form components support both controlled and uncontrolled usage:
```javascript
// Controlled:
<input type="text" value={value} onChange={handleChange} />

// Uncontrolled:
<input type="text" defaultValue="foo" ref={inputRef} />
// Use `inputRef.current.value` to read the current value of <input>
```
<a id="4"></a>
### 4. Features & why React??
The major features of React are:

* It uses **VirtualDOM** instead RealDOM considering that RealDOM manipulations are expensive.
* Supports **server-side** rendering.
* Follows **Unidirectional** data flow or data binding.
* Uses **reusable/composable** UI components to develop the view.

#### Why react
1. **Fast Learning Curve :** React is very a simple and lightweight library that only deals with the view layer. It is not a beast like other MV* frameworks such as Angular or Ember.
2. **Reusable Components :** React provides a component based structure. Each component decides how it should be rendered. Each component has its own internal logic. This approach has some amazing results. You can re-use components anywhere you need. As a result, (1) your app has consistent look and feel, (2) code re-use makes it easier to maintain and grow your codebase, and (3) it is easier to develop your app.
3. **Fast render with Virtual DOM :** Updating DOM is usually the bottleneck when it comes to the web performance. React is trying to solve this problem by using something called virtual DOM; a DOM kept in memory. Any view changes are first reflected to virtual DOM, then an efficient diff algorithm compares the previous and current states of the virtual DOM and calculates the best way (minimum amount of updates needed) to apply these changes. Finally those updates are applied to the DOM to ensure minimum read/write time. This is the main reason behind React’s high performance.
4. **Clean Abstraction :** One of the powerful sides of React is that it provides a good abstraction which means that it does not expose any complex internals to the user.
5. **Great Developer Tools :** Developer toolset is another important factor when you are choosing a development platform. There are two great tools you should be aware of: *React Developer Tools* and *Redux Developer Tools*. Both can be installed as Chrome extensions.
6. **React Native :** Learning React comes with a bonus: React Native. React is not a ‘write once run anywhere library’, as the creators says, it’s a ‘learn once write anywhere’ library. Yes, you can write native apps for Android and iOS using React Native. Although you will not be able to use the exact same code you wrote for web, you will be able to use the same methodology and the same architecture.
7. **It’s SEO-Friendly :** React lets you use similar code for both the client and server-side of an application.This means that any website using React immediately has an advantage in terms of speed and, thus, is more attractive for both users and crawlers.

### 5. What is state in React?
State of a component is an object that holds some information that may change over the lifetime of the component. We should always try to make our state as simple as possible and minimize the number of stateful components. Let's create an user component with message state,

```javascript
    class User extends React.Component {
        constructor(props) {
            super(props)

            this.state = {
            message: 'Welcome to React world'
            }
        }

        render() {
            return (
                <div>
                    <h1>{this.state.message}</h1>
                </div>
            )
        }
    }

```

### 6. What are props in React?
Props are inputs to components. They are single values or objects containing a set of values that are passed to components on creation using a naming convention similar to HTML-tag attributes. They are data passed down from a parent component to a child component.

The primary purpose of props in React is to provide following component functionality:

    1. Pass custom data to your component.
    2. Trigger state changes.
    3. Use via `this.props.reactProp` inside component's `render()` method.
<a id="7"></a>
### 7. refs & forwardRef?
The *ref* is used to return a reference to the element. They *should be avoided* in most cases, however, they can be useful when you need a direct access to the DOM element or an instance of a component.

```javascript
    class MyComponent extends React.Component {
        constructor(props) {
            super(props)
            this.myRef = React.createRef()
        }
        render() {
            return <div ref={this.myRef} />
        }
    }
```
#### Forward ref
React forwardRef is a method that allows parent components pass down (i.e., “forward”) refs to their children. Using forwardRef in React gives the child component a reference to a DOM element created by its parent component. This then allows the child to read and modify that element anywhere it is being used.
```javascript
import * as React from "react";
import ReactDOM from "react-dom";
 
const InputText = React.forwardRef((props, ref) => (
 <input ref={ref} {...props} />
));
 
export default function App() {
 const ref = React.useRef();
 
 function focus() {
   ref.current.focus();
 }
 
 return (
   <div className="App">
     <InputText ref={ref} placeholder="my input" />
     <button onClick={focus}>Focus</button>
   </div>
 );
}
 
const rootElement = document.getElementById("root");
ReactDOM.render(<App />, rootElement);
```
### 8. What is the purpose of using super constructor with props argument?

A child class constructor cannot make use of `this` reference until `super()` method has been called. The same applies for ES6 sub-classes as well. The main reason of passing props parameter to `super()` call is to access `this.props` in your child constructors.

    ** Passing props: **

```javascript
        class MyComponent extends React.Component {
            constructor(props) {
                super(props)

                console.log(this.props) // prints { name: 'John', age: 42 }
            }
        }
```

    ** Not passing props: **

```javascript
    class MyComponent extends React.Component {
        constructor(props) {
            super()

            console.log(this.props) // prints undefined

            // but props parameter is still available
            console.log(props) // prints { name: 'John', age: 42 }
        }

        render() {
            // no difference outside constructor
            console.log(this.props) // prints { name: 'John', age: 42 }
        }
    }
```

The above code snippets reveals that `this.props` is different only within the constructor. It would be the same outside the constructor.
<a id="9"></a>
### 9. Closure in Javascript ?
A **closure** is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). In other words, a closure gives you access to an outer function’s scope from an inner function. In JavaScript, closures are created every time a function is created, at function creation time.

Closure means that an inner function always has access to the vars and parameters of its outer function, even after the outer function has returned.

```javascript
function sum(a){
    return function sum2(b){
        return function sum3(c){                
            return a+b+c;                
        }
    }
}

console.log(sum(1)(2)(4));
//Output will be 7
```
#### Latest (New answer)
Whenever a function is invoked, a new scope is created for that call. The local variable declared inside the function belong to that scope – they can only be accessed from that function.
#### Remember:
1. The function scope is created for a function call, not for the function itself
2. Every function call creates a new scope
When the function has finished the execution, the scope is usually destroyed.
```javascript
function buildName(name) { 
    var greeting = "Hello, " + name; 
    return greeting;
}
```
It doesn’t get more simple than that. The function `buildName()` declares a local variable greeting and returns it. Every function call creates a new scope with a new local variable and. After the function is done executing, we have no way to refer to that scope again, so it’s garbage collected.
```javascript
function buildName(name) { 
    var greeting = "Hello, " + name + "!"; 
    var sayName = function() {
        var welcome = greeting + " Welcome!";
        console.log(greeting); 
    };
    return sayName; 
}

var sayMyName = buildName("John");
sayMyName();  // Hello, John. Welcome!
sayMyName();  // Hello, John. Welcome!
sayMyName();  // Hello, John. Welcome!
```
The function `sayName()` from this example is a closure.

A closure is a function which has access to the variable from another function’s scope. This is accomplished by creating a function inside a function. Of course, the outer function does not have access to the inner scope.

The `sayName()` function has it’s own local scope (with variable welcome) and has also access to the outer (enclosing) function’s scope. It this case, the variable greeting from `buildName()`.

After the execution of buildName is done, the scope is not destroyed in this case. The `sayMyName()` function still has access to it, so it won’t be garbage collected. However, there is not other way of accessing data from the outer scope except the closure.

This is the big gotcha of the entire concept. The closure serve as the gateway between the global context and the outer scope. I cannot access directly variables from the outer scope if the closure is not allowing it. This way, I can protect the variables from the outer scope. They are – by all means – private and the closure can serve as a getter or setter for them.
#### Remember:
1. Closure are nested function which has access to the outer scope
2. After the outer function is returned, by keeping a reference to the inner function (the closures) we prevent the outer scope to be destroyed.
Another extremely important thing to understand is that a closure is created at every function call. Whenever I’m using the closure, it will reference the same outer scope. If any variable is change in the outer scope, than the change will be visible in the next call as well.
```javascript
function buildContor(i) { 
    var contor = i;
    var displayContor = function() {
        console.log(contor++);
        contor++;
    };
    return displayContor; 
}

var myContor = buildContor(1);
myContor(); // 1
myContor(); // 2
myContor(); // 3

// new closure - new outer scope - new contor variable
var myOtherContor = buildContor(10);
myOtherContor(); // 10 
myOtherContor(); // 11

// myContor was not affected 
myContor(); // 4
```
#### Use closures to store private data
Javascript’s object system does not particularly encourage or enforce information hiding. The name of every property is a string, and any piece of a program can get access to any of the properties of an object simply by asking for it by name. Features such as for...in, loops and ES5’s Object.keys() and Object.getOwnPropertyNames() functions even make it easy to learn all the property names of an object.

Often, JS programmers resort to coding conventions rather than absolute enforcement mechanism for private properties. For example, some programmers use naming conventions such as prefixing or suffixing ‘private’ property names with an underscore character – _ -. This does nothing to enforce information hiding, but it suggests to well-behaved users to not modify that property.

Through closure, you can achieve real private data in Javascript. As we saw above, the closure is the gateway between the outer scope and the rest of the program. It can choose what data to expose and what not. Let’s see in example:
```javascript
function initializeData() {
    var myVar = 1; 
    return { 
        getVar: function() {
            return myVar;
        },
        setVar: function(v) {
            myVar = v;
        }
    };
}

obj = initializeData();

console.log(obj.getVar()); // 1

obj.setVar(2);
console.log(obj.getVar()); // 2

obj.setVar("string");
console.log(obj.getVar()); // string

```


### 10. Generator functions ?
Regular functions return only one, single value (or nothing).
Generators can return (“yield”) multiple values, one after another, on-demand. They work great with iterables, allowing to create data streams with ease.

```javascript
function* generateSequence() {
    yield 1;
    yield 2;
    return 3;
}

let data = generateSequence();

console.log(data.next()); // {value: 1, done: false}
console.log(data.next()); // {value: 2, done: false}
console.log(data.next()); // {value: 3, done: true}
    
```

### 11. Promise ?
`A promise is an object that may produce a single value some time in the future:` either a resolved value, or a reason that it’s not resolved (e.g., a network error occurred). A promise may be in one of 3 possible states: **fulfilled**, **rejected**, or **pending**. Promise users can attach callbacks to handle the fulfilled value or the reason for rejection.

Promise execution is asynchronous, which means that it's executed, but the program won't wait until it's finished to continue with the rest of the code.

```javascript
console.log('1');

var myPromise = new Promise(function(resolve, reject){
    if(true){
        resolve('2');
    }else{
        reject('Didn\'t work');
    }
})

myPromise.then(function(result){
    console.log(result);    
}).catch(function(){
    console.log('err')
})
console.log('3');

// Output
// 1
// 3
// 2
```
A promise can have 3 states which are the following:

* **Pending:** This is the initial state of the promise, the promise is now waiting for either to be resolved or rejected. For example, when are reaching out to the web with an AJAX request and wrapping the request in a promise. Then the promise will be pending in the time window in which the request is not returned.
* **Fulfilled:** When the operation is completed succesfully, the promise is fulfilled. For example, when we are reaching out to be web using AJAX for some JSON data and wrapping it in a promise. When we are succesfully getting data back the promise is said to be fulfilled.
* **Rejected:** When the operation has failed, the promise is rejected. For example, when we are reaching out to be web using AJAX for some JSON data and wrapping it in a promise. When we are getting a 404 error the promise has been rejected.

The *then* callback receives as a first argument the resolved value (the string 'success' in this case).

The *catch* callback receives as a first argument the rejected value (the string 'Error' in this case).

The *finally* method receives a callback which is executed on both promise fulfillment and rejection. Here we can write 'cleanup' code which need to be executed always regardless of promise outcome.

### 11. Clone Objects ?
1. Using Spread

```javascript
    const food = { chicken: '🥩', bacon: '🥓' };
    const cloneFood = { ...food };
    console.log(cloneFood);
    // { chicken: '🥩', bacon: '🥓' }
```
2. Using Object.assign

```javascript
    const food = { chicken: '🥩', bacon: '🥓' };
    const cloneFood = Object.assign({}, food);
    console.log(cloneFood);
    // { chicken: '🥩', bacon: '🥓' }
```
3. Using JSON
```javascript
    const food = { chicken: '🥩', bacon: '🥓' };
    const cloneFood = JSON.parse(JSON.stringify(food))
    console.log(cloneFood);
    // { chicken: '🥩', bacon: '🥓' }
```
#### Lodash DeepClone vs JSON
* **JSON.stringify/parse** only work with Number and String and Object literal without function or Symbol properties.
* **deepClone** work with all types, function and Symbol are copied by reference.

### 12. Transpilers
A source-to-source compiler, *transcompiler* or *transpiler* is a type of compiler that takes the source code of a program written in one programming language as its input and produces the equivalent source code in another programming language.

**List of ES6 Transpilers**
1. **Babel**: To transpile ES6 into ES5 for static builds
2. **Babelify**: To incorporate babel into your Gulp, Grunt, or npm run build process
3. **Traceur compiler**: ES6 features > ES5. Includes classes, generators, promises, destructuring patterns, default parameters & more.
4. **es6ify**: Traceur compiler wrapped as a Browserify v2 transform

### 13. isArray() ?
The Array.isArray() method determines whether the passed value is an Array.
```javascript
    Array.isArray([1, 2, 3]);  // true
    Array.isArray({foo: 123}); // false
    Array.isArray('foobar');   // false
    Array.isArray(undefined);  // false
```

### 14. PureComponent ?
*PureComponent* is exactly the same as *Component* except that it handles the *shouldComponentUpdate* method for you.

If your React component’s *render()* function renders the same result given the same props and state, you can use *React.PureComponent* for a performance boost in some cases.

PureComponent changes the life-cycle method *shouldComponentUpdate* and adds some logic to automatically check whether a re-render is required for the component. This allows a PureComponent to call method render only if it detects changes in *state* or *props*
<a id="15"></a>
### 15. Higher Order Component ?
**A higher-order component is a function that takes a component and returns a new component.**
HOC is not a feature in React or any other programming language, but a pattern evolved from the compositional ( made of components ) nature of react.
#### Functional programming and higher order functions
A higher order function is a function that accepts another function as an argument. You would have already used the `map` function which falls under this category.
```javascript
const reverse = (PassedComponent) =>
  ({ children, ...props }) =>
    <PassedComponent {...props}>
      {children.split("").reverse().join("")}
    </PassedComponent>

const name = (props) => <span>{props.children}</span>
const reversedName = reverse(name)
<reversedName>Hello</reversedName>
//=> <span>olleH</span>
```
The above example takes a component and reverses the content inside it. reverse is a HOC, that takes in an element ( name in the example ), find the content inside that element, reverses it and spits out an element with reversed content.
### 16. Context API ?
**Context** provides a way to pass data through the component tree without having to pass props down manually at every level.

React’s Context API is introduced to address issue of props drilling, where higher order component’s context can be accessed by child component without mapping props in intermediate components.
```javascript
    import React from 'react';

    const AppContext = React.createContext({ colour: 'blue', lang: 'en' });

    const App = () => 
        <AppContext.Provider value={{ colour: 'blue', lang: 'fr' }}>
            <Menu />
        </AppContext.Provider>;

    function Menu() {
        return <MenuItem />
    }

    const MenuItem = () =>
        <AppContext.Consumer>
            { value =>
                <div>
                    <p>Theme colour: {value.colour}</p>
                    <p>Locale: {value.lang}</p>
                </div>
            }
        </AppContext.Consumer>

    export default App;
```
Reference: https://medium.com/@chathuranga94/introduction-to-react-context-api-90f5e4d7a7a9

### 17. Difference b/w function somthing(){...} & const someting = () => {...} ?
A function declaration, that is:
```javascript
    doSomething() // works!
    function doSomething() {}
```
A function expression, that is:

```javascript
[const | let | var] = function () {} (or () =>
```
Is the creation of an anonymous function (function () {}) and the creation of a variable, and then the assignment of that anonymous function to that variable.

```javascript
if (true) {
    doSomething() // will fail
    const doSomething = function () {}
}
```
Will fail since doSomething is not defined. (It will throw a ReferenceError)

### 18. Function Declaration & Function Expression ?
**Function declarations** load before any code is executed.
```javascript
alert(foo()); // ERROR! foo wasn't loaded yet
var foo = function() { return 5; } 
```

**Function expressions** load only when the interpreter reaches that line of code.
```javascript
alert(foo()); // Alerts 5. Declarations are loaded before any code can run.
function foo() { return 5; } 
```
So if you try to call a function expression before it's loaded, you'll get an error! If you call a function declaration instead, it'll always work, because no code can be called until all declarations are loaded.

* A *Function Declaration* defines a named function variable without requiring variable assignment. Function Declarations occur as standalone constructs and cannot be nested within non-function blocks. 
* A *Function Expression* defines a function as a part of a larger expression syntax (typically a variable assignment ). Functions defined via Functions Expressions can be named or anonymous. Function Expressions must not start with “function”.

```javascript
//anonymous function expression
var a = function() {
    return 3;
}
 
//named function expression
var a = function bar() {
    return 3;
}
 
//self invoking function expression
(function sayHello() {
    alert("hello!");
})();
```

### 19. Hoisting ?
Hoisting is a JavaScript mechanism where variables and function declarations are moved to the top of their scope before code execution.

*Variables and constants declared with ```let``` or ```const``` are not hoisted!*

**Only declarations are hoisted**
JavaScript only hoists declarations, not initializations. If a variable is declared and initialized after using it, the value will be undefined. For example:

```javascript
console.log(num); // Returns undefined, as only declaration was hoisted, no initialization has happened at this stage 
var num; // declaration
num = 6; // initialization
```

#### example for function declaration
```javascript
function catName(name) {
    console.log("My cat's name is " + name);
}
catName("Tiger");
/*
The result of the code above is: "My cat's name is Tiger"
*/
```

```javascript
catName("Chloe");

function catName(name) {
    console.log("My cat's name is " + name);
}
/*
The result of the code above is: "My cat's name is Chloe"
*/
```
#### example for function expression
```javascript
catName();

var catName = function(){
    console.log("My cat's name");
}
/*
    It'll give error
    Javascript only hoists declarations
*/
```
### 20. Persistant data (or) Persistant Storage ?
After page refresh even if we want to get data without calling API's to server. Data will be stored in client side(Browser) using localstorage or cookies to maintain the persistancy.

### 21. render & hydrate ? 
**render** may change your node if there is a difference between the initial *DOM* and the *current DOM*. **hydrate** will only attach event handlers.

### 22. Single page application(SPA) & Multi page application(MPA)
**SPA**
A *single-page application* works in the browser and requires no page reloads and no extra time for waiting. The page doesn’t need to be updated since content is downloaded automatically. We use these types of applications every day.
#### Advantages
* **Fast and responsive :** Since single-page applications don’t update the entire page but only required content, they significantly improve a website’s speed. Most resources (HTML/CSS/Scripts) are only loaded once throughout the lifespan of an application. Only data is transmitted back and forth.
* **Caching capabilities :** A single-page app can cache any local data effectively. An SPA sends only one request to a server and then stores all the data it receives. Then it can use this data and work even offline.
* **Debugging with Chrome :** It’s easy to debug an SPA with Chrome since such apps are developed on frameworks like AngularJS Batarang and React developer tools. These frameworks have their own Chrome developer tools that make debugging much easier than with MPAs.
#### Disadvantages
* **SEO optimization :** Some people are of the opinion that SPAs provide poor SEO optimization. This is because single-page apps operate on JavaScript and download data on request from the client side. The URL doesn’t really change and different pages don’t have their unique URL addresses.
* **Browser history :** An SPA doesn’t save visitors’ jumps between states. This means that when users click the back button, they won’t go back. A browser only takes users to the previous page, not to the previous state in an app.
* **Security issues :** Single-page apps are less immune to cross-site scripting (XSS) attacks than are multi-page apps. Using XSS, hackers can inject client-side scripts into web applications.

**MPA**
Different to SPAs, multi-page applications (MPAs) are complex websites. To put it more simply, it is a traditional web application that reloads the entire page whenever the user interacts with the website.

Each time that the data is exchanged, the application makes a request from the server to display different information in the browser.
#### Advantages
* **SEO :** SEO is better on MPAs since the architecture is native to search engine crawlers. Such apps provide better control over SEO thanks to multiple pages and changing content. Moreover, developers can add meta tags to every page.
* **Unlimited scalability :** MPAs allow you to create new content and place it on new pages. Multi-page apps can include as much information about products or services as required, with no page limitations. Single-page applications don’t allow a lot of features on one page, which can lead to longer loading times.
* **Insights from Google Analytics :** MPAs can provide lots of analytics with valuable information on how a website is performing: which features are working and which aren’t. With a single-page app, the only useful information you can collect is who visitors are and for how long they stay on the site.
#### Disadvantages
* **Slow speed and performance :** With multi-page apps, a server needs to reload most resources such as HTML, CSS, and scripts with every interaction. When loading another page, the browser completely reloads page data and downloads all resources again, even components that are repeated throughout all pages (e.g. the header and footer). This affects speed and performance.
* **More time for development :** Compared to SPAs, MPAs take longer to develop. In most cases, developers have to code the backend from scratch. There are also difficulties in frontend and backend separation since they interact very closely with each other.
* **Maintenance and updates :** Maintaining and updating multiple pages can be a chore, and things only get worse the larger a website becomes. In addition, maintaining security may be problematic because developers need to secure each separate page.

### 23. Synthetic events
In React apps, events are written in the camelCase format, which means the *onclick* event will be written as *onClick* in a React app.

Synthetic events is a cross-browser wrapper around the browser’s native event. It has the same interface as the browser’s native event, including **stopPropagation()** and **preventDefault()**, except the events work identically across all browsers.

### 24. forceUpdate()
Actually, **forceUpdate()** is the only correct solution as **setState()** might not trigger a re-render if additional logic is implemented in *shouldComponentUpdate()* or when it simply returns false.

**forceUpdate()**
Calling forceUpdate() will cause render() to be called on the component, skipping *shouldComponentUpdate()*.

**setState()**
setState() will always trigger a re-render unless conditional rendering logic is implemented in *shouldComponentUpdate()*

### 25. What will happen if you use setState() in constructor?
When you use ```setState()```, then apart from assigning to the object state React also re-renders the component and all its children. You would get error like this: *Can only update a mounted or mounting component*. So we need to use ```this.state``` to initialize variables inside constructor.

### 26. How to prevent components from re-rendering?
* ```shouldComponentUpdate()``` — returns ‘true’ by default. You can override if you know which props have to trigger an update.
* ```PureComponents``` — The difference between them is that React.Component doesn’t implement shouldComponentUpdate method but ```React.PureComponent``` implements it with a shallow prop and state comparison.
* ```React.memo``` — The same as the previous one but it works with functional components.

### 27. Deep & Shallow copy ?
{Imagine two objects: A and B of same type _t(with respect to C++) and you are thinking about shallow/deep copying A to B}

**Shallow Copy**: Simply makes a copy of the reference to A into B. Think about it as a copy of A's Address. So, the addresses of A and B will be the same i.e. they will be pointing to the same memory location i.e. data contents.

**Deep copy:** Simply makes a copy of all the members of A, allocates memory in a different location for B and then assigns the copied members to B to achieve deep copy. In this way, if A becomes non-existant B is still valid in the memory. The correct term to use would be cloning, where you know that they both are totally the same, but yet different (i.e. stored as two different entities in the memory space). You can also provide your clone wrapper where you can decide via inclusion/exclusion list which properties to select during deep copy. This is quite a common practice when you create APIs.
<a id="28"></a>
### 28. Prototype property ?
JavaScript is a prototype based language, so, whenever we create a function using JavaScript, JavaScript engine adds a prototype property inside a function, Prototype property is basically an object (also known as Prototype object), where we can attach methods and properties in a prototype object, which enables all the other objects to inherit these methods and properties.

The JavaScript ```prototype``` property allows you to add new properties to object constructors:
```javascript
function Person(first, last, age, eye) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eye;
}
Person.prototype.nationality = "English";
var myFather = new Person("John", "Doe", 50, "blue");
document.getElementById("demo").innerHTML = "The nationality of my father is " + myFather.nationality;
```

The JavaScript ```prototype``` property also allows you to add new methods to objects constructors:

```javascript
function Person(first, last, age, eye) {
    this.firstName = first;
    this.lastName = last;
    this.age = age;
    this.eyeColor = eye;
}

Person.prototype.name = function() {
    return this.firstName + " " + this.lastName
};

var myFather = new Person("John", "Doe", 50, "blue");
document.getElementById("demo").innerHTML = "My father is " + myFather.name();
```
<a id="29"></a>
### 29. Debounce and Throttle ?
Tackling performance often crops up in JavaScript applications.
Throttling and debouncing give us control over the rate at which a function is called.

**Debouncing** enforces that a function not be called again until a certain amount of time has passed without it being called. For example, *“execute this function only if 100 milliseconds have passed without it being called.”*

**Throttling** enforces a maximum number of times a function can be called over time. For example, *“execute this function at most once every 100 milliseconds.”*

<a id="30"></a>
### 30. bind()
The `bind()` method creates a new function that, when called, has its `this` keyword set to the provided value, with a given sequence of arguments preceding any provided when the new function is called.

The `bind()` method returns a new function, when invoked, has its `this` sets to a specific value.

**Using JavaScript `bind()` for function binding**
When a method an object is passed to another function as a callback, the `this` value is lost.

```javascript
let person = {
    name: 'John Doe',
    getName: function() {
        console.log(this.name);
    }
};

setTimeout(person.getName, 1000);

// Output
undefined
```

```javascript
let f = person.getName.bind(person);
setTimeout(f, 1000);
```

In this code:
* First, bind the `person.getName` method to the `person` object.
* Second, pass the bound function `f` with `this` value set to the `person` object to the `setTimeout()` function.

**Using `bind()` method to borrow methods from a different object**

Suppose you have a `runner` object that has the `run()` method:
```javascript
let runner = {
    name: 'Runner',
    run: function(speed) {
        console.log(this.name + ' runs at ' + speed + ' mph.');
    }
};
```
And the `flyer` object that has the `fly()` method:
```javascript
let flyer = {
    name: 'Flyer',
    fly: function(speed) {
        console.log(this.name + ' flies at ' + speed + ' mph.');
    }
};
```
If you want the `flyer` object to be able to run, you can use the `bind()` method to create the `run()` function with the `this`  sets to the `flyer` object:

```javascript
let run = runner.run.bind(flyer, 20);
run();
```
In this statement:

* Call the `bind()` method of the `runner.run()` method and pass in the `flyer` object as the first argument and 20 as the second argument.
* Invoke the `run()` function.

<a id="31"></a>
### 31. call and apply
The `call()` method calls a function with a given `this` value and arguments provided individually.
The `apply()` method calls a function with a given `this` value, and arguments provided as an array (or an array-like object).

`call` and `apply` are very similar—they invoke a function with a specified `this` context, and optional arguments. The only difference between `call` and `apply` is that `call` requires the arguments to be passed in one-by-one, and `apply` takes the arguments as an array.
```javascript
const book = {
    title: 'Brave New World',
    author: 'Aldous Huxley',
}

function summary() {
    console.log(`${this.title} was written by ${this.author}.`)
}
summary()

// Output
"undefined was written by undefined"
```
Since `summary` and `book` have no connection, invoking `summary` by itself will only print `undefined`, as it's looking for those properties on the global object.

However, you can use `call` and `apply` to invoke the `this` context of `book` on the function.
```javascript
summary.call(book)
// or:
summary.apply(book)
// Output
"Brave New World was written by Aldous Huxley."
```
There is now a connection between `book` and `summary` when these methods are applied. Let's confirm exactly what `this` is.
```javascript
function printThis() {
  console.log(this)
}

printThis.call(book)
// or:
whatIsThis.apply(book)
// Output
{title: "Brave New World", author: "Aldous Huxley"}
```
This is how `call` and `apply` are the same, but there is one small difference. In addition to being able to pass the `this` context as the first argument, you can also pass additional arguments through.
```javascript
function longerSummary(genre, year) {
    console.log(
        `${this.title} was written by ${this.author}. It is a ${genre} novel written in ${year}.`
    )
}
```
With `call` each additional value you want to pass is sent as an additional argument.
```javascript 
longerSummary.call(book, 'dystopian', 1932) 
//Output
"Brave New World was written by Aldous Huxley. It is a dystopian novel written in 1932."
```
If you try to send the exact same arguments with `apply`, this is what happens:
```javascript
longerSummary.apply(book, 'dystopian', 1932)
// Output
Uncaught TypeError: CreateListFromArrayLike called on non-object at <anonymous>:1:15
```
Instead, for `apply`, you have to pass all the arguments in an array.
```javascript
longerSummary.apply(book, ['dystopian', 1932])
// Output
"Brave New World was written by Aldous Huxley. It is a dystopian novel written in 1932."
```
<a id="32"></a>
### 32. Arrow function ?
Inside a regular function, `this` keyword refers to the function where it is called.
However, `this` is not associated with arrow functions. Arrow function does not have its own `this`. So whenever you call `this`, it refers to its parent scope.

Arrow functions do not have their own `this` binding. Instead, they go up to the next level of execution.
```javascript
const whoAmI = {
    name: 'Leslie Knope',
    regularFunction: function() {
        console.log(this.name)
    },
    arrowFunction: () => {
        console.log(this.name)
    },
}

whoAmI.regularFunction() // "Leslie Knope"
whoAmI.arrowFunction() // undefined
```
It can be useful to use the arrow function in cases where you really want `this` to refer to the outer context. For example, if you had an event listener inside of a class, you would probably want `this` to refer to some value in the class.
```javascript
const button = document.createElement('button')
button.textContent = 'Click me'
document.body.append(button)

class Display {
    constructor() {
        this.buttonText = 'New text'

        button.addEventListener('click', event => {
        event.target.textContent = this.buttonText
        })
    }
}

new Display()
```
#### Arguments Binding
Regular functions have arguments binding. That's why when you pass arguments to a regular function, you can access them using the `arguments` keyword. For example,
```javascript
let x = function () {
    console.log(arguments);
}
x(4,6,7); // Arguments [4, 6, 7]
```
Arrow functions do not have arguments binding.
When you try to access an argument using the arrow function, it will give an error. For example,
```javascript
let x = () => {
    console.log(arguments);
}

x(4,6,7); 
// ReferenceError: Can't find variable: arguments
```
To solve this issue, you can use the **spread** syntax. For example,
```javascript
let x = (...n) => {
    console.log(n);
}

x(4,6,7); // [4, 6, 7]
```
#### Things You Should Avoid With Arrow Functions
1. You should not use arrow functions to create methods inside objects.
```javascript
let person = {
    name: 'Jack',
    age: 25,
    sayName: () => {

        // this refers to the global .....
        //
        console.log(this.age);
    }
}

person.sayName(); // undefined
```
2. You cannot use an arrow function as a constructor. For example,
```javascript
let Foo = () => {};
let foo = new Foo(); // TypeError: Foo is not a constructor
```
<a id="33"></a>
### 33. Create Objects ?
**Using Object literal**
```javascript
var human = {
	firstName: "Virat",
	lastName: "Kohli",
	age: 30,
	fullName: function(){
		return this.firstName + " " + this.lastName		
	}
}
```
**Using new Object() syntax**
```javascript
var human = new Object()
console.log(human);// Creates an empty object
```
Creating objects using `new Object()` and object literal does the same thing. For simplicity, readability and execution speed, use object literal.
We can add new properties and methods to the above objects using the dot and/or square notation.
**Object Constructor**
Constructor function in JavaScript is used for creating new objects using a blueprint. Just like classes are used for creating objects in Java, C# we can use constructors to create objects in JavaScript.
Objects can be created using the constructor function syntax using the following two steps:
1. Define the object blueprint(class) by defining the constructor function. By convention, name of the constructor function should start with capital letter
2. Create the object by instantiating the constructor function using new operator
```javascript
function Human(firstName, lastName) {
	this.firstName = firstName,
	this.lastName = lastName,
	this.fullName = function() {
		return this.firstName + " " + this.lastName;
	}
}

var person1 = new Human("Virat", "Kohli");

console.log(person1)
```
This is just the blueprint. To create the object we will use the new operator.
```javascript
var viratKohli = new Human("Virat", "Kohli");
var sachinTendulkar = new Human("Sachin", "Tendulkar");
```

### 34. Is JavaScript synchronous or asynchronous ?
JavaScript is always synchronous and single-threaded. If you're executing a JavaScript block of code on a page then no other JavaScript on that page will currently be executed.

JavaScript is only asynchronous in the sense that it can make, for example, Ajax calls. The Ajax call will stop executing and other code will be able to execute until the call returns (successfully or otherwise), at which point the callback will run synchronously. No other code will be running at this point. It won't interrupt any other code that's currently running.

JavaScript timers operate with this same kind of callback.

Describing JavaScript as asynchronous is perhaps misleading. It's more accurate to say that JavaScript is synchronous and single-threaded with various callback mechanisms.

### 35. setState is synchronous or asynchronous ?
`setState` in react is asynchronous

### 36. Output
```javascript
console.log(1);

setTimeout(() => {
    console.log(2);    
}, 1000);

setTimeout(() => {
    console.log(3);    
}, 0);

console.log(4);

// Output
1
4
3
2
```

### 37. Async & Await ?
These features basically act as syntactic sugar on top of promises, making asynchronous code easier to write and to read afterwards.

`await` is a new operator used to wait for a promise to resolve or reject. It can only be used inside an `async` function.

```javascript
function scaryClown() {
    return new Promise(resolve => {
        setTimeout(() => {
            resolve('🤡');
        }, 2000);
    });
}

async function msg() {
    const msg = await scaryClown();
    console.log('Message:', msg);
}

msg(); // Message: 🤡 <-- after 2 seconds
```

### 38. DOM & Virtual DOM ?
* DOM stands for *Document Object Model*. DOM is a platform and language-neutral interface that allows programs and scripts to dynamically access and update the content, structure, and style of a document.
* The DOM represents the document as nodes and objects.
* The DOM is an object-oriented representation of a web page, which can be modified with a scripting language like JavaScript.

Virtual DOM is an abstraction layer on top of DOM. It consists of react application's element.

Changing the state means that React triggers an update when we call the setState function (in React Hooks you get this fn from useState). This doesn't only mean that the render function of the component will be called, but also that all its subsequent child-components will re-render, regardless of whether their props have changed or not.

1. Whenever anything may have changed, the entire UI will be re-rendered in a Virtual DOM representation.
2. The difference between the previous Virtual DOM representation and the new one will be calculated.
3. The real DOM will be updated with what has actually changed. This is very much like applying a patch.

### 39. Callback ?
In JavaScript, you can also pass a function as an argument to a function. This function that is passed as an argument inside of another function is called a callback function. For example,
```javascript
// function
function greet(name, callback) {
    console.log('Hi' + ' ' + name);
    callback();
}

// callback function
function callMe() {
    console.log('I am callback function');
}

// passing function as an argument
greet('Peter', callMe);

//Output
Hi Peter
I am callback function
```
In JavaScript, functions are objects. Because of this, functions can take functions as arguments, and can be returned by other functions. Functions that do this are called higher-order functions. Any function that is passed as an argument is called a callback function.

#### Benefit of Callback Function

The benefit of using a callback function is that you can wait for the result of a previous function call and then execute another function call.

### 40. DOM ?
The Document Object Model (DOM) is an application programming interface (API) for manipulating HTML and XML documents.

The DOM represents a document as a tree of nodes. It provides API that allows you to add, remove, and modify parts of the document effectively.

Note that the DOM is cross-platform and language-independent ways of manipulating HTML and XML documents.

#### A document as a hierarchy of nodes
The DOM represents an HTML or XML document as a hierarchy of nodes. Consider the following HTML document:
```html
<html>
    <head>
        <title>JavaScript DOM</title>
    </head>
    <body>
        <p>Hello DOM!</p>
    </body>
</html>
```
In the DOM tree, the document is the root node. The root node has one child which is the `<html>` element. The `<html>` element is called the document element.

Each document can have only one document element. In an HTML document, the document element is the `<html>` element. Each markup can be represented by a node in the tree.

### 50. Variable shadowing
```javascript
// global variable
var message = "Hello";
function say() {
    // local variable
    var message = 'Hi';
    console.log(message); // which message?
}
say();// Hi
console.log(message); // Hello
```
In this example, we have two variables that share the same name: message. The first message variable is a global variable whereas the second one is the local variable.

Inside the say() function, the global message variable is shadowed. It cannot be accessible inside the say() function but outside of the function. This is called variable shadowing.

### 51. Accessing global variable inside the function

```javascript
// global variable
var message = "Hello";
function say() {
    // local variable
    message = 'Hi';
    console.log(message); // which message?
}
say();// Hi
console.log(message); // Hi
```
In this example, we define a global variable named message. In the say() function, we reference the global message variable by omitting the var keyword and change its value to a string of Hi.

Although it is possible to refer to a global variable inside a function, it is not recommended. This is because the global variables are very difficult to maintain and potentially cause much confusion.
#### Non-strict mode
The following example defines a function and declares a variable `message`. However, the `var` keyword is not used.

```javascript
function say() {
    message = 'Hi'; // what?
    console.log(message);
}
say(); // Hi
console.log(message); // Hi
Code language: JavaScript (javascript)
```
When you execute the script, it outputs the  Hi string twice in the output.

Because when we call the say() function, the JavaScript engine looks for the variable named message inside the scope of the function.

As a result, it could not find any variable declared with that name so it goes up to the next immediate scope which is the global scope in this case and asks whether or not the `message` variable has been declared.

Because the JavaScript engine couldn’t find any of global variable named `message` so it creates a new variable with that name and adds it to the global scope.

#### strict mode
To avoid creating a global variable accidentally inside a function because of omitting the var keyword, you use the strict mode by adding the `"use strict";` at the beginning of the JavaScript file (or the function) as follows:

```javascript
"use strict";

function say() {
    message = 'Hi'; // ReferenceError
    console.log(message);
}
say(); // Hi
console.log(message); // Hi
```
### 52. Semantic & non semantic Elements
**Semantic Elements:-** HTML5 semantic tags define the purpose of the element. By using semantic markup, you help the browser understand the meaning of the content instead of just displaying it. By providing this extra level of clarity, HTML5 semantic elements also help search engines to read the page and find the required information faster.
Ex: ```<header>, <footer>, <nav>, <section>, <article>```....,.

Some of the benefits from writing semantic markup are as follows:
* Search engines will consider its contents as important keywords to influence the page's search rankings.
* Screen readers can use it as a signpost to help visually impaired users navigate a page.
* Finding blocks of meaningful code is significantly easier than searching through endless divs with or without semantic or namespaced classes.
* Suggests to the developer the type of data that will be populated.
* Semantic naming mirrors proper custom element/component naming.

**Non Semantic Elements:-** Traditionally, developers have implemented non-semantic elements with a class attribute to define the structure and express the meaning of content. It tells nothing about its content. The element has no special meaning at all. It represents its children. It can be used with the class, lang, and title attributes to mark up semantics common to a group of consecutive elements.
Ex: `<span>`, `<div>`

### 53. Declaring a DOCTYPE

All HTML need to have a `DOCTYPE` declared. The `DOCTYPE` is not actually an element or HTML tag. It lets the browser know how the document should be interpreted, by indicating what version or standard of HTML (or other markup language) is being used.
HTML5 Documents

For HTML5 documents (which nearly all new web documents should be), the DOCTYPE declaration should be:

```<!DOCTYPE html>```

### 54. Call stack
JavaScript engine uses a call stack to manage execution contexts: the Global Execution Context and Function Execution Contexts.

The call stack works based on the LIFO principle i.e., last-in-first-out.

When you execute a script, the JavaScript engine creates a Global Execution Context and pushes it on top of the call stack.

Whenever a function is called, the JavaScript engine creates a Function Execution Context for the function, pushes it on top of the Call Stack, and starts executing the function.

If a function calls another function, the JavaScript engine creates a new Function Execution Context for the function that is being called and pushes it on top of the call stack.

When the current function completes, the JavaScript engine pops it off the call stack and resumes the execution where it left off in the last code listing.
```javascript
function add(a, b) {
    return a + b;
}

function average(a, b) {
    return add(a, b) / 2;
}

let x = average(10, 20);
```

![Call Stack](../helper_doc/images/JavaScript-Call-Stack.png)

#### Stack overflow

The call stack has a fixed size, depending on the implementation of the host environment, either the web browser or Node.js.

If the number of the execution contexts exceeds the size of the stack, a stack overflow will occur.

For example, when you execute a recursive function that has no exit condition, it will result in a stack overflow error:

```javascript
function foo() {
    foo();
}

foo(); // stack overflow
```

### 55. Swap 2 number without use of temp

ES6 (Firefox and Chrome already support it (Destructuring Assignment Array Matching)):

```javascript
let a = 5, b = 6;
[a, b] = [b, a];
console.log(`${a} ${b}`);
```

### 56. render()
First of all, `render()` is not user callable. It is part of the React component lifecycle and is called by React at various app stages, generally when the React component is first instantiated, or when there is a new update to the component state. Render does not take any arguments, and returns a `JSX.Element` which contains the view hierarchy of the current component. This view hierarchy will later be translated into HTML and displayed in the browser window.

Within the lifecycle, these are the scenarios where render is called:

* After the React component is first instantiated, following the constructor() call.
* After an update to the component’s props
* After a setState() call

### 57. router
A router allows your application to navigate between different components, changing the browser URL, modifying the browser history, and keeping the UI state in sync.

### 58. First class citizens or first class functions
[Link](https://www.javascripttutorial.net/javascript-functions-are-first-class-citizens/)

Functions in JavaScript are first-class objects, which means you can store functions in variables, pass them to other functions as arguments, and return them from other functions as values.

#### Storing functions in variables
Functions are the first-class citizens in JavaScript. In other words, you can treat functions like values of other types.

The following declares the `add()` function and assigns the function name to the variable `sum`:
```javascript
function add(a, b) {
    return a + b;
}

let sum = add;
```
In the assignment statement, we didn’t include the opening and closing parentheses at the end of the add identifier. We also didn’t execute the function but referencing the function itself.

It means that we have two ways to execute the same function. We can call it normally as follows:
```javascript
let result = add(10, 20);
```
Or execute it like this:
```javascript
let result = sum(10,20);
```

#### Returning functions from functions
Since functions are values, you can return a function from another function.
The following compareBy() function returns a function that compares two objects by a provided property:
```javascript
function compareBy(propName) {
    return function (a, b) {
        let x = a[propName],
            y = b[propName];

        if (x > y) {
            return 1;
        } else if (x < y) {
            return -1
        } else {
            return 0;
        }
    }
}
```

#### Passing a function to another function
Because functions are values, you can pass a function as an argument into another function.
The following declares the `average()` function that takes three arguments. The third argument is a function:
```javascript
function add(a, b) {
    return a + b;
}

let sum = add;

function average(a, b, fn) {
    return fn(a, b) / 2;
}

let result = average(10, 20, sum);

console.log(result);
//Output: 15
```
Now, you can pass the sum function to the `average()` function as follows:

<a id="59"></a>
### 59. Anonymous Functions
An anonymous function is a function without a name. An anonymous function is often not accessible after its initial creation.

The following shows an anonymous function that displays a message:
```javascript
let show = function () {
    console.log('Anonymous function');
};

show();
```
In this example, the anonymous function has no name between the `function` keyword and parentheses `()`.

#### Using anonymous functions as arguments of other functions
We often use anonymous functions as arguments of other functions. For example:
```javascript
setTimeout(function () {
    console.log('Execute later after 1 second')
}, 1000);
```
In this example, we pass an anonymous function into the `setTimeout()` function. The `setTimeout()` function executes this anonymous function one second later.
#### Immediately invoked function execution
If you want to create a function and execute it immediately after declaration, you can use the anonymous function like this:
```javascript
(function() {
    console.log('IIFE');
})();
```
#### Arrow functions
ES6 introduced arrow function expression that provides a shorthand for declaring anonymous functions:

For example, this function:
```javascript
let show = function () {
    console.log('Anonymous function');
};
```
… can be shortened using the following arrow function:
```javascript
let show = () => console.log('Anonymous function');
```
<a id="60"></a>
### 60. Event propagation (Bubbling and capturing)
Event propagation is a mechanism that defines how events propagate or travel through the DOM tree to arrive at its target and what happens to it afterward.

#### Bubbling
**When an event happens on an element, it first runs the handlers on it, then on its parent, then all the way up on other ancestors.**
```html
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```
A click on the inner `<p>` first runs onclick:
1. On that `<p>`.
2. Then on the outer `<div>`.
3. Then on the outer `<form>`.
4. And so on upwards till the document object.
The process is called “bubbling”, because events “bubble” from the inner element up through parents like a bubble in the water.

**Stopping bubbling**
A bubbling event goes from the target element straight up. Normally it goes upwards till `<html>`, and then to `document` object, and some events even reach `window`, calling all handlers on the path.
But any handler may decide that the event has been fully processed and stop the bubbling.
The method for it is `event.stopPropagation()`.

#### Event Capturing
Event capturing is one of two ways to do event propagation in the HTML DOM. In event capturing, an event propagates from the outermost element to the target element. It is the opposite of event bubbling, where events propagate outwards from the target to the outer elements.

**Note :** *Capturing happens before bubbling. The three phases of event propagation are: capturing, target, and bubbling.*
```javascript
function addClickListener (msg, num, type) {
  document.querySelector("#div" + num)
    .addEventListener("click", () => alert(msg + num), type);
}
bubble  = (num) => addClickListener("bubble ", num, false);
capture = (num) => addClickListener("capture ", num, true);

// first capture then bubble
capture(1);
capture(2);
bubble(2);
bubble(1);

// try reverse order
bubble(3);
bubble(4);
capture(4);
capture(3);
```
```html
<div id="div1">
  div 1
  <div id="div2">
    div 2
  </div>
</div>
<div id="div3">
  div 3
  <div id="div4">
    div 4
  </div>
</div>
```
<a id="61"></a>
### 61. Event delegation
[Link](https://www.javascripttutorial.net/javascript-dom/javascript-event-delegation/)

JavaScript event delegation that adds a single event handler to the parent element instead of having to register multiple event handlers to the child elements.

* Having a large number of event handlers will take up memory and degrade the performance of a page.
* The event delegation technique utilizes the event bubbling to handle event at a higher level in the DOM than the element on which the event originated.

Suppose that you have the following menu:
```javascript
<ul id="menu">
    <li><a id="home">home</a></li>
    <li><a id="dashboard">Dashboard</a></li>
    <li><a id="report">report</a></li>
</ul>
```
To handle the click event of each menu item, you may add the corresponding click event handlers:
```javascript
let home = document.querySelector('#home');
home.addEventListener('home',(event) => {
    console.log('Home menu item was clicked');
});

let dashboard = document.querySelector('#dashboard');
dashboard.addEventListener('dashboard',(event) => {
    console.log('Dashboard menu item was clicked');
});

let report = document.querySelector('#report');
report.addEventListener('report',(event) => {
    console.log('Report menu item was clicked');
});
```
In JavaScript, if you have a large number of event handlers on a page, these event handlers will directly impact the performance because of the following reasons:
* First, each event handler is a function which is also an object that takes up memory. The more objects in the memory, the slower the performance.
* Second, it takes time to assign all the event handlers, which causes a delay in the interactivity of the page.

To solve this issue, you can leverage the event bubbling.

Instead of having multiple event handlers, you can assign a single event handler to handle all the click events:
```javascript
let menu = document.querySelector('#menu');

menu.addEventListener('click', (event) => {
    let target = event.target;

    switch(target.id) {
        case 'home':
            console.log('Home menu item was clicked');
            break;
        case 'dashboard':
            console.log('Dashboard menu item was clicked');
            break;
        case 'report':
            console.log('Report menu item was clicked');
            break;
    }
});
```
<a id="62"></a>
### 62. useMemo()
[Link](https://www.digitalocean.com/community/tutorials/react-usememo)

There are two problems that useMemo seeks to address:
* referential equality
* computationally expensive operations

In the lifecycle of a component, React re-renders the component when an update is made. When React checks for any changes in a component, it may detect an unintended or unexpected change due to how JavaScript handles equality and shallow comparisons. This change in the React application will cause it to re-render unnecessarily.

Additionally, if that re-rendering is an expensive operation, like a long `for loop`, it can hurt performance. Expensive operations can be costly in either time, memory, or processing. In addition to potential technical issues, this may lead to poor user experience.

If one part re-renders, it re-renders the entire component tree.

Thus, React released the `memo` idea to fix this.

**Understanding Memoization**

*Memoization* is an optimization technique which passes a complex function to be memoized. In memoization, the result is “remembered” when the same parameters are passed-in subsequently.

If we have a function compute `1 + 1`, it will return `2`. But if it uses memoization, the next time we run `1`’s through the function, it won’t add them up; it will just remember the answer is `2` without executing the adding function.

From the official React documentation, useMemo’s signature looks like this:

Creating a useMemo Example

Here is an abstract example of using `useMemo` for an array of items that uses two computationally expensive functions:
```javascript
const List = React.useMemo(() =>
  listOfItems.map(item => ({
    ...item,
    itemProp1: expensiveFunction(props.first),
    itemProp2: anotherPriceyFunction(props.second)
  })), [listOfItems]
)
```
In the above example, the useMemo function would run on the first render. It would block the thread until the expensive functions complete, as useMemo runs in the first render.

Initially, this will not look as clean as useEffect, since useEffect can render a loading spinner until the expensive functions finish and the effects fire off.

However, in subsequent renders, the expensive functions would not need to run again as long as listOfItems never changed. useMemo would “remember” the return value of each function.
<a id="63"></a>
### 63. Shallow compare
Shallow compare does check for equality. When comparing scalar values (numbers, strings) it compares their values. When comparing objects, it does not compare their attributes - only their references are compared (e.g. "do they point to same object?").

Let's consider the following shape of user object
```javascript
user = {
  name: "John",
  surname: "Doe"
}
```
**Example 1:**
```javascript
const user = this.state.user;
user.name = "Jane";

console.log(user === this.state.user); // true
```
Notice you changed users name. Even with this change, the objects are equal. The references are exactly the same.

**Example 2:**
```javascript
const user = clone(this.state.user);
console.log(user === this.state.user); // false
```

```javascript
const memoizedValue = React.useMemo(() => computeExpensiveValue(a, b), [a, b]);
```
<a id="64"></a>
### 64. React Lazy and Suspense

**React.lazy()**
It is a new function in react that lets you load react components lazily through code splitting without help from any additional libraries. Lazy loading is the technique of rendering only-needed or critical user interface items first, then quietly unrolling the non-critical items later. It is now fully integrated into core react library itself. We formerly used react-loadable to achieve this but now we have react.lazy() in react core.

**Suspense**
Suspense is a component required by the lazy function basically used to wrap lazy components. Multiple lazy components can be wrapped with the suspense component. It takes a fallback property that accepts the react elements you want to render as the lazy component is being loaded.

**Why is Lazy Loading (& Suspense) Important**
Firstly, bundling involves aligning our code components in progression and putting them in one javascript chunk that it passes to the browser; but as our application grows, we notice that bundle gets very cumbersome in size. This can quickly make using your application very hard and especially slow.*With Code splitting, the bundle can be split to smaller chunks where the most important chunk can be loaded first and then every other secondary one lazily loaded.*
```javascript
import React, { lazy, Suspense } from ‘react’;
import ReactDOM from ‘react-dom’;
import ‘./index.css’;
// import Artists from ‘./Artists’;
const Artists = lazy(() => import(‘./Artists’))
class App extends React.Component { 
 render(){  
  return(   
   <div className=”App”>    
    <Suspense fallback={<h1>Still Loading…</h1>}>     
     <Artists />    
    </Suspense>   
   </div>  
  ); 
 }
}
ReactDOM.render(<App />, document.getElementById(‘root’));
```

<a id="65"></a>
### 65. JSX (JavaScript eXtension)
[Link](https://www.geeksforgeeks.org/reactjs-introduction-jsx/)
```javascript
const ele = <h1>This is sample JSX</h1>;
```
The above code snippet somewhat looks like HTML and it also uses a JavaScript-like variable but is neither HTML nor JavaScript, it is JSX. JSX is basically a syntax extension of regular JavaScript and is used to create React elements. These elements are then rendered to the React DOM. We will learn about rendering and DOM in the next article in detail.
#### Why JSX?
* It is faster than normal JavaScript as it performs optimizations while translating to regular JavaScript.
* It makes it easier for us to create templates.
* Instead of separating the markup and logic in separated files, React uses components for this purpose. We will learn about components in detail in further articles.

**Using JavaScript expressions in JSX:** In React we are allowed to use normal JavaScript expressions with JSX. To embed any JavaScript expression in a piece of code written in JSX we will have to wrap that expression in curly braces {}.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';
 
const name = "Learner";
 
const element = <h1>Hello,
{ name }.Welcome to GeeksforGeeks.< /h1>;
  
ReactDOM.render(
    element,
    document.getElementById("root")
);
```
<a id="66"></a>
### 66. .env
Your project can consume variables declared in your environment as if they were declared locally in your JS files. By default you will have `NODE_ENV` defined for you, and any other environment variables starting with `REACT_APP_`.

**The environment variables are embedded during the build time.** Since Create React App produces a static HTML/CSS/JS bundle, it can’t possibly read them at runtime.

#### Referencing Environment Variables in the HTML

You can also access the environment variables starting with `REACT_APP_` in the `public/index.html`. For example:
```html
<title>%REACT_APP_WEBSITE_NAME%</title>
```
Note that the caveats from the above section apply:
* Apart from a few built-in variables (`NODE_ENV` and `PUBLIC_URL`), variable names must start with `REACT_APP_` to work.
* The environment variables are injected at build time. If you need to inject them at runtime, follow this approach instead.

#### What other .env files can be used?#
* `.env`: Default.
* `.env.local`: Local overrides. This file is loaded for all environments except test.
* `.env.development`, `.env.test`, `.env.production`: Environment-specific settings.
* `.env.development.local`, `.env.test.local`, `.env.production.local`: Local overrides of environment-specific settings.

**1 create Root/.env**
```javascript
#.env file
REACT_APP_SECRET_NAME=secretvaluehere123
```
*Important notes it MUST start with **REACT_APP_** *

**2 Access ENV variable**
```javascript
#App.js file or the file you need to access ENV
<p>print env secret to HTML</p>
<pre>{process.env.REACT_APP_SECRET_NAME}</pre>

handleFetchData() { // access in API call
  fetch(`https://awesome.api.io?api-key=${process.env.REACT_APP_SECRET_NAME}`)
    .then((res) => res.json())
    .then((data) => console.log(data))
}
```
<a id="67"></a>
### 67. Array.splice() vs Array.slice()

[Link](https://stackoverflow.com/questions/37601282/javascript-array-splice-vs-slice)

* The splice() method returns the removed item(s) in an array and slice() method returns the selected element(s) in an array, as a new array object.
* The splice() method changes the original array and slice() method doesn’t change the original array.
* The splice() method can take n number of arguments:

**Splice with Example**

**Argument 1:** Index, Required. An integer that specifies at what position to add /remove items, Use negative values to specify the position from the end of the array.

**Argument 2:** Optional. The number of items to be removed. If set to 0(zero), no items will be removed. And if not passed, all item(s) from provided index will be removed.

**Argument 3…n:** Optional. The new item(s) to be added to the array.
```javascript
var array=[1,2,3,4,5];
console.log(array.splice(2));
// shows [3, 4, 5], returned removed item(s) as a new array object.
 
console.log(array);
// shows [1, 2], original array altered.
 
var array2=[6,7,8,9,0];
console.log(array2.splice(2,1));
// shows [8]
 
console.log(array2.splice(2,0));
//shows [] , as no item(s) removed.
 
console.log(array2);
// shows [6,7,9,0]
```

**Slice with Example**

**Argument 1:** Required. An integer that specifies where to start the selection (The first element has an index of 0). Use negative numbers to select from the end of an array.

**Argument 2:** Optional. An integer that specifies where to end the selection but does not include. If omitted, all elements from the start position and to the end of the array will be selected. Use negative numbers to select from the end of an array.

```javascript
var array=[1,2,3,4,5]
console.log(array.slice(2));
// shows [3, 4, 5], returned selected element(s).
 
console.log(array.slice(-2));
// shows [4, 5], returned selected element(s).
console.log(array);
// shows [1, 2, 3, 4, 5], original array remains intact.
 
var array2=[6,7,8,9,0];
console.log(array2.slice(2,4));
// shows [8, 9]
 
console.log(array2.slice(-2,4));
// shows [9]
 
console.log(array2.slice(-3,-1));
// shows [8, 9]
 
console.log(array2);
// shows [6, 7, 8, 9, 0]
```
<a id="68"></a>
### 68. filter() vs find()
ES6 ships with several array methods that enable one to perform operations such as:
* Filtering values (`filter`)
* Summing up array elements (`reduce`)
* Performing the same operation on each element (`map`)

Assuming you want to find a single value given a condition, you’d most likely use find. But, if you always use the methods mentioned above (`filter`, `map` and `reduce`), it’s time to re-learn find.

#### Getting the user using filter
```javascript
const users = [
  {
    name: "Alice",
    age: 19,
    id: 1
  },
  {
    name: "Bob",
    age: 24,
    id: 2
  },
]

let user = users.filter((user) => user.id === 2);
console.log(user);
```
However, the code above returns `[{name: "Bob", age: 24, id: 2}]`. So, what we need is being returned inside an array.

To resolve this, we simply do the following:
```javascript
user = users.filter((user) => user.id === 2)[0];
console.log(user);
// which then returns {name: "Bob", age: 24, id: 2}
```
#### Getting the user using find
```javascript
const users = [
  {
    name: "Alice",
    age: 19,
    id: 1
  },
  {
    name: "Bob",
    age: 24,
    id: 2
  },
]

const user = users.find((user) => user.id === 2)
// returns {name: "Bob", age: 24, id: 2}
```
There you have it. You’ve relearned the find array method.

<a id="69"></a>
### 69. JavaScript Object.assign()
The JavaScript `Object.assign()` method copies all enumerable own properties of given objects to a single object and returns it.
```javascript
Object.assign(target, ...sources)
```

#### assign() Parameters
The `assign()` method takes in:
* `target` - The target object — what to apply the sources' properties to, which is returned after it is modified.
* `sources` - The source object(s) — objects containing the properties you want to apply.

**Example 1: Using Object.assign() to clone & merge objects**
```javascript
// cloning objects
const obj = {
  name: "Alan Turing",
  age: 120,
};

let newObject = {};

const copy = Object.assign(newObject, obj);
// modifies the target object
console.log(newObject);
// returns the modified object
console.log(copy);

// merging objects
const o1 = { a: 1, b: 2, c: 3 };
const o2 = { b: 12, c: 13 };
const o3 = { c: 23 };

// Earlier source keys are overwritten by later sources
const o4 = Object.assign({}, o1, o2, o3);

console.log(o4); // { a: 1, b: 12, c: 23 }

//Output
{ name: 'Alan Turing', age: 120 }
{ name: 'Alan Turing', age: 120 }
{ a: 1, b: 12, c: 23 }
```
**Example 2: Using Object.assign()**
```javascript
const obj = Object.create(
  { a: 1 },
  {
    // a is on obj's prototype chain.
    b: {
      value: 2, // b is a non-enumerable property.
    },
    c: {
      value: 3,
      enumerable: true, // c is an own enumerable property.
    },
  }
);

let copy = Object.assign({}, obj);
console.log(copy); // { c: 3 }

copy = Object.assign(copy, "abc", 100, null);
console.log(copy); // { '0': 'a', '1': 'b', '2': 'c', c: 3 }
//Output
{ c: 3 }
{ '0': 'a', '1': 'b', '2': 'c', c: 3 }
```
As you can see, properties on the prototype chain and non-enumerable properties cannot be copied.

Also, primitives will be wrapped to objects, null and undefined are ignored.

Since only strings have enumerable property, they get copied to target object.
<a id="70"></a>
### 70. Destructuring assignment
The two most used data structures in JavaScript are `Object` and `Array`.
* Objects allow us to create a single entity that stores data items by key.
* Arrays allow us to gather data items into an ordered list.

*Destructuring assignment* is a special syntax that allows us to “unpack” arrays or objects into a bunch of variables, as sometimes that’s more convenient.
#### Array destructuring
```javascript
// we have an array with the name and surname
let arr = ["John", "Smith"]

// destructuring assignment
// sets firstName = arr[0]
// and surname = arr[1]
let [firstName, surname] = arr;

alert(firstName); // John
alert(surname);  // Smith
```
**“Destructuring” does not mean “destructive”.**

It’s called “destructuring assignment,” because it “destructurizes” by copying items into variables. But the array itself is not modified.

It’s just a shorter way to write:
```javascript
// let [firstName, surname] = arr;
let firstName = arr[0];
let surname = arr[1];
```
**Ignore elements using commas**

Unwanted elements of the array can also be thrown away via an extra comma:
```javascript
// second element is not needed
let [firstName, , title] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];

alert( title ); // Consul
```
In the code above, the second element of the array is skipped, the third one is assigned to title, and the rest of the array items is also skipped (as there are no variables for them).

**Works with any iterable on the right-side**
```javascript
let [a, b, c] = "abc"; // ["a", "b", "c"]
let [one, two, three] = new Set([1, 2, 3]);
```
That works, because internally a destructuring assignment works by iterating over the right value. It’s kind of syntax sugar for calling `for..of` over the value to the right of `=` and assigning the values.

**Assign to anything at the left-side**

We can use any “assignables” at the left side.
```javascript
let user = {};
[user.name, user.surname] = "John Smith".split(' ');

alert(user.name); // John
alert(user.surname); // Smith
```
**Swap variables trick**

There’s a well-known trick for swapping values of two variables using a destructuring assignment:
```javascript
let guest = "Jane";
let admin = "Pete";

// Let's swap the values: make guest=Pete, admin=Jane
[guest, admin] = [admin, guest];

alert(`${guest} ${admin}`); // Pete Jane (successfully swapped!)
```
#### The rest ‘…’
Usually, if the array is longer than the list at the left, the “extra” items are omitted.

For example, here only two items are taken, and the rest is just ignored:
```javascript
let [name1, name2] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];

alert(name1); // Julius
alert(name2); // Caesar
// Further items aren't assigned anywhere
```
If we’d like also to gather all that follows – we can add one more parameter that gets “the rest” using three dots `"..."`:
```javascript
let [name1, name2, ...rest] = ["Julius", "Caesar", "Consul", "of the Roman Republic"];

// rest is array of items, starting from the 3rd one
alert(rest[0]); // Consul
alert(rest[1]); // of the Roman Republic
alert(rest.length); // 2
```

#### Object destructuring
The destructuring assignment also works with objects.
```javascript
let {var1, var2} = {var1:…, var2:…}
```
We should have an existing object at the right side, that we want to split into variables. The left side contains an object-like “pattern” for corresponding properties. In the simplest case, that’s a list of variable names in `{...}`.

```javascript
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

let {title, width, height} = options;

alert(title);  // Menu
alert(width);  // 100
alert(height); // 200
```
Properties `options.title`, `options.width` and `options.height` are assigned to the corresponding variables.

The order does not matter. This works too:

```javascript
// changed the order in let {...}
let {height, width, title} = { title: "Menu", height: 200, width: 100 }
```

The pattern on the left side may be more complex and specify the mapping between properties and variables.

If we want to assign a property to a variable with another name, for instance, make `options.width` go into the variable named `w`, then we can set the variable name using a colon:
```javascript
let options = {
  title: "Menu",
  width: 100,
  height: 200
};

// { sourceProperty: targetVariable }
let {width: w, height: h, title} = options;

// width -> w
// height -> h
// title -> title

alert(title);  // Menu
alert(w);      // 100
alert(h);      // 200
```

The colon shows “what : goes where”. In the example above the property `width` goes to `w`, property `height` goes to `h`, and `title` is assigned to the same name.

#### The rest pattern “…”

What if the object has more properties than we have variables? Can we take some and then assign the “rest” somewhere?

We can use the rest pattern, just like we did with arrays. It’s not supported by some older browsers (IE, use Babel to polyfill it), but works in modern ones.

It looks like this:
```javascript
let options = {
  title: "Menu",
  height: 200,
  width: 100
};

// title = property named title
// rest = object with the rest of properties
let {title, ...rest} = options;

// now title="Menu", rest={height: 200, width: 100}
alert(rest.height);  // 200
alert(rest.width);   // 100
```
 #### Gotcha if there’s no let
 In the examples above variables were declared right in the assignment: `let {…} = {…}`. Of course, we could use existing variables too, without `let`. But there’s a catch.

This won’t work:
```javascript
let title, width, height;

// error in this line
{title, width, height} = {title: "Menu", width: 200, height: 100};
```
<a id="71"></a>
### 71. Spread Operator
**Spread operator** allows an iterable to expand in places where 0+ arguments are expected. It is mostly used in the variable array where there is more than 1 values are expected. It allows us the privilege to obtain a list of parameters from an array. Syntax of Spread operator is same as Rest parameter but it works completely opposite of it.
```javascript
var variablename1 = [...value]; 
```
Spread operator can be used in many cases,like when we want to `expand`,`copy`,`concat`,with `math` object. Let’s look at each of them one by one:
#### concat()
The `concat()` method provided by javascript helps in concatenation of two or more strings(String `concat()` ) or is used to merge two or more arrays. In case of arrays,this method does not change the existing arrays but instead returns a new array.
```javascript
// normal array concat() method
let arr = [1,2,3];
let arr2 = [4,5];
  
arr = arr.concat(arr2);
  
console.log(arr); // [ 1, 2, 3, 4, 5 ]

//We can achieve the same output with the help of the spread operator, the code will look something like this:

// spread operator doing the concat job
let arr = [1,2,3];
let arr2 = [4,5];
  
arr = [...arr,...arr2];
console.log(arr); // [ 1, 2, 3, 4, 5 ]
```
**Note:** *Though we can achieve the same result, but it is not recommended to use the spread in this particular case, as for a large data set it will work slower as when compared to the native concat() method.*

#### Copy
```javascript
// copying without the spread operator
let arr = ['a','b','c'];
let arr2 = arr;
  
console.log(arr2); // [ 'a', 'b', 'c' ]
```
The above code works fine because we can copy the contents of one array to another, but under the hood, it’s very different as when we mutate new array it will also affect the old array(the one which we copied).

We can make use of the spread operator in this case, like this:
```javascript
// spread operator for copying 
let arr = ['a','b','c'];
let arr2 = [...arr];
  
console.log(arr); // [ 'a', 'b', 'c' ]
  
arr2.push('d'); //inserting an element at the end of arr2
  
console.log(arr2); // [ 'a', 'b', 'c', 'd' ]
console.log(arr); // [ 'a', 'b', 'c' ]
```
#### Expand
Whenever we want to expand an array into another we do something like this:
```javascript
// normally used expand method
let arr = ['a','b'];
let arr2 = [arr,'c','d'];
  
console.log(arr2); // [ [ 'a', 'b' ], 'c', 'd' ]
```
Even though we get the content on one array inside the other one, but actually it is array inside another array which is definitely what we didn’t want. If we want the content to be inside a single array we can make use of the spread operator.
```javascript
// expand using spread operator
  
let arr = ['a','b'];
let arr2 = [...arr,'c','d'];
  
console.log(arr2); // [ 'a', 'b', 'c', 'd' ]
```
#### Math
The Math object in javascript has different properties that we can make use of to do what we want like finding the minimum from a list of numbers, finding maximum etc. Consider the case that we want to find the minimum from a list of numbers,we will write something like this:
```javascript
console.log(Math.min(1,2,3,-1)); //-1 
```
Now consider that we have an array instead of a list, this above Math object method won’t work and will return NaN, like:
```javascript
// min in an array using Math.min()
let arr = [1,2,3,-1];
console.log(Math.min(arr)); //NaN
```
When `…arr` is used in the function call, it “expands” an iterable object arr into the list of arguments
```javascript
// with spread 
let arr = [1,2,3,-1];
  
console.log(Math.min(...arr)); //-1
```
#### Objects
ES6 has added spread property to object literals in javascript. The spread operator (…) with objects is used to create copies of existing objects with new or updated values or to make a copy of an object with more properties. Let’s take at an example of how to use the spread operator on an object,
```javascript
const user1 = {
    name: 'Jen',
    age: 22
};
  
const clonedUser = { ...user1 };
console.log(clonedUser);
```
Here we are spreading the user1 object. All key-value pairs of the user1 object are copied into the clonedUser object. Let’s look on another example of merging two objects using the spread operator,
```javascript
const user1 = {
    name: 'Jen',
    age: 22,
};
  
const user2 = {
    name: "Andrew",
    location: "Philadelphia" 
};
  
const mergedUsers = {...user1, ...user2};
console.log(mergedUsers)
```
<a id="72"></a>
### 72. When to choose React & when to choose Angular
[Link](https://rubygarage.org/blog/how-to-choose-between-angular-and-react)

[Second Link](https://www.cuelogic.com/blog/what-are-the-differences-between-angular-and-react)

Before we address this perplexing choice, let's first identify the main aspects of Angular and React that we must consider:
* Performance;
* Suitability for projects of various sizes;
* Search engine optimization (SEO);
* Development speed.

**Choose ReactJS for your project if:**

* You will greatly extend the functionality of your application in the future;
* You need an application that will update the view(s) simultaneously and constantly;
* You’re okay with a somewhat slower initial stage of development.

**Choose AngularJS for your project if:**

* You need a Minimum Viable Product;
* You need an application that updates only several parts of one view at a time;
* You want to start development of an application immediately.
#### Angular
You should choose the Angular framework when developing a large-scale application that is loaded with various features. It gives you a reliable and scalable framework. The use of the Angular framework makes the development of real-time message apps easy. Also, teams well-versed in TypeScript find Angular easier to work with.

![Angular Benefits](../helper_doc/images/angular_ben.webp)
#### React
Reacts are used to develop single-page and cross-platform applications with ease. They can be used to extend an app’s existing capabilities. React has made it possible for developers to make light applications that have modern capabilities in a short time-frame. React offers a plethora of advantages, one of them is the availability of tools that can be used to personalize your webpage with ease. 

![React Benefits](../helper_doc/images/react_ben.webp)
<a id="73"></a>
### 73. forEach() vs map()
**forEach() :**

return value : undefined

originalArray : not modified after the method call

newArray is not created after the end of method call.

**map() :**

return value : new Array populated with the results of calling a provided function on every element in the calling array

originalArray : not modified after the method call

newArray is created after the end of method call.

One thing to point out is that foreach skips uninitialized values while map does not.
```javascript
var arr = [1, , 3];

arr.forEach(function(element) {
    console.log(element);
});
//Expected output: 1 3

console.log(arr.map(element => element));
//Expected output: [1, undefined, 3];
```
<a id="74"></a>
### 74. Array.reduce()
The `arr.reduce()` method in JavaScript is used to reduce the array to a single value and executes a provided function for each value of the array (from left-to-right) and the return value of the function is stored in an accumulator.
```javascript
array.reduce( function(total, currentValue, currentIndex, arr), initialValue )
```
**Parameter:** This method accepts five parameters as mentioned above and described below:
* function(total, currentValue, index, arr): It is the required parameter and used to run for each element of array. It contains four parameter which are listed below:
 * total: It is required parameter and used to specify the initialValue, or the previously returned value of the function.
 * currentValue: It is required parameter and used to specify the value of the current element.
 * currentIndex: It is optional parameter and used to specify the array index of the current element.
 * arr: It is optional parameter and used to specify the array object the current element belongs to.
* initialValue: It is optional parameter and used to specify the value to be passed to the function as the initial value.

```javascript
var arr = [10, 20, 30, 40, 50, 60];
   
function sumofArray(sum, num) {
    return sum + num;
}
function myGeeks(item) {
    document.getElementById("GFG").innerHTML
	    = arr.reduce(sumofArray);
}
console.log(arr.reduce(sumofArray));
//Output
210
```
<a id="75"></a>
### 75. Redux
Redux is a library for managing state in a predictable way in JavaScript applications.
#### Three Principles
1. The state of your whole application is stored in an object tree within a single store.
2. The only way to change the state is to emit an action, an object describing what happened.
3. To specify how the state tree is transformed by actions, you write pure reducers.

#### Actions
1. The only way your application can interact with store.
2. Carry some info from your app to the redux store.
3. Plain javascript objects and have a 'type' property that indicates the type of action being performed.
4. The 'type' property is typically defined as string constant.

#### Reducers
1. Specify how the app's state changes in response to actions sent to the store.
2. Function that accepts state and action as arguments, and returns the next state of the application.
3. (previousState, action) => newState

#### Store
One store for the entire application
Responsibilities:
* Holds appln state
* Allows access to state via `getState()`
* Allows state to be updated via `dispatch(action)`
* Registers listeners via `subscribe(listeners)`
* Handles unregestering of listeners via the function returned by subscribe(listener)

#### Middleware
* Is the suggested way to extend Redux with custom functionality
* Provides a third-party extension point between dispatching an action, and the moment it reaches the reducer
* Use middleware for logging, crash reporting, performing asynchronous tasks etc
```javascript
npm i redux --save-dev
```
**Store holds** all of the application's state.

Next up create a new file, src/js/store/index.js and initialize the store:
```javascript
// src/js/store/index.js

import { createStore } from "redux";
import rootReducer from "../reducers/index";

const store = createStore(rootReducer);

export default store;
```
As you can see, **store is the result of calling createStore**, a function from the Redux library. **createStore takes a reducer as the first argument and in our case we passed in rootReducer** (not yet present).
#### Reducer

A Redux reducer is just a **JavaScript function**. It takes two parameters: the **current state** and **action**.

In a typical React component the local state might be mutated in place. In Redux you're not allowed to do that. The **third principle of Redux (as outlined by its creator) prescribes that the state is immutable and cannot change in place**.

In other words the reducer must be pure. A pure function returns the exact same output for the given input.

Then create a new file, `src/js/reducers/index.js`:
```javascript
const initialState = {
  articles: []
};

function rootReducer(state = initialState, action) {
  return state;
};

export default rootReducer;
```
Notice how the initial state is passed as a default parameter. But as of now our reducer does nothing, other than returning the initial state.

The second principle of Redux says **the only way to change the state is by sending a signal to the store**. This signal is an action. So **"dispatching an action"** means sending out a signal to the store.
```json
{
  type: 'ADD_ARTICLE',
  payload: { title: 'React Redux Tutorial', id: 1 }
}
```
The type property drives how the state should change and it's always required by Redux. The payload property instead describes what should change, and might be omitted if you don't have new data to save in the store.

As a best practice in Redux we wrap every action within a function, so that object creation is abstracted away. Such function takes the name of action creator: let’s put everything together by creating a simple action creator.

Then create a new file, `src/js/actions/index.js`:
```javascript
// src/js/actions/index.js

export function addArticle(payload) {
  return { type: "ADD_ARTICLE", payload }
};
```

You can notice that the type property is a string. Strings are prone to typos and duplicates and for this reason it's better to declare actions as constants. Create a new folder for them:

Then create a new file, `src/js/constants/action-types.js`:
```javascript
// src/js/constants/action-types.js

export const ADD_ARTICLE = "ADD_ARTICLE";
```

```javascript
// src/js/actions/index.js

import { ADD_ARTICLE } from "../constants/action-types";

export function addArticle(payload) {
  return { type: ADD_ARTICLE, payload };
}
```

<a id="76"></a>
### 76. Immutable Object ?
Immutability in object means we don’t want our objects to change in any ways once we create them i.e make them read-only type.
```javascript
const myTesla = {
	maxSpeed: 250,
	batteryLife: 300,
	weight: 123
}
```
#### Object.preventExtensions()
This method prevents the addition of new properties to our existing object. preventExtensions() is a irreversible operation. We can never add extra properties to the object again.
```javascript
Object.isExtensible(myTesla); // true
Object.preventExtensions(myTesla);
Object.isExtensible(myTesla); // false
myTesla.color = 'blue';
console.log(myTesla.color) // undefined
```
#### Object.seal()
It prevents additions or deletion of properties. seal() also prevents the modification of property descriptors.
```javascript
Object.isSealed(myTesla); // false
Object.seal(myTesla);
Object.isSealed(myTesla); // true

myTesla.color = 'blue';
console.log(myTesla.color); // undefined

delete myTesla.batteryLife; // false
console.log(myTesla.batteryLife); // 300

Object.defineProperty(myTesla, 'batteryLife'); // TypeError: Cannot redefine property: batteryLife
```
#### Object.freeze()
It does the same that Object.seal() plus it makes the properties non-writable.
```javascript
Object.isFrozen(myTesla); // false
Object.freeze(myTesla);
Object.isFrozen(myTesla); // true

myTesla.color = 'blue';
console.log(myTesla.color); // undefined

delete myTesla.batteryLife;
console.log(myTesla.batteryLife); // 300

Object.defineProperty(myTesla, 'batteryLife'); // TypeError: Cannot redefine property: batteryLife

myTesla.batteryLife = 400;
console.log(myTesla.batteryLife); // 300
```
<a id="77"></a>
### 77. Flatten the array ?
To flatten an array means to reduce the dimensionality of an array. In simpler terms, it means reducing a multidimensional array to a specific dimension.
```javascript
let arr = [[1, 2],[3, 4],[5, 6, 7, 8, 9],[10, 11, 12]];
```
1. **concat() and apply()**
In this example,`this` is an empty array as seen in `apply([], arr)` and `arr` is the argument list we are passing (we’ve defined it above). So, we are saying take this argument and merge it with what you already got - another empty array, in this case. MSDN really explains `concat()` and `apply()`.
```javascript
let flattened = [].concat.apply([], arr);
// [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 ]
```
2. **spread operator in ES6**

Here, we are using the spreadoperator to `spread` out the array. Then concatenate it with an empty array.
```javascript
let flattened = [].concat(...arr);
// [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 ]
```
3. **array.reduce()**

The `reduce()` method as the name suggests reduces an array to a single value. I have explained in great detail what it is and how to use it.
```javascript
let flattened = arr.reduce((acc, curVal) => {return acc.concat(curVal)}, []);
// [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 ]
```

**Array.prototype.flat()**

The `flat()` method has an optional argument known as **depth** (the number of levels to flatten the array) and returns a new array - flattened version of the original array, removing any *array hole* (empty slot(s) in the array).

The default depth is 1. Otherwise, the passed argument defines the depth for flattening the array. You can also set the depth as **Infinity** (unlimited levels).

Let’s see some working examples.
```javascript
let arr = [[1, 2],[3, 4, [5, 6 [7, 8], 9]],[10 [11, 12]]];

// since the default depth is 1
// only the first level arrays in the parent array are flattend
// you would notice that there's a second level nesting after the number '4', in the parent array

arr.flat();
// [ 1, 2, 3, 4, [ 5, 6, [ 7, 8 ], 9 ], 10, [ 11, 12 ] ]

// we supplied 2 as the depth

arr.flat(2);
// [ 1, 2, 3, 4, 5, 6, [ 7, 8 ], 9, 10, 11, 12 ]

// using 'Infinty' as an argument means flatten all levels to a one-dimensional array

arr.flat(Infinity);
// [ 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12 ]

// the flat() method also remove empty slots in the array, otherwise called 'array holes'
let arrrayHole = [1, 2, , 4, 5];
arrayHole.flat();
// [1, 2, 4, 5]
```
<a id="78"></a>
### 78. Function overloading ?
JavaScript does not support Function Overloading. The following shows function overloading −
```javascript
function funcONE(x,y) {
   return x*y;
}
function funcONE(z) {
   return z;
}
```
The above will not show an error, but you won't get desired results. On calling,
```javascript
// prints 5
funcONE(5);

// prints 5, not 30
funcONE(5,6);
```
JavaScript does not support function overloading natively. If we will add functions with the same name and different arguments, it considers the last defined function.
<a id="79"></a>
### 79. Primitive data type ?
[Link](https://developer.mozilla.org/en-US/docs/Glossary/Primitive)

In JavaScript, a primitive (primitive value, primitive data type) is data that is not an `object` and has no `methods`. 
There are 7 primitive data types: 
* string
* number
* bigint
* boolean
* undefined
* symbol
* null

All primitives are **immutable**, i.e., they cannot be altered. It is important not to confuse a primitive itself with a variable assigned a primitive value. The variable may be reassigned a new value, but the existing value can not be changed in the ways that objects, arrays, and functions can be altered.
```javascript
// Using a string method doesn't mutate the string
var bar = "baz";
console.log(bar);               // baz
bar.toUpperCase();
console.log(bar);               // baz

// Using an array method mutates the array
var foo = [];
console.log(foo);               // []
foo.push("plugh");
console.log(foo);               // ["plugh"]

// Assignment gives the primitive a new (not a mutated) value
bar = bar.toUpperCase();       // BAZ
```
**Note:** *A primitive can be replaced, but it can't be directly altered.*
<a id="80"></a>
### 80. Why Do We Write super(props)?
[Link](https://overreacted.io/why-do-we-write-super-props/)

In JavaScript, `super` refers to the parent class constructor.

Importantly, you can’t use `this` in a constructor until after you’ve called the parent constructor. JavaScript won’t let you:
```javascript
class Checkbox extends React.Component {
  constructor(props) {
    // 🔴 Can’t use `this` yet
    super(props);
    // ✅ Now it’s okay though
    this.state = { isOn: true };
  }
  // ...
}
```
There’s a good reason for why JavaScript enforces that parent constructor runs before you touch `this`. Consider a class hierarchy:
```javascript
class Person {
  constructor(name) {
    this.name = name;
  }
}

class PolitePerson extends Person {
  constructor(name) {
    this.greetColleagues(); // 🔴 This is disallowed, read below why
    super(name);
  }
  greetColleagues() {
    alert('Good morning folks!');
  }
}
```
Imagine using `this` before `super` call was allowed. A month later, we might change `greetColleagues` to include the person’s name in the message:
```javascript
greetColleagues() {
    alert('Good morning folks!');
    alert('My name is ' + this.name + ', nice to meet you!');
}
```
But we forgot that `this.greetColleagues()` is called before the `super()` call had a chance to set up `this.name`. So `this.name` isn’t even defined yet! As you can see, code like this can be very difficult to think about.

To avoid such pitfalls, **JavaScript enforces that if you want to use `this` in a constructor, you have to call `super` first**.
<a id="81"></a>
### 81. Babel-Polyfill?
#### Babel
Babel’s a tool that converts modern JavaScript’s syntax (ECMAScript 2015 and above) into a compatible version (ES5) for older browsers such as IE.

Babel can basically handle these things:
* Converts syntax to ES5.
* Injects polyfill features automatically into the code.
* Transpiles TypeScript

So when you write ES6+ code:
```javascript
const test = [1,2,3,4,5]
test.map(t => t * 2)
```

Babel transforms syntax like this:
```javascript
"use strict";
var test = [1, 2, 3, 4, 5];
test.map(function (t) {
  return t * 2;
});
```
You can see `const` is converted to `var`, and the arrow function is transformed to the `function` statement.

#### Polyfill
A *polyfill* is a piece of code that allows you to use the functionality of modern Web APIs on older browsers that don’t support this functionality.

Polyfill can be used to provide the code snippet that allows functionality in those browsers.

#### Difference
The only thing Babel can do is to convert ES6+ syntax to ES5, if there aren’t any plugins.

The thing polyfills can do is inject a snippet code to support web APIs.
<a id="82"></a>
### 82. Psuedo class & Psuedo element ?
A CSS **pseudo-class** is a keyword added to a selector that specifies a special state of the selected element(s). For example, `:hover` can be used to apply a style when the user hovers over a button.
```css
div:hover {
  background-color: #F89B4D;
}
```
A CSS **pseudo-element** is a keyword added to a selector that lets you style a specific part of the selected element(s). For example, `::first-line` can be used to style the first line of a paragraph.
```css
/* The first line of every <p> element. */
p::first-line {
 color: blue;
 text-transform: uppercase;
}

/* The following rule selects any content placed after a "q" element and inserts the smart close quote. */

q:after{
 content : close-quote;
}
```
<!-- Promise, Clousure, Recursion, CSS position property, call bind apply, Semantic Elements, async await, JSON web token  -->
<!-- redux, hoc, purecomponents, callback, GraphQL, performance, call stack, callback hell, callback -->
<a id="83"></a>
### 83. Datalist ?
The HTML `<datalist>` element contains a set of `<option>` elements that represent the permissible or recommended options available to choose from within other controls.

The `<datalist>` tag is used to provide autocomplete feature in the HTML files. It can be used with an input tag so that users can easily fill the data in the forms using select the data.
```html
<input list="ice-cream-flavors" id="ice-cream-choice" name="ice-cream-choice" />

<datalist id="ice-cream-flavors">
    <option value="Chocolate">
    <option value="Coconut">
    <option value="Mint">
    <option value="Strawberry">
    <option value="Vanilla">
</datalist>
```
<a id="84"></a>
### 84. for..in and for..of ?
1. `for..in` iterates over all enumerable **property keys of an object**
2. `for..of` iterates over the **values of an iterable object**. Examples of iterable objects are arrays, strings, and NodeLists.
```javascript
let list = [4, 5, 6];

for (let i in list) {
   console.log(i); // "0", "1", "2",
}

for (let i of list) {
   console.log(i); // "4", "5", "6"
}
```
<a id="85"></a>
### 85. withRouter ?
`withRouter` is a **higher order component** that will pass closest route's `match`, current `location`, and `history` props to the wrapped component whenever it renders. simply it connects component to the router.

When you include a main page component in your app, it is often wrapped in a `<Route>` component like this:
```javascript
<Route path="/movies" component={MoviesIndex} />
```
By doing this, the `MoviesIndex` component has access to `this.props.history` so it can redirect the user with `this.props.history.push`.

Some components (commonly a header component) appear on every page, so are not wrapped in a `<Route>`:
```javascript
render() {
  return (<Header />);
}
```
This means the header cannot redirect the user.

To get around this problem, the header component can be wrapped in a `withRouter` function, either when it is exported:
```javascript
export default withRouter(Header)
```
This gives the Header component access to `this.props.history`, which means the header can now redirect the user.
<a id="86"></a>
### 86. map & flatMap ?
The `flatMap()` method first maps each element using a mapping function, then flattens the result into a new array. It is identical to a map() followed by a flat() of depth 1

`Stream.flatMap`, as it can be guessed by its name, is the combination of a `map` and a `flat` operation. That means that you first apply a function to your elements, and then flatten it. `Stream.map` only applies a function to the stream without flattening the stream.

To understand what *flattening* a stream consists in, consider a structure like `[ [1,2,3],[4,5,6],[7,8,9] ]` which has "two levels". Flattening this means transforming it in a "one level" structure : `[ 1,2,3,4,5,6,7,8,9 ]`.
<a id="87"></a>
### 87. HTTP 2 vs HTTP 1.1?
[Link](https://factoryhr.medium.com/http-2-the-difference-between-http-1-1-benefits-and-how-to-use-it-38094fa0e95b)
#### HTTP
Hypertext Transfer Protocol (HTTP) is an application protocol that is, currently, **the foundation** of data communication for the World Wide Web.

HTTP is based on the Client/Server model. Client/Server model can be explained as two computers, Client (receiver of service) and Server (provider of service) that are communicating via requests and responses.
#### HTTP/2
HTTP/2 was released in 2015 as a major revision to the HTTP/1.1 protocol. It was derived from the SPDY protocol as a way to improve the online experience by speeding up page loads and reducing round-trip time (RTT), especially on resource-heavy web pages.
#### Main goals of developing HTTP/2 was:
1. Protocol negotiation mechanism — protocol electing, eg. HTTP/1.1, HTTP/2 or other.
2. High-level compatibility with HTTP/1.1 — methods, status codes, URIs and header fields.
3. Page load speed improvements trough:
4. Compression of request headers
5. Binary protocol
6. HTTP/2 Server Push
7. Request multiplexing over a single TCP connection
8. Request pipelining
9. HOL blocking (Head-of-line) — Package blocking
#### Request multiplexing
HTTP/2 can send **multiple requests** for data in parallel over a **single** TCP connection. This is the most advanced feature of the HTTP/2 protocol because **it allows you to download web files asynchronously from one server**. Most modern browsers limit TCP connections to one server.
![TCP_Connection](../helper_doc/images/tcp_coonection.png)
This reduces additional round trip time (RTT), **making your website load faster** without any optimization, and makes *domain sharding* unnecessary.
#### Header compression
HTTP/2 compress a large number of redundant header frames. It uses the HPACK specification as a simple and secure approach to header compression. Both client and server maintain a list of headers used in previous client-server requests.
#### Binary protocol
The latest HTTP version has evolved significantly in terms of capabilities and attributes such as transforming from a text protocol to a binary protocol. HTTP1.x used to process text commands to complete request-response cycles. HTTP/2 will use binary commands (in 1s and 0s) to execute the same tasks. This attribute eases complications with framing and simplifies implementation of commands that were confusingly intermixed due to commands containing text and optional spaces.
##### Benefits:
1. Low overhead in parsing data — a critical value proposition in HTTP/2 vs HTTP1.
2. Less prone to errors.
3. Lighter network footprint.
4. Effective network resource utilization.
5. Eliminating security concerns associated with the textual nature of HTTP1.x such as response splitting attacks.
6. Enables other capabilities of the HTTP/2 including compression, multiplexing, prioritization, flow control and effective handling of TLS.
7. Compact representation of commands for easier processing and implementation.
8. Efficient and robust in terms of processing of data between client and server.
9. Reduced network latency and improved throughput.
#### HTTP/2 Server Push
This capability allows the server to send additional cacheable information to the client that isn’t requested but is anticipated in future requests. For example, if the client requests for the resource X and it is understood that the resource Y is referenced with the requested file, the server can choose to push Y along with X instead of waiting for an appropriate client request.

##### Benefits:
1. The client saves pushed resources in the cache.
2. The client can reuse these cached resources across different pages.
3. The server can multiplex pushed resources along with originally requested information within the same TCP connection.
4. The server can prioritize pushed resources — a key performance differentiator in HTTP/2 vs HTTP1. 
5. The client can decline pushed resources to maintain an effective repository of cached resources or disable Server Push entirely.
6. The client can also limit the number of pushed streams multiplexed concurrently.
<a id="88"></a>
### 88. React.cloneElement() and createElement()?
The `React.cloneElement()` function returns a copy of a specified element. Additional props and children can be passed on in the function. You would use this function when a parent component wants to add or modify the prop(s) of its children.
```javascript
React.cloneElement(element, [props], [...children]);
```
**`element`**: The element to be cloned.
**`props`**: The props to be added to the cloned element in addition to that of the original element.
**`children`**: The children of the cloned object. Note that the children of exisitng object are not copied.
```javascript
import React from 'react';
export default class App extends React.Component {
  // rendering the parent and child component
  render() {
    return (
      <ParentComp>
        <MyButton/>
        <br></br>
        <MyButton/>
      </ParentComp>
    )
  }
}
// The parent component
class ParentComp extends React.Component {
  render() {
    // The new prop to the added.
    let newProp = 'red';
      // Looping over the parent's entire children,
      // cloning each child, adding a new prop.
    return (
      <div>
        {React.Children.map(this.props.children,
          child => {
            return React.cloneElement(child,
            {newProp}, null);
        })}
      </div>
    )
  }
}
// The child component
class MyButton extends React.Component {
  render() {
    return <button style =
    {{ color: this.props.newProp }}>
    Hello World!</button>
  }
}
```
JSX elements will be transpiled to `React.createElement()` functions to create React elements which are going to be used for the object representation of UI. Whereas `cloneElement` is used to clone an element and pass it new props.

Using `cloneElement` will be usually be faster because you only need to instantiate one initial component.

This ['jsperf'](https://jsperf.com/react-createelement-vs-react-cloneelement) test shows `cloneElement` to be nearly twice as fast as `createElement` for Chromium 45 on Linux:

* cloneElement ~1.7m ops/second
* createElement ~0.85m ops/second

If you have a base component that you can clone without changing, then using `cloneElement` is a clear choice, both semantically and in terms of performance.
<a id="89"></a>
### 89. Executuion context
Executuion context is a container where whole JS code is executed.

Executuion context has 2 components,
* Memory(Variable Environment) : This is where all vairables stores as key value pair and also the functions.
* Code (Thread of execution) : This is where code get executed one line at a time.

*Javascript is a synchronous single-threaded language.*

```javascript
var n=2;
function square(num){
  var ans = num*num;
  return ans;
}
var square2 = square(n);
var square4 = square(4);
```
#### Memory phase
JS run through code and allocates memory
* JS allocates memory to `var n` and stores `undefined` as value or placeholder in that variable.
* In case of functions it stores whole code in the memory. That is`square` function completely copied into memory.
* JS allocates memory to `var square2` and stores `undefined` as value or placeholder in that variable.
* JS allocates memory to `var square4` and stores `undefined` as value or placeholder in that variable.
#### Code execution phase
JS code again run line by line and executes the code.
* value 2 is assigned to `var n` in the memory.
* `square(n)` function is invoked and whenever function invoked a new execution context will be created
  1. It'll allocate memory to `num` parameter with `undefined` and same to the `ans` variable.
  2. In next phase 2 is allocated to `num`.
  3. The code will execute that is `num*num` and `undefined` of `ans` is replaced with 4.
  4. `return ans` tells that return the `ans` where this function was invoked.(Return the control back to invoked function)
  5. After `return ans` the newly created execution context will be deleted.
![Execution context](../helper_doc/images/1_execution_context.png)
![Execution context](../helper_doc/images/2_execution_context.png)
<a id="90"></a>
### 90. Lexical Environment ?
Wherever **execution context** created a **lexical environment** will also created.

Lexical environment is the local memory along with lexical environment of its parent.

***Lexical mean** : In Hierarchy or in sequence*
```javascript
function a(){
 var b = 10;
 c();
 function c(){
 
 }
}

a();
console.log(b);
```
Function `c()` is lexicaly sitting inside function `a()`
<a id="91"></a>
### 91. Why hooks ?
Hooks brings into a functional component all the powers previously accessible in class components and made them available in functional components. With React Hooks, you can now use state and other features of React outside of the construct of a class

#### Why
* They let you add state to your functional components.
* You share state and logic between components easier.
* You write less code.
* Your code becomes more declarative.
* Removes a lot of complexity.

#### 1. You don’t have to refactor a functional component into a class component when it grows
Usually, there are times when a React component starts with a functional component, which only depends on the props and later evolves into a Class component with having a state. Changing from a functional component to a class component requires a little bit of refactoring, depending on how complex the component is.

With React Hooks, since functional components have the capability of tapping into the state, the refactoring effort will be minimal.
#### 2. You don’t have to worry about “this” anymore
The above sentence is from React documentation. One of the reasons for this confusion is this keyword. If you are familiar with JavaScript, you know that this in JavaScript doesn’t work exactly like in other languages. When it comes to React Hooks, you don’t have to worry about this at all. This is good for beginners as well as experienced developers.
#### 3. No more method bindings
Instead of binding all the methods, there are some syntax proposals where you can get around this. For example, we can rewrite the function to an arrow function.
#### 4. Easier to decouple logic from UI, making both more reusable
Using hooks, logic and UI are easier to separate. No need for HOC or render props. Hooks do it elegantly with less boilerplate and more intuitive compositions of UI and logic.
<a id="92"></a>
### 92. React diffing algorithm ?
#### Virtual DOM
In simple words, virtual DOM is just a copy of the original DOM kept in the memory and synced with the real DOM by libraries such as ReactDOM. This process is called **Reconciliation**.

Virtual DOM has the same properties that of the Real DOM, but it lacks the power to directly change the content of the screen.

Think of Virtual DOM as the blueprint of a machine, changes made to the blueprint doesn't reflects on the machine itself.

#### How Virtual DOM works
So when there is a update in the virtual DOM, react compares the virtual DOM with a snapshot of the virtual DOM taken right before the update of the virtual DOM.

With the help of this comparison React figures out which components in the UI needs to be updated. This process is called **diffing**. The algorithm that is used for the diffing process is called as the **diffing algorithm**.

Once React knows which components has been updated, then it replaces the original DOM nodes with the updated DOM node.
<a id="93"></a>
### 93. Prop drilling ?
Prop drilling is the process in a React app where props are passed from one part of a tree to another by going through other parts that do not need the data, but only help in passing it through the tree.
<a id="94"></a>
### 94. React Portal ?
[Link](https://blog.bitsrc.io/understanding-react-portals-ab79827732c7)

React Portal is a first-class way to render child components into a DOM node outside of the parent DOM hierarchy defined by the component tree hierarchy. The Portal's most common use cases are when the child components need to visually break out of the parent container as shown below.
* Modal dialog boxes
* Tooltips
* Hovercards
* Loaders
A Portal can be created using `ReactDOM.createPortal(child, container)`. Here the **child** is a React element, fragment, or a string, and the **container** is the DOM location(node) to which the portal should be injected.

Even though a Portal is rendered outside of the parent DOM element, it behaves similarly to a regular React component within the application. It can access props and the context API. This is because the Portal resides inside the React Tree hierarchy.
#### Why do we need it?
When we use a modal inside a particular element (a parent component), the modal's height and width will be inherited from the component in which the modal resides. So there is a possibility that the modal will be cropped and not be shown properly in the application. A traditional modal will require CSS properties like `overflow:hidden` and `z-index` to avoid this issue.

Let's see how React Portal can be used here. The following code will resolve this issue using the **createPortal()** to create a DOM node outside of the **root** tree hierarchy.
```javascript
const Modal =({ message, isOpen, onClose, children })=> {
  if (!isOpen) return null;
  return ReactDOM.createPortal(
     <div className="modal">
      <span>{message}</span>
      <button onClick={onClose}>Close</button>
     </div>
    ,document.body);
  }function Component() {
  const [open, setOpen] = useState(false)
  return (
    <div className="component">
      <button onClick={() => setOpen(true)}>Open Modal</button>
      <Modal 
       message="Hello World!" 
       isOpen={open} 
       onClose={() => setOpen(false)}
      />
    </div>
  )
}
```
#### Things to consider when using Portals
* **Event Bubbling will work as usual** — Event bubbling will work as expected by propagating events to the React tree ancestors, regardless of the Portal node location in the DOM.
* **React has control over Portal nodes and its lifecycle** — When rendering child elements through Portals, React still has control over their lifecycle.
* **Portals only affect the DOM structure** — Portals only affect the HTML DOM structure and not impact the React components tree.
* **Predefine HTML mount point** — When using Portals, you need to define an HTML DOM element as the Portal component’s mount point.




```javascript```
<a id="1000"></a>
![alt img name](../helper_doc/images/no.png)

#### Javascript
https://www.javascripttutorial.net/
	
https://www.programiz.com/javascript

#### Closure
https://www.codingame.com/playgrounds/6516/closures-in-javascript-for-beginners

#### ssr
https://www.youtube.com/watch?v=LfA2XDmgVboß
https://www.youtube.com/watch?v=kImeJY8B7ao

#### Class & functional components
https://www.twilio.com/blog/react-choose-functional-components

#### React advanced concepts
https://reactresources.com/topics/advanced-concepts

#### Event loop
https://www.javascripttutorial.net/javascript-event-loop/
	
<!-- JS interview -->
https://www.youtube.com/channel/UC3N9i_KvKZYP4F84FPIzgPQ

https://medium.com/javascript-in-plain-english/the-only-introduction-to-redux-and-react-redux-youll-ever-need-8ce5da9e53c6

https://www.taniarascia.com/this-bind-call-apply-javascript/#:~:text=Call%20and%20Apply,the%20arguments%20as%20an%20array.

https://www.sitepoint.com/demystifying-javascript-variable-scope-hoisting/#:~:text=JavaScript%20has%20two%20scopes%20%E2%80%93%20global,function%20and%20any%20nested%20functions.

https://www.geeksforgeeks.org/creating-objects-in-javascript-3-different-ways/

https://www.valentinog.com/blog/redux/#read-this-redux-where-are-we-now
