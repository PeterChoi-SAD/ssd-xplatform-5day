# Cross-Platform Development: Introduction to Flutter and Dart

1. [ Introduction ](#1)
2. [ Adding the Flutter SDK ](#2)
3. [ Updating Your IDE ](#3)
4. [ Review the Template App Code ](#4)
5. [ Build Your Own MaterialApp Widget Tree ](#5)
6. [ AppBar and Body ](#6)
7. [ Exercise](#7)


<a name="1"></a>
## 1) Introduction
We are going to use our last two sessions of cross-platform mobile development to explore [Google's Flutter UI ToolKit](https://flutter.dev/) with an introduction to the Dart programming language.Flutter and Dart are a newer entry in the cross-platform mobile dev space and while the community is still growing, the [documentation](https://flutter.dev/docs) and tools are already quite well established.
If after these two sessions you are interested in going further with Flutter and Dart, I recommend that you review the docs in a more throrough fashion so that you can build your skills off of a more solid foundation. For now, we can do a simple comparison to React Native to adjust our perspective and dive right in!
[Flutter for React Native developers](https://flutter.dev/docs/get-started/flutter-for/react-native-devs)

<a name="2"></a>
## 2) Adding the Flutter SDK
We will follow the steps in the flutter install guide for your operating system. Basically you need to get the Flutter SDK, add flutter to your PATH, and add flutter and dart to your code editor (Android Studio or VS Code).
[Install Flutter](https://flutter.dev/docs/get-started/install)

### My macOS (Catalina) Install
The steps for my install were as follows:

*(Step 1 and 2 were because I am running Catalina...if running an earlier macOS, skip to Step 3!)*

Step 1:
```bash
chsh -s /bin/zsh
echo $SHELL
```
As I am using Catalina, I ensure that I have effectiely switched to the zsh shell for terminal. Earlier versions of macOS can use the default bash...running echo $SHELL would display /bin/bash.

<img src="https://i.imgur.com/xJrU0CN.png" width="400"/>

Step 2:
Similar to .bashrc or .bash_profile ensure that you have a .zshrc file. If not run the following to create one.
```bash
setopt interactivecomments
# the above command allows comments such as this

# the following command adds the run commands file
touch ~/.zshrc
```

Step 3:
```bash
# some choose to put sdk's in a development folder
cd ~/development
git clone https://github.com/flutter/flutter.git -b stable
```

Step 4:
```bash
echo -n 'export PATH=~/development/flutter/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
open ~/.zshrc
```
Flutter has been added to my run commands file:

<img src="https://i.imgur.com/6pE7gIm.png" width="400"/>

Flutter is a recognized command in terminal:

<img src="https://i.imgur.com/ClBdMHp.png" width="600"/>

<a name="3"></a>
## 3) Updating Your IDE
As we have already begun developing mobile apps for both Android and iOS we already have Android Studio and XCode available to us, as well as the Android Emulator(s) and iOS Simulators.
In order to make our development experience more efficient we will need to update our IDE (I will be using Android Studio, however you are welcome to use VS Code) with the related Flutter and Dart extensions.

<img src="https://i.imgur.com/bSh3CTF.png" width="300" />

### Install Flutter by flutter.dev (and Dart)

![](https://i.imgur.com/uAN2Pum.png)

**When asked if you want to install Dart select "Yes".**

Now when you open Android Studio you will see a Flutter project option 

![](https://i.imgur.com/6Akvq7m.png)

### Create a Flutter Application

![](https://i.imgur.com/bduEp0K.png)

Ensure that your SDK path is set:

![](https://i.imgur.com/hwTViBE.png)

Similar to other mobile projects, ensure that your package name is com.yourname.appname where my current package would be com.philweier.flutterapp.

### Run the App!
Select your emulator and Run the app:

<img src="https://i.imgur.com/JURkIKp.png" width="300" />

Open your iOS Simulator:

<img src="https://i.imgur.com/EUBXViQ.png" width="300" />

Toggle the device selection and Run the app again:

<img src="https://i.imgur.com/tOsjjc6.png" width="300" />

You should then see a small 2 by the Run icon:

![](https://i.imgur.com/eg1jtWG.png)

And both devices can be seen running your app!

![](https://i.imgur.com/InTq6PH.png)
<br />
### Hot Reload and Code Formatting
You can add a feature called hot reload on save that will drastically reduce your dev time by updating your app in the emulator/simulator every time you save changes to your app.
Also you can auto format your code to the appropriate tabbing and remove whitespace each time you save changes to your app.
Open Preferences and under the Languages & Frameworks section, ensure the following two features are applied for Flutter:

![](https://i.imgur.com/9RZvcgu.png)


<a name="4"></a>
## 4) Review the Template App Code
The template application provides a ton of helpful comments to introduce you to building a Flutter UI.

The entry point to the app:
```dart
void main() {
  runApp(MyApp());
}
```

Inside your runApp() you can start adding Flutter Widgets. These widgets will define how Flutter will render your app.

To better understand the widgets that exist in your app open the Flutter Inspector on the right side of Android Studio:

![](https://i.imgur.com/XrYjigx.png)

The above image is what is called the "widget tree" where MyApp is the root of the application.

<a name="5"></a>
## 5) Build Your Own MaterialApp Widget Tree
Let's create a new app to see what adding our own widgets is like in a simpler context.
Create a new Flutter project under File->New->New Flutter Project... (you can open this in a new window if you would like to keep the starter code of flutter_app available for comparison).

Collapse each class in the main.dart file and delete MyHomePage and _MyHomePageState.

![](https://i.imgur.com/q1hAs7Q.png)

Inside MyApp clear out everything inside MaterialApp so that your main.dart file is as follows:
```javascript=
import 'package:flutter/material.dart';

void main() => runApp(MyApp());

class MyApp extends StatelessWidget {
  // This widget is the root of your application.
  @override
  Widget build(BuildContext context) {
    return MaterialApp();
  }
}
```
If you run your application and view your flutter inspector you will see that you now have 2 widgets (MyApp and MaterialApp). We will keep this is our starting point because we do want to leverage the build function which will run each time the widget is changed (our hot reload). We will also want to keep [MaterialApp](https://api.flutter.dev/flutter/material/MaterialApp-class.html) which uses [Material Design](https://material.io/) to help us layout and style our widgets.

To resolve the error, lets add the home property back to your MaterialApp, but this time simply add a Scaffold widget.

[Scaffold](https://api.flutter.dev/flutter/material/Scaffold-class.html) is the base implementation of the material design layout structure.
```javascript
return MaterialApp(
    home: Scaffold(),
);
```
The hot reload should update your app when you hit save, but if you would like to see your new widget tree simply hit the refresh icon at the top of the Flutter Inspector.

<img src="https://i.imgur.com/qjIWIEv.png" width="400" />

<a name="6"></a>
## 6) AppBar and Body
Add the appBar and body properties within Scaffold. The appBar should be an AppBar widget, and for now let's add a simple Text widget to the body.
```javascript
return MaterialApp(
      home: Scaffold(
        appBar: AppBar(
          title: Text("My AppBar Text"),
        ),
        body: Text("My Body Text"),
      ),
    );
```

Our new widget tree:

![](https://i.imgur.com/sEJoyZ2.png)

Let's center our body text by using a short cut ;)
Click on the Text widget that is your body text and either click the light bulb to the left or hit Option+Return and select "Wrap with Center"

***This will help to layout your apps quicker and more efficiently in the future.***

![](https://i.imgur.com/huVzglv.png)

![](https://i.imgur.com/AsF6ZWc.png)

Add a comma immediately after the closing parenthasis of the Text widget:

![](https://i.imgur.com/MQrul15.png)

Save changes and see what the code formatter does for you.

<a name="7"></a>
## 7) Exercise
Within the [Flutter documentation codelabs](https://flutter.dev/docs/codelabs) take a few minutes to go through the First Flutter App [part 1](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt1/#0) & [part 2](https://codelabs.developers.google.com/codelabs/first-flutter-app-pt2/#0)





