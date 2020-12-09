# Style Guide Implementation via Tooling

The following tools are recommended to ease implementation of the many code style rules recommended in these guides. These tools include linting plugins which can run as part of a project's git workflow or build pipeline as well as text editor plugins that can enforce rules in real-time while coding.

## Table of Contents

  1. [Linting](#linting)
  1. [Editor](#editor)

## Linting

The following Node packages can be added to your project to enforce a majority of the rules from the style guides. These should be added as `devDependencies`.

  * eslint
  * eslint-config-airbnb
  * eslint-config-prettier
  * eslint-plugin-import
  * eslint-plugin-jsx-a11y
  * eslint-plugin-react
  * eslint-plugin-react-hooks

Even with these plugins, some adjustments and exceptions are needed to match the rules in the guides. These can be applied via an eslint configuration file. It is recommended to keep these centralized within the package.json of the project.

```
  "eslintConfig": {
    "extends": [
      "airbnb",
      "airbnb/hooks",
      "prettier"
    ],
    "env": {
      "browser": true,
      "es2020": true,
      "jest": true,
      "node": true
    },
    "parserOptions": {
      "ecmaVersion": 2020,
      "sourceType": "module",
      "ecmaFeatures": {
        "impliedStrict": true,
        "jsx": true
      }
    },
    "rules": {
      "import/prefer-default-export": "error",
      "no-use-before-define": [
        "error",
        "nofunc"
      ]
    }
```

Once the packages are installed and the configuration is set, your project may be linted with the following command

```
npx eslint --ext <extension of files to check> <directory to check, recursively>
```

Multiple extensions may be supplied. See the example below that lints all the files and folders within the `src` directory for any `.js` or `.jsx` file.

```
npx eslint --ext .js --ext .jsx src
```

This will produce a list of any rule that is not correctly implemented (if that rule is enforced from the list of plugins; not all are). Some rules can be automatically fixed at the time of linting by supplying the `--fix` option when you run the command.

```
npx eslint --fix --ext .js --ext .jsx src
```

## Editor

Once your repo includes the above linting packages and configuration, you can further ease development by including plugins in your text editor or IDE. These plugins will enforce the rules in realtime as you code.

  * VS Code
     * [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)
     * [Prettier+](https://marketplace.visualstudio.com/items?itemName=svipas.prettier-plus)
  * WebStorm
     * [Prettier format on Save](https://prettier.io/docs/en/webstorm.html)

These editor plugins use the available configuration from within your project, but additional configuration may be needed to tune the experience to your preferences (such as linting or formatting on save, for example).
