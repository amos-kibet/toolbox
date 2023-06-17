## React Native

### Views

In Android and iOS development, a **view** is the basic building block of UI: a small rectangular element on the screen which can be used to display text, images, or respond to user input. Even the smallest visual elements of an app, like a line of text or a button, are kinds of views. Some kinds of views can contain other views.

![React Native Views](https://reactnative.dev/docs/assets/diagram_ios-android-views.svg)

### Native Components

These are platform-specific components, written in Kotlin or Java for Android platform, and Swift or Objective-C for iOS platform. With React Native, you can invoke these views with JavaScript using React components. At runtime, React Native creates the corresponding Android and iOS views for those components.

React Native comes with a set of ready-to-use Native Components, called **Core Components.**

React Native also lets you build your own Native Components for Android and iOS. Also, there are [community-contributed components](https://reactnative.directory/).

### [Core Components](https://reactnative.dev/docs/components-and-apis)

RN has many core components, but you will mostly work with the following Core Components:

| React Native UI Component | Android View   | iOS View         | Web Analogy             | Description                                                                                           |
| ------------------------- | -------------- | ---------------- | ----------------------- | ----------------------------------------------------------------------------------------------------- |
| `<View>`                  | `<ViewGroup>`  | `<UIView>`       | A non-scrolling `<div>` | A container that supports layout with flexbox, style, some touch handling, and accessibility controls |
| `<Text>`                  | `<TextView>`   | `<UITextView>`   | `<p>`                   | Displays, styles, and nests strings of text and even handles touch events                             |
| `<Image>`                 | `<ImageView>`  | `<UIImageView>`  | `<img>`                 | Displays different types of images                                                                    |
| `<ScrollView>`            | `<ScrollView>` | `<UIScrollView>` | `<div>`                 | A generic scrolling container that can contain multiple components and views                          |
| `<TextInput>`             | `<EditText>`   | `<UITextField>`  | `<input type="text">`   | Allows the user to enter text                                                                         |

### ReactJS Recap

Since RN runs on React, it's important to highlight some key core components of React:

- components
- JSX
- props
- state

**Components**

- Function Components

```js
import React from 'react';
import {Text} from 'react-native;

const Cat = () => {
    return (
        <Text>Hello, I am your cat!</Text>;
    )
}

export default Cat;
```

- Class Components

```js
import React, {Component} from 'react';
import {Text} from 'react-native';

class Cat extends Component {
    render() {
        return (
            <Text>Hello, I am your cat!</Text>;
        )
    }
}

export default Cat;
```

- React lets you nest core components inside each other to create new components. For example, you can create a function component that returns a view with nested components:

```js
import React from "react";
import { Text, View } from "react-native";

const MyCustomComponent = () => {
  return (
    <View>
      <Text>Text 1</Text>
      <Text>Text 2</Text>
      <Text>Text 3</Text>
      <Text>Text 4</Text>
    </View>
  );
};

export default MyCustomComponent;
```

- You can render the `MyCustomComponent` component above multiple times and in multiple places without repeating your code, by using `<MyCustomComponent>`.

**JSX**

- A syntax that lets you write elements inside JavaScript.
- Because JSX is JavaScript, you can use variables inside it, you just embed an already declared variable in JSX with curly braces;

```js
import React from "react";
import { Text } from "react-native";

const Cat = () => {
  // declare variable; name
  const name = "Maru";
  // interpolate variable inside JSX
  return <Text>Hello, I am {name}!</Text>;
};

export default Cat;
```

- In JSX, JavaScript values are referenced with `{}`. This is handy if you are passing something other than a string as props, like an array or number: `<Cat food={["fish", "kibble"]} age={2} />`. However, JS objects are also denoted with curly braces: `{width: 200, height: 200}`. Therefore, to pass a JS object in JSX, you must wrap the object in another pair of curly braces: `{{width: 200, height: 200}}`
- Any JavaScript expression will work between curly braces, including function calls like `{getFullName("Rum", "Tum", "Tugger")}`
- You can think of curly braces as creating a portal into JS functionality in your JSX.
- Because JSX is included in the React library, it won’t work if you don’t have import React from 'react' at the top of your file!

**Props**

- Short for "properties". Props let you customize React components, for example, here you pass each `<Cat>` a different `name`:

```js
import React from 'react';
import {Text, View} from 'react-native';

type CatProps {
    name: string;
}

const Cat = (props: CatProps) => {
    return (
        <View>
            <Text>Hello, I am your cat!</Text>
        </View>
    )
}

const Cafe = () => {
    return (
        <View>
            <Cat name="Maru" />
            <Cat name="Jellylorum" />
            <Cat name="Spot" />
        </View>
    )
}
```

- Other examples of props are: `source` for images, `style`, etc.

**State**

- While you can think of props as arguments you use to configure how components render, **state** is like a component’s personal data storage.
- State is useful for handling data that changes over time or that comes from user interaction.
- State gives your components memory!

-**NOTE:** As a general rule, use props to configure a component when it renders. Use state to keep track of any component data that you expect to change over time.

- You can add state to a component by calling React's **`useState Hook`**.
- A Hook is a kind of function that lets you “hook into” React features. For example, `useState` is a Hook that lets you add state to function components.

```js
import React, { useState } from "react";
import { Button, Text, View } from "react-native";

type CatProps = {
  name: string,
};

const Cat = (props: CatProps) => {
  // declare a `isHungry` state with a default value of `true`
  const [isHungry, setIsHungry] = useState(true);

  return (
    <View>
      <Text>
        I am {props.name}, and I am {isHungry ? "hungry" : "full"}!
      </Text>
      <Button
        onPress={() => {
          setIsHungry(false);
        }}
        disabled={!isHungry}
        title={isHungry ? "Pour me some milk, please!" : "Thank you!"}
      />
    </View>
  );
};

const Cafe = () => {
  return (
    <>
      <Cat name="Munkustrap" />
      <Cat name="Spot" />
    </>
  );
};

export default Cafe;
```

- You can use `useState` to track any kind of data: `strings`, `numbers`, `Booleans`, `arrays`, `objects`. For example, you can track the number of times a cat has been petted with `const [timesPetted, setTimesPetted] = useState(0)`.
- Calling `useState` does two things:
  - it creates a “state variable” with an initial value—in this case the state variable is `isHungry` and its initial value is `true`,
  - it creates a function to set that state variable’s `value—setIsHungry`.
- It doesn’t matter what names you use. But it can be handy to think of the pattern as `[<getter>, <setter>] = useState(<initialValue>)`.
