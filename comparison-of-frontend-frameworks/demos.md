
# Demos (Short Version)

## (Basic) Component Development

* Templating
* Properties
* Events

### React

* Two types of components
  * Stateful class components
  * Stateless functional components

#### Stateful Class Components

* A stateful class component extends the React Component class and defines a `render()` method
* The `render()` method returns a React element
  * This example makes use of JSX
  * Syntax extension to JavaScript
  * JSX (after it's been transpiled by Babel) produces React "elements"

```javascript
class App extends Component {
  render() {
    const title = 'Let It Be';

    return (
      <div>
        <h2>{title}</h2>
      </div>
    );
  }
}
```

#### JSX

```javascript
class App extends Component {
  render() {
    const title = 'Let It Be';

    return (
      <div>
        <h2 className="album">{title}</h2>
      </div>
    );
  }
}
```

* JSX is not HTML!
* Element attributes mirror their JavaScript element properties
* How to create elements
  * HTML elements start with a lowercase letter
  * Components start with an uppercase letter
* How to set element attribute values

#### Events

To handle an event, you wire up an event handler method to the element or component event you want to handle:

```javascript
class App extends Component {
  handleViewDetails = () => {
    alert('Viewing details!');    
  }

  render() {
    const title = 'Let It Be';

    return (
      <div>
        <h2>{title}</h2>
        <button onClick={this.handleViewDetails}>View Details</button>
      </div>
    );
  }
}
```

* Notice the use of property initializer syntax for the `handleViewDetails()` method
  * This approach is being used in order to workaround a problem that arises with `this` not being bound to the expected object instance
  * There are other ways to workaround "this" problem, including calling `bind()` on all class methods that will be used as event handlers

#### Stateless Functional Components

* Stateless functional components are just ordinary functions
* A props object is passed into the function
  * This object gives you access to the properties that are set on the component

```javascript
function Album(props) {
  return (
    <div>
      <h2 className="album">{props.title}</h2>
      <button onClick={props.viewDetails}>View Details</button>
    </div>
  );
}

class App extends Component {
  handleViewDetails = () => {
    alert('View details!');
  }

  render() {
    const title = 'Let It Be';

    return (
      <div>
        <Album title={title} viewDetails={this.handleViewDetails} />
      </div>
    );
  }
}
```

#### Basic Completed Example

See the [demos/basic/react-cli](demos/basic/react-cli) folder

* Implemented `componentDidMount()` in order to fetch data from our API
* Calls `setState()` to update the state of our component
* Using the Array `map()` method to iterate over our collection of albums and render an Album component for each

#### Props

* Props are readonly!
  * Setting a `props` property value will result in an error

#### State

* State must not be directly mutated
  * Mutating state directly will result in a warning
* Use `setState()` to mutate the state object

### Angular

#### Components

* The Angular CLI, by default, generates four files for each component in your application
  * CSS file... scoped styles for your component
  * HTML file... this is your template
  * A TypeScript spec file... unit tests for your component
  * A TypeScript class file... the class for the component

#### The Component Class

```javascript
import { Component } from '@angular/core';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
  title = 'Let It Be';
}
```

* Components are just plain TypeScript classes that are decorated with a Component decorator
  * No base class is required
  * The decorator is what makes our class a component
* We can any number of properties and methods on our class to be used internally by the component
  * As we'll see in a bit, anything that we want to expose outside of our component needs to be declared as "input" or "output"

#### The Component Template

* An Angular template just uses HTML
* You write values using interpolation:
* Any attribute that begins with an asterisk (*) is a structural directive
  * They shape or reshape the structure of the DOM

```html
<div>
  <h2 class="album">{{ title }}</h2>
</div>
```

* You can bind element attributes to properties

```html
<div>
  <h2 [class]="headingClass">{{ title }}</h2>
</div>
```

* You can bind element events to methods

```html
<div>
  <h2 [class]="headingClass">{{ title }}</h2>
  <button (click)="viewDetails()">View Details</button>
</div>
```

#### Basic Completed Example

See the [/demos/basic/angular-cli](/demos/basic/angular-cli) folder

* API call is being made when the component is being initialized
  * Notice the use of Angular's HttpClient... which uses RxJS (instead of a Promise) to handle the async HTTP call
* The instance of the HttpClient will be injected into our component via Angular's DI container

#### Component Input and Output

* Components that accept input or raise events as output need to declare that using "Input" and "Output" decorators
  * Like React props, "Input" properties provide one-way data flow (from parent to child)
  * That being said, Angular allows you to declare a two-way binding (i.e. a banana in a box `[()]`) if you defined an input property `someProp` and an output property `somePropChange` (see [https://angular.io/guide/template-syntax#two-way-binding---](https://angular.io/guide/template-syntax#two-way-binding---))

```javascript
@Component({
  selector: 'app-album',
  templateUrl: './album.component.html',
  styleUrls: ['./album.component.css']
})
export class AlbumComponent {
  @Input() album: Album;
  @Output() onViewDetails = new EventEmitter();

  constructor() { }

  viewDetails() {
    this.onViewDetails.emit();
  }
}
```

And the template would look like:

```html
<div>
  <h2>{{ album.title }}</h2>
  <button (click)="viewDetails()">View Details</button>
</div>
```

And the parent component class would look like:

```javascript
@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
  albums: Album[] = [];

  constructor(private http: HttpClient) { }

  handleViewDetails() {
    alert('View details!');
  }

  ngOnInit(): void {
    this.http.get<Album[]>('http://localhost:3000/albums')
      .subscribe(data => this.albums = data);
  }
}
```

And its template would look like:

```html
<div>
  <div *ngFor="let album of albums">
    <app-album [album]="album" (onViewDetails)="handleViewDetails()"></app-album>
  </div>
</div>
```

#### Modules and Components

* Angular gives you a container for your components
  * You can think of the module conceptually as an assembly
  * You can share modules across apps
  * You can control the visibility of components within a module

```javascript
@NgModule({
  declarations: [
    AppComponent,
    AlbumComponent
  ],
  imports: [
    BrowserModule,
    HttpClientModule
  ],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule { }
```

### Vue (Downgraded Experience)

#### Project Creation

Just create an HTML file!

```html
<html>

<head></head>

<body>
  <div id="app">
    <h2 v-text="appName"></h2>
  </div>

  <script src="https://unpkg.com/vue@2.5.17/dist/vue.js"></script>
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        appName: 'Our Awesome App!'
      }
    });
  </script>
</body>

</html>
```

* The Vue instance
* The mounting point in the HTML becomes your template

#### Dev Workflow

* Make changes to your code
* Run a local server... I like `http-server`
* Refresh the page in the browser

#### The Vue Instance

* Every Vue application has a top level Vue instance
* Created by instantiating an instance of Vue
* `el` is the selector for the mounting point
* `data` is an object literal that defines the state for our app
  * Properties in the data object are reactive (as their values change Vue will update the DOM)

#### The Vue Instance Template

* The DOM element that we bind our instance to provides our template
* The `v-bind` directive allows you to bind to element attributes
* You can also use interpolation to write values to the DOM

```html
<html>

<head></head>

<body>
  <div id="app">
    <h2 v-bind:class="headingClass">{{ title }}</h2>
  </div>

  <script src="https://unpkg.com/vue@2.5.17/dist/vue.js"></script>
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        title: 'Let It Be',
        headingClass: 'album'
      }
    });
  </script>
</body>

</html>
```

* The `v-on` directive allows you to bind to events

```html
<html>

<head></head>

<body>
  <div id="app">
    <h2 v-bind:class="headingClass">{{ title }}</h2>
    <button v-on:click="viewDetails">View Details</button>
  </div>

  <script src="https://unpkg.com/vue@2.5.17/dist/vue.js"></script>
  <script>
    var app = new Vue({
      el: '#app',
      data: {
        title: 'Let It Be',
        headingClass: 'album'
      },
      methods: {
        viewDetails() {
          alert('View details!');
        }
      }
    });
  </script>
</body>

</html>
```

#### Basic Completed Example

See the "demos/basic/vue-downgraded" folder

* `created()` is one of the lifecycle events we can hook into

```javascript
var app = new Vue({
  el: '#app',
  data: {
    albums: []
  },
  created() {
    fetch('http://localhost:3000/albums')
      .then(response => response.json())
      .then(data => this.albums = data);
  }
});
```

* Vue provides built-in directives that we can use in our templates
  * `v-for` is used to create a list

```html
<div id="app">
  <div v-for="album in albums">
    <h2>{{ album.title }}</h2>
  </div>
</div>
```

#### Defining Components

To define a global component (available to all Vue instances), you call the Vue `component()` method

```
Vue.component('album', {
  props: ['album'],
  methods: {
    viewDetails() {
      this.$emit('view-details', this.album.id);
    }
  },
  template: `
    <div>
      <h2>{{ album.title }}</h2>
      <button v-on:click="viewDetails">View Details</button>
    </div>
  `
});
```

* Notice that just like with our top level Vue instance, we pass an object literal
  * Basically the same object shape
* `props` defines the properties that our component exposes
* `template` defines the HTML template for our component
* We use the special `$emit()` method to raise an event that can be handled by the parent component

### Vue (Upgraded Experience)

Introduces single file components!

#### Single File Components

* Syntax highlighting
* CommonJS modules
* Component-scoped CSS
  * CSS is extracted from each component and combined into a single resource
* Precompiled templates

```
<template>
  <div class="column is-half">
    <div class="box">
      <div class="columns">
        <div class="column">
          <figure class="image is-square">
            <img v-bind:src="imageUrl" v-bind:alt="album.title" />
          </figure>
        </div>
        <div class="column">
          <h2 class="title">{{ album.title }}</h2>
          <h3 class="subtitle">{{ album.artist }}</h3>
          <div>
            Category: {{ album.category }}
          </div>
        </div>      
      </div>      
    </div>
  </div>
</template>

<script>
export default {
  name: 'AlbumBox',
  props: {
    album: {
      type: Object,
      required: true
    },
  },
  computed: {
    imageUrl() {
      return require(`../assets/${this.album.id}.png`);
    }
  },
}
</script>

<style scoped>
</style>
```

#### Basic Completed Example

See the "demos/basic/vue-cli" folder
