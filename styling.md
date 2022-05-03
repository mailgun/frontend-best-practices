# Styling and CSS Guide (Draft)

There are a myriad of techniques and conventions which may be used to style a React app. While it is useful to understand the differences in how each works and which advantages and disadvantages each provide, we have opted to only adopt a small handful of patterns for our codebase. This reduces the learning curve and helps to maintain consistency.

## Table of Contents

  1. [Mailjet React Components](#mailjet-react-components)
  1. [Styled Components](#styled-components)
  1. [Style Hierarchy](#style-hierarchy)

## Mailjet React Components

Pathwire's in-house React component UI lib is [Mailjet React Component library](https://storybook.mailjet.tech/), shortened as MJRC. All design mock-ups are typically created based on the components available in this library.

## Styled Components

MJRC uses the CSS styling framework, [Styled Components](https://styled-components.com/) to style its components. Likewise, all Pathwire React apps implement this library for custom or extended styling. This should be the primary method for applying styles to app components.

## Style Hierarchy

* **Whenever possible**, use the default styling provided by the components in MJRC.
* **When only layout changes are needed**, MJRC's Div component can be used as a layout component, wrapping one or more children components to apply spacing or sizing. Div supports params to adjust borders, display, margins, paddings, and sizes. A common technique is to make use of the `di=flex` prop to adjust the layout for children components. This is unfortunately an undocumented feature, but you can explore the [source code](https://gitlab.mailforce.tech/front/react-components/-/tree/develop/src/helpers/injectedStyle) to see this params in more detail.
* **When more than just layout changes are needed**, prefer to create a reusable [atom](https://atomicdesign.bradfrost.com/chapter-2/#atoms) or [molecule](https://atomicdesign.bradfrost.com/chapter-2/#molecules) tier component which encapsulates the various style options and accepts a `variant` prop to determine which styling is used. These should be located in a shared component module/folder.
* **Styling unique or single-use components** at the [molecule](https://atomicdesign.bradfrost.com/chapter-2/#molecules) or [organism](https://atomicdesign.bradfrost.com/chapter-2/#organisms) tier should be handled within a single styled component in the feature-specific component module or folder. See more info below.

## Styled Component Guidelines

* For unique or single-use components, use a single, module-scoped, feature component-specific, styled component to encapsulate all styling.

> Why? This pattern creates fewer styled component abstractions which results in cleaner DOM and faster React VDOM diffing. It also makes it easier to identify which element maps to the component when looking in the rendered source code. Finally, it results in less code that is easier to reason able where a style is being applied.

* Name your module scoped styled component with the format `Styled<NameOfComponent>`

> Why? This makes it very convenient to identify the original component markup within the rendered DOM. 

```jsx
// bad - Name doesn't provide much context
const StyledContainer = styled.div`
    display: flex;
    padding: 12px 32px;
`;

// bad - Too many additional styled components
const StyledLabel = styled.div`
    margin-right: 16px;
`;

export function MetricsToggles() {
    return (
        <StyledContainer>
            <StyledLabel>Metrics:</StyledLabel>

            <CheckboxRadioGroup ... />
        </StyledContainer>
    );
}

// good
const StyledMetricsToggles = styled.div`
    display: flex;
    padding: 12px 32px;
    
    label.MetricsToggles-label {
      margin-right: 16px;
    }
`;

export function MetricsTogglePanel() {
    return (
        <StyledMetricsToggles>
            <label classname="MetricsToggles-label">Metrics:</label>

            <CheckboxRadioGroup ... />
        </StyledMetricsToggles>
    );
}
```

* To style native child elements within the feature-level styled component, create css classes with minimal additional specificity

> Why? Additional classes are needed in lieu of not creating extraneous stlyed components. Adding specificity of 1 or 2 levels gives extra context for the effected child elements while reducing the occurance of selector collision. Keeping specificity at a minimum ensures performance.  

```jsx
// bad - Unnecessary additional component
const StyledLabel = styled.div`
    margin-right: 16px;
`;

const StyledMetricsToggles = styled.div`
    // ok
    .MetricsToggles-label {
      margin-right: 16px;
    }
    
    // best
    label.MetricsToggles-label {
      margin-right: 16px;
    }
`;
```

* For native child element classes, use naming conventions of `<NameOfComponent>-<semantic element-identifier>`, ex. `MetricsToggle-label`

> Why? By grouping these styling into a single component, the number of produced classes is reduced, but the probability of an internal style collision increases. This naming convention reduces the likelyhood of collisions while providing extra context about the purpose and targeted element of a selector. The rendered class names are also useful during dev-time debugging (the names are minified in production).

```jsx
const StyledMetricsToggles = styled.div`
    // worst - too generic and likely to collide
    .labels {
      margin-right: 16px;
    }
    
    // bad - doesn't reference parent component name
    .metric-labels {
      margin-right: 16px;
    }
    
    // good
    label.MetricsToggles-label {
      margin-right: 16px;
    }
`;
```

* In the rare case that a parent component needs to override the styling from a child MJRC component, it is recommended to use the ["Component Selector" pattern](https://styled-components.com/docs/advanced#referring-to-other-components).

> Why? This pattern clearly identifies that a styled component is being modified. It keeps all the relvant styling in a single location. And it is a simple pattern that provides a very efficient and effective method of selecting the appropriate element to change.

```jsx
// Link is a styled component from the Mailjet React Component lib
const Link = styled.a`
  color: palevioletred;
`;

const Icon = styled.svg`
  flex: none;
  transition: fill 0.25s;
  width: 48px;
  height: 48px;

  // good - used ${MyComponent} syntax
  ${Link}:hover & {
    fill: rebeccapurple;
  }
`;
```

## Brainstorming / Workspace / Notes
* Present a limited, concise set of styling principals and patterns to reduce unique generated classes/sytled components and acheive desired look in a clear, simple manner.
* Atomic Design principles as applicable to Styling
* Layout components (Div and Flex)
* All components should support className
* Helper/mixin classes for common cases: pointer, z-index
