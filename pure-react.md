## Pure React

By Dave Ceddia

### Chatper 1: Introduction

- Building small, throwaway apps is a much more effective way to learn a new tool or framework. This ensure that we're working on learning the tool itself and not getting bogged down by other factors like architecture, design, scalability etc.
- Designers have a term called "copywork" which essentially means copying to learn new design concepts/tools/things. This frees them from having to make product decisions like "where should the user go after login.".

### Chatper 2: Hello World

- While learing a new language, framework or tool, typing things out by hand helps drill concepts in your brain. It is a much better way to learn than copy-pasting from a tutorial. This is because when you do things by hand, you build new neuron pathways.
- The HTML-looking JSX `<HelloWorld/>` is actually plain-old JavaScript.
- React uses the concept of a _virtual_ DOM where it creates a representation of your component hierarchies and then renders those components as real DOM elements - inserting them where you instruct it to.

### Chatper 3: JSX: What and Why

- JSX is compiled to JavaScript. Each JSX element is _transpiled_ to a function call, where it's arguments are its attributes ("props") and its contents ("children"). JSX is just syntactic sugar!
- "The disdain towards HTML in JS has a bit of cargo-cult "tradition" behind it. It's a piece of lore passed down through the generations about the Right Way to build web apps. 'It's always been done this way.'"
- There is fear that if you mix HTML and JS then the codebase will get tangled and messy. But if you stepback and think about it, its rare that you can ever add new functionality without changing both your template (HTML) and controller (JS).
- Separate files does not mean you have separated concerns.

### Chatper 4: Working with JSX

- JSX, like HTML, allows us to nest elements inside one another.
- Since JSX gets transpiled to vanilla JS, component functions *must* return a single element. Consider the following example:

```javascript

function Hello() {
  return <span>Hello</span>;
}

function World() {
  return <span>World</span>;
}

// Suppose we don't return a single element from a component
function HelloWorld() {
  return (<Hello/> <World/>);
}

// The above gets transpiled to the following
function HelloWorld() {
  return (
    React.createElement(Hello, null)
    React.createElement(World, null)
  );
}

// And clearly, returning two things from a JS function is absurd!

```

- The easy fix to get around the problem of returning a single element from components is wrapping the component's JSX in a `<div>` or `<span>`. However, that can have side effects of influencing our DOM structure. Also, maybe at times we just want to return `<td>`s from an element without having to wrap them in a `<div>` or `<span>` given that they might be a part of a `<table>` we are working on.
- The other fix to the problem of returning a single element from components is using React's _fragments_. `<React.Fragment>` is an element that React provides us which "disappears" after transpilation, leaving only its children in the DOM. This way we can avoid all wrapper components! 
- Real JavaScript can be inserting inside JSX. This can be done using single curly braces.

```javascript

function submitButton() {
  const buttonLabel = "Submit";
  return <button>{ buttonLabel }</button>;
}

```

- An important thing to note while using JS in JSX is that the things inside the curly braces *must* be _expressions_ and not _statements_. Expressions return a single value while statements don't produce values.
- Examples of expressions and statements:

```javascript

// Expressions
1 + 2
buttonLabel
aFunctionCall()
aFunctionName

// Statements
var a = 5
if (true) { 17; }
while (i < 7) { i++; }

```

- Using "If" in JSX can be achieved using the ternary operator.
- Component names in React *must* start with capital letters. This is because lowercased tag names in React are reserved for built-in HTML and SVG elements like `div`, `ul`, `rect` etc.
- JSX requires every element be closed. So: `return <br/>;` not `return <br>;`.
