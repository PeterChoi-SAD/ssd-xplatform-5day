# React Native: Building a Single View App
1. [ Prerequisites ](#1)
2. [ Introduction ](#2)
3. [ React Native ](#3)
4. [ Expo ](#4)
5. [ Creating our First React Native App ](#5)
6. [ Running Apps on Devices and Simulators/Emulators ](#6)
7. [ Adding Components ](#7)
8. [ Anatomy of a React Native Component ](#8)
9. [ Passing Props ](#9)
10. [ Adding Assets ](#10) 
11. [ ScrollView and SafeAreaView ](#11)
12. [ Managing State ](#12)
13. [ Assignment ](#13)


<a name="1"></a>
## 1) Prerequisites

1) A code editor of your choice to write React Native code. I will be using Visual Studio Code.

2) Ensure you have a recent version of node. If you are on your own machine, consider updating to the most recent version. You can get this from the [Node.js website](https://nodejs.org/en/). 

    You can also manage node versions using the [Node Version Manager](https://github.com/nvm-sh/nvm).

3) If you have a mobile device, install the expo client app from the App Store or Google Play.

<img src="https://i.imgur.com/DpzIJNu.png" width="300" />


<a name="2"></a>
## 2) Introduction
Cross-platform app development allows you to create an app on multiple platforms such as Android, iOS, and Windows simultaneously. The main goal is to deliver a native-like experience, regardless of the client device, from a single code base.
There are many technologies and tools available for cross-platform development, such as [Facebook's React Native](https://facebook.github.io/react-native/), [Adobe's PhoneGap](https://phonegap.com/), [Microsoft's Xamarin](https://dotnet.microsoft.com/apps/xamarin), and more recently [Google's Flutter](https://flutter.dev/). Without question, React Native has been the most reliable technology to build production level cross-platform apps.

<a name="3"></a>
## 3) React Native
React Native is a JavaScript library that is used to build user interfaces for mobile devices. You can build an entire app using only React Native components or you can suppliment native code written in Objective C, Swift, Java, or Kotlin in existing iOS/Android projects with components written in React.

<a name="4"></a>
## 4) Expo

Expo is a tool that helps us create React Native apps quickly and more easily than with the standard React-Native cli installation.

<a name="5"></a>
## 5) Creating our First React Native App

Let's create a new project named "petz". Open your terminal, navigate to the folder in which you would like your project directory and execute the following command:

```text
npx expo-cli init petz
```

Choose 'blank' app template when prompted:

<img src="https://i.imgur.com/PtWmUmf.png" />

Navigate to the petz directory and type:

```text
npm start
```
If prompted to install Expo CLI globally, type Y.

*If on a Mac and the install errors, you may need to follow the error message suggested install with a sudo command*

If prompted to grant access to your browser, click "OK".

![](https://i.imgur.com/KyT8YkF.png)

You should see the metro bundler open in your browser.

![](https://i.imgur.com/R8KUtS0.png)


<a name="6"></a>
## 6) Running Apps on Devices and Simulators/Emulators

### Physical Devices

Ensure the Expo app is installed via the App Store or Google Play.

Open your camera and scan the QR code on the Metro Bundler window.

You should see an Expo notification pop up.

Click this notification to load your app in Expo.

<img src="https://i.imgur.com/PHBCHaS.jpg" width="300" /></br>

This is what our app looks like right now!

<img src="https://i.imgur.com/aGlMTPH.jpg" width="300" />

Shake your screen to get to the Expo menu.

<img src="https://i.imgur.com/CVHo0U3.jpg" width="300" />

### iOS Simulator and Android Emulator

From the metro bundler screen, change the connection type to "local".

<img src="https://i.imgur.com/aVEbw5P.png" />

Ensure that Android Studio is running, and that you have an emulator configured and running, then click on "Run on Android Emulator/Device" in the left-hand menu of metro bundler.

For iOS, ensure that simulators run correctly from XCode, and click on "Run on iOS Simulator".

<img src="https://i.imgur.com/ImbXpVp.png" />


### Automatic Refresh

One of the nicest things about working with React Native is the automatic refresh on devices/simulators/emulators. When we make updates to our code and save our updates, any devices we have running our code via Expo will (should) show the updates immediately!

In a text editor of your choice, open our petz     project, and look at the App.js file.

Update the `<Text></Text>` tag with some new text.

```javascript   
export default function App() {
  return (
    <View style={styles.container}>
      <Text>HAI YOU GUYSSSS</Text>
    </View>
  );
}
```

After you save your file, take a look at the devices you have running the app. They should be updated!

<img src="https://i.imgur.com/rdBBLlS.png" />

<a name="7"></a>
## 7) Adding Components

Let's add a very basic component to contain information about a single pet. We want to be able to reuse this component on the screen for our different pets.

Add a new folder called `src`. Inside `src` add a folder called `components`.

<img src="https://i.imgur.com/ajUwBDo.png" />
 
Add a file called `PetDetail.js` in `src/components`.

<img src="https://i.imgur.com/fRxshYs.png" />

<a name="8"></a>
## 8) Anatomy of a React Native Component

Typically React Native components files will have 4 different sections.

1) An import section
2) The component
3) The stylsheet declaration
4) The export call, so we can use our component elsewhere in our project. Sometimes this section is wrapped up in the component (see App.js for an example of this).

Add the following code to your `PetDetail.js` file

```jsx
// 1
import React from 'react';
import { StyleSheet, Text } from 'react-native';

// 2
const PetDetail = () => {
  return <Text>Fluffy</Text>
}

// 3
const styles = StyleSheet.create({});

// 4
export default PetDetail;
```

Now let's use our new component from the parent screen.

Open `App.js`, and add the following import statement:

```javascript
import Pet from './src/components/PetDetail'
```

Update the `App()` function to include our `<Pet />` component

```jsx
export default function App() {
  return (
    <View style={styles.container}>
      <Pet />
    </View>
  );
}
```

Take a look at your devices:

<img src="https://i.imgur.com/cx6QRkD.png" />

<a name="9"></a>
## 9) Passing Props

React Native components work much the same as regular react components. Let's try passing some props to our Pet component so we can customize the name.

In `PetDetail.js` let's update our component to recieve props, and then apply a prop called `name` inside our Text tag.

```jsx
const PetDetail = (props) => {
  return <Text>{props.name}</Text>
}
```

Update App.js to pass a name prop to each Pet component

```jsx
export default function App() {
  return (
    <View style={styles.container}>
      <Pet name="Muffin" />
      <Pet name="Sir Charles Flooferson III" />
      <Pet name="Cookie" />
    </View>
  );
}
```

<img src="https://i.imgur.com/83nswPL.png" width="300" />

<a name="10"></a>
## 10) Adding Assets

Unzip the resources folder that you [downloaded from github](https://github.com/philweier/ssd-xplatform-5day/blob/master/class1Assets.zip). Drag and drop the 3 cat images from the downloaded folder to your project assets folder.

<img src="https://i.imgur.com/ew2H6K4.png" width="300" />

Let's add an Image to our PetDetail component.

Start by adding the View and Image components to the react-native import statement:

```javascript
import { StyleSheet, Text, View, Image } from 'react-native';
```

Let's update our PetDetail component to include an Image for which the source will be passed in as a prop called 'imageSrc'.

In order to prevent our images from looking too large, let's adjust the size and pass this via the style prop (default prop that comes with Image).

```jsx
// 2
const PetDetail = (props) => {
  return (
    <View>
      <Image source={props.imageSrc} style={styles.image} />
      <Text>{props.name}</Text>
    </View>
  );
}

// 3
const styles = StyleSheet.create({
  image: {
    width: 300,
    height: 200
  }
});
```

To access an image asset, we need to use require, and pass it a relative path.

```jsx
export default function App() {
  return (
    <View style={styles.container}>
      <Pet name="Muffin" imageSrc={require('./assets/cat1.jpg')} />
      <Pet name="Sir Charles Flooferson III" imageSrc={require('./assets/cat2.jpg')} />
      <Pet name="Cookie" imageSrc={require('./assets/cat3.jpg')} />
    </View>
  );
}
```

<img src="https://i.imgur.com/jt6TNgd.jpg" width="300" />

Let's make this look a little cleaner by adding some styling to the Text component in PetDetail.

```jsx
// 2
const PetDetail = (props) => {
  return (
    <View>
      <Image source={props.imageSrc} style={styles.image} />
      <Text style={styles.text}>{props.name}</Text>
    </View>
  );
}

// 3
const styles = StyleSheet.create({
  image: {
    width: 300,
    height: 200
  },
  text: {
    fontSize: 20,
    paddingTop: 10,
    paddingBottom: 20
  }
});
```

<a name="11"></a>
## 11) ScrollView and SafeAreaView

Since our content is larger than some screens, we should make sure the user can scroll in order to access all content.

Let's add a ScrollView and wrap this in a SafeAreaView. We use the SafeAreaView to prevent the ScrollView from exceeding the area of the screen that a user can safely interact with.

Update the import section of App.js to include the following changes:

```javascript
import { StyleSheet, Text, ScrollView, SafeAreaView } from 'react-native';
...
import Constants from 'expo-constants';
```

Replace the View tag with a ScrollView tag, and wrap this in a SafeAreaView. Move the style prop to be passed into the SafeAreaView tag.

```jsx
export default function App() {
  return (
    <SafeAreaView style={styles.container}>
      <ScrollView>
        <Pet name="Muffin" imageSrc={require('./assets/cat1.jpg')} />
        <Pet name="Sir Charles Flooferson III" imageSrc={require('./assets/cat2.jpg')} />
        <Pet name="Cookie" imageSrc={require('./assets/cat3.jpg')} />
      </ScrollView>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    marginTop: Constants.statusBarHeight,
    backgroundColor: '#fff',
    alignItems: 'center',
    justifyContent: 'center'
  }
});
```

Scrolling should now work on your app!

<a name="12"></a>
## 12) Managing State

Sometimes we have components that should update based on new information. In order to ensure our users are seeing correct information on the UI, we need to use special state management tactics provided to us by React.

To understand this, let's add a treat counter to our PetDetail component.

In `PetDetail.js` update the following import statement to include `useState`:

```javascript
import React, { useState } from 'react';
```

Also add Button to our react-native imports:

```javascript
import { StyleSheet, Text, View, Image, Button } from 'react-native';
```

At the top of our PetDetail component, add the following:

```javascript
// 2
const PetDetail = (props) => {
  // initialize treatCount to a default value of 0
  const [treatCount, setTreatCount] = useState(0);
  ...
```

`treatCount` is a state variable. Anytime we want to update treatCount, we need to use `setTreatCount` and pass it the new value.

Let's add a treat count label and "give treats" button to our component.

Anytime a user taps our "Give Treats" button, we want to increase the value of treatCount by one. Button has a handy onPress option that we can use to do this.

```jsx
...
return (
    <View>
      <Image source={props.imageSrc} style={styles.image} />
      <Text style={styles.text}>{props.name}</Text>
      <Text>Treat Count: {treatCount}</Text>
      <Button
        title="Give Treats"
        onPress={() => {
          setTreatCount(treatCount + 1)
        }}
        />
    </View>
 );
```

Try out your app!

<a name="13"></a>
## 13) Assignment
Make your best effort to build the MonsterMatch App (Android Day 2) for both Android and iOS using React Native. Monster image assets were included in [todays asset folder](https://github.com/philweier/ssd-xplatform-5day/blob/master/class1Assets.zip)

Have a partner code review your work before the end of today and at least once more before the start of our next X-Platform Mobile class.

Submission: Add philweier as a collaborator and ensure that at least 2 code reviews took place within GitHub. Submit your github repo url to Learning Hub.
