# 2. Install Node.js and use the command line

[Back to notes index](../README.md)

| [Previous: Node.js runtime and JavaScript execution](./01-nodejs-runtime-and-javascript-execution.md) | [Notes index](../README.md) | [Next: CommonJS modules and project structure](./03-commonjs-modules-and-project-structure.md) |
| --- | --- | --- |

## Install and verify the runtime

Install a supported Node.js release from the official download page. A Node.js installation normally includes the **node** executable and **npm**. Open a new terminal after installation, then check both commands:

~~~sh
node --version
npm --version
~~~

If the shell cannot find **node**, check that the installation directory is on PATH, open a fresh terminal, and run the version command again.

## Run a file, an expression, and the REPL

A JavaScript file can be passed directly to Node.js:

~~~sh
node app.js
~~~

For a short expression, use **-e**:

~~~sh
node -e "console.log(process.platform)"
~~~

Start the interactive REPL by running **node** without a file. Type JavaScript expressions and press Enter. Use **.exit** or press Ctrl+C twice to leave.

The REPL is useful for checking a small expression. Put repeatable work in a file so it can be reviewed and run again.

## Read command-line arguments

Create **greet.js**:

~~~js
const name = process.argv[2] || 'friend'
console.log('Hello, ' + name)
~~~

Run:

~~~sh
node greet.js Ashish
~~~

**process.argv** is an array. Its first entries identify the Node executable and the script path. Values after those entries are the arguments supplied to the script. Treat arguments as untrusted input and validate them before using them in file paths or commands.

To inspect all values, add this line:

~~~js
console.log(process.argv)
~~~

## Read environment variables

Environment variables are strings supplied to the process by the operating system, shell, or hosting platform. A process can read them through **process.env**:

~~~js
const portText = process.env.PORT || '3000'
const port = Number(portText)

if (!Number.isInteger(port) || port < 1 || port > 65535) {
  throw new Error('PORT must be a valid TCP port')
}

console.log('Listening port:', port)
~~~

Do not assume an environment value has the right type just because it looks numeric. Convert it and validate the range before using it. Avoid placing secrets directly in a source file or printing them to a terminal log.

## Set a process exit code

A successful process normally exits with code **0**. A nonzero code tells the shell or another program that work failed.

~~~js
const input = process.argv[2]

if (!input) {
  console.error('Usage: node check.js <value>')
  process.exitCode = 1
} else {
  console.log('Received:', input)
}
~~~

Prefer setting **process.exitCode** after handling the work. Calling **process.exit()** immediately can interrupt pending output or cleanup.

Check a command's exit code in PowerShell with **$LASTEXITCODE**, or in a POSIX shell with **echo $?**.

## Useful command-line options

Node.js supports options for inspecting and running code. Check the options available in the installed runtime with:

~~~sh
node --help
~~~

For example, **--check** asks Node.js to parse a JavaScript file without running it:

~~~sh
node --check app.js
~~~

The command-line interface can change between releases. When a flag matters to a project, confirm it in the documentation for the Node.js version used by that project.

## Practice

Create a script that accepts a person’s name and a numeric port. Print a useful message when either value is missing or invalid. Do not start a server yet. Run it with valid and invalid values and inspect the process exit code.

## Check what you learned

1. Which commands confirm that Node.js and npm are available?
2. What happens when you run **node** without a script path?
3. What is **node -e** useful for?
4. Which entries in **process.argv** contain the script arguments?
5. Why should environment values be parsed and validated?
6. What does exit code zero communicate to the shell?
7. Why is **process.exitCode** often safer than an immediate process exit?
8. How can you check whether a JavaScript file parses without running it?

## References

- [Node.js downloads](https://nodejs.org/en/download)
- [Node.js command-line options](https://nodejs.org/api/cli.html)
- [Process API](https://nodejs.org/api/process.html)
- [Node.js environment variables](https://nodejs.org/api/environment_variables.html)