- [Instructions](#instructions)
    - [Create React Project](#create-react-project)
    - [Follow on screen instructions](#follow-on-screen-instructions)
    - [Package.json](#packagejson)
    - [Vite Config `vite.config.js`](#vite-config-viteconfigjs)
  - [Add Vitest](#add-vitest)
    - [Add sample test](#add-sample-test)



# Instructions
A simple step by step guide on how to setup a react project with vite, and add vitest for testing. This is just a rough draft. Things I want to add to this template are the following:
- [ ] Scss starter Files
  - [ ] Utility classes
  - [ ] Typography
  - [ ] Themes
  - [ ] Vertical Rhythm
  - [ ] Simple Grid
- [ ] Font Families
- [ ] Auth
- [ ] Layout Components

### Create React Project
```powershell
npm create vite@latest <app-name> -- --template react-swc
```

If you are inside the working directory, that is the directory that will house your React project, then you can use the following:

```powershell
npm create vite@latest . -- --template react-swc
```
This will scaffold a React Project incorporating Typescript in the build tool-chain. The project structure as of the time of this tutorial looks like the following:

```ascii
public
src/
├── assets
├── App.jsx
├── index.css
├── main.jsx
└── App.css
.gitignore
eslint.config.js
index.html
package.json
README.md
vite.config.js
```

### Follow on screen instructions
```
npm install
```

```powershell
npm install @types/node vitest @testing-library/react @testing-library/jest-dom @vitest/ui sass-embedded postcss jsdom @testing-library/user-event autoprefixer
```


### Package.json

```json
{
  "name": "your-name-here",
  "private": true,
  "version": "0.0.0",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "vite build",
    "lint": "eslint .",
    "preview": "vite preview",
    "test": "vitest",
    "test:ui": "vitest --ui",
    "coverage": "vitest --coverage"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@eslint/js": "^9.15.0",
    "@testing-library/jest-dom": "^6.6.3",
    "@testing-library/react": "^16.0.1",
    "@testing-library/user-event": "^14.5.2",
    "@types/node": "^22.10.1",
    "@types/react": "^18.3.12",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react-swc": "^3.5.0",
    "@vitest/ui": "^2.1.8",
    "autoprefixer": "^10.4.20",
    "eslint": "^9.15.0",
    "eslint-plugin-react": "^7.37.2",
    "eslint-plugin-react-hooks": "^5.0.0",
    "eslint-plugin-react-refresh": "^0.4.14",
    "globals": "^15.12.0",
    "jsdom": "^25.0.1",
    "postcss": "^8.4.49",
    "sass-embedded": "^1.81.1",
    "vite": "^6.0.1",
    "vitest": "^2.1.8"
  }
}

```

### Vite Config `vite.config.js`

```js
/// <reference types="vitest" />
/// <reference types="vite/client" />

import { ConfigEnv, CSSOptions, defineConfig, PluginOption, ServerOptions, UserConfig } from "vite";
import react from '@vitejs/plugin-react-swc'
import autoprefixer from "autoprefixer";
import cssnano from "cssnano";
import { InlineConfig } from "vitest/node";

/**
 * @type ServerOptions
 */
const ServerOptionProps = {
  port: 3000,
  host: "127.0.0.1",
};

/**
 * @type CSSOptions
 */
const CSSDevOptionProps = {
  postcss: {
    plugins: [autoprefixer],
  },
  modules: {
    localsConvention: "camelCaseOnly",
    exportGlobals: true,
  },
}

/**
 * @type CSSOptions
 */
const CSSOptionProps = {
  postcss: {
    plugins: [autoprefixer, cssnano],
  },
  modules: {
    localsConvention: "camelCaseOnly",
    exportGlobals: true,
  },
}

/**
 * @type InlineConfig
 */
const VitestConfig = {
  environment: 'jsdom',
  globals: true,
  setupFiles: ['tests/setup.js']
}

/**
 * @type PluginOption[] | undefined
 */
const VitePluginArray = [react()]

/**
 * 
 * @param {ConfigEnv} param0 
 * @returns {UserConfig}
 */
function UserConfigFunction({ command }) {
  if (command === "serve") {
    return {
      server: ServerOptionProps,
      css: CSSDevOptionProps,
      plugins: VitePluginArray,
      test: VitestConfig
    };
  } else if (command === 'build') {
    return {
      server: ServerOptionProps,
      css: CSSOptionProps,
      plugins: VitePluginArray,
    };
  }
}

/** @type {import('vite').UserConfig} */
export default defineConfig(UserConfigFunction);

```


## Add Vitest

This part is simple because we have done all the setup above. 

```powershell
mkdir tests; cd tests; ni setup.ts;
```

Then add the following to `setup.ts`:
```ts
import { expect, afterEach } from 'vitest';
import { cleanup } from '@testing-library/react'
import '@testing-library/jest-dom/vitest'

afterEach(() => {
  cleanup();
})
```

### Add sample test
```powershell
ni tests/sample.test.tsx
```

Then add the following to `sample.test.jsx`:

```tsx
import { render, screen } from '@testing-library/react';
import { describe, expect, it } from "vitest";
import React from "react";

import App from "../src/App";

describe('App', () => {

    it('renders headline', () => {
      render(<App />);
      const headline = screen.getByText(/Vite \+ React/i);
      expect(headline).toBeInTheDocument();
    });
  });
```

Now run your tests with `npm test`. And you should get something resembling the following. 

```powershell
 DEV  v2.1.6 D:/<project-name>

 ✓ tests/sample.test.tsx (1)
   ✓ App (1)
     ✓ renders headline

 Test Files  1 passed (1)
      Tests  1 passed (1)
   Start at  23:16:26
   Duration  1.31s (transform 49ms, setup 317ms, collect 87ms, tests 29ms, environment 602ms, prepare 99ms)

 PASS  Waiting for file changes...
       press h to show help, press q to quit
```

> Now push this to a github repo. And use Github Template feature and turn your repo into a Template. Then when you need to create a new React Project, you just use the Template. 😊