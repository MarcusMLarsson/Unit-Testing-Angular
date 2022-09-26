<h1>Unit-Testing-Angular</h1>

##### Table of Contents  
- [Intro](#intro)  
  * [Testing overview](#testing) 
  * [Mocking](#mocking) 
  * [Unit test](#unit)
  * [Tools](#tools) 
  * [Our first test](#first)
  * [Writing good tests](#good)
- [Isolated Unit Tests](#isolated)  
  * [Testing a pipe](#pipe) 
  * [Testing a service](#service) 
- [Shallow Integration Tests](#shallow)  
  * [The TestBed](#testbed)  
  * [Testing a component](#component) 
  * [Using NO_ERROR_SCHEMA](#schema) 
  * [Testing rendered HTML](#html) 
  * [Native- vs Debug-element](#element)
- [asynchronous tests]
- [Deep Integration Tests](#deep)  
  * [Work in progress](#wip) 
- [Testing DOM Interaction & Routing Components](#dom)  
  * [Work in progress](#wip)
- [Advanced Topics](#advanced)  
  * [Work in progress](#wip)
- [Jest](#jest)  
  * [Work in progress](#wip)
  
<a name="intro"/>
<h2> Intro</h2>

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
<p> A very important concept in unit testing is mocking. Mocking allows us to make sure that we are only testing a single unit of code at a time. In most cases, a class does not exist in isolation. Most classes and components have dependencies. For instance, an user component might use something like an user service, which is injected into the component. When we write our unit tests, we don't want to use the real user service. It's easier to write a seperate unit test for the user service. Also, the user service might do HTTP calls, which we don't want to necessary call in the unit test. We do using a Mock (instead of using the real user service). A Mock is an instance of the original class, but a Mock has other data injected into it so you can solely focus on testing . There are several different types of mocks: </p>
  
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

<a name="first"/> 
<h4> Writing our first test </h4>
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

<p> To run the test, you can either run ng test or npm test (as defined in the package.json file). This will execute Karma which will run all the tests that can be found.  </p>
     
<a name="good"/> 
<h4> Writing good unit tests </h4> 
<p> There are a few principals of writing good units tests that are generally agreed upon by the programming commmunity at large. First, it's important to know how to structure a test. Structuring tests follows what called the AAA pattern. In a good test, we actually see the code doing this things in this order. First we setup what we need to setup. Then we make a change and then we check that the change happend in the way that we expected.</p>
                                                                                                                                         
<ul> 
<li> First we <b>Arrange</b> all the necessary preconditions and inputs. </li>
<li> then we <b>Act</b> on the object or class under test </li>
<li> <b>Assert</b> that the expected results have occured. </li>
</ul>
<p> There is also a concept in testing of DAMP vs DRY. </p>
<ul>
<li> <b>DRY</b>: Dont repreat yourself, only if necessary. </li>
<li> <b>DAMP</b>: Repeat yourself if necessary. A good test should tell a story. The story is we start at a given place, we make a change and we check that we arrived were we got. That complete story should me inside the it() function. We should not need to look around a whole lot in order to understand what is going on in the test. A tips is to move less interesting setup into the beforeEach(). If it's some setup that needs to be there but which is not critical for the test that we are creating we can move it here. Critical setup should however be inside the it() function.</li>
</ul>

<a name="isolated"/>
<h2> Isolated unit test</h2>


<a name="pipe"/>
<h4> Testing a pipe</h4>
<p> Below, is the content of the pipe that we will create a test for. This StrenghtPipe has no dependencies, making it easy to create a test for </p>

```js
export class StrengthPipe implements PipeTransform {
 
 transform(value: number): string {
   
   if(value < 10) {
      return value + " (weak)";
    } 
    else if(value >= 10 && value < 20) {
      return value + " (strong)";
    } 
    else {
      return value + " (unbelievable)";
    }
  }
}
                                                                                                                                         
```
                                      
<p> To test this pipe, we create a strength.pipe.spec.ts file. Then run npm test strength.pipe.spec.ts. The test should pass and display 'should display weak if strength is 3'. </p>                                    
                                      
```js
import { StrengthPipe } from "./strength.pipe";
                                              
describe('Strength Pipe', () => {
                                              
   it('should display weak if strength is 3', () => {
       
       //Arrange
       let pipe = new StrengthPipe();
       
       //Act
       let val = pipe.transform(3);
       
       //Assert
       expect(val).toEqual('3 (weak)');
   })
})
                                                                                                                                         
```

<a name="service"/>
<h4> Testing a service</h4>
<p> In this section we will learn how to test a service. The service contains the two methods, <code>add()</code> and <code>clear()</code>. The <code>add()</code> method takes in a string and then pushes that string to the array. The <code>clear()</code> method clears the array.  </p> 

```js                                            
import { Injectable } from '@angular/core';

@Injectable()
export class MessageService {
  messages: string[] = [];

  add(message: string) {
    this.messages.push(message);
  }

  clear() {
    this.messages = [];
  }
}
```
                       
<p> Let's create a message.service.spec.ts file to test the service. </p>
                                              
                                              
```js                                              
import { MessageService } from "./message.service";

describe('Message Service', ()=>{
   let service : MessageService;

// this can be removed into the it method to keep the aaa structure.    
beforeEach(() => {
        service = new MessageService();                                          
})
                                                  
it('should have no messages to start',()=>{      
       //Arrange
       service = new MessageService();
       //Act
       let val = service.messages.length;
       //Assert
       expect(val).toBe(0);
   })
   it('should add a message on calling add',()=>{
       //Arrange
       service = new MessageService();
       service.add('newMessage');
       //Act
       let val = service.messages.length;
       //Assert
       expect(val).toBe(1);
   })
   it('should remove all messages when clear is called',()=>{
       //Arrange
       service = new MessageService();
       service.add('newMessage');
       //Act
       service.clear();
       //Assert
       expect(service.messages.length).toBe(0);
   })
})
```
<p> Let's now test a service with dependencies. </p>

</p>

```js
import { HttpClient } from '@angular/common/http'
import { Injectable } from '@angular/core'
import { Observable } from 'rxjs'

@Injectable({
	providedIn: 'root',
})
export class FakeService {
	constructor(private http: HttpClient) {}

	getDataV1(): Observable<any> {
		const url = 'https://jsonplaceholder.typicode/com/tools/1'

		return this.http.get(url)
	}
}
```

<p> The FakeService, uses the HttpClient in order to make a HTTP get call. We don't want to make a real HTTP call when running the unit test. We need to draw a boundary around the unit that we are testing by using a mock. The mock, in this case the httpCLientSpy, is created using Jest. Keep in mind that Angular comes with Jasmin out of the box, but in order to use Jest you will need to change some configurations. The mockReturnValue needs to be casted to an observable, using of(), since the HttpClient library returns an observable. </p>


```js
import { of } from 'rxjs'
import { FakeService } from './fake.service'

describe('FakeService', () => {
	let service: FakeService
	let httpClientSpy: any

	beforeEach(() => {
		httpClientSpy = {
			get: jest.fn(),
		}

		service = new FakeService(httpClientSpy)
	})

	it('should be created', () => {
		expect(service).toBeTruthy()
	})

	it('should test getDataV1()', () => {
		const res = 'Metry'
		const url = 'https://jsonplaceholder.typicode/com/tools/1'

		jest.spyOn(httpClientSpy, 'get').mockReturnValue(of(res))
		service.getDataV1()
		expect(httpClientSpy.get).toHaveBeenCalledTimes(1)
		expect(httpClientSpy.get).toHaveBeenCalledWith(url)
	})
})
```

<p> The angular guide demonstrates two different ways of testing a service. </p> 
<ul>
<li> Calling new Service() and providing the dependencies to the constructor directly, demonstrated above. </li>
<li> Using dependency injection by calling <code>TestBed.inject(Service)</code>, demonstrated below. </p>
</ul>

```js
TestBed.configureTestingModule({ providers: [SomeService] });

it('should use SomeService', () => {
  service = TestBed.inject(SomeService);
  expect(service.getValue()).toBe('real value');
});

```

<p> When you call <code>TestBed.configureTestingModule({ providers: [SomeService] })</code> an NgModule is setup that can be used in subsequent tests. To inject a service into this Module, we use the TestBed.inject() method. Basically these two methods are the same if you are mocking all of your dependencies and if you don't need to access the DOM. Instantiating classes without the TestBed is significantly faster because there isn't the overhead of loading the dependency injector for every test.</p>

 
<a name="shallow"/>
<h2> Shallow Integration Test</h2>
<p> It's time now to begin writing our first integration test. It will be a shallow integration test, meaning that we are only going to test a single component, and non of its child components or directives.</p>

<a name="testbed"/>
<h4> The TestBed</h4>
<p> To setup our component for integration tests, we use a special utility called the TestBed. The TestBed is defined inside the beforeEach. The TestBed is what allows us to test both our component and its template running together. What is happening is that we are creating a special module, just for test purposes. The TestBed object has many different method, but the one we need are <code>TestBed.configureTestingModule()</code>. We are creating a module specifically for testing. The <code>configureTestingModule()</code> takes a single parameter that is an object. That object matches exactly the layout of when we create an AppModule. Once the testing module has been created, we can now create our component. We do this by calling <code>TestBed.createComponent()</code>. Calling this functions tells the TestBed to use the testing module and to construct the HeroComponenet. <code>The createComponent()</code> actually returns a component fixture, which is basically a wrapper for the component that is used in testing and it has a few other properties more then what the component by itself has. One of them is the component instance itself. Add fixture.detectChanges() to detect changes. Along side declerations there is this schema section for modules. That schema section, just like the declerations settings is an array. This configuration tells Angular how to process the HTML that is has been handed. Using NO_ERROR_SCHEMA tells Anuglar that it should not validate the schema or the template that we use.</p>

 ```js
 import { ComponentFixture, TestBed, waitForAsync } from '@angular/core/testing'
 
 	beforeEach(waitForAsync(() => {
		TestBed.configureTestingModule({
		declerations: [HeroComponent], // for components
		providers: [AuthService], // for services
		schemas: [NO_ERROR_SCHEMA], // tells Anuglar that it should not validate the schema or the template that we use
		})
		TestBed.createComponent(HeroComponent) // creates a wrapper for the component
	}))

  ```
 <a name="component"/>
<h4> Testing a component</h4>
<p> Let's write a test for this component </p>

```js
import { Component, OnInit } from '@angular/core'
import { FakeService } from '../fake.service'

@Component({
	selector: 'app-data',
	templateUrl: './data.component.html',
	styleUrls: ['./data.component.scss'],
})
export class DataComponent implements OnInit {
	public serviceData: any
	public errorMessage: any

	constructor(private fakeService: FakeService) {}

	ngOnInit(): void {
		this.getServiceData()
	}

	public getServiceData() {
		this.fakeService.getDataV1().subscribe({
			next: (data) => (this.serviceData = data),
			error: (err) => (this.errorMessage = err.statusText),
			complete: () => {
				console.log('Finished')
			},
		})
	}
}
```                                                 

<p> To test the <code>getServiceData()</code> method we cant just provide an empty object, since it uses the fakeService, <code>this.fakeService.getDataV1()</code>. So we need to pass in an object that looks like the fakeService. This is where Jest can be used to create a mock. </p>                                               
                                                  
 
 ```js
import { ComponentFixture, TestBed } from '@angular/core/testing'
import { of } from 'rxjs'
import { FakeService } from '../fake.service'

import { DataComponent } from './data.component'

describe('DataComponent', () => {
	let component: DataComponent
	let fixture: ComponentFixture<DataComponent>
	let fakeServiceMock: any

	beforeEach(async () => {
		fakeServiceMock = {
			getDataV1: jest.fn(),
		}

		await TestBed.configureTestingModule({
			declarations: [DataComponent],
			providers: [
				{
					provide: FakeService,
					useValue: fakeServiceMock,
				},
			],
		}).compileComponents()
	})

	beforeEach(() => {
		fixture = TestBed.createComponent(DataComponent)
		component = fixture.componentInstance
	})

	it('should create', () => {
		expect(component).toBeTruthy()
	})

	it('should getServiceData set serviceData', () => {
		const expResp = {
			name: 'Metry',
		}

		jest.spyOn(fakeServiceMock, 'getDataV1').mockReturnValue(of(expResp))
		fixture.detectChanges()

		expect(component.serviceData.name).toBe(expResp.name)
	})
})
 ```
  
  
  
  
 <a name="html"/>
<h4> Test Rendered HTML</h4>
<p> let's write a test that tests that our template is correct.</p>
