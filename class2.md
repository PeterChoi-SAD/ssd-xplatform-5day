# React Native: List-Based Apps
1. [ Introduction ](#1)
2. [ Getting Started ](#2)
3. [ FlatList ](#3)
4. [ Adding RecipeItem Component ](#4)
5. [ Navigating Between Screens ](#5)
6. [ Screen Components ](#6)
7. [ Adding A Navigation Container ](#7)
8. [ Adding a Detail View ](#8)
9. [ Passing Data ](#9)
10. [ Improve DetailScreen Styling ](#10)

<a name="1"></a>
## 1) Introduction

We're building CookIt today in React Native:
<br />
![](https://i.imgur.com/JV72Meg.gif)

<a name="2"></a>
## 2) Getting Started

Create a new expo app called Cookit:

```txt
npx expo-cli init cookit
```

Run your app `npm start` and ensure you can see it running on a device.

<a name="3"></a>
## 3) FlatList
https://facebook.github.io/react-native/docs/flatlist

Let's get started by adding a really basic list to our app. In our first app we added a ScrollView to load our cat collection, however when rendering tabular data from a dynamic list, it can be more efficient to use a FlatList. ScrollView's load all items at once while FlatLists allow for scrolling as well as other functionality that can boost performance such as rendering elements once they are about to come into view on the screen and removing elements from memory as they become farther from the view of the screen.

In `App.js`, update our react-native import statement to the following:

```javascript
import { StyleSheet, Text, FlatList, SafeAreaView } from 'react-native';        
```

Update the App component to the following:

```jsx
export default function App() {
  const recipes = [
    { title: "Best Brownies", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/45202/brownie-dessert-cake-sweet-45202.jpeg" },
    { title: "Banana Bread", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/830894/pexels-photo-830894.jpeg" },
    { title: "Chocolate Chip Cookies", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/230325/pexels-photo-230325.jpeg" }
  ]

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        keyExtractor={recipe => recipe.title}
        data={recipes}
        renderItem={({ item }) => {
          return <Text>{item.title}</Text>
      }} />
    </SafeAreaView>
  );
}
```

The `FlatList` component requires 2 props to correctly render a list:

1) `data`: the array of data that we would like to pass into `renderItem`
2) `renderItem`: a function that returns the jsx for each item in the data array. Right now we're just render a Text component with the recipe title.

For performance reasons, it's also a good idea to include a `keyExtractor` prop that takes a function that returns a unique string identifier for each item.

In this case, we are using the title as the key.

<img src="https://i.imgur.com/ei5qwcP.png" width="300" />

<a name="4"></a>
## 4) Adding RecipeItem Component

```jsx
return (
    <SafeAreaView style={styles.container}>
      <FlatList
        keyExtractor={recipe => recipe.title}
        data={recipes}
        renderItem={({ item }) => {
          return <RecipeItem recipe={item} />
      }} />
    </SafeAreaView>
);
```

```jsx
// 1
import React from 'react';
import { StyleSheet, View, Image, Text } from 'react-native';

// 2
const RecipeItem = ({ recipe }) => {
  return(
    <View style={styles.container}>
      <Text style={styles.text} >{recipe.title}</Text>
      <Image source={{ uri: recipe.imageURL}} style={styles.image} />
    </View>
  );
}

// 3
const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',
    justifyContent: 'space-between',
    alignItems: 'center',
    padding: 10
  },
  image: {
    width: 50,
    height: 50
  },
  text: {
    paddingRight: 10
  }
});

// 4
export default RecipeItem;
```

<img src="https://i.imgur.com/vHZkrJQ.png" width="300" />

<a name="5"></a>
## 5) Navigating Between Screens

https://reactnavigation.org/docs/en/getting-started.html
1. Install React Navigation

```text
npm install @react-navigation/native @react-navigation/stack
```

2. Install Dependencies

```text
expo install react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

3. Start the app and clear cache

```text
npm start -c
```

<a name="6"></a>
## 6) Screen Components

Create a `screens` directory inside the `src` directory.

Add a file called `HomeScreen.js`.

Let's move our recipe list to this new screen component:

```jsx
import React from 'react';
import { StyleSheet, FlatList, SafeAreaView } from 'react-native';
import RecipeItem from '../components/RecipeItem';

const HomeScreen = () => {
  const recipes = [
    { title: "Best Brownies", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/45202/brownie-dessert-cake-sweet-45202.jpeg" },
    { title: "Banana Bread", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/830894/pexels-photo-830894.jpeg" },
    { title: "Chocolate Chip Cookies", steps: ["step1", "step2"], imageURL: "https://images.pexels.com/photos/230325/pexels-photo-230325.jpeg" }
  ]

  return (
    <SafeAreaView style={styles.container}>
      <FlatList
        keyExtractor={recipe => recipe.title}
        data={recipes}
        renderItem={({ item }) => {
          return <RecipeItem recipe={item} />
      }} />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'stretch',
    justifyContent: 'center',
  }
});

export default HomeScreen;
```

<a name="7"></a>
## 7) Adding A Navigation Container

Let's update `App.js` with a `NavigationContainer` and `Stack.Navigator`. This will enable us to switch between screens.

```jsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import HomeScreen from './src/screens/HomeScreen'

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="CookIt" component={HomeScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

We can now see a navigation bar on the top of the screen:

<img src="https://i.imgur.com/QQkDvBP.png" width="300" />

<a name="8"></a>
## 8) Adding a Detail View

Let's start by updating `HomeScreen` so we can navigate to another screen.

First we will need to access the `navigation` prop that is passed in to every screen component (definition) in stack navigator.

```javascript
const HomeScreen = ({ navigation }) => { ...
```

We will use this navigation prop when the user taps on a recipe they will navigate to its detail screen. In order to make the FlatList items "tappable" we need to wrap each item inside a `TouchableOpacity`
https://facebook.github.io/react-native/docs/touchableopacity

```jsx
<FlatList
    keyExtractor={recipe => recipe.title}
    data={recipes}
    renderItem={({ item }) => {
      return(
        <TouchableOpacity onPress={() => {
          navigation.navigate('RecipeDetails')
        }}>
          <RecipeItem recipe={item} />
        </TouchableOpacity>
      );
    }} />
```

Let's refactor this slightly to make it easier to read. Above the call to return, add the following:

```jsx
const showDetail = () => {
  navigation.navigate('RecipeDetails')
}
```

Let's update our onPress function to use `showDetail()`.

```jsx
...

<TouchableOpacity onPress={() => showDetail()}>
  <RecipeItem recipe={item} />
</TouchableOpacity>
```

This won't work until we add our recipe detail screen. Inside `src/screens` add a file called `RecipeDetailScreen.js`.

```jsx
import React from 'react';
import { StyleSheet, Text, SafeAreaView } from 'react-native';

const RecipeDetailScreen = () => {
  return (
    <SafeAreaView style={styles.container}>
      <Text>Detail!</Text>
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'stretch',
    justifyContent: 'center',
  }
});

export default RecipeDetailScreen;
```

Let's not forget to update our Stack.Navigator with this new screen. Open `App.js` and import `RecipeDetailScreen` and add a `Stack.Screen` component for RecipeDetailScreen.

```jsx
import React from 'react';
import { View, Text } from 'react-native';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import HomeScreen from './src/screens/HomeScreen'
import RecipeDetailScreen from './src/screens/RecipeDetailScreen'

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="CookIt" component={HomeScreen} />
        <Stack.Screen name="RecipeDetails" component={RecipeDetailScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

Try tapping on one of the recipe cells:

<img src="https://i.imgur.com/c6GI10K.png" width="300" />

<a name="9"></a>
## 9) Passing Data

Update our `showDetail` function in `HomeScreen.js` to pass the recipe to our navigate call:

```jsx
const showDetail = (recipe) => {
  navigation.navigate('RecipeDetails', recipe);
}

...

<TouchableOpacity onPress={() => showDetail(item)}>
  <RecipeItem recipe={item} />
</TouchableOpacity>
```

We can access the recipe object we passed to our RecipeDetailScreen component by using the `route` prop, (specifically we want params):

```jsx
import React from 'react';
import { StyleSheet, Text, SafeAreaView, Image, FlatList } from 'react-native';

const RecipeDetailScreen = ({ route }) => {
  const { title, imageURL, steps } = route.params

  return (
    <SafeAreaView style={styles.container}>
      <Image source={{ uri: imageURL}} style={styles.image} />
      <Text>{ title }</Text>
      <FlatList
        data={steps}
        keyExtractor={item => item}
        renderItem={({ item }) => {
          return <Text>{item}</Text>;
        }} />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'stretch',
    justifyContent: 'center',
  },
  image: {
    width: 200,
    height: 200
  }
});

export default RecipeDetailScreen;
```

<img src="https://i.imgur.com/gfe8Kzv.png" width="300" />

<a name="10"></a>
## 10) Improve DetailScreen Styling
Let's improve the way the recipe detail screen looks:

```jsx
import React from 'react';
import { StyleSheet, Text, SafeAreaView, Image, FlatList } from 'react-native';

const RecipeDetailScreen = ({ route }) => {
  const { title, imageURL, steps } = route.params

  return (
    <SafeAreaView style={styles.container}>
      <Image source={{ uri: imageURL}} style={styles.image} />
      <Text style={styles.heading}>{ title }</Text>
      <Text style={styles.heading}>Steps</Text>
      <FlatList
        data={steps}
        keyExtractor={item => item}
        renderItem={({ item }) => {
          return <Text style={styles.stepItem}>{item}</Text>;
        }} />
    </SafeAreaView>
  );
}

const styles = StyleSheet.create({
  container: {
    flex: 1,
    backgroundColor: '#fff',
    alignItems: 'stretch',
    justifyContent: 'center'
  },

  heading: {
    fontWeight: 'bold',
    paddingTop: 10,
    paddingLeft: 10
  },
  image: {
    height: 250
  },
  stepItem: {
    paddingTop: 10,
    paddingBottom: 10,
    paddingLeft: 20
  }
});

export default RecipeDetailScreen;
```

<img src="https://i.imgur.com/vEZGT96.jpg" width="300" />
