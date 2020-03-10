# Flutter Development: API-Based Apps

1. [ Introduction ](#1)
2. [ Getting Started ](#2)
3. [ Testing the API Connection ](#3)
4. [ Add .env for API Key ](#4)
5. [ Tapping into Device Geolocator ](#5)
6. [ Future Class ](#6)
7. [ Refactor To Use State ](#7)
8. [ Adding the Business Data To the Screen ](#8)
9. [ Replace Text with ListView ](#9)
10. [ Add a Business Service ](#10)
11. [ Refactor fetchBusinessList into business.dart service ](#11)
12. [ Lab Assignment ](#12)

<a name="1"></a>
## 1) Introduction
Today we are going to compare our ability to build out a Flutter App that connects to the Yelp Fusion API to retrieve a list of local businesses, similar to what we did with React Native.

<a name="2"></a>
## 2) Getting Started

Create a new Flutter Project in Android Studio and remove unnecessary code from the main.dart file.

```javascript=
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark(),
      home: Center(
        child: Text("Yelp Businesses"),
      ),
    );
  }
}
```

<a name="3"></a>
## 3) Testing the API Connection
We will be using the [dart http package](https://pub.dev/packages/http) as our http client to asynchronously get the business data from the API.

### Add the package to your pubspec.yaml file:
```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^0.12.0+4
  cupertino_icons: ^0.1.2
```

Remember to run `flutter pub get` by clicking the **`Packages get`** link in the Flutter commands bar above the pubspec.yaml code file.

### Add import statements to main.dart:
Add the following import statements to the top of your main.dart file:
```javascript
import 'package:http/http.dart' as http;
import 'dart:io';
import 'dart:convert';
```

These will allow is to use the http package, add our required HttpHeader for Authorization, and decode the JSON response body.

### Add a fetch method to hit the API:
At the top of your `MyApp` class add the following method and call it from the top of your `@override Widget build()` before returning the MaterialApp widget.

```javascript
void fetchBusinessList() async {
    var response = await http.get(
      'https://api.yelp.com/v3/businesses/search' +
          "?&latitude=49.2820&longitude=-123.1171",
      headers: {
        HttpHeaders.authorizationHeader:
            "Bearer [yourTokenHere]"
      },
    );
    print(json.decode(response.body));
  }
```

We will later tap into the devices Geolocation to dynamically set the longitude and latitude hence the added get params.

Run the app in your emulator and see the printed values in the Run window:

![](https://i.imgur.com/74YJUAf.png)

<a name="4"></a>
## 4) Add .env for API Key
Prior to commiting or deploying our code we will want to remove our api key from plain text within the main.dart and create an environment variable.

### Exercise (10 Minutes)
Review the [flutter_dotenv](https://pub.dev/packages/flutter_dotenv) package and implement it within your project so that your code is similar to the following, AND your .env file is not tracked in git...
#### main.dart:
```javascript
void fetchBusinessList() async {
    await DotEnv().load('.env');
    var response = await http.get(
      'https://api.yelp.com/v3/businesses/search' +
          "?&latitude=49.2820&longitude=-123.1171",
      headers: {
        HttpHeaders.authorizationHeader: "Bearer ${DotEnv().env['API_KEY']}"
      },
    );
    print(json.decode(response.body));
  }
```
#### .env not tracked:
<img src="https://i.imgur.com/DXpTIdY.png" width="400" /> <br />

#### API still responds:
![](https://i.imgur.com/m6HWcnk.png)

<a name="5"></a>
## 5) Tapping into Device Geolocator
Let's use the device geolocator to dynamically generate the longitude and latitude of our search based on the users current location.

Add the [geolocator Flutter package](https://pub.dev/packages/geolocator) to your project.

Inside your lib folder, add a new directory called services and inside that directory create a file called location.dart.
```javascript=
import 'package:geolocator/geolocator.dart';

class Location {
  double latitude;
  double longitude;

  Future<void> getLocation() async {
    Position position = await Geolocator()
        .getCurrentPosition(desiredAccuracy: LocationAccuracy.low);

    latitude = position.latitude;
    longitude = position.longitude;
  }
}
```

<a name="6"></a>
## 6) Future Class
You can see in the above Location class that we used a keyword of Future. This represents a delayed computation of the location value (or error). You will see this regularily when using asynchronous calls in dart as it allows us to align with other async await calls within our code.

Add the following to our async fetchBusinessList method:
```javascript
Location location = Location();
    await location.getLocation();
    var response = await http.get(
      'https://api.yelp.com/v3/businesses/search' +
          "?&latitude=${location.latitude}&longitude=${location.longitude}",
```
Notice how we use the await keyword as getLocation could take some time hence our use of Future/async/await as to not halt our UI.

### Run the App...
<img src="https://i.imgur.com/yZuDrho.png" width="300" />

This will most likely not work as we have not granted access to the device(s) geolocation.

![](https://i.imgur.com/sJV1eT9.png)

As noted in the [geolocator Docs](https://pub.dev/packages/geolocator#android) we should add the following to our AndroidManifest and Info.plist

`AndroidManifest.xml`
```xml=
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```

`info.plist`
```xml=
<plist version="1.0">
<dict>
    <key>NSLocationWhenInUseUsageDescription</key>
    <string>This app needs access to location when open.</string>
    ...
```

### Fresh Restart your App
After changing the Manifest and plist it is a good idea to fresh reload your app in your test device(s).
Note: If you do not get a response from the API add some logging to see where the breakdown occurs...I had some issues with certain emulators (Nexus 6) where the getCurrentPosition method would silently fail :( I switched to a Nexus 5 and had no problems:

![](https://i.imgur.com/AFIqu1Q.png)

<a name="7"></a>
## 7) Refactor To Use State  
By having our call to fetchBusinessList() inside the build function of our stateless widget, we are hitting the API everytime the widget is built (and re-built). It would be better to only hit the API once when our Widget is created and added to our widget tree. For this we will create a Statefull Widget called LoadingScreen and we can override the initState function to call fetchBusinessList() only once when this new Stateful Widget is initialized.

Add `loading_screen.dart` to your lib folder and move all of the API logic to the new widget. Replace the home: Center() widget with the new `LoadingScreen()` widget and add an import statement to the new code file. `main.dart` should look like this:

```javascript=
import 'package:flutter/material.dart';
import 'package:feedmeflutter/loading_screen.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      theme: ThemeData.dark(),
      home: LoadingScreen(),
    );
  }
}

```

`loading_screen.dart`
```javascript=
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:io';
import 'dart:convert';
import 'package:flutter_dotenv/flutter_dotenv.dart';
import 'package:feedmeflutter/services/location.dart';

class LoadingScreen extends StatefulWidget {
  @override
  _LoadingScreenState createState() => _LoadingScreenState();
}

class _LoadingScreenState extends State<LoadingScreen> {
  
  void fetchBusinessList() async {
    await DotEnv().load('.env');
    Location location = Location();
    await location.getLocation();
    print("Location: ${location.latitude} :  ${location.longitude}");
    var response = await http.get(
      'https://api.yelp.com/v3/businesses/search' +
          "?&latitude=${location.latitude}&longitude=${location.longitude}",
      headers: {
        HttpHeaders.authorizationHeader: "Bearer ${DotEnv().env['API_KEY']}"
      },
    );
    print(json.decode(response.body));
  }

  @override
  Widget build(BuildContext context) {
    // TODO: implement build
    return Center(
      child: Text("Loading Screen"),
    );
  }
}
```

Now add the override to initState to the _LoadingScreenState class:
```javascript
@override
  void initState() {
    super.initState();
    fetchBusinessList();
  }

```

When you hit save (Flutter Hot Reload) you should not see any print statement:

![](https://i.imgur.com/AqaGqJy.png)

However if you Flutter Hot Restart then you should see the print of the location as well as the api result.

![](https://i.imgur.com/Y0XCPde.png)


<a name="8"></a>
## 8) Adding the Business Data To the Screen
Now that we have dynamically retrieved the user's location from their device and used that location to find restaurants near them, we want to render that data to their device.
We can leverage our asynchronous programming with Future's to store the response data as a Future collection of Business objects and load them to the body using a FutureBuilder widget.
Let's add a simple FutureBuilder by replacing our Center widget with a FutureBuilder widget and storing a Future<String> as a value returned following the API call:
`loading_screen.dart`
```javascript
class _LoadingScreenState extends State<LoadingScreen> {
// Future variable
  Future<String> _futureData;
  
  @override
  void initState() {
    super.initState();
    // hit API and assign value ONCE when widget is added to the tree
    _futureData = _fetchBusinessList();
  }

// returns a Future asynchronously
  Future<String> _fetchBusinessList() async {
    await DotEnv().load('.env');
    Location location = Location();
    await location.getLocation();
    print("Location: ${location.latitude} :  ${location.longitude}");
    var response = await http.get(
      'https://api.yelp.com/v3/businesses/search' +
          "?&latitude=${location.latitude}&longitude=${location.longitude}",
      headers: {
        HttpHeaders.authorizationHeader: "Bearer ${DotEnv().env['API_KEY']}"
      },
    );
    
    // later we will return a collection of Businesses
    return "Happy Times!";
  }

// render the future to the screen via FutureBuilder
  @override
  Widget build(BuildContext context) {
    return FutureBuilder<String>(
        future: _futureData,
        builder: (context, snapshot) {
          if (snapshot.hasData) {
            return Text(snapshot.data);
          } else if (snapshot.hasError) {
            return Text(snapshot.error);
          }
          // default show a loading spinner
          return CircularProgressIndicator();
        });
  }
}
```

![](https://i.imgur.com/juwAhR0.png)

<a name="9"></a>
## 9) Replace Text with ListView
Because we will be rendering a collection of many restaurants we will nest Card widgets within a ListView.
Let's test this with a list of the business names.
Replace the return statement of _fetchBusinessList with the following:
```javascript
// get the businesses from the response
Iterable decodedData = jsonDecode(response.body)['businesses'];

// extract the names to a list
List<String> businessNames = decodedData
    .map((businessJson) => businessJson['name'].toString())
    .toList();

return businessNames;
```

Add a ListView within your FutureBuilder and update all associated Future types from String to List<String>. I have added some additional widgets as functional examples:
```javascript
if (snapshot.hasData) {
return ListView.builder(
  itemCount: snapshot.data.length,
  itemBuilder: (context, index) {
    return Column(
      children: <Widget>[
        Container(
          height: 120.0,
          width: 120.0,
          decoration: new BoxDecoration(
            image: DecorationImage(
              image: new NetworkImage(
                "http://s3-media2.fl.yelpcdn.com/bphoto/MmgtASP3l_t4tPCL1iAsCg/o.jpg"),
              fit: BoxFit.fill,
              ),
              shape: BoxShape.circle,
            ),
        ),
        Card(
          child: Text('${snapshot.data[index]}'),
        ),
      ],
    );
  });
}
```

![](https://i.imgur.com/PsCxDzD.png)


<a name="10"></a>
## 10) Add a Business Service
Add a new file to the services directory called business.dart. This will serve to define the values that will define a Business object within our app and will provide mapping from the api json response.

```javascript=
class Business {
  final String id;
  final String name;
  final String image_url;

  Business({this.id, this.name, this.image_url});

  factory Business.fromJson(Map<String, dynamic> json) {
    return Business(
      id: json['id'],
      name: json['name'],
      image_url: json['image_url'],
    );
  }
}
```

Update the _fetchBusinessList to return a collection of businesses rather than just the names:
```javascript
List<Business> businesses = decodedData
        .map((businessJson) => Business.fromJson(businessJson))
        .toList();

    return businesses;
```


<a name="11"></a>
## 11) Refactor fetchBusinessList into business.dart service
The actual API call should not be done from inside our activity/screen, let's move it to the business.dart file as a helper method related to Businesses.
As Flutter does not have private/public/protected...the underscore at the start of the method makes it behave as private. Change the method name to fetchBusinessList and move it to the service class along with all necessary import statements.

<a name="12"></a>
## 12)Lab Assignment
See if you can make each item clickable and render a detail page where you list the restaurant name, address, and all images from the photos array of the business details api response.



