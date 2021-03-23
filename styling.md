# Styling and CSS Guide (Draft)

There are a myriad of techniques and conventions which may be used to style a React app. While it is useful to understand the differences in how each works and which advantages and disadvantages each provide, we have opted to only adopt a small handful of patterns for our codebase. This reduces the learning curve and helps to maintain consistency.

## Table of Contents

  1. [Mailjet React Components](#mailjet-react-components)
  1. [Styled Component](#styled-components)
  1. [Style Hierarchy](#style-hierarchy)

## Mailjet React Components

Pathwire's in-house React component UI lib is [Mailjet React Component library](https://storybook.mailjet.tech/), shortened as MJRC. All design mock-ups are typically created based on the components available in this library.

## Styled Components

MJRC uses the CSS styling framework, [Styled Components](https://styled-components.com/) to style its components. Likewise, all Pathwire React apps implement this library for custom or extended styling. This should be the primary method for applying styles to app components.

## Style Hierarchy

* Whenever possible, prefer using the default styling provided by the components in MJRC.
* When layout changes are needed, use MJRC to alter grid and margin/padding spacing (TODO: Provide detailed examples)
* When styling custom [atoms](https://atomicdesign.bradfrost.com/chapter-2/#atoms) or [molecules](https://atomicdesign.bradfrost.com/chapter-2/#molecules) tier components, prefer to create a reusable extended styled component, located in a shared component module/folder.
* Styling non-reusable [molecules](https://atomicdesign.bradfrost.com/chapter-2/#molecules) or [organisms](https://atomicdesign.bradfrost.com/chapter-2/#organisms) should be handled within a styled component in the feature-specific component module or folder.

## Styled Component Guidelines

* Use a single, module scoped, feature-component-specific styled component to encapsulate all styling

> Why? This pattern creates fewer styled component abstractions which results in cleaner DOM and faster React VDOM diffing. It also makes it easier to identify which element maps to the component when looking in the rendered source code. Finally, it results in less code that is easier to reason able where a style is being applied.

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
    
    label.MetricsToggles--label {
      margin-right: 16px;
    }
`;

export function MetricsTogglePanel() {
    return (
        <StyledMetricsToggles>
            <label classname="MetricsToggles--label">Metrics:</label>

            <CheckboxRadioGroup ... />
        </StyledMetricsToggles>
    );
}
```

* To style child elements within the feature-level styled component, create css classes with minimal additional specificity

> Why? Additional classes are needed in lieu of not creating extraneous stlyed components. Adding specificity of 1 or 2 levels gives extra context for the effected child elements while reducing the occurance of selector collision. Keeping specificity at a minimum ensures performance.  

```jsx
// bad - Unnecessary additional component
const StyledLabel = styled.div`
    margin-right: 16px;
`;

const StyledMetricsToggles = styled.div`
    // ok
    .MetricsToggles--label {
      margin-right: 16px;
    }
    
    // best
    label.MetricsToggles--label {
      margin-right: 16px;
    }
`;
```

* Use BEM-like naming conventions of TitleCase component name followed by two dashes and a semantic element-identifier

> Why? BEM is a commonly practiced CSS naming convention that should be familiar among front-end developers. Using BEM significantly reduces the likelyhood of classname/style collisions while providing extra context about the purpose and targeted element of a selector.

```jsx
const StyledMetricsToggles = styled.div`
    // worst - too generic and likely to collide
    .labels {
      margin-right: 16px;
    }
    
    // bad - doesn't abide by BEM
    .metric-labels {
      margin-right: 16px;
    }
    
    // good
    label.MetricsToggles--label {
      margin-right: 16px;
    }
`;
```

* It is recommended to use the ["Component Selector" pattern](https://styled-components.com/docs/advanced#referring-to-other-components) when overriding the styling of a child component which is already a styled component.

> Why? This pattern clearly identifies that a styled component is being modified. It is also a simple pattern that provides a very efficient and effective method of selecting the appropriate element to change.

```jsx
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
