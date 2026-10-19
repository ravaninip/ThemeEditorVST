# Security Policy

## Reporting a vulnerability

Please do **not** open a public issue for a security problem. Use GitHub's private reporting instead: go to the
**Security** tab of this repository and choose **Report a vulnerability**. You will get an answer within a few
days.

## Scope

The Theme Studio is a static page that runs entirely in the browser: it loads no external code, makes no network
requests and stores nothing. The relevant risks are therefore a malicious theme file (handled by strict
validation and size limits, both here and in the plug-ins that load themes) and anything that would make the page
load or run code from elsewhere.
