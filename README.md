# CreateNPMPackage

- mkdir my-npm-package
- cd my-npm-package
- npm init //This will add package.json file
- npm install typescript --save-dev
- tsc --init //This will add tsconfig.json
- open tsconfig.json file & add "declaration": true, so it generates .d.ts definition files.
- open package.json file & add "build": "tsc" to scripts section. 
- add int index.ts & write a simple function "function GreetUser(name: string) { return "Greetings " + name; };"
- export the function. "export default GreetUser;"
- git init & add .gitignore file & include node_modules & any other targets as needed.
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
