# $http

## Overview

One common requirement of apps is the ability to make HTTP requests. Luckily, Angular provides us a service named `$http` to do these with ease - let's take a look!

## Objectives

- Describe $http
- Use $http methods
- Fetch data from an API
- Update data from an API

## Making HTTP requests

In JavaScript, if we need to make HTTP requests, we'd use `XMLHTTPRequest`. This is quite an outdated aspect of JavaScript, and it didn't provide the most simplistic API:

```js
var request = new XMLHttpRequest();

request.onreadystatechange = function() {
	if (request.readyState === 4 && request.status === 200) {
	  console.log('data loaded!');
	}
};

request.open('GET', 'http://api.com/api/method', true);
request.send();
```
That's a lot of work just to make one HTTP request. Luckily, Angular gives us a better way - enter `$http`.  

## What is $http?

$http is a core Angular service that provides a simplistic API to allow us to communicate with HTTP endpoints with ease. It is a wrapper for `XMLHTTPRequest` to allow us to use a simplistic, easy API.

There are a few ways to do requests - let's take a look at them

### $http()

We can use `$http` as a function, passing through a configuration object with it, as such:

```js
$http({
	method: 'GET',
	url: '/someURL'
});

$http({
	method: 'POST',
	data: {
		username: 'Bill'
	},
	url: '/someOtherURL'
});
```

This will return a promise with the data - we call the `.then` function that the function call returns - passing through a callback function that will get called when the request has finished.

A "promise" is just a specification on implementing certain methods. The $http function returns an object of methods, one of them being `then`. We use `then` to execute a callback whenever the "promise" is resolved (the request has finished loading).

```js
$http({
	method: 'GET',
	url: '/someURL'
})
	.then(function (data) {
		console.log(data);
	});
```

This will load the response from our URL, and allow us to consume the data somewhere after it's returned the response.

### $http.get() and $http.post();

$http also gives us helper functions instead of calling it as a function itself.

They act similar to the example above, but as we already have the method in the name we can just pass through the URL we would like as a string.

```js
$http.get('/someURL');

$http.post('/someOtherURL', { username: 'Bill' });
```

### Using these in our services

Now, any usage of `$http` should be done in a custom service that we create. This makes our controllers nice and thin, allowing us to call our API from anywhere in the application. If we didn't abstract our API calls out into a service, we might end up having two calls to the same endpoint somewhere in our application. If that endpoint then changed, we'd have to look around the application for any usage of the endpoint, instead of just changing it once in one file.

This fits into our `MVVM` architecture - thin controllers and all the logic being done by helpers. It means we have code that can be shared between the whole application.

Let's have a look at how we'd put our `$http` usage into our services.

```js
function UserService($http) {
	this.getLoggedInUser = function () {
		return $http.get('/rest/user');
	}
}

angular
	.module('app')
	.service('UserService', UserService);
```

Here we have a function that we can call in our controllers to get information on the currently logged in user. We're returning the `$http` call so we can then use the `.then` method in our controllers to then update our data. You'd consume this data in a controller as follows:

```js
function HeaderController(UserService) {
	var ctrl = this;

	ctrl.user = '';

	UserService
		.getLoggedInUser()
		.then(function (res) {
			ctrl.user = res.data.username;
		});
}

angular
	.module('app')
	.controller('HeaderController', HeaderController);
```

We call our `UserService.getLoggedInUser()` function and then update our controller's values with the response that's returned. Awesome!

Now, we might have a form to update the user's email address. We'd then use `$http.post()` in our service:

```js
function UserService($http) {
	this.getLoggedInUser = function () {
		return $http.get('/rest/user');
	};

	this.updateEmail = function (emailAddress) {
		return $http.post('/rest/user/email', {email: emailAddress});
	};
}

angular
	.module('app')
	.service('UserService', UserService);
```

We could then call this in our controller, after a button is clicked for example:

```js
function SettingsController(UserService) {

	this.emailAddress = ''; // this is bound to an input via `ng-model`

	this.submitForm = function () {
		UserService
	        .updateEmail(this.emailAddress)
	        .then(function () {
	            alert('Email updated!');
	        });
	};
}

angular
	.module('app')
	.controller('SettingsController', SettingsController);
```

Simple!

<p class='util--hide'>View <a href='https://learn.co/lessons/angular-http-readme'>HTTP Readme</a> on Learn.co and start learning to code for free.</p>
