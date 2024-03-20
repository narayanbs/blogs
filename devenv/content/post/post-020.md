---
title: "React configuration with create-react-app"
date: 2022-11-30T09:39:17+05:30
publishdate: 2022-11-30
lastmod: 2022-11-30
tags: [javascript]
categories: []
---

#### Basic javascript project configuration

```
# Install prettier if it is not added
npm install --global prettier

# Install eslint or eslint_d
npm install --save-dev eslint_d

# Install eslint-prettier integration
npm install --save-dev eslint-config-prettier eslint-plugin-prettier

```

#### Add configuration

.eslintrc.json

```
{
  "parserOptions": {
    "ecmaVersion": "latest"
  },
  "env": {
    "es6": true
  },
  "extends": ["prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": ["error"]
  }
}
```

if you want more preset rules, you can install airbnb plugin and modify .eslintrc.json as follows

```
npx install-peerdeps --dev eslint-config-airbnb
```

```
    "extends": ["airbnb", "prettier"],
    "plugins": ["prettier"],
    "rules": {
        "prettier/prettier": ["error"]
    }

```

.prettierrc

```
{
"printWidth": 120,
}

```

#### React project configuration

```
# create the project
npx create-react-app <project-name> (or npx create-react-app ./  if you want to use the current folder)

# Install prettier if it is not added
npm install --global prettier

# Install eslint_d if it is not added
npm install --global eslint_d

# Install eslint react plugins
npm install --save-dev eslint-plugin-react

# Install plugin for prettier/eslint integration
npm install --save-dev eslint-config-prettier eslint-plugin-prettier
```

.eslintrc.json

```
{
  "env": {
    "browser": true,
    "es2021": true
  },
  "extends": ["eslint:recommended", "plugin:react/recommended", "plugin:prettier/recommended"],
  "overrides": [],
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module"
  },
  "rules": {
    "prettier/prettier": "error",
    "react/prop-types": "off",
    "react/react-in-jsx-scope": "off",
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx", ".ts"] }]
  }
}
```

.prettierrc

```
{
  "semi": true,
  "tabWidth": 2,
  "printWidth": 120,
  "singleQuote": true,
  "trailingComma": "none",
  "bracketSameLine": true
}
```
