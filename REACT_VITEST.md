- [Instructions](#instructions)
  - [Setup Vitest React Project](#setup-vitest-react-project)
    - [Create React Project](#create-react-project)
    - [Follow on screen instructions](#follow-on-screen-instructions)
    - [Package.json](#packagejson)
    - [If you want Eslint Setup `esling.config.js`](#if-you-want-eslint-setup-eslingconfigjs)
    - [Vite Config `vite.config.ts`](#vite-config-viteconfigts)
    - [Alternate Option for Types Discovery `tsconfig.app.json`](#alternate-option-for-types-discovery-tsconfigappjson)
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

## Setup Vitest React Project

### Create React Project
```powershell
npm create vite@latest <app-name> -- --template react-swc-ts
```

If you are inside the working directory, that is the directory that will house your React project, then you can use the following:

```powershell
npm create vite@latest . -- --template react-swc-ts
```
This will scaffold a React Project incorporating Typescript in the build tool-chain. The project structure as of the time of this tutorial looks like the following:

```ascii
public
src/
├── assets
├── App.tsx
├── index.css
├── main.tsx
├── vite-env.d.ts
└── App.css
.gitignore
eslint.config.js
index.html
package.json
README.md
tsconfig.json
tsconfig.node.json
vite.config.json
```

### Follow on screen instructions
```
npm install
```

```powershell
npm install @types/node eslint-plugin-react vitest @testing-library/react @testing-library/jest-dom @vitest/ui sass-embedded postcss jsdom @testing-library/user-event autoprefixer
```

### Package.json
```json
{
  "name": "ts-react-vitest-template",
  "private": true,
  "version": "0.0.1",
  "type": "module",
  "scripts": {
    "dev": "vite",
    "build": "tsc -b && vite build",
    "lint": "eslint .",
    "preview": "vite preview"
  },
  "dependencies": {
    "react": "^18.3.1",
    "react-dom": "^18.3.1"
  },
  "devDependencies": {
    "@eslint/js": "^9.15.0",
    "@testing-library/jest-dom": "6.6.3",
    "@testing-library/react": "16.0.1",
    "@testing-library/user-event": "14.5.2",
    "@types/node": "22.10.1",
    "@types/react": "^18.3.12",
    "@types/react-dom": "^18.3.1",
    "@vitejs/plugin-react-swc": "^3.5.0",
    "@vitest/ui": "2.1.6",
    "autoprefixer": "10.4.20",
    "cssnano": "^7.0.6",
    "eslint": "^9.15.0",
    "eslint-plugin-react": "7.37.2",
    "eslint-plugin-react-hooks": "^5.0.0",
    "eslint-plugin-react-refresh": "^0.4.14",
    "globals": "^15.12.0",
    "jsdom": "25.0.1",
    "postcss": "8.4.49",
    "sass-embedded": "^1.81.0",
    "typescript": "~5.6.2",
    "typescript-eslint": "^8.15.0",
    "vite": "^6.0.1",
    "vitest": "2.1.6"
  }
}
```

### If you want Eslint Setup `esling.config.js`

```js
import js from '@eslint/js';
import globals from 'globals';
import reactHooks from 'eslint-plugin-react-hooks';
import reactRefresh from 'eslint-plugin-react-refresh';
import tseslint from 'typescript-eslint';
import react from 'eslint-plugin-react'

export default tseslint.config(
  { ignores: ['dist'] },
  {
    extends: [js.configs.recommended, ...tseslint.configs.strictTypeChecked, ...tseslint.configs.stylisticTypeChecked],
    files: ['**/*.{ts,tsx}'],
    languageOptions: {
      ecmaVersion: 2020,
      globals: globals.browser,
      parserOptions: {
        project: ['./tsconfig.node.json', './tsconfig.app.json'],
        tsconfigRootDir: import.meta.dirname,
      },
    },
    plugins: {
      'react-hooks': reactHooks,
      'react-refresh': reactRefresh,
    },
    rules: {
      ...reactHooks.configs.recommended.rules,
      ...react.configs.recommended.rules,
      ...react.configs['jsx-runtime'].rules,
      'react-refresh/only-export-components': [
        'warn',
        { allowConstantExport: true },
      ],
    },
  },
)
```

### Vite Config `vite.config.ts`
```ts
/// <reference types="vite/client" />
/// <reference types="vitest" />

import { ConfigEnv, CSSOptions, defineConfig, PluginOption, ServerOptions, UserConfig } from "vite";
import react from "@vitejs/plugin-react-swc";
import autoprefixer from "autoprefixer";
import cssnano from "cssnano";
import { InlineConfig } from "vitest/node";

const ServerOptionProps: ServerOptions = {
  port: 3000,
  host: "127.0.0.1",
};

const CSSDevOptionProps: CSSOptions = {
  postcss: {
    plugins: [autoprefixer],
  },
  modules: {
    localsConvention: "camelCaseOnly",
    exportGlobals: true,
  },
}

const CSSOptionProps: CSSOptions = {
  postcss: {
    plugins: [autoprefixer, cssnano],
  },
  modules: {
    localsConvention: "camelCaseOnly",
    exportGlobals: true,
  },
}

const VitestConfig: InlineConfig = {
  environment: 'jsdom',
  globals: true,
  setupFiles: ['tests/setup.ts']
}

const VitePluginArray: PluginOption[] | undefined = [react()]

function UserConfigFunction({ command }: ConfigEnv): UserConfig {
  if (command === "serve") {
    return {
      server: ServerOptionProps,
      css: CSSDevOptionProps,
      plugins: VitePluginArray,
      test: VitestConfig
    };
  } else {
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

### Alternate Option for Types Discovery `tsconfig.app.json`
```json
// tsconfig.app.json
{
  "compilerOptions": {
    "tsBuildInfoFile": "./node_modules/.tmp/tsconfig.app.tsbuildinfo",
    "target": "ES2020",
    "useDefineForClassFields": true,
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "module": "ESNext",
    "skipLibCheck": true,

    /* Bundler mode */
    "moduleResolution": "bundler",
    "allowImportingTsExtensions": true,
    "isolatedModules": true,
    "moduleDetection": "force",
    "noEmit": true,
    "jsx": "react-jsx",
    "types": ["vite/client"],

    /* Linting */
    "strict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "noUncheckedSideEffectImports": true
  },
  "include": ["src"]
}
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

Then add the following to `sample.test.tsx`:

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
 DEV  v2.1.6 D:/Ts_React_Vitest

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