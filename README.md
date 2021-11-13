# Weather App - Hands on Session 

## Getting Started
To get started, Create a new Flutter project:

    flutter create weatherapp
A `weatherapp` directory is created, containing Flutter’s starter app.

Navigate to this directory:

    cd weatherapp
To launch the app in the Simulator/Emulator, ensure that it is running and enter:

    flutter run
Replace the default code with the code in this repository.
```dart
import 'package:flutter/material.dart';  
  
void main() {  
  runApp(MyApp());  
}  
  
class MyApp extends StatelessWidget {  
  // This widget is the root of your application.  
  @override  
  Widget build(BuildContext context) {  
    return MaterialApp(  
      title: 'Weather App',  
      home: HomePage(),  
    );  
  }  
}
```

## 1. Getting the location of the Device
**Objective:** Create a simple UI with a RaisedButton which when pressed, prints the location of the Device in the Terminal.

```dart
import 'package:flutter/material.dart';  
  
class HomePage extends StatefulWidget {  
  @override  
  _HomePageState createState() => _HomePageState();  
}  
  
class _HomePageState extends State<HomePage> {  
  @override  
  Widget build(BuildContext context) {  
    return Scaffold(  
      body: Center(  
        child: RaisedButton(  
          onPressed: (){
          getLocation();
          },  
          child: Text(  
            'Get Location'  
             ),  
        ),  
      ),  
    );  
  }  
}
```
Create a Function getLocation() which is called by onPressed()

 - We use the [geolocator](https://pub.dev/packages/geolocator) Flutter package.  
 - Add the permissions for both Android and iOS.

```dart
void getLocation() async{  
  Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
  print(position.latitude);  
  print(position.longitude);  
}
```
Since, we are using a button to print the location. It isn't very cool :(
So, we'll tap into the Widget lifecycles to getLocation each time our app runs.

```dart
@override  
void initState() {  
  super.initState();  
  getLocation();  
}
```
**Dart Exception Handling**: What if the User denies permission?

Wrap the funtion in a try-catch block and print any exceptions.
```dart
void getLocation() async{  
  try{  
    Position position = await Geolocator.getCurrentPosition(desiredAccuracy: LocationAccuracy.low);  
    print(position.latitude);  
    print(position.longitude);  
  }  
  catch (e) {  
    print(e);  
  }  
}
```
Create local variables and save the Latitude and Longitude to them.

## 2. Use the Location to get Weather Data

We'll use the [**OpenWeather API**](https://openweathermap.org/api). 

 - Create an account to get your API Key.
 - We'll need to make API call from with the app.

**Networking using [HTTP Package](https://pub.dev/packages/http):**

 - Add the package to `pubspec.yaml`
 - Import it to the file.
```dart
import 'package:http/http.dart' as http;  
import 'dart:convert' as convert;

void getLocation() async {
    Position position = await Geolocator.getCurrentPosition(
        desiredAccuracy: LocationAccuracy.high);
    var latitude = position.latitude;
    var longitude = position.longitude;
    getData(latitude, longitude);
  }

 void getData(var latitude, var longitude) async {
    var url = Uri.parse(
        'https://api.openweathermap.org/data/2.5/weather?lat=$latitude&lon=$longitude&appid=$apiKey');
    var response = await http.get(url);
    print('Response status: ${response.statusCode}');
    print('Response body: ${response.body}');
 }
  
@override  
void initState()  {  
  super.initState();  
  getLocation();  
}
```

**Error:** 
HTTP connection error [FIX]: https://stackoverflow.com/a/65578828/11690853  
https://flutter.dev/docs/release/breaking-changes/network-policy-ios-android

*So far, we have been able to print the Weather Data to console.*
