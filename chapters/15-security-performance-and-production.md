# 15. Security, performance, and production practices

[Back to notes index](../README.md)

| [Previous: Testing with the built-in test runner](./14-testing-with-built-in-test-runner.md) | [Notes index](../README.md) | [Next: Build and test a small Node.js service](./16-build-a-small-nodejs-service.md) |
| --- | --- | --- |

## Validate data at the boundary

Command-line arguments, environment values, HTTP headers, URL parameters, and request bodies can be incomplete or malicious. Validate their type, length, range, and allowed shape before using them.

For an HTTP endpoint, set a maximum body size, accept only the content types it supports, and reject malformed data with a clear client error. Do not trust a client-supplied file path or use it directly in a shell command.

## Protect secrets and error details

Keep passwords, tokens, private keys, and connection strings out of source files and committed configuration. Supply secrets through a protected environment or secret store. Never print them as part of a startup log or request dump.

Return a safe error message to clients. Keep stack traces and internal file paths in access-controlled server logs. Avoid logging full request bodies by default because they may contain credentials or personal information.

## Limit resource use

Set reasonable limits for request bodies, open connections, concurrent jobs, and external operation timeouts. A limit prevents one unusually large or slow input from using unlimited memory or holding resources for too long.

Keep synchronous work in request handlers small. Expensive loops, large JSON transformations, and synchronous filesystem calls can block the event loop. Measure representative workloads, then move CPU-heavy work to a bounded worker pool if needed.

## Review dependencies

Every third-party dependency adds code and maintenance obligations. Prefer built-in APIs when they meet the need. For packages you do add:

- Check whether the package is maintained and has a license that fits the project.
- Commit the package manifest and lockfile.
- Use **npm ci** for a clean install from the lockfile in automated builds.
- Review **npm audit** findings and understand the affected dependency before changing versions.
- Remove packages that are no longer used.

An audit command can report known advisories, but it does not prove that an application is secure.

## Run with appropriate access

Run a service with only the operating-system and network permissions it needs. Keep debugging interfaces such as the Node inspector off public interfaces. Use HTTPS for traffic that crosses an untrusted network, either directly or through a correctly configured trusted proxy.

Do not evaluate user-provided strings as JavaScript. Avoid shell execution with concatenated user input. Use safe APIs and validated values.

## Keep production behavior observable

Write logs with enough context to diagnose failures without exposing secrets. Track service health, request failures, and resource use. Use a process manager or hosting platform that can restart a process after an unexpected failure and deliver a termination signal during deployment.

Handle termination by stopping new work and closing active resources. Deploy stateless service instances where possible so more than one instance can serve requests without relying on local memory for durable data.

## Practice

Review the HTTP server from Chapter 10. Confirm it has a body-size limit, validates the title, returns safe errors, and does not expose stack traces. Check **.gitignore** for local secrets, run **npm audit**, and write down which finding would need an update and why.

## Check what you learned

1. Which values should be validated at an application boundary?
2. Why should request bodies have size limits?
3. Which details should not be returned to an untrusted client?
4. How can large synchronous work affect a Node.js service?
5. What does a dependency lockfile help reproduce?
6. Why does a clean audit report not prove an application is secure?
7. Why should a service use the least permissions it needs?
8. What should an application do when its host sends a termination signal?

## References

- [Node.js security best practices](https://nodejs.org/en/learn/getting-started/security-best-practices)
- [Don't block the event loop](https://nodejs.org/en/learn/asynchronous-work/dont-block-the-event-loop)
- [Node.js permission model](https://nodejs.org/api/permissions.html)
- [npm ci](https://docs.npmjs.com/cli/v11/commands/npm-ci/)
- [npm audit](https://docs.npmjs.com/cli/v11/commands/npm-audit/)