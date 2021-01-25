# Frontend Testing

<!-- Other Style Guides

  - [React](react/)
  - [CSS-in-JavaScript](css-in-javascript/)
  - [CSS & Sass](https://github.com/airbnb/css) -->
  
## High Level Philosophies

![Testing Trophy](https://testingjavascript.com/static/trophyWithLabels@2x-4d0c19a94d88ac607cc5cbeaa8f8708d.png)

### Golden Rules
- Don’t write production code for tests
    - Exceptions
        - `data-<id>` tags for Cypress
        - When possible, prefer single-use methods to be exported in a per-context utility module instead of being private component module scoped 

- Don’t repeat coverage
    - Exception: high level E2E use cases

### End to End/Functional
- Uses Cypress
- Actual API (staging) data when possible 
- Mocks when needed
- Should cover all high level customer use cases only (Cross organism)

### Unit
- Uses Testing Library
- 100% coverage for Functional, stateless utility libraries/methods
- Uses mocks

### Snapshots
- Only for MJ React Component Library
- Potentially available for object/value comparison but not for rendered markup

### Integration
- Majority of organism coverage done in Testing Library
- High-level E2E coverage in Cypress 
