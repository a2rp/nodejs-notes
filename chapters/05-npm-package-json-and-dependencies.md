# 5. npm, package.json, and dependencies

[Back to notes index](../README.md)

| [Previous: ECMAScript modules](./04-ecmascript-modules.md) | [Notes index](../README.md) | [Next: Asynchronous JavaScript and the event loop](./06-async-javascript-and-event-loop.md) |
| --- | --- | --- |

## Create a project manifest

npm is the package manager commonly installed with Node.js. A **package.json** file describes a project, including its name, scripts, module mode, and dependency declarations.

Create a folder and initialize its manifest:

~~~sh
mkdir nodejs-practice
cd nodejs-practice
npm init -y
~~~

The **-y** option accepts the defaults. Open the generated file and make the project's choices explicit:

~~~json
{
  "name": "nodejs-practice",
  "version": "1.0.0",
  "private": true,
  "type": "module",
  "scripts": {
    "start": "node app.js",
    "test": "node --test"
  }
}
~~~

The **private** field helps prevent accidentally publishing this practice project to the npm registry. The **type** field selects ESM for its **.js** files.

## Use package scripts

A script gives a project a short, repeatable command:

~~~sh
npm run
npm run start
npm test
~~~

Use **npm run <name>** for a custom script. npm also provides shorthand commands for common names such as **start** and **test**.

Scripts run from the package directory and can find locally installed command-line tools. Keep scripts focused on tasks a developer or automated build needs to repeat.

## Add dependencies

A production dependency is required by the application when it runs. A development dependency is used for local work, tests, or build steps.

~~~sh
npm install package-name
npm install --save-dev another-package
~~~

npm records production packages in **dependencies** and development packages in **devDependencies**. The package names above are examples. Before adding a real dependency, check its maintenance, license, security history, and whether Node.js already provides the needed feature.

The application imports an installed package by its declared name:

~~~js
import { something } from 'package-name'
~~~

Only use an import after the package has been installed and appears in the project manifest.

## Understand version ranges and the lockfile

A dependency declaration usually contains a version range. Semantic versioning expresses a version as major, minor, and patch numbers. A range describes which releases the package manager may select when resolving the tree.

The **package-lock.json** records the resolved dependency tree. Commit it for an application so other installs can reproduce the same tree. Do not commit **node_modules**; npm can recreate that directory from the manifest and lockfile.

After cloning a project, use:

~~~sh
npm ci
~~~

**npm ci** requires a compatible lockfile, removes an existing **node_modules** directory, and installs the locked project without rewriting the manifest or lockfile. Use **npm install** when intentionally adding or changing dependencies. Review and commit both updated metadata files when a change is expected.

## Inspect and maintain dependencies

List the top-level installed packages:

~~~sh
npm ls --depth=0
~~~

Check for dependency advisories:

~~~sh
npm audit
~~~

An audit report needs review. Do not blindly force major dependency changes just to make the report disappear. Check the advisory, the affected dependency path, and the compatibility impact before updating.

## Practice

Create a tiny ESM project with **npm init -y**. Add a **start** script and a **test** script. Run each script. Add one package only if the project has a real need for it, then inspect the changes to **package.json** and **package-lock.json**.

## Check what you learned

1. What information does **package.json** keep for a project?
2. What is the purpose of a package script?
3. Where does npm record production dependencies?
4. When does a package belong in **devDependencies**?
5. What does a semantic version range communicate?
6. Why should an application commit **package-lock.json**?
7. How does **npm ci** differ from **npm install** when using a lockfile?
8. Why should an audit report be reviewed before forcing dependency changes?

## References

- [Creating a package.json file](https://docs.npmjs.com/creating-a-package-json-file/)
- [Dependencies and devDependencies](https://docs.npmjs.com/specifying-dependencies-and-devdependencies-in-a-package-json-file/)
- [package-lock.json](https://docs.npmjs.com/cli/v11/configuring-npm/package-lock-json)
- [npm ci](https://docs.npmjs.com/cli/v11/commands/npm-ci/)
- [npm scripts](https://docs.npmjs.com/cli/v11/using-npm/scripts)