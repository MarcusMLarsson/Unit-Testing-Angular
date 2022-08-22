<h1>Unit-Testing-Angular</h1>

##### Table of Contents  
- [Intro](#intro)  
  * [Testing overview](#testing) 
  * [Mocking](#mocking) 
  * [Unit test](#unit)
  * [Tools](#tools) 
- [Isolated Unit Tests](#isolated)  
  * [Work in progress](#wip) 
- [Shallow Integration Tests](#shallow)  
  * [Work in progress](#wip) 
- [Deep Integration Tests](#deep)  
  * [Work in progress](#wip) 
- [Testing DOM Interaction & Routing Components](#dom)  
  * [Work in progress](#wip)
- [Advanced Topics](#advanced)  
  * [Work in progress](#wip)

<a name="testing"/>
<h4> Testing overview</h4>
<p> There are three types of automated tests. </p>
   
<ul> 
<li> Unit testing: is done against a single "unit" of code. The word unit can mean different things to different people. Generally it can be accepted as the class. </li>
<li> End-to-end testing: done against the full live application. This is generally done by automating the browser, to do things like clicking buttons, type into a form, navigate around etc. The benefit is that you can validate that your application works as a whole. </li>
<li> Integration & functional testing: Defined as more than an unit but less then the whole application.</li>
</ul>

<p> Components in Angular have templates. Hence, Angular has tooling that allows for a special kind of test, which Angular calls an integration test. It uses the template and the component together to make sure that those two pieces are working together. I leave it up to you to to decide if this is truley an integration test or just another kind of unit test that has two different pieces; but the offical name used by the Angular team is an integration test.</p>

<a name="mocking"/>
<h4> Mocking </h4>
<p> A very important concept in unit testing is mocking. Mocking allows us to make sure that we are only testing a single unit of code at a time. In most cases, a class does not exist in isolation. Most classes and components have dependencies. For instance, an user component might use something like an user service, which is injected into the component. When we write our unit tests, we don't want to use the real user service. It's easier to write a seperate unit test for the user service. Also, the user service might do HTTP calls, which we don't want to necessary call in the unit test. We need to draw a boundary around the unit that we are testing. We do using a Mock (instead of using the real user service). A Mock is an instance of the original class, but a Mock has other data injected into it so you avoid testing the injected parts and solely focus on testing . There are several different types of mocks: </p>
  
<ul>
<li> Dummies: an object that fills a place. A dummy is used instead of a real object. For example, if a method requires a paramater that is an object but it does not care what it is. Then a dummy is the perfect mock. </li>
<li> Stubs: object that has a controllable behaviour. If we call a certain method on a stub, we can decide in our test what value that method call will return.</li>
<li> Spies: Keeps track of which of its method where called, and how many times they where called.</li>
<li> True mocks: more complex objects that varify that they were used in a specific way.</li>
</ul>
    
<a name="unit"/> 
<h4> Unit tests </h4>
<p> Angular has several different types of unit tests:</p>
<ul>
<li> Isolated: What we think of when we think of a unit test. We simply exercise a simple unit of code, for instance a class of a component or the class of a service. We construct that class by hand and give it the constructor parameters</li>
<li> Integration test: In an Angular integration test, we create a module, in which we put just the code that we are going to test. This is used so we can test the component with its template. There are two types of integration tests supported in Angular, shallow (only test a single component) and deep (for example both parent and child component).</li>
</ul>
 
<a name="tools"/> 
<h4> Tools </h4>
<p> In Angular, the CLI sets up the testing for you. It uses two different tools:</p>

<ul> 
<li> Karma: which is the test runner that execute our tests in the browser </li>
<li> Jasmin: the tool that are used to create mocks. </li>
</ul>
                                                
<p> There are other unit tests that are available. There is a very popular testing library called Jest, created by facebook, that is popular with other frameworks but can be used in Angular. There are Mocha and Chai which are replacements for Jasmin. There is Sinon which is a specialised mocking library. There is TestDouble which is a competitor to Sinon. There is a payed tool called Wallaby that allows you to see your code coverage of your test in the IDE. Cypress is tradtionally considered to be an end-to-end testing tool. </p>
                                                
<h4> Writing our first unit test </h4>
<p> To create a test file in Angular, we end our unit test name with "spec.ts" (short for specification), for example "first-test.spec.ts". We start with the describe function, which is an Jasmin function that let us group tests together. The describe function has two parameters, the first one is a string and the second one is a callback function that will contain our tests.</p> 

```js
 describe('my first test', () => {
    //short for system under test
    let sut;
    
    //common setup that will run before every test. This will reset the state so that with every tests we have no effect from previous tests.
    beforeEach(() => {
          //inital sut variable to be an empty object
          sut = {}                                                                                                                               
    }
  });

  // the test 
  // it's custom to start the it statement with the word should.
  it('should be true if true', () => {
           
   // arrange
   sut.a = false; 
   // act
   sut.a = true;
                                                                                                                                         
   // assert that a property is true                                                                                                                                   
   except(sut.a).toBe(true);
});
                                                                                                                                         
```

<p> This whole test, as it stands right now, is a silly example since all we do is create a property on an object, initialize it to one value, set it to another value and then assert that it is the second value. But this is the core essence of what a test is. We get our inital state, we chang that state and we assert that the new state is what we expect it to be. </p>
