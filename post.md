## 1 A Pure AngularJS Feature


1. Seeing info about a workshops
 - before talk => Show Slide & Countdown Timer
 - after talk => Show Recording
2. Getting the user to take action depending on if the user has 'access' and 'RSVPed'
 - no access => Flow to get AirPair membership
 - have access, but not rsvp'ed => Flow to RSVP
 - have access, already rsvp'ed => Show they are attending

Today's goal was to show a workshop's title + description using a mocked API Workshop response.

## 2 First AngularJS Lessons

We covered the following AngularJS concepts to get things working.

### 2.1 ng-app and Angular Modules

Angular code is generally packaged into AngularJS modules. The Angular module system has it's own unique Dependency Injection pattern. To link an html page to code (a module, probably with some controllers and routes), use the `ng-app` attribute on the html declaration. 

<!--?prettify lang=html linenums=false?-->

    <!DOCTYPE html>
    <html ng-app="APWorkshops">
    <head>

<!--?prettify lang=javascript linenums=true?-->

    angular.module("APWorkshops", ['ngRoute'])

    .run(['$rootScope', function($rootScope) {
	  	$rootScope.title = 'Hello Airpair';
    }])

    .controller('WorkshopsCtrl', function($scope) {
      $scope.entries = []; 
    })

    .controller('WorkshopCtrl', ['$scope', '$http', '$routeParams', function($scope, $http, $routeParams) {	
      $http.get('/api/workshops/'+$routeParams.id).success(function (data) {
        $scope.entry = data;
      });
    }])

    .config(function($routeProvider) {
		
      $routeProvider.when('/', {
	      templateUrl: '/workshops/list.html',
	      controller: 'WorkshopsCtrl as workshops'
      });

      $routeProvider.when('/:id', {
	      templateUrl: '/workshops/show.html',
	      controller: 'WorkshopCtrl as workshop'
      });

    });

### 2.2 AngularJS Routing

To get started with angular routes install angular route `bower i angular-route -S`

Routes map (client-side) urls to templates and controllers. When a route is hit, Angular will automatically invokes the controller function and shows the configured template.

It's really nice ti pair with an Angular Core Team member, as I was informed that there will be a new Angular Router  released a few weeks. Hence I won't dive in any more detail.

`$routeParams` is the mechanism that allows us to pull variables out from our urls. You can see it used on line 12.

### 2.3 AngularJS Controllers & $scope

Unlike <i>BackboneJS</i> Views, which are some form of hybrid ViewController, Angular controllers feel like more pure controllers. They're like a Rails controllers, but instead of database calls you make network requests. One big difference is that AngularJS controllers don't return some kind of result to format/render the data. Instead you focus only on changing the local `$scope` (or self) object.

When ever you make a change the $scope object, angular automatically updates the templates for you. You don't even need to wire up event listeners like in BackboneJS. At first glance you might think, Angular updates on a polling mechanism, but it doesn't. Instead the framework has various hooks to *check in* when things like http calls happen. This is one reason why angular uses its own `$http` mechanism rather than `jquery.ajax`.

`$rootScrope` is a special scope that is accessible in all controllers and all templates.

### 3 First client + server route conflicts

At the end of the session, we were struggling to get the workshops index.html page loading. After debugging solo for half an hour afterwards, there were multiple issues going on.

1. We had set the node app directory to load static resources. This lead to the confusing behavior of automatically opening `/app/workshops/index.html` our client side list view template, instead of `/app/workshops/workshops.hbs` the "index html file" of our SPA.
2. The angular route definitions had to be changes from `/workshops` & `/workshop/:id` to just `/` and `/:id` which might not feel immediately intuitive.

### 4 AngularJS then() vs success() promise

The last thing I got caught on before achieving my mission was using the `then()` promise instead of `success()` on return of our API request. They return slightly different objects, i.e. a request vs pure data, so if using then you need to access the 'response.data' attribute of the result.

<!--?prettify lang=javascript linenums=false?-->

      $http.get('/api/workshops/'+$routeParams.id).then(function (response) {
        $scope.entry = response.data; // Here we need to access .data
      });

      $http.get('/api/workshops/'+$routeParams.id).success(function (data) {
        $scope.entry = data;
      });  


### 5 Summary

Things so far seem pretty simple with a couple of gotchas. Ready to step it up tomorrow.