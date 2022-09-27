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
  * [Testing rendered HTML](#html) 
  * [Native- vs Debug-element](#element)
  * [Fixture.detectChanges()](#detectChanges)
  * [dispatchEvent()](#dispatchEvent())
- [Asynchronous tests](#asynchronous)
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
 <p> Here is a way, you can create a reusable Testbed that contains the most commom imports, providers etc. Create a file <code>test-utilities.ts</code> </p>
 
```js
 export function getTestConfig(
	component,
	isDeclarable = true,
	isProvidable = false
) {

	const testConfig = {
		imports: [
			CommonModule,
			TranslateModule,
			MatDialogModule,
			MatAutocompleteModule,
			MatTableModule,
			MatIconModule,
			RouterTestingModule,
		] as Array<any>,
		providers: [
			ConfigService,
			{ provide: MatDialogRef, useClass: MatDialogRefMock },
			{ provide: TranslateService, useClass: TranslateServiceMock },
			{ provide: LanguageService, useClass: LanguageServiceMock },
			{ provide: MatDialog, useClass: MatDialogMock },
			{ provide: ActivatedRoute, useClass: ActivatedRouteMock },
		] as Array<any>,
		schemas: [CUSTOM_ELEMENTS_SCHEMA, NO_ERRORS_SCHEMA],
		declarations: [
			NormalNumberPipe,
		] as Array<Type<any> | any[]>,
		entryComponents: [],
	}

	if (isDeclarable) {
		testConfig.declarations.push(component)
	}

	if (isProvidable) {
		testConfig.providers.push(component)
	}

	const newConfig = Object.assign(testConfig, {})

	return newConfig
}
```

<p> Then inside you component, you can call </p>

```js
import { getTestConfig } from '/test-utilities'

const testConfig = getTestConfig(YourComponent)

// if you want to push an additional service that are not defined in testConfig.
TestConfig.providers.push([
			provideMock(NewService, NewServiceMock),
		])
// if you want to push an additional pipe that are not defined in testConfig.
testConfig.declarations.push([NewPipe])

// use testConfig
TestBed.configureTestingModule(testConfig).compileComponents()
```
  
<a name="component"/> 
<h4> Testing a component</h4>
<p>As the component has dependencies, we use the TestBed to both create the component and its dependencies. </p>

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
 
 <p> Let's take another example. Let's write a test for the componenet below. The WelcomeComponent has decision logic that interacts with the service, logic that makes this component worth testing.</p>
 
 ```js
 
import { Component, OnInit } from '@angular/core';
import { UserService } from '../model/user.service';

@Component({
  selector: 'app-welcome',
  template: '<h3 class="welcome"><i>{{welcome}}</i></h3>'
})
export class WelcomeComponent implements OnInit {
  welcome = '';
  constructor(private userService: UserService) { }

  ngOnInit(): void {
    this.welcome = this.userService.isLoggedIn ?
      'Welcome, ' + this.userService.user.name : 'Please log in.';
  }
}
 
 ```
 
 <p> The purpose of the spec is to test the component, not the service. Injecting the real UserService could be a nightmare. The real service might ask the user for login credentials and attempt to reach an authentication server. It is far easier and safer to create and register a test double in place of the real UserService. This particular test suite supplies a minimal stub of the UserService. </p>
 
  ```js
 let userServiceStub: Partial<UserService>;  //make all of the properties of a type optional

beforeEach(() => {
  // stub UserService for test purposes
  userServiceStub = {
    isLoggedIn: true,
    user: { name: 'Test User' },
  };

  TestBed.configureTestingModule({
     declarations: [ WelcomeComponent ],
     providers: [ { provide: UserService, useValue: userServiceStub } ],
  });

  fixture = TestBed.createComponent(WelcomeComponent);
  comp    = fixture.componentInstance;

  // UserService from the root injector
  userService = TestBed.inject(UserService);

  //  get the "welcome" element by CSS selector (e.g., by class name)
  el = fixture.nativeElement.querySelector('.welcome');
  
  it('should welcome the user', () => {
  fixture.detectChanges();
  const content = el.textContent;
  expect(content)
    .withContext('"Welcome ..."')
    .toContain('Welcome');
  expect(content)
    .withContext('expected name')
    .toContain('Test User');
});

it('should welcome "Bubba"', () => {
  userService.user.name = 'Bubba'; // welcome message hasn't been shown yet
  fixture.detectChanges();
  expect(el.textContent).toContain('Bubba');
});
});
 ```
 <p> </p>
 
 <a name="html"/>
<h4> Test Rendered HTML</h4>
<p> A component, unlike all other parts of an Angular application, combines an HTML template and a TypeScript class. The component truly is the template and the class working together. Many components have complex interactions with the DOM elements described in their templates, causing HTML to appear and disappear as the component state changes. To adequately test these components, you have to create the DOM elements associated with the components, you must examine the DOM to confirm that component state displays properly at the appropriate times, and you must simulate user interaction with the screen to determine whether those interactions cause the component to behave as expected.</p>

```js
describe('BannerComponent (with beforeEach)', () => {
  let component: BannerComponent;
  let fixture: ComponentFixture<BannerComponent>;

  beforeEach(() => {
    TestBed.configureTestingModule({declarations: [BannerComponent]});
    fixture = TestBed.createComponent(BannerComponent); // a wrapper for the component that is used in testing
    component = fixture.componentInstance;
  });

  it('should create', () => {
    expect(component).toBeDefined();
  });
  
  it('should contain "banner works!"', () => {
  const bannerElement: HTMLElement = fixture.nativeElement;
  expect(bannerElement.textContent).toContain('banner works!');
});
});
 ```
 
 <p> The createComponent() actually returns a component fixture, which is basically a wrapper for the component that is used in testing. Another way of saying this is  that the ComponentFixture is a test harness for interacting with the created component and its corresponding element. Think of a Test Harness as an 'enabler' that actually does all the work of executing tests using a test library and generating reports. The harness has a few other properties more then what the component by itself has. One of them is the component instance itself. </p>
 
 <a name="element"/>
 <h4>Native- vs Debug-element </h4>
 
<p>To get the component's element use fixture.nativeElement and look for the expected text. The value of ComponentFixture.nativeElement has the any type. Angular can't know at compile time what kind of HTML element the nativeElement is or if it even is an HTML element.  Knowing that it is an HTMLElement of some sort, use the standard HTML querySelector to dive deeper into the element tree. </p>

```js
it('should have <p> with "banner works!"', () => {
  const bannerElement: HTMLElement = fixture.nativeElement;
  const p = bannerElement.querySelector('p')!;
  expect(p.textContent).toEqual('banner works!');
});
```

<p>The properties of the nativeElement depend upon the runtime environment. You could be running these tests on a non-browser platform that doesn't have a DOM or whose DOM-emulation doesn't support the full HTMLElement API. Angular relies on the DebugElement abstraction to work safely across all supported platforms. Instead of creating an HTML element tree, Angular creates a DebugElement tree that wraps the native elements for the runtime platform. The nativeElement property unwraps the DebugElement and returns the platform-specific element object.</p>

```js
const bannerDe = fixture.debugElement;
const bannerEl = bannerDe.nativeElement;
});
```

<p> Although the tests in this guide all run in the browser, some applications might run on a different platform at least some of the time. For example, the component might render first on the server as part of a strategy to make the application launch faster on poorly connected devices. The server-side renderer might not support the full HTML element API. If it doesn't support querySelector, the previous test could fail. The DebugElement offers query methods that work for all supported platforms. </p>

```js
it('should find the <p> with fixture.debugElement.query(By.css)', () => {
  const bannerDe: DebugElement = fixture.debugElement;
  const paragraphDe = bannerDe.query(By.css('p'));
  const p: HTMLElement = paragraphDe.nativeElement;
  expect(p.textContent).toEqual('banner works!');
});
```

<ul>
<li> The By.css() static method selects DebugElement nodes with a standard CSS selector. </li>
<li> The query returns a DebugElement for the paragraph. </li>
<li> You must unwrap that result to get the paragraph element. </li>
</ul>

<a name="detectChanges"/>
<h4> fixture.detectChanges()</h4>

```js
@Component({
  selector: 'app-banner',
  template: '<h1>{{title}}</h1>',
  styles: ['h1 { color: green; font-size: 350%}']
})
export class BannerComponent {
  title = 'Test Tour of Heroes';
}

```

<p> The above component looks very straightforward to test but can create some challanges as createComponent() does not bind data</p>

```js
let component: BannerComponent;
let fixture: ComponentFixture<BannerComponent>;
let h1: HTMLElement;

beforeEach(() => {
  TestBed.configureTestingModule({
    declarations: [ BannerComponent ],
  });
  fixture = TestBed.createComponent(BannerComponent);
  component = fixture.componentInstance; // BannerComponent test instance
  h1 = fixture.nativeElement.querySelector('h1');
});

it('should display original title', () => {
  expect(h1.textContent).toContain(component.title);
});
```

<p> For example, the above test would fail and would display in the console expected '' to contain 'Test Tour of Heroes'. Binding happens when Angular performs change detection. In production, change detection kicks in automatically when Angular creates a component or the user enters a keystroke or an asynchronous activity (for example, AJAX) completes. The TestBed.createComponent does not trigger change detection. You must tell the TestBed to perform data binding by calling fixture.detectChanges(). Only then does the h1 tag have the expected title. </p>

<p> Some testers prefer that the Angular test environment run change detection automatically. That's possible by configuring the TestBed with the ComponentFixtureAutoDetect provider.</p>

```js
import { ComponentFixtureAutoDetect } from '@angular/core/testing';

TestBed.configureTestingModule({
  declarations: [ BannerComponent ],
  providers: [
    { provide: ComponentFixtureAutoDetect, useValue: true }
  ]
});
```

<a name="dispatchEvent()">
<h4> dispatchEvent() </h4>
<p> To simulate user input, find the input element and set its value property. Then you need to call fixture.detectChanges() to trigger Angular's change detection. But there is an essential, intermediate step. Angular doesn't know that you set the input element's value property. It won't read that property until you raise the element's input event by calling dispatchEvent(). Then you call detectChanges(). The following example demonstrates the proper sequence.</p>

```js
it('should convert hero name to Title Case', () => {
  // get the name's input and display elements from the DOM
  const hostElement: HTMLElement = fixture.nativeElement;
  const nameInput: HTMLInputElement = hostElement.querySelector('input')!;
  const nameDisplay: HTMLElement = hostElement.querySelector('span')!;

  // simulate user entering a new name into the input box
  nameInput.value = 'quick BROWN  fOx';

  // Dispatch a DOM event so that Angular learns of input value change.
  nameInput.dispatchEvent(new Event('input'));

  // Tell Angular to update the display binding through the title pipe
  fixture.detectChanges();

  expect(nameDisplay.textContent).toBe('Quick Brown  Fox');
});
```

<a name="asynchronous"/>
<h2> Asynchronous tests</h2>

<p> To access variables in an observable, we have to be in its function scope. Because of that, one could imagen the test below could work. </p>

```js
it('should be', () => {
  anyObservable$()
    .subscribe((r) => {
      expect(r).toBeTruthy();
    });
});
```
<p> In reality, this test will not always work as intended. This is because the test, <code>expect(r).toBeTruthy()</code>, will sometimes execute faster than the subscribe() callback is called. To overcome this, frameworks like Jest or Karma provides a done() function. It’s a marker for test runners not to finish the test until we call it. </p>

```js
it('should be green for async operation', (done) => {
  timeout(500)
    .subscribe((el) => {
      expect(el).toBeTruthy();
      done();
    });
});
```

<p> Do we always need to use done() in callback? When callbacks are synchronous, we don’t really need to use expect() inside callback.  For example</p>

```js
it('should be', () => {
  // given
  const result = [];

  // when
  of(1, 2)
    .subscribe((el) => result.push(el));

  // then
  expect(result).toEqual([1, 2]);
});
```
	
<p> because of this, one solution to testing asynchronous code is to use a spy and then return an observable using the of(). The spy will now return a synchronous observable and can hence be tested as usuall. </p>

<p> Here is one more exampe with observable and promises </p>

```js
describe('ValueService', () => {
  let service: ValueService;
  beforeEach(() => { service = new ValueService(); });

  it('#getValue should return real value', () => {
    expect(service.getValue()).toBe('real value');
  });

  it('#getObservableValue should return value from observable',
    (done: DoneFn) => {
    service.getObservableValue().subscribe(value => {
      expect(value).toBe('observable value');
      done();
    });
  });

  it('#getPromiseValue should return value from a promise',
    (done: DoneFn) => {
    service.getPromiseValue().then(value => {
      expect(value).toBe('promise value');
      done();
    });
  });
});
```
<p> When your component is using a service that uses asynchronous code, the easiest solution is to mock that service. When you are mocking it, you are turning all its methods to be syncronous. However, when your component is using for example setTimeout() we need another solution. In these scenarions we can use fakeAsync(). </p>
	
```js
  it('should', fakeAsync(() => {
	fixture.detectChanges()
	
	// 1 second pass before test is executed
	tick(1000)
	
	fixture.detectChanges()
	
	expect(...).toEqual(..)
  }));	
	
```
