---
path: "/advanced-styling"
title: "Advanced Styling"
order: "4A"
section: "Advanced"
description: "Learn React Native With Youssouf El Azizi"
---

I have used almost all CSS-in-JS 🤨 approaches to style my React Naive application and same as React for the web by the, My conclusion was to use Tailwind CSS for web and restyle for React Native 😎.

Restyle library by Shopify provides a type-enforced system for building UI components in React Native with TypeScript. It's a library for building UI libraries, with themability as the core focus.

This library assumes that the UI is built upon a design system that (at the very least) defines a set of colors and spacing constants that lays as a foundation. While the library acknowledges that there can be exceptions to the system by allowing any style to be overridden, it keeps the developer most productive when one-off values are kept to a minimum.

![Restyle](https://user-images.githubusercontent.com/688415/75268245-91084b80-57f7-11ea-905b-2a9046aa5ca3.gif)

### Install restyle deps

```bash
yarn add @shopify/restyle
```

### Defining Your Theme

Any project using this library should have a global theme object. It specifies set values for spacing, colors, breakpoints, and more. These values are made available to Restyle components, so that you can for example write backgroundColor="cardPrimary" to use the named color from your theme. In fact, TypeScript enforces the backgroundColor property to only accept colors that have been defined in your theme, and autocompletes values for you in a modern editor.

Create a folder `ui/theme/index.tsx` under `src` and defined the theme 👇

```jsx
import { createTheme } from "@shopify/restyle";

const palette = {
  purpleLight: "#8C6FF7",
  purplePrimary: "#5A31F4",
  purpleDark: "#3F22AB",

  greenLight: "#56DCBA",
  greenPrimary: "#0ECD9D",
  greenDark: "#0A906E",

  black: "#0B0B0B",
  white: "#F0F2F3",
};

const theme = createTheme({
  colors: {
    mainBackground: palette.white,
    cardPrimaryBackground: palette.purplePrimary,
  },
  spacing: {
    s: 8,
    m: 16,
    l: 24,
    xl: 40,
  },
  breakpoints: {
    phone: 0,
    tablet: 768,
  },
});

export type Theme = typeof theme;
export default theme;
```

Add `ThemeProvider` to your root tree like the following :

```jsx
import { ThemeProvider } from "@shopify/restyle";
import theme from "ui/theme";

const App = () => (
  <ThemeProvider theme={theme}>{/* Rest of the app */}</ThemeProvider>
);
```

### Create Common Components

Create Custom `View` and `Text` components using restyle [Predefined Components](https://github.com/Shopify/restyle#predefined-components)

```jsx
// ui/View.tsx
import {createBox} from '@shopify/restyle';
import {Theme} from './theme';

export const View = createBox<Theme>();
```

```jsx
// ui/Text.tsx
import {createText} from '@shopify/restyle';
import {Theme} from './theme';

export const Text = createText<Theme>();
```

### Variants

Restyle also support Variants to help you control your design system.

A variant is a form of Restyle function that maps a prop into multiple other props to use with Restyle functions. A variant needs to always map to a key in the theme.

```jsx
// In your theme
const theme = createTheme({
  ...,
  textVariants: {
    header: {
      lineHeight: 42.5,
      color: 'black',
    },
    subheader: {
      fontWeight: '600',
      fontSize: 28,
      lineHeight: 36,
      color: 'black',
    },
    body: {
      fontSize: 16,
      lineHeight: 24,
      color: 'black',
    },
  },
});

// In a component
<Text variant="header">Header</Text>

```

### Custom Components

restyle come with a lot of helper function to help you create Custom component based on your theme.
here is an example of A Generic button

```jsx
// ui/Button.tsx

import React from 'react';
import {ActivityIndicator, TouchableOpacity} from 'react-native';
import {
  useRestyle,
  spacing,
  border,
  backgroundColor,
  SpacingProps,
  BorderProps,
  BackgroundColorProps,
  VariantProps,
  createRestyleComponent,
  createVariant,
} from '@shopify/restyle';

import {Text} from './Text';
import {View} from './View';
import {Theme} from './theme';

const buttonVariant = createVariant({themeKey: 'buttonVariants'});
const ButtonContainer = createRestyleComponent<
  VariantProps<Theme, 'buttonVariants'> & React.ComponentProps<typeof View>,
  Theme
>([buttonVariant], View);

const restyleFunctions = [
  buttonVariant as any,
  spacing,
  border,
  backgroundColor,
];

type Props = SpacingProps<Theme> &
  VariantProps<Theme, 'buttonVariants'> &
  BorderProps<Theme> &
  BackgroundColorProps<Theme> & {
    onPress: () => void;
    label?: string;
    outline?: boolean;
    loading?: boolean;
    disabled?: boolean;

    children?: React.ReactNode;
  };

export const Button = ({
  onPress,
  label,
  disabled = false,
  loading = false,
  variant = 'primary',
  children,
  ...rest
}: Props) => {
  const props = useRestyle(restyleFunctions, {...rest, variant});
  const textVariant = 'button_' + variant;

  return (
    <TouchableOpacity onPress={onPress} disabled={disabled}>
      <ButtonContainer
        opacity={disabled ? 0.4 : 1}
        borderRadius={12}
        flexDirection="row"
        paddingVertical="m"
        paddingHorizontal="xl"
        marginVertical="s"
        justifyContent="center"
        alignItems="center"
        {...props}>
        {loading ? (
          <ActivityIndicator size="small" color="#FFF" />
        ) : (
          <>
            {children ? (
              children
            ) : (
              <Text
                variant={textVariant as Partial<keyof Theme['textVariants']>}>
                {label}
              </Text>
            )}
          </>
        )}
      </ButtonContainer>
    </TouchableOpacity>
  );
};

```

## 🧑‍💻 Exercise

- Add Restyle to your project and refactor the login screen using the new Approach.
  - Create a custom `Input` component
  - Create a Generic `Button` component with two variant `outline` and `primary`

## HomeWork

Create All static screens using restyle `Login`, `Home`, `Profile` , `AddToDo` screens.

## Helpful Links

- [Restyle](https://github.com/Shopify/restyle)