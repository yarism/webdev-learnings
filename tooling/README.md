# JavaScript Tooling & Build Process

Understanding modern JavaScript development tools and build processes.

## Table of Contents
- [Bundlers](#bundlers)
- [Transpiling & Polyfills](#transpiling--polyfills)
- [Monorepos](#monorepos)
- [Package Managers](#package-managers)
- [Development Tools](#development-tools)
- [Best Practices](#best-practices)

---

## Bundlers

Bundlers combine multiple JavaScript files and assets into optimized bundles for production.

### Why Use Bundlers?

- **Code splitting**: Split code into smaller chunks
- **Tree shaking**: Remove unused code
- **Minification**: Reduce file size
- **Asset management**: Handle CSS, images, fonts
- **Development server**: Hot reload during development
- **Module resolution**: Handle npm packages

### Webpack

The most popular and configurable bundler.

**Installation:**
```bash
npm install --save-dev webpack webpack-cli webpack-dev-server
```

**webpack.config.js:**
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',
  
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[contenthash].js',
    clean: true
  },
  
  module: {
    rules: [
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use: {
          loader: 'babel-loader'
        }
      },
      {
        test: /\.css$/,
        use: ['style-loader', 'css-loader']
      },
      {
        test: /\.(png|svg|jpg|jpeg|gif)$/i,
        type: 'asset/resource'
      }
    ]
  },
  
  plugins: [
    new HtmlWebpackPlugin({
      template: './src/index.html'
    })
  ],
  
  optimization: {
    splitChunks: {
      chunks: 'all'
    }
  },
  
  devServer: {
    static: './dist',
    hot: true,
    port: 3000
  }
};
```

**Code Splitting:**
```javascript
const button = document.querySelector('#load-chart');

button.addEventListener('click', async () => {
  const { Chart } = await import('./chart.js');
  const chart = new Chart();
  chart.render();
});
```

**package.json scripts:**
```json
{
  "scripts": {
    "dev": "webpack serve --mode development",
    "build": "webpack --mode production",
    "build:dev": "webpack --mode development"
  }
}
```

### Vite

Modern, fast bundler with instant server start.

**Installation:**
```bash
npm create vite@latest my-app
cd my-app
npm install
```

**vite.config.js:**
```javascript
import { defineConfig } from 'vite';

export default defineConfig({
  server: {
    port: 3000,
    open: true
  },
  
  build: {
    outDir: 'dist',
    sourcemap: true,
    rollupOptions: {
      output: {
        manualChunks: {
          vendor: ['react', 'react-dom']
        }
      }
    }
  },
  
  optimizeDeps: {
    include: ['lodash-es']
  }
});
```

**Why Vite?**
- Lightning-fast HMR (Hot Module Replacement)
- Native ES modules in development
- Optimized production builds with Rollup
- Out-of-the-box TypeScript/JSX support

**package.json:**
```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "preview": "vite preview"
  }
}
```

### Rollup

Focused on ES modules and libraries.

**Installation:**
```bash
npm install --save-dev rollup
```

**rollup.config.js:**
```javascript
import resolve from '@rollup/plugin-node-resolve';
import commonjs from '@rollup/plugin-commonjs';
import { terser } from 'rollup-plugin-terser';

export default {
  input: 'src/index.js',
  
  output: [
    {
      file: 'dist/bundle.cjs.js',
      format: 'cjs'
    },
    {
      file: 'dist/bundle.esm.js',
      format: 'esm'
    },
    {
      file: 'dist/bundle.umd.js',
      format: 'umd',
      name: 'MyLibrary'
    }
  ],
  
  plugins: [
    resolve(),
    commonjs(),
    terser()
  ]
};
```

**Best for:**
- Building libraries
- Tree-shaking optimization
- ES module output

### Parcel

Zero-configuration bundler.

**Installation:**
```bash
npm install --save-dev parcel
```

**package.json:**
```json
{
  "scripts": {
    "dev": "parcel src/index.html",
    "build": "parcel build src/index.html"
  }
}
```

**Features:**
- No configuration needed
- Automatic asset handling
- Built-in dev server
- Fast builds with caching

### Comparison

| Feature | Webpack | Vite | Rollup | Parcel |
|---------|---------|------|--------|--------|
| **Configuration** | Complex | Simple | Medium | Zero |
| **Dev Speed** | Slow | Very Fast | N/A | Fast |
| **Build Speed** | Medium | Fast | Fast | Fast |
| **Tree Shaking** | Yes | Yes | Excellent | Yes |
| **Best For** | Large apps | Modern apps | Libraries | Quick prototypes |
| **Learning Curve** | Steep | Easy | Medium | Very Easy |

---

## Transpiling & Polyfills

Converting modern JavaScript to older versions for browser compatibility.

### Babel

The most popular JavaScript transpiler.

**Installation:**
```bash
npm install --save-dev @babel/core @babel/cli @babel/preset-env
```

**babel.config.js:**
```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: {
        browsers: ['> 1%', 'last 2 versions', 'not dead']
      },
      useBuiltIns: 'usage',
      corejs: 3
    }]
  ],
  
  plugins: [
    '@babel/plugin-proposal-class-properties',
    '@babel/plugin-proposal-optional-chaining'
  ]
};
```

**What Babel Does:**

**Before (Modern JS):**
```javascript
const greet = (name) => `Hello, ${name}!`;

class Person {
  name = 'Alice';
  
  greet() {
    console.log(greet(this.name));
  }
}

const user = { name: 'Bob' };
console.log(user?.name ?? 'Guest');
```

**After (Transpiled):**
```javascript
"use strict";

var greet = function greet(name) {
  return "Hello, ".concat(name, "!");
};

var Person = function() {
  function Person() {
    this.name = 'Alice';
  }
  
  Person.prototype.greet = function greet() {
    console.log(greet(this.name));
  };
  
  return Person;
}();

var user = { name: 'Bob' };
console.log((user === null || user === void 0 ? void 0 : user.name) !== null && user.name !== void 0 ? user.name : 'Guest');
```

### Browserslist

Define target browsers.

**.browserslistrc:**
```
> 0.5%
last 2 versions
not dead
not IE 11
```

**Or in package.json:**
```json
{
  "browserslist": [
    "> 0.5%",
    "last 2 versions",
    "not dead"
  ]
}
```

### Core-js (Polyfills)

Provides polyfills for modern JavaScript features.

**Installation:**
```bash
npm install core-js
```

**Usage with Babel:**
```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      useBuiltIns: 'usage',
      corejs: 3
    }]
  ]
};
```

**Manual Import:**
```javascript
import 'core-js/stable';
import 'regenerator-runtime/runtime';

const result = [1, 2, 3].includes(2);
const promise = Promise.resolve('Hello');
```

### SWC

Extremely fast Rust-based transpiler (alternative to Babel).

**Installation:**
```bash
npm install --save-dev @swc/core @swc/cli
```

**.swcrc:**
```json
{
  "jsc": {
    "parser": {
      "syntax": "ecmascript",
      "jsx": true
    },
    "target": "es5"
  },
  "module": {
    "type": "commonjs"
  }
}
```

**Much faster than Babel** (~20-70x faster).

---

## Monorepos

Managing multiple related packages in a single repository.

### What is a Monorepo?

A single repository containing multiple projects/packages.

**Benefits:**
- Shared code and dependencies
- Consistent tooling across projects
- Easy cross-project refactoring
- Atomic commits across projects
- Simplified dependency management

**Drawbacks:**
- Larger repository size
- More complex CI/CD
- Learning curve for tooling

### When to Use Monorepos

**Good Use Cases:**
- Multiple apps sharing common libraries
- Component libraries with documentation site
- Microservices in the same ecosystem
- Design system with multiple packages

**Not Recommended:**
- Unrelated projects
- Small projects with no shared code
- Teams with very different release cycles

### Structure Example

```
my-monorepo/
├── packages/
│   ├── core/
│   │   ├── src/
│   │   └── package.json
│   ├── utils/
│   │   ├── src/
│   │   └── package.json
│   ├── web-app/
│   │   ├── src/
│   │   └── package.json
│   └── mobile-app/
│       ├── src/
│       └── package.json
├── package.json
└── pnpm-workspace.yaml
```

### Turborepo

Modern build system for monorepos.

**Installation:**
```bash
npx create-turbo@latest my-monorepo
```

**turbo.json:**
```json
{
  "$schema": "https://turbo.build/schema.json",
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": []
    },
    "lint": {
      "outputs": []
    },
    "dev": {
      "cache": false
    }
  }
}
```

**Features:**
- Incremental builds
- Remote caching
- Parallel execution
- Task pipelines

**package.json:**
```json
{
  "scripts": {
    "build": "turbo run build",
    "dev": "turbo run dev",
    "test": "turbo run test",
    "lint": "turbo run lint"
  }
}
```

### pnpm Workspaces

Efficient package manager with workspace support.

**Installation:**
```bash
npm install -g pnpm
```

**pnpm-workspace.yaml:**
```yaml
packages:
  - 'packages/*'
  - 'apps/*'
```

**Root package.json:**
```json
{
  "name": "my-monorepo",
  "private": true,
  "scripts": {
    "build": "pnpm -r build",
    "dev": "pnpm -r --parallel dev",
    "test": "pnpm -r test"
  }
}
```

**Installing dependencies:**
```bash
pnpm add lodash --filter @myapp/core

pnpm add @myapp/core --filter @myapp/web-app
```

### Nx

Powerful monorepo toolkit.

**Installation:**
```bash
npx create-nx-workspace@latest my-monorepo
```

**nx.json:**
```json
{
  "tasksRunnerOptions": {
    "default": {
      "runner": "nx/tasks-runners/default",
      "options": {
        "cacheableOperations": ["build", "test", "lint"]
      }
    }
  }
}
```

**Features:**
- Computation caching
- Distributed task execution
- Dependency graph visualization
- Affected command (only build changed projects)

**Commands:**
```bash
nx build my-app

nx affected:build

nx graph

nx run-many --target=test --all
```

### Lerna

Classic monorepo tool (now maintained by Nx team).

**Installation:**
```bash
npx lerna init
```

**lerna.json:**
```json
{
  "version": "independent",
  "npmClient": "pnpm",
  "useWorkspaces": true,
  "packages": ["packages/*"]
}
```

**Commands:**
```bash
lerna bootstrap

lerna run build

lerna publish
```

---

## Package Managers

Tools for managing project dependencies.

### npm

Default Node.js package manager.

```bash
npm install package-name

npm install --save-dev package-name

npm install -g package-name

npm update

npm outdated

npm audit
```

### yarn

Fast, reliable package manager.

```bash
yarn add package-name

yarn add --dev package-name

yarn global add package-name

yarn upgrade

yarn outdated

yarn audit
```

### pnpm

Efficient package manager using hard links.

```bash
pnpm add package-name

pnpm add -D package-name

pnpm add -g package-name

pnpm update

pnpm outdated

pnpm audit
```

**Advantages:**
- Disk space efficient (shared dependencies)
- Faster installation
- Strict dependency resolution

### Comparison

| Feature | npm | yarn | pnpm |
|---------|-----|------|------|
| **Speed** | Medium | Fast | Very Fast |
| **Disk Usage** | High | High | Low |
| **Workspaces** | Yes | Yes | Yes |
| **Lock File** | package-lock.json | yarn.lock | pnpm-lock.yaml |

---

## Development Tools

Essential tools for modern JavaScript development.

### ESLint

Code quality and style checker.

**Installation:**
```bash
npm install --save-dev eslint
npx eslint --init
```

**.eslintrc.js:**
```javascript
module.exports = {
  env: {
    browser: true,
    es2021: true,
    node: true
  },
  extends: [
    'eslint:recommended',
    'plugin:react/recommended'
  ],
  parserOptions: {
    ecmaVersion: 'latest',
    sourceType: 'module'
  },
  rules: {
    'no-unused-vars': 'warn',
    'no-console': 'off',
    'semi': ['error', 'always'],
    'quotes': ['error', 'single']
  }
};
```

### Prettier

Opinionated code formatter.

**Installation:**
```bash
npm install --save-dev prettier
```

**.prettierrc:**
```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 80
}
```

**.prettierignore:**
```
dist
build
node_modules
*.min.js
```

### TypeScript

Static type checking.

**Installation:**
```bash
npm install --save-dev typescript
npx tsc --init
```

**tsconfig.json:**
```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM"],
    "jsx": "react",
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

### Husky

Git hooks made easy.

**Installation:**
```bash
npm install --save-dev husky
npx husky install
```

**Add pre-commit hook:**
```bash
npx husky add .husky/pre-commit "npm test"
```

**.husky/pre-commit:**
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run lint
npm test
```

### lint-staged

Run linters on git staged files.

**Installation:**
```bash
npm install --save-dev lint-staged
```

**package.json:**
```json
{
  "lint-staged": {
    "*.js": ["eslint --fix", "prettier --write"],
    "*.css": ["prettier --write"]
  }
}
```

**.husky/pre-commit:**
```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

---

## Best Practices

### 1. Choose the Right Bundler

```javascript
// For apps: Webpack or Vite
// For libraries: Rollup
// For quick prototypes: Parcel
```

### 2. Configure Babel Properly

```javascript
module.exports = {
  presets: [
    ['@babel/preset-env', {
      targets: '> 0.5%, not dead',
      useBuiltIns: 'usage',
      corejs: 3
    }]
  ]
};
```

### 3. Use Source Maps

```javascript
// webpack.config.js
module.exports = {
  devtool: 'source-map'
};
```

### 4. Optimize Bundle Size

```bash
npx webpack-bundle-analyzer dist/stats.json

npm install --save-dev @next/bundle-analyzer
```

### 5. Use Environment Variables

```javascript
// .env
API_URL=https://api.example.com
NODE_ENV=production

// Access in code
console.log(process.env.API_URL);
```

### 6. Set Up CI/CD

```yaml
# .github/workflows/ci.yml
name: CI

on: [push, pull_request]

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run build
```

---

## Key Takeaways

- **Bundlers**: Webpack for complex apps, Vite for modern fast development, Rollup for libraries
- **Transpiling**: Babel converts modern JS to older versions for compatibility
- **Polyfills**: Core-js provides missing features in older browsers
- **Monorepos**: Great for related projects with shared code (use Turborepo or Nx)
- **Package Managers**: pnpm is fastest and most efficient, npm is default
- **Dev Tools**: ESLint + Prettier + TypeScript + Husky for quality code
- **Choose wisely**: Not every project needs every tool
- **Start simple**: Add complexity only when needed 