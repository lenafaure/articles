## How I (sort of) got around the Google Maps API results limit

#### A practical example of using closures in JavaScript

When I was looking for a job as a web developer a while ago, I searched everywhere for a list of all the web agencies in Paris. I wanted to compare every one of them and crawled the web for a detailed and exhaustive list that I never found.

So I saw no other solution than to make it myself. I started wondering where those agencies could be referenced and accessed  easily on the web, without getting lost in the 5+ million results of a google search.



##### Googles Places is a good way of finding businesses by keywords in an given area

I realized that almost every web agency was referenced as a Place on Google Maps. And guess what ? [Google Maps API](https://developers.google.com/maps/), and more specifically [Google Places API](https://developers.google.com/places/), allows you to do all sorts of amazing things, including searching for places with a  keywords filter, and fetching the place's name, along with its corresponding website urls, photos and user ratings.

If I could find a way to fetch as much results as possible for the Paris area using Google Places, a great part of the job would be automatically done, and save me a valuable amount of time.

**Spoiler :** I managed to get up to **190 results** for the Paris area, which I know for a fact is not the total number of web agencies (some of them are not registered on Google Places, others simply don't appear in the results for unknown reasons), but allowed me to find some interesting results I wouldn't have found otherwise. This is what it looks like :

**The map:**

![Agencies Map](https://i.imgur.com/TPnubpv.png)

**The list (abridged):** 

![Agencies List](https://i.imgur.com/Si3B8Ay.png)



**Google Places API** seemed to have everything I needed for my purposes : once a search has been made and results are returned, the [Place Details request](https://developers.google.com/places/web-service/details) allows you to fetch the name, website url, photos and rating of each Place. I would have liked to be able to evaluate the size of each agency by number of employees, but hey.



#### Initiating the search

I set up a project on localhost (I would need to use PHP later to write the results in a file), and followed the JavaScript API documentation to initiate the map (get it [here](https://developers.google.com/maps/documentation/javascript/tutorial)) and make a Radar Search request (get it [here](https://developers.google.com/places/web-service/search)) : 

```
var map;
var infowindow;
// Defining the keywords for filtering the search
var searchwords = "agence+web";

// Initiate Map
function initMap() {
    var paris = {lat: 48.8704907, lng: 2.3309359};

    map = new google.maps.Map(document.getElementById('map'), {
        center: paris,
        zoom: 13,
        styles: [{
            stylers: [{ visibility: 'simplified' }]
        }, {
            elementType: 'labels',
            stylers: [{ visibility: 'off' }]
        }]
    });

    infowindow = new google.maps.InfoWindow();

    var populationOptions = {
        strokeColor: '#FF0000',
        strokeOpacity: 0.1,
        strokeWeight: 2,
        fillColor: '#FF0000',
        fillOpacity: 0.075,
        map: map,
        center: paris,
        radius: 7000
    };
    
    // Add the circle for this city to the map.
    cityCircle = new google.maps.Circle(populationOptions);

	// Call the Places API Web Service
    var service = new google.maps.places.PlacesService(map);
    
    // Initiate Radar Search 
    service.radarSearch({
        location: paris,
        radius: 7000,
        keyword: searchwords
    }, callback);
    
}
```



A Text Search or a Nearby Search request would let you access directly the response parameters you need, but with one major restriction : they will return a maximum of 20 results per query. Each search can return as many as 60 results, split across three pages, which means that you can call a **next_page_token** data and get a **grand total of 60 results**, but then you are stuck.



#### Getting around the 60 results query limit

The **Radar Search** request lets you to search for up to **200 places at once**, but with less detail than is typically returned from a Text Search or Nearby Search request. You basically get and ID for each place, but not much more details about it, not even a name.

This means that you will have to somehow iterate through your results to call the [Place Details request](https://developers.google.com/places/web-service/details) by passing it the ID of each of the Places you found. The Place Details request is where all the parameters we look for are returned.

This will take place in the callback of our `service.radarSearch` function: 

```
// Create array for storing results 
var agencies = [];

function callback(results, status) {

    if (status === google.maps.places.PlacesServiceStatus.OK) {
        for (var i = 0; i < results.length; i++) {
           
            //Fetch the ID of each Place returned by the Radar Search                
          	var request = {
				placeId: results[i]['place_id']
			};

            // Call the Places API Web Service
            service = new google.maps.places.PlacesService(map);

            // Initiate Place Details request
            service.getDetails(request, callback);

            function callback(place, status) {
                if (status == google.maps.places.PlacesServiceStatus.OK) {
                	
                    createMarker(place);
                    agencies.push([place.name, place.website, place.rating]);
					
					// When reaching the last result, send to PHP file to print results
                    if(results.length == agencies.length){
                        console.log(agencies);
                        var request = new XMLHttpRequest();
                        request.open('POST', 'http://localhost/agency-map/src/save.php', true);
                        request.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');
                        request.send(JSON.stringify(agencies));
                    }
                }
            }
        }
    }
}
```



This works... partially. I **only get 10 detailed results** by looping through the results although the total number is 186. Why is that ?



#### Getting around the 10 queries per second limit : `setTimeout` and closures

Well, the problem with the Place Details request is that it lets you query *only 10 results per second*. The execution of the code takes way less than one second, so it seems that we hit a wall here, doesn't it ?

What if we used the `setTimeout` function to query one result per second, therefore never reaching the limit of  10 results per second ?

This is where [the closure in JavaScript will be of great help](https://github.com/lenafaure/closures/blob/master/closures.md), allowing us to store every value found throughout our 186 results and storing each of them in the `agencies` array :

```
// Using setTimeout and closure because limit of 10 queries /second for getDetails //

(function (j) {

	var request = {
		placeId: results[i]['place_id']
	};

	service = new google.maps.places.PlacesService(map);

	setTimeout(function() {
		service.getDetails(request, callback);
	}, j*1000);

})(i);
```



In the end, the full JavaScript code snippet is : 

```
var map;
var infowindow;
var searchwords = "agence+web";

// Initiate Map
function initMap() {
    var paris = {lat: 48.8704907, lng: 2.3309359};

    map = new google.maps.Map(document.getElementById('map'), {
        center: paris,
        zoom: 13,
        styles: [{
            stylers: [{ visibility: 'simplified' }]
        }, {
            elementType: 'labels',
            stylers: [{ visibility: 'off' }]
        }]
    });

    infowindow = new google.maps.InfoWindow();

    var populationOptions = {
        strokeColor: '#FF0000',
        strokeOpacity: 0.1,
        strokeWeight: 2,
        fillColor: '#FF0000',
        fillOpacity: 0.075,
        map: map,
        center: paris,
        radius: 7000
    };
    // Add the circle for this city to the map.
    cityCircle = new google.maps.Circle(populationOptions);

    var service = new google.maps.places.PlacesService(map);
    service.radarSearch({
        location: paris,
        radius: 7000,
        keyword: searchwords
    }, callback);
}

var agencies = [];

function callback(results, status) {
    console.log(results.length);
    console.log(results);
    if (status === google.maps.places.PlacesServiceStatus.OK) {
        for (var i = 0; i < results.length; i++) {

            //Using setTimeout and closure because limit of 10 queries /second for getDetails */
            (function (j) {
                var request = {
                    placeId: results[i]['place_id']
                };

                service = new google.maps.places.PlacesService(map);
                setTimeout(function() {
                    service.getDetails(request, callback);
                }, j*1000);


            })(i);

            function callback(place, status) {
                if (status == google.maps.places.PlacesServiceStatus.OK) {
                    createMarker(place);
                    console.log(place.name +  results.length + agencies.length);
                    agencies.push([place.name, place.website, place.rating]);

                    if(results.length == agencies.length){
                        console.log(agencies);
                        var request = new XMLHttpRequest();
                        request.open('POST', 'http://localhost/agency-map/src/save.php', true);
                        request.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded; charset=UTF-8');
                        request.send(JSON.stringify(agencies));
                    }
                }
            }
        }
    }
}

function createMarker(place) {
    var photos = place.photos;
    if (!photos) {
        return;
    }
    var placeLoc = place.geometry.location;
    var marker = new google.maps.Marker({
        map: map,
        position: place.geometry.location,
        title: place.name,
        icon: photos[0].getUrl({'maxWidth': 50, 'maxHeight': 50})
    });

    google.maps.event.addListener(marker, 'click', function() {
        infowindow.setContent(place.name + " : " + place.website);
        infowindow.open(map, this);
    });
}
```



I can now use it to fetch the results I want and use the data, and the result is pretty fun to watch :

![real-time results](https://i.imgur.com/kJpsjNb.gif)



Of course this would not  work for a real-time application, but for the purpose of fetching some data about specific places I wanted to find, this worked like a charm !





​	





###  





