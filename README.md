# <img src="https://user-images.githubusercontent.com/38172/37561065-eaf948f0-2a3c-11e8-8cfe-f1922bf42df0.png" height="110px" />

`graphql-css` is a blazing fast CSS-in-GQL™ library.

[![npm version][version-badge]][npm]
[![npm downloads][downloads-badge]][npm]
[![gzip size][size-badge]][size]
[![MIT License][license-badge]][license]
[![PRs Welcome][prs-badge]][prs]
![Blazing Fast][fast-badge]
![Modern][modern-badge]
![Enterprise Grade][enterprise-badge]


## Installation

```bash
npm install graphql-css
# or
yarn add graphql-css
```

#### Dependencies

`graphql-css` has three peer dependencies:

*   `glamor`
*   `graphql`
*   `graphql-css`

It's likely you already have these installed, but in the case you don't you just need to run `npm install <package>` or `yarn add <package>` to install them.

## Quick start

```jsx
import gqlCSS from "graphql-css";
import styles from "your-style-guide";

const textStyles = gql`
    {
        typography {
            h2
        }
        marginLeft: spacing {
            xl
        }
        color: colors {
            green
        }
    }
`;

const Text = graphqlCSS(styles)(textStyles);

const App = () => <Text>This is a styled text</Text>;
```

Playground: https://codesandbox.io/s/jq22wyqm3

## API

`graphql-css` exports a few different components and helper functions:

*   `gqlCSS()`: a helper function to build glamorous components
*   `<GqlCSS>`: a component that displays the queried styles
*   `<GqlCSSProvider>`: a provider component that broadcasts the styles object to any child `<GqlCSS>` component
*   `withGqlCSS`: a High-Order Component that injects styles in the components
*   `<WithGqlCSS>`: a function-as-a-child component that does the same thing as the HOC but it's cooler

#### gqlCSS

`gqlCSS` needs to be initialised with the styles from the styleguide in a JSON format (check examples folder for a detailed example).

It works with the following format `gqlCSS(styles)(query, element)`:

| Arg    | Type                | Default | Definition                                                          |
| ------ | ------------------- | ------- | ------------------------------------------------------------------- |
| styles | object              |         | The styleguide object with all the rules                            |
| query  | gql                 |         | The gql query to get the styles                                     |
| component    | string \|\| node \|\| boolean | "div"   | HTML element or React component to be displayed. If set to false only styles are returned. |

Here's how you can use it:

```jsx
const Text = gqlCSS(styles)(query);
...
<Text>This is a styled text<Text>
```

alternatively you can also initialise it separately and reuse it:

```jsx
const getStyles = gqlCSS(styles);
...
const ComponentOne = getStyles(queryOne);
const ComponentTwo = getStyles(queryTwo);
```

`gqlCSS` returns a `glamorous` component by default, which means it accepts everything that `glamorous` supports such as additional styling through the `css` prop or changing the HTML element used.

```jsx
const Component = gqlCSS(styles)(query);
const ComponentH1 = gqlCSS(styles)(query, "h1");
...
<Component css={{ marginTop: 10 }} />
<ComponentH1 />
```

If the `component` argument is set to false it'll only return the styles object so it can be used with other libraries or just inline styles.

```jsx
const styles = gqlCSS(styles)(query, false);
...
<div styles={styles}>Inline styled text</div>
```

#### GqlCSS

`<GqlCSS>` component allows for a more declarative API and accepts these props:

| Prop   | Type   | Default | Definition                               |
| ------ | ------ | ------- | ---------------------------------------- |
| styles | object |         | The styleguide object with all the rules |
| query  | gql    |         | The gql query to get the styles          |
| component    | string \|\| node | "div"   | HTML element or React component  to be displayed                 |

All the remaining props are passed to the generated component so you can still use `glamorous` API. Here are some examples:

```jsx
...
<GqlCSS styles={styles} query={query}>This is a styled text</GqlCSS>
<GqlCSS styles={styles} query={queryH1} component="h1">This is a styled H1 heading</GqlCSS>
<GqlCSS styles={styles} query={queryH1} css={{ marginBottom: 10 }}>This is a custom styled text</GqlCSS>
...
```

#### GqlCSSProvider

The `<GqlCSSProvider>` component allows to pass down the styles definition to any `<GqlCSS>` component that exists down the tree. Ideally, you'd use `<GqlCSSProvider>` in the root of your application.

```jsx
<GqlCSSProvider styles={styles}>
    <App />
</GqlCSSProvider>

// Somewhere inside your App
<GqlCSS query={h1Styles} css={{ marginTop: 30 }}>
    Using provider
</GqlCSS>
<div>
    <div>
        <span>
            <GqlCSS query={h2Styles}>Deep nested child using provider</GqlCSS>
        </span>
    </div>
</div>
```

#### withGqlCSS

`withGqlCSS` is a HOC that injects the styles to your existing component through the `gqlStyles` prop.

```jsx
// In component.js
const Component = ({ gqlStyles }) => <div styles={gqlStyles}>...</div>;

export default withGqlCSS(styles, query)(Component);
```

To avoid always adding the styles you can initialise the existing HOC with your styleguide and then reuse it in the code.

```jsx
// in your HOC file
import styles from "your-style-guide";
import { withGqlCSS } from "graphql-css";

export const myHOC = (query) => withGqlCSS(styles, query);

// in your component file
export myHOC(query)(Component);
```

#### WithGqlCSS

`<WithGqlCSS>` works similarly to `withGqlCSS` but uses the function-as-a-child aka render props pattern.

```jsx
<WithGqlCSS styles={styles} query={h2Styles}>
    {({ gqlStyles }) => <div style={gqlStyles}>Render props component</div>}
</WithGqlCSS>
```

## Styles object

The styles object is a valid JSON object that is used to define the styleguide of your project. Usually it includes definitions for colors, spacing, typography, etc.

```js
const styles = {
    typography: {
        scale: {
            s: "12px",
            base: "16px",
            m: "24px",
            l: "36px",
            xl: "54px",
            xxl: "81px",
        },
        weight: {
            thin: 300,
            normal: 400,
            bold: 700,
            bolder: 900,
        },
    },
    spacing: {
        s: "4px",
        base: "8px",
        m: "16px",
        l: "24px",
        xl: "32px",
        xxl: "40px",
    },
    colors: {
        blue: "blue",
        green: "green",
        red: "red",
    },
};
```

This is completely up to you and one of the big advantages of using `graphql-css` as you can adapt it to your needs. As long as the styles and the queries match their structure, there shouldn't be much problem.

## Building the GraphQL query

The GraphQL query follows the structure of the styles object with a few particular details. When building the query you need to alias the values you're getting from the style guide to the correspondent CSS property. Here's a quick example:

```js
{
    typography {
        fontSize: scale {
            xl
        }
        fontWeight: weight {
            bold
        }
    }
}
```

This also means that you can reuse the same query by using different alias:

```js
{
    marginLeft: spacing {
        l
    }
    paddingTop: spacing {
        xl
    }
}
```

Because _This is just GraphQL™_, you can also create fragments that can then be included in other queries:

```js
const h1Styles = gql`
    fragment H1 on Styles {
        typography {
            fontSize: scale {
                xl
            }
            fontWeight: weight {
                bold
            }
        }
    }
`

const otherH1Styles = gql`
    ${h1Styles}
    {
        ...H1
        color: colors {
            blue
        }
    }
`;
```

This is a powerful pattern that avoids lots of repetitions and allows for a bigger separation of concerns.

## Developing

You can use `yarn run dev` and `yarn run dev:server` to run the examples and test this library before using it in your project.

## Contributing

Please follow our [contributing guidelines](https://github.com/braposo/graphql-css/blob/master/CONTRIBUTING.md).

## License

[MIT](https://github.com/davidgomes/graphql-css/blob/master/LICENSE)

[npm]: https://www.npmjs.com/package/graphql-css
[license]: https://github.com/braposo/graphql-css/blob/master/LICENSE
[prs]: http://makeapullrequest.com
[size]: https://unpkg.com/graphql-css/dist/graphql-css.min.js

[version-badge]: https://img.shields.io/npm/v/graphql-css.svg?style=flat-square
[downloads-badge]: https://img.shields.io/npm/dm/graphql-css.svg?style=flat-square
[license-badge]: https://img.shields.io/npm/l/graphql-css.svg?style=flat-square
[fast-badge]: https://img.shields.io/badge/🔥-Blazing%20Fast-red.svg?style=flat-square
[modern-badge]: https://img.shields.io/badge/💎-Modern-44aadd.svg?style=flat-square
[enterprise-badge]: https://img.shields.io/badge/🏢-Enterprise%20Grade-999999.svg?style=flat-square
[prs-badge]: https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square
[size-badge]: http://img.badgesize.io/https://unpkg.com/graphql-css/dist/graphql-css.min.js?compression=gzip&style=flat-square