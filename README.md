# Using TypeScript with Node.js

This template project demonstrates how to integrate TypeScript with Node.js. It also covers linting, prettier, and debugging in Visual Studio Code.

Install the latest version of [Node.js](https://nodejs.org/en) and npm.

Make a folder for your project.

```bash
mkdir project
```

Open the project folder

```bash
cd project
```

Create a package.json file

```bash
npm init -y
```

Add the following configuration to `package.json`

```json

"type": "module"
```

Install and configure TypeScript.

```bash
npm install -D typescript
```

After TypeScript installation, you should have the `tsc` command available in your project.

```bash
tsc --version
```

The TypeScript compiler needs a `tsconfig.json` configuration file. Create the file and add the following content into it.

```json
{
  "extends": "@tsconfig/node20/tsconfig.json",
  "compilerOptions": {
    "outDir": "dist"
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules"]
}
```

`extends` defines a way to inherit from [another configuration file](https://github.com/tsconfig/bases/blob/main/bases/node20.json). Install this configuration as a npm package using the following command.

Install the inherited tsconfig.json file

```bash
npm install -D @tsconfig/node20
```

These comments are convinient for temporarily bypass problematic files when transitioning from JavaScript to TypeScript and you don't have the capacity to address them immediately. You can use the comments at the beginning of each file.

- `// @ts-check`
- `// @ts-nocheck`
- `// @ts-ignore`
- `// @ts-expect-error`

Install the [@types/node]() package for providing type definitions for the build-in Node.js APIs which are needed when the TypeScript compiler compiles TypeScript files.

```bash
npm install -D @types/node
```

Create `src` for and `index.ts` TypeScript file inside of it.

```bash
mkdir src
cd src
touch index.ts
```

Insert this code into the `index.ts` file.

```typescript
import { createServer } from 'http';

const hostname = '127.0.0.1';
const port = 3000;
const server = createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

After editing and saving the file, run the TypeScript compiler.

```bash
tsc
```

After compiling the `index.ts` file from TypeScript to JavaScript, `dist` directory should contain the compiled `index.js` file.

Try to run the server.

```bash
node dist/index.js
```

And run `curl`

```
curl localhost:3000
```

Or open a web browser and go to: `localhost:3000`

Install `nodemon` that automatically restarts the node application when file changes in the directory are detected.

```bash
npm install -D nodemon
```

Create a configuration file `nodemon.json` for Nodemon. Add the following configuration into the file.

```bash
{
  "watch": ["src"],
  "ext": ".js, .ts",
  "ignore": [],
  "exec": "tsc && node dist/index.js"
}
```

Run the server by using nodemon.

```bash
nodemon
```

Add this configuration into the package.json file.

```json
  "scripts": {
    "watch": "nodemon"
  },
```

Now you should be able to start server in a watch mode.

```bash
npm run watch
```

## How to fix type errors caused by third-party libraries

TypeScript handles A library written in JavaScript by using `any` for the entire library. This defeats the entire purpose of using TypeScript, as you won't get compile-time type checks.

By enabling `noImiplicitAny` TypeScript compiler option, TypeScript will throw an error if it can't infer a type, rather that defaulting it to `any`

Some libraries include type declarations in their main package, other libraries provide type declaration files `.d.ts` in their packages. Some JavaScript libraries don't provide type declarations, but the TypeScript community publishes their type declarations separately under the [Definitely Typed Repository](https://github.com/DefinitelyTyped/DefinitelyTyped)

You can install required type declarations as a separate npm package.

```bash
npm install --save-dev @types/express
```

If you can find type declarations for a less popular library, you may need to write custom type declarations files for a library. Read more about this in [TypeScript handbook](https://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)

## Linting TypeScript

Linting catches potential errors and enforces a consistant coding style. See [ESLint](https://www.npmjs.com/package/eslint).

```bash
npm install -D eslint @typescript-eslint/parser @typescript-eslint/eslint-plugin

```

Create `.eslintrc.json` file in your project root and add the following content into it.

```json
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
    "ecmaVersion": "latest",
    "sourceType": "module",
    "project": "tsconfig.json"
  },
  "extends": ["eslint:recommended", "plugin:@typescript-eslint/recommended"],
  "env": {
    "node": true
  },
  "rules": {},
  "ignorePatterns": ["dist"]
}
```

If you use Visual Studio Code, install the ESLint plugin and you'll get linting message in your editor.

Update `package.json` files to include the following line.

```json
"scripts": {
  "lint": "eslint . --fix"
}
```

Run `npm run lint` to lint your project and automatically fix any auto-fixable issues.

```bash
npm run lint
```

You can fine tune how Lint works by editing a `.eslintrc.json` file. [Configure ESLint](https://eslint.org/docs/latest/use/configure/) and [typescript-eslint](https://typescript-eslint.io/)

## Formatting TypeScript code with Prettier

Install Prettier

```bash
npm install -D prettier
npm install -D eslint-config-prettier eslint-plugin-prettier
```

Create `.prettierrc.json` configuration file.

```json
{
  "printWidth": 80,
  "singleQuote": true,
  "trailingComma": "es5",
  "bracketSpacing": true
}
```

Modify the .eslintrc.json file as shown below.

```json
"extends": [
  "plugin:prettier/recommended"
]
```

You can run Prettier to format your TypeScript files:

```bash
npx prettier --write src/index.ts
```

You can configure your code editor to automatically fixes ESLint issues and prettier your code on save. [Use Prettier and ESLint in Visual Studio Code. Lint and Format your code](https://www.youtube.com/watch?v=kWIlrSorqFE)

## Debugging with Visual Code

For debugging TypeScript code, you need to generate source maps. Add the following link into the tsconfig.json file.

```json
"compilerOptions": {
  "sourceMap": true
}
```

Create a launch configuration file `.vscode/launch.json` for Visual Code and TypeScript debugging.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Debug server.ts",
      "type": "node",
      "request": "launch",
      "program": "${workspaceFolder}/src/server.ts",
      "preLaunchTask": "tsc: build - tsconfig.json",
      "outFiles": ["${workspaceFolder}/dist/**/*.js"]
    }
  ]
}
```

After this, you can start debugging by pressing `F5` in Visual Studio Code.
