# GHSummary
An app that generate an SVG file with a GitHub activity summary to include it in the profile file.

## Stack

- Golang project
- Deploy to Vercel
- LLM for summary - TBD

## Plan of work

- Generate a simple project that creates an SVG with a test text
- Use GitHub API to get a few last commits of a user and write a log to SVG file
- Integrate LLM API to write a summary of the last changes to give a context what the user was doing