# React Native: API Based Apps

1. [ Introduction ](#1)
2. [ Getting Started ](#2)
3. [ Building Out Our Screens ](#3)
4. [ Navigation ](#4)
5. [ Search Bar](#5)
6. [ State Hook ](#6)
7. [ Axios ](#7)
8. [ Result List View ](#8)
9. [ Grouping Restaurants by Price Category ](#9)
10. [ Remove Scroll Bar and Add Vertical Scroll for Smaller Screens ](#10)
11. [ Detail View In-class Assignment ](#11)

<a name="1"></a>
## 1) Introduction
Today we will be building an app called FeedMe which will connect to the Yelp api to return restaurant data related to user search parameters.

<a name="2"></a>
## 2) Getting Started

Sign up for an API key at [https://www.yelp.com/fusion](https://www.yelp.com/fusion).

Create a new expo app called FeedMe:

```txt
npx expo-cli init feedme
```

```txt
cd feedme
```

Install Dependencies:

1. Install React Navigation

```text
npm install @react-navigation/native @react-navigation/stack
```

2. Install Dependencies

```text
expo install react-native-reanimated react-native-gesture-handler react-native-screens react-native-safe-area-context @react-native-community/masked-view
```

3. Start the app

```text
npm start
```

<a name="3"></a>
## 3) Building Out Our Screens

Create a `src/screens` directory.

Add a file called `SearchScreen.js` and `DetailScreen.js`.

### Search Screen

Let's build out our search screen before we hook up the yelp API.

```jsx
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';

const SearchScreen = () => {
  return (
    <View>
        <Text>Search</Text>
    </View>
  );
}

const styles = StyleSheet.create({});

export default SearchScreen;
```

<a name="4"></a>
## 4) Navigation

Let's set up our navigation so our search screen is the first screen we see when we load our app.

```jsx
import React from 'react';
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';
import SearchScreen from './src/screens/SearchScreen'

const Stack = createStackNavigator();

const App = () => {
  return (
    <NavigationContainer>
      <Stack.Navigator>
        <Stack.Screen name="FeedMe" component={SearchScreen} />
      </Stack.Navigator>
    </NavigationContainer>
  );
}

export default App;
```

<a name="5"></a>
## 5) Search Bar

For our search bar, let's add a new component called SearchBar and add this to our SearchScreen.

Create a folder called components inside the src directory, and add a file called SearchBar.js

```jsx
import React from 'react';
import { StyleSheet, View, TextInput } from 'react-native';

const SearchBar = () => {
  return (
    <View>
      <TextInput placeholder="Search" />
    </View>
  );
}

const styles = StyleSheet.create({});

export default SearchBar;
```

Change SearchScreen to show our SearchBar component

```jsx
import React from 'react';
import { StyleSheet, View } from 'react-native';
import SearchBar from '../components/SearchBar';

const SearchScreen = () => {
  return (
    <View>
      <SearchBar />
    </View>
  );
}

const styles = StyleSheet.create({});

export default SearchScreen;
```

<img src="https://i.imgur.com/eYzipzD.png" width="300" />

Let's add a search icon and a some styles to our search bar

You can access loads of icons via the Expo Vector Icons directory: 
[https://expo.github.io/vector-icons/](https://expo.github.io/vector-icons/)

```jsx
import React from 'react';
import { StyleSheet, View, TextInput } from 'react-native';
import { EvilIcons } from '@expo/vector-icons'

const SearchBar = () => {
  return (
    <View style={styles.background}>
      <EvilIcons style={styles.icon} name="search" />
      <TextInput style={styles.input} placeholder="Search" />
    </View>
  );
}

const styles = StyleSheet.create({
  background: {
    height: 50,
    borderRadius: 6,
    marginHorizontal: 15,
    flexDirection: 'row',
    alignItems: 'center'
  },
  input: {
    flex: 1
  },
  icon: {
    fontSize: 35,
    alignSelf: 'center',
    marginHorizontal: 15
  }
});

export default SearchBar;
```

<a name="6"></a>
## 6) State Hook

Add the useState hook to SearchScreen and pass the searchTerm and onTermChange props to SearchBar

```jsx
import React, { useState } from 'react';
import { StyleSheet, View } from 'react-native';
import SearchBar from '../components/SearchBar';

const SearchScreen = () => {
  const [term, setTerm] = useState('')

  return (
    <View>
      <SearchBar term={term} onTermChange={(newTerm) => setTerm(newTerm)} />
    </View>
  );
}

const styles = StyleSheet.create({});

export default SearchScreen;
```
### Handle the props in `SearchBar.js`
```jsx
...

const SearchBar = ({ term, onTermChange }) => {
  return (
    <View style={styles.background}>
      <EvilIcons style={styles.icon} name="search" />
      <TextInput
        value={term}
        style={styles.input}
        placeholder="Search"
        onChangeText={newTerm => onTermChange(newTerm)} />
    </View>
  );
}
  
  ...
```

<a name="7"></a>
## 7) Axios
An alternative to the built in `fetch()` api is the axios library. There are number of methods and features to axios that make it easier to work with in many situations. From automatically stringified json to HTTP Interceptors and default Download Progress...most features can be realized using fetch, however it just easier with axios ;)

```txt
npm install axios
```

Now let's research the API that we are going to use for our restaurant data:
https://www.yelp.ca/developers/documentation/v3/authentication

https://www.yelp.com/developers/documentation/v3/business_search

Add a new folder called `api` inside the src folder. Add a file here called yelp.js

`src/api/yelp.js`
```javascript
import axios from 'axios';

export default axios.create({
  baseURL: 'https://api.yelp.com/v3/businesses',
  headers: {
    Authorization: 'Bearer YOUR_YELP_API_KEY'
  }
})
```
Now we can import our yelp component, call the /search endpoint from the user SearchBar term, and store the values from the response in a state variable.

`src/screens/SearchScreen.js`
```jsx
...
import yelp from '../api/yelp';

const SearchScreen = () => {
  const [term, setTerm] = useState('')
  const [businesses, setBusinesses] = useState([]);

  const searchAPI = async () => {
    const response = await yelp.get('/search', {
      params: {
        limit: 30,
        term: term,
        location: 'vancouver'
      }
    });

    setBusinesses(response.data.businesses);
    console.log(businesses);
  }

  return (
    <View>
      <SearchBar
        term={term}
        onTermChange={(newTerm) => setTerm(newTerm)}
        onTermSubmit={() => searchAPI()} />
    </View>
  );
}

const styles = StyleSheet.create({});

export default SearchScreen;
```

Handle the onTermSubmit call in `SearchBar.js` and add it to the user action of completing their edit of the TextInput:
```jsx
import React from 'react';
import { StyleSheet, View, TextInput } from 'react-native';
import { EvilIcons } from '@expo/vector-icons'

const SearchBar = ({ term, onTermChange, onTermSubmit }) => {
  return (
    <View style={styles.background}>
      <EvilIcons style={styles.icon} name="search" size={35} />
      <TextInput
        value={term}
        style={styles.input}
        placeholder="Search"
        onChangeText={newTerm => onTermChange(newTerm)}
        onSubmitEditing={() => onTermSubmit()} />
    </View>
  );
}

const styles = StyleSheet.create({
  background: {
    height: 50,
    borderRadius: 6,
    marginHorizontal: 15,
    flexDirection: 'row',
    alignItems: 'center'
  },
  input: {
    flex: 1
  },
  icon: {
    fontSize: 35,
    alignSelf: 'center',
    marginHorizontal: 15
  }
});

export default SearchBar;
```

When you search, you should see the raw business results in the console:

![](https://i.imgur.com/H3rW71R.png)

### Loading Businesses When App Loads
In addition to loading businesses based on the search term provided by our user, we also want to ping the api on initial screen render.
For this we will use the useEffect hook along with an empty second parameter to trigger searchAPI ONCE on initial page load.

`useEffect(() => {})`
This will run the arrow function **every time** the component is rendered

`useEffect(() => {}, [])`
This will run the arrow function **only** when the component is first rendered

`useEffect(() => {}, [value])`
This will run the arrow function **only** when the component is first rendered, **AND** when the `value` changes.

Update `src/screens/SearchScreen.js` with the appropriate useEffect hook that fires **ONCE** on initial load.

```jsx
...
  useEffect(() => {
    searchAPI();
  }, [])
  
  console.log(businesses);

  return (
...
```

<a name="8"></a>
## 8) Result List View
We will use our dynamic FlatList component to render our restaurant data to the screen. Reminder we will need a data and a renderItem prop within our FlatList, we will also add a keyExtractor for enhanced performance.

`src/screens/SearchScreen.js`

```jsx
...

return (
    <View>
      <SearchBar
        term={term}
        onTermChange={(newTerm) => setTerm(newTerm)}
        onTermSubmit={() => searchAPI()} />

      <FlatList
        data={businesses}
        keyExtractor={(business) => business.id}
        renderItem={({ item }) => {
          return <Text>{item.name}</Text>
        }}
       />
    </View>
  );
```

<img src="https://i.imgur.com/i7FoPVp.png" width="300" />

<a name="9"></a>
## 9) Grouping Restaurants by Price Category
To make our app a little more interesting, let's divide our results by the price category that comes back from the yelp api for each business.
Because the price categories range from $ (least expensive) to $$$$ (most expensive) but each category will have the same basic layout...let's create a new component to store our result lists that have been separated by price.
In `src/components/` create a component called `PriceResultList.js`.

```jsx
import React from 'react';
import { StyleSheet, View, Text } from 'react-native';

const PriceResultList = () => {
  return (
    <View>
        <Text style={styles.heading}>Price Category</Text>
    </View>
  );
}

const styles = StyleSheet.create({
heading : {
    fontSize: 16,
    fontWeight: 'bold'
}
});

export default PriceResultList;
```

Modify the `SearchScreen.js` to load 4 of these components:

```jsx
import PriceResultList from '../components/PriceResultList';
...
<PriceResultList />
<PriceResultList />
<PriceResultList />
<PriceResultList />
```

<img src="https://i.imgur.com/wP1cN3p.png" width="300" />

### Exercise
Pass a title prop to each PriceResultList to render the following:

<img src="https://i.imgur.com/zXoxuNZ.png" width='300' />

### Filter Array by Price
Add a helper method to your `SearchScreen.js` to filter the businesses collection based on each provided price category:

```jsx
const filterByPrice = (price) => {
    return businesses.filter( business => {
        return business.price === price
    });
}
```

Now pass the result of this function as a prop to each of our PriceResultList components:

```jsx
<PriceResultList results={filterByPrice('$')} title='Budget Eats'/>
<PriceResultList results={filterByPrice('$$')} title='Average'/>
<PriceResultList results={filterByPrice('$$$')} title="Gettin' Pricey"/>
<PriceResultList results={filterByPrice('$$$$')} title='Once in a Blue Moon'/>
```

### Exercise
Move the FlatList from the SearchScreen to the PriceResultList and render it as shown here:
**NOTE:** Your FlatList's should scroll horizontally ;)

<img src='https://i.imgur.com/MrWGyPZ.png' width='300' />

<a name="10"></a>
## 10) Style the Result and Add Images and Reviews
If you look back at the API documentation you can see that we have more data than just the name and price category of a restaurant. Let's add the default image, the average star rating, and the number of reviews to our collections.

Create a new component to layout what each result will look like, call it `PriceResult.js`

```jsx
import React from 'react';
import { StyleSheet, View, Text, Image } from 'react-native';

const PriceResult = ({result}) => {
  return (
    <View>
        <Image style={styles.image} source={{ uri: result.image_url}}/>
        <Text style={styles.name}>{result.name}</Text>
    </View>
  );
}

const styles = StyleSheet.create({
image : {
    width: 200,
    height: 100,
    borderRadius: 4
},
name: {
    fontWeight: 'bold'
}
});

export default PriceResult;

```

Replace the FlatList renderItem return Text element with the the PriceResult component and pass the item as a prop to our new PriceResult component:
```jsx
<PriceResult result={ item } />
```
### Exercise
Add the number of stars and reviews to each restaurant:

<img src='https://i.imgur.com/z2Fcen8.png' width='300' />


<a name="10"></a>
## 10) Remove Scroll Bar and Add Vertical Scroll for Smaller Screens

Add the following you your FlatList:
```jsx
showsHorizontalScrollIndicator={false}
```

In order to display all of the items in all 4 collections on smaller screens we can wrap our four PriceResultList components in a ScrollView within `SearchScreen.js`. This works nicely on iOS however does not full expose the content at the bottom of Android screens. Add a flex 1 prop to the parent view of `SearchScreen.js`:
```jsx
<View style={{flex: 1}} >
```
<img src='https://i.imgur.com/T2Ba67e.png' width='300' />

<a name="11"></a>
## 11) Detail View In-class Assignment

The yelp API also has a single business endpoint:

Create a detail page for every search result. You should be able to click on a search result, and see a detail page of this particular business.

Display the images associated with this business on the detail page.

You will need to use the Yelp business details endpoint.

[https://www.yelp.com/developers/documentation/v3/business](https://www.yelp.com/developers/documentation/v3/business)

![](https://i.imgur.com/wXbXWr2.gif)
