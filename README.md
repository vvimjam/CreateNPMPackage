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
