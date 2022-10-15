# CreateNPMPackage (Simple function)
- mkdir my-npm-package
- cd my-npm-package
- npm init -y //This will add package.json file
- npm install typescript --save-dev
- tsc --init //This will add tsconfig.json
- open tsconfig.json file & add "declaration": true, so it generates .d.ts definition files.
- open package.json file & add "build": "tsc" to scripts section. 
- add int index.ts & write a simple function "function GreetUser(name: string) { return "Greetings " + name; };"
- export the function. "export default GreetUser;"
- touch .gitignore then add node_modules & any other targets as needed.
- git init
- push all changes to your repo on git

# Installing custom npm package
- Install this package using "npm install github:vvimjam/GreetUserNpmPackage"
- import your package
```typescript
import GreetUser from 'greeting-npm-package';
console.log(GreetUser("venkat"));
```
- add "build":"tsc"
- npm run build
- node index.js //this should output your function return condition 

You usually publish your package on to npm registry but if in case you want to publish one which is only accessible within your org then this is a simple way to publish & use a package.

# Targeting specific version
- If you want to install a specific version of your package you will have to target that specific commit SHA
```
Ex: 
0c25d9abc73b638de30b2c277894dc276fb2c876 corresponds to v1.0.0
c332868ec6f56107833d2ccd6256c8fec805854f corresponds to v1.1.0

So if you want to install v1.0.0 specifically then use
npm install github:vvimjam/GreetUserNpmPackage#0c25d9abc73b638de30b2c277894dc276fb2c876
```

- After installing your custom package if you inspect your package-lock.json file. Find your module name & under it find the resolved prop & its value. If you look at the end of resolved line you will see a SHA code. These SHA codes corresponds to a git specific commit. 
Ex: 0c25d9abc73b638de30b2c277894dc276fb2c876 is a SHA associated to a commit. 
```javascript
      "resolved": "git+ssh://git@github.com/vvimjam/GreetUserNpmPackage.git#0c25d9abc73b638de30b2c277894dc276fb2c876",
```

# CreateNPMPackage (WithPeerDependencies & storybook)

## Setting core project from scratch
- mkdir my-npm-package
- cd my-npm-package
- npm init -y //This will add package.json file
- tsc --init //This will add tsconfig.json
- open tsconfig.json file & set 

```typescript
{
      "declaration": true, //so it generates .d.ts definition files.
      "declarationDir": "lib",
      "outDir": "lib",
      "jsx": "react" //jsx property allows us to use .tsx files in a project
}
```
- touch .gitignore then add node_modules & any other targets as needed.
- git init

## Storybook setup
- npm storybook init
- Choose project type as react or vue or as needed
- Storybook might not be compatible with npm version being used, choose run npm7 migration. Which just adds "leagacy-peer-deps=true" in a .npmrc file.
- Look into .storybook/main.js file. This controls the behaviour of sb server.
- Remove existing stories keys & add "../src/**/*.stories.tsx".
- Remove stories folder at root dir as this contains just examples.

## Add mui & react as dev dependencies & peerDependencies
- npm i react react-dom @mui/material @emotion/react @emotion/styled -D //This saves them as devDependencies
- open package.json & manually add "peerDependencies" section with all the above packages in it (except react-dom). 
- Peer dependencies express compatibility. Ex: about which version of react your package is compatible with.
```
Favor using Peer Dependencies when one of the following is true:
    Having multiple copies of a package would cause conflicts
    The dependency is visible in your interface
    You want the developer to decide which version to install

Source: https://indepth.dev/posts/1187/npm-peer-dependencies
```

## Setting up core files
- create src\components\Button dirs
- create Button.tsx, Button.stories.tsx & index.ts file under Button dir
- create index.ts file at src
- create button.tsx component
```typescript
import { Button, ButtonProps } from "@mui/material";
import React from 'react';

type MuiButtonProps = Pick<ButtonProps, 'variant' | 'name'>;

const MuiButton = (props: MuiButtonProps) => {
    return <Button variant={props?.variant ?? 'text'}>{props?.name ?? 'Button'}</Button>;
}

export default MuiButton;
export type { MuiButtonProps };
```
- export button in both index.ts files
```typescript
export { default, MuiButtonProps } from './Button';
```

- create stories in Button.stories.tsx
```
import MuiButton, { MuiButtonProps } from "./Button";
import { Meta, Story } from "@storybook/react/types-6-0";
import React from "react";

export default {
    title: "Components/MuiButton",
    component: MuiButton,
} as Meta;

const Template: Story<MuiButtonProps> = (args) => <MuiButton {...args} />;

export const TextVariant = Template.bind({});
TextVariant.args = { variant: "text" };


export const OutlinedVariant = Template.bind({});
OutlinedVariant.args = { variant: "outlined" };

export const ContainedVariant = Template.bind({});
ContainedVariant.args = { variant: "contained" };
```

## Running storybook server & testing
- npm run storybook
- verify your stories are working as intended

## Package your component 
- rollup is best suited for bundling libraries while webpack for apps.
- npm i --dev rollup rollup-plugin-typescript2 @rollup/plugin-commonjs @rollup/plugin-node-resolve rollup-plugin-peer-deps-external rollup-plugin-postcss postcss -D
- add types & main keys pointing to build files in package.json
```
  "main": "lib/index.js",
  "module": "lib/index.esm.js",
  "types": "lib/index.d.ts",
```
- create rollup.config.js & add the following
```typescript
import peerDepsExternal from "rollup-plugin-peer-deps-external";
import resolve from "@rollup/plugin-node-resolve";
import commonjs from "@rollup/plugin-commonjs";
import typescript from "rollup-plugin-typescript2";
import postcss from "rollup-plugin-postcss";

const packageJson = require("./package.json");

export default {
  input: "src/index.ts",
  output: [
    {
      file: packageJson.main,
      format: "cjs",
      sourcemap: true
    },
    {
      file: packageJson.module,
      format: "esm",
      sourcemap: true
    }
  ],
  plugins: [
    peerDepsExternal(),
    resolve(),
    commonjs(),
    typescript({ useTsconfigDeclarationDir: true }),
    postcss({
        extensions: ['.css']
    })
  ]
};
```
- Modify tsconfig to include & exclude dir to transpile
```
"compilerOptions": {}, //Ignore this
"include": [
    "src"
  ],
  "exclude": [
    "node_modules",
    "lib"
  ]
```
- In package.json file add   "type": "module"
- Add "build": "rollup -c --bundleConfigAsCjs" to scripts section in package.json file.
- more at https://prateeksurana.me/blog/react-component-library-using-storybook-6/

## Peer dependencies observations
- ReactMuiNpmPackage was created with react18 & mui5. 
- If the project consuming this library has no react or mui then npm throws no warning. Why ? If you look at node_module folder then you can see that both react & mui are installed/downloaded there. 
- If the project consiming this library has either react or mui then npm will throw a warning if the react or mui version are diffrent from what the peerDependency is specifying. Npm will not throw a warnign if they are same.
