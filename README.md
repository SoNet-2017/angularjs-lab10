## Pizza++ with Firebase (AngularFire): Google Maps Integration ##

1. Open your project (imported from [https://github.com/SoNet-2017/angularjs-lab9](https://github.com/SoNet-2017/angularjs-lab9)

2. Try to run the project and only if it works go to the next step, otherwise solve existing problems

3. We want to add a map in the main view (pizzaView) to show (by using markers) all the pizza shops we know.

4. Search on Google for "angularJS google maps"

5. The best "library" for Google Maps in AngularJS is "AngularJS Google Maps V3 Directives" available at [https://ngmap.github.io/](https://ngmap.github.io/)

6. In the top right corner of the page we find two button 
- Download (8.7k) ng-map.js
- Fork project on GitHub

7. Click on the second link to access the repository of the project and its documentation

8. Following the documentation the first thing to do is the download of the script that defines the needed module. We can do it by using "bower" (as suggested in documentation) or by using the link "Download (8.7k) ng-map.js" we found in the first website. Choose the second option and download it by [ng-map.min.js](https://rawgit.com/allenhwkim/angularjs-google-maps/master/build/scripts/ng-map.min.js) 

9. Save it in the lib folder of your project

10. Now, continue to follow the instructions reported in the documentation

11. Include "ng-map.min.js" in your index.html
    ```
    <script src="../lib/ng-map.min.js"></script>
    ```

12. Include Google maps:
    ```
    <script src="http://maps.google.com/maps/api/js"></script>
    ```
    
13. Add ngMap as a dependency in the module definition (in app.js)
    ```
    angular.module('myApp', [
        "firebase",
      'ngRoute',
      'ngMap',
      ...
    ])
    ```
    
14. Now it's needed to understand where the maps should be loaded in the page.
We want to create a two column layout in which on the left side we load the maps and in the right side the list of pizzas.
Consequently, modify the pizzaView.html in this way:
    ```
    <div class="col-md-8">
        
    </div>
    <div class="row">
        <div class="col-xs-12 col-md-4">
            <ul class="media-list">
                ...
            </ul>
        </div>
    </div>
    ```
    
15. Now, following the documentation, we can add a lazy map by adding the following code:
    ```
    <div map-lazy-load="https://maps.google.com/maps/api/js">
      <ng-map center="41,-87" zoom="3"></ng-map>
    </div>
    ```
    
    Thus, copy it the code we created at point 14 to obtain the following result:
    ```
    <div map-lazy-load="https://maps.google.com/maps/api/js" class="col-md-8">
        <ng-map center="41,-87" zoom="3"></ng-map>
    </div>
    <div class="row">
        <div class="col-xs-12 col-md-4">
            <ul class="media-list">
                ...
            </ul>
        </div>
    </div>
    ```
    
16. Try to load the page and the map should be in the right place. However, we cannot yet see the map. Looking at the Chrome Developer Console we can see the error:
    ```
    Google Maps API warning: NoApiKeys https://developers.google.com/maps/documentation/javascript/error-messages#no-api-keys
    ```
    
17. Clicking on the reported link we can find the problem: to use the Google Maps JavaScript API, we must register our app project on the Google API Console and get a Google API key which we can add to our app.

18. Click on the "GET A KEY" button

19. Select the right project from the list and accept the "Terms of Service"

20. Copy the obtained API (or leave the page opened).

21. In the documentation, under the section "Lazy Loading of Google Maps Javascript" there was a suggestion to use Google maps with a KEY:
- set a scope variable in our controller (pizzaView.js) (e.g. $scope.googleMapsUrl="https://maps.googleapis.com/maps/api/js?key=OUR_KEY_HERE";)
- add the tag ```map-lazy-load-params="{{googleMapsUrl}}"``` to the div that should contain the map:
    ```
    <div map-lazy-load="https://maps.google.com/maps/api/js" map-lazy-load-params="{{googleMapsUrl}}"class="col-md-8">
        <ng-map center="41,-87" zoom="3"></ng-map>
    </div>
    <div class="row">
        <div class="col-xs-12 col-md-4">
            <ul class="media-list">
                ...
            </ul>
        </div>
    </div>
    ```
    
22. If we try to execute we continue to obtain the same error, thus we may find a solution in the issues of the GitHub repository associated to the project.
We can find a similar error here: ```https://github.com/allenhwkim/angularjs-google-maps/issues/681``` and they suggest to remove the ```<script>``` tag related to google map defined in index.html.

23. By performing the suggested operation, it actually works!

24. Change lat and long so that the map is centered on Turin ()45.071087, 7.686567) and increment the zoom to 12
    ```
    <ng-map center="45.071087, 7.686567" zoom="12"></ng-map>
    ```

25. Now that everything is working (check it before going on) we want to add some random markers to our view: we want to add as many markers as many pizzas we have in the list.


26. Let's look for some example that could help us. On the first website we found ([https://ngmap.github.io/](https://ngmap.github.io/)) there are a lot of examples. Trying to search for markers we find "marker_dynamic_ng_repeat.html".
It shows how we can implement a function that loads new markers every defined seconds. It's too complicated for use, but we can use it as an example from which we can copy needeed code.

27. Modify pizzaView.js in this way:
    ```
    ...
    .controller('View1Ctrl', ['$scope','Pizza', function($scope,Pizza) {
        $scope.dati={};
        $scope.dati.pizzas = Pizza.getData();
        $scope.googleMapsUrl="https://maps.googleapis.com/maps/api/js?key=AIzaSyD6qAQOEvZs2XlUUu3ziu-nrDX-WWZXap4";
        $scope.dati.vm = this;
        $scope.dati.vm.positions = [];
        $scope.dati.pizzas.$loaded().then(function () {
            for (var i = 0; i < $scope.dati.pizzas.length; i++) {
                var lat = 45.071087 + (Math.random() / 100);
                var lng = 7.686567 + (Math.random() / 100);
                $scope.dati.vm.positions.push({lat: lat, lng: lng});
            }
        });
    }]);
    ```

28. Modify pizzaView.html:
    ```
    <div map-lazy-load="https://maps.google.com/maps/api/js" map-lazy-load-params="{{googleMapsUrl}}" class="col-md-8">
        <ng-map center="45.071087, 7.686567" zoom="12">
            <marker ng-repeat="pos in dati.vm.positions" position="{{pos.lat}},{{pos.lng}}"></marker>
        </ng-map>
    </div>
    ```
31. Open our project on firebase and add the "address" field to each existing pizza.

30. Modify detailsView.js:
    ```
    ...
    .controller('detailsViewCtrl', ['$scope', '$routeParams', 'SinglePizza',
        function($scope, $routeParams, SinglePizza) {
            $scope.dati = {};
            $scope.dati.pizza = SinglePizza.getSinglePizza($routeParams.pizzaId);
            $scope.googleMapsUrl="https://maps.googleapis.com/maps/api/js?key=AIzaSyD6qAQOEvZs2XlUUu3ziu-nrDX-WWZXap4";
            $scope.dati.vm = this;
            $scope.dati.vm.positions = [];
            $scope.dati.pizza.$loaded().then(function () {
                var address = $scope.dati.pizza.address;
                $scope.dati.vm.positions.push({address: address});
                console.log("vm.positions", $scope.dati.vm.positions);
            });
        }]);
    ```
    
31. Modify detailsView.html:
    ```
    <div map-lazy-load="https://maps.google.com/maps/api/js" map-lazy-load-params="{{googleMapsUrl}}" class="col-md-8">
        <ng-map center="45.071087, 7.686567" zoom="12">
            <marker ng-repeat="pos in dati.vm.positions" position="{{pos.address}}"></marker>
        </ng-map>
    </div>
    <h1>{{dati.pizza.nome_pizza}}</h1>
    
    <p>{{dati.pizza.nome_pizzeria}}</p>
    
    <p>{{dati.pizza.address}}</p>
    ```
