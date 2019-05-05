## Pure React

By Dave Ceddia

### Chatper 1: Introduction

- Building small, throwaway apps is a much more effective way to learn a new tool or framework. This ensure that we're working on learning the tool itself and not getting bogged down by other factors like architecture, design, scalability etc.
- Designers have a term called "copywork" which essentially means copying to learn new design concepts/tools/things. This frees them from having to make product decisions like "where should the user go after login.".

### Chatper 4: Hello World

- While learing a new language, framework or tool, typing things out by hand helps drill concepts in your brain. It is a much better way to learn than copy-pasting from a tutorial. This is because when you do things by hand, you build new neuron pathways.
- The HTML-looking JSX `<HelloWorld/>` is actually plain-old JavaScript.
- React uses the concept of a _virtual_ DOM where it creates a representation of your component hierarchies and then renders those components as real DOM elements - inserting them where you instruct it to.

### Chatper 5: JSX: What and Why

- JSX is compiled to JavaScript. Each JSX element is _transpiled_ to a function call, where it's arguments are its attributes ("props") and its contents ("children"). JSX is just syntactic sugar!
- "The disdain towards HTML in JS has a bit of cargo-cult "tradition" behind it. It's a piece of lore passed down through the generations about the Right Way to build web apps. 'It's always been done this way.'"
- There is fear that if you mix HTML and JS then the codebase will get tangled and messy. But if you stepback and think about it, its rare that you can ever add new functionality without changing both your template (HTML) and controller (JS).
- Separate files does not mean you have separated concerns.

### Chatper 6: Working with JSX
