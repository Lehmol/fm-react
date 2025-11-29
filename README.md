## Overview

This project runs a React application inside a FileMaker WebViewer. React is compiled to static files (`dist/`) and served locally using a small HTTP server. FileMaker starts the server when the FileMaker file opens and stops it when the file closes.

## Requirements

* Node.js (LTS)
* npm
* FileMaker Pro 19+
* Windows (for start/stop `.cmd` scripts)

## Installation

```bash
git clone <repo-url>
cd fm-react
npm install
```

## Install the local static server

This project uses `serve` to host the built React files.

### Recommended (global install)

```bash
npm install -g serve
serve --version
```

If you see a version number, the install succeeded.

### Alternative (no global install)

```bash
npm install --save-dev serve
```

If using this method, ensure your `start-server.cmd` calls the local `serve.js` inside `node_modules`.

## Build React

Any time you modify code in `src/`, rebuild the static files:

```bash
npm run build
```

Output goes into:

```
dist/
  index.html
  assets/
```

`dist/` is what FileMaker loads.

## Starting the static server (manual test)

To test outside FileMaker, run:

```bash
start-server.cmd
```

You should see the server running at:

```
http://localhost:3000
```

Stopping the server:

```bash
stop-server.cmd
```

## FileMaker Integration

### 1. WebViewer URL

Set the WebViewer calculation to:

```
"http://localhost:3000"
```

### 2. OnOpen Script (start the server)

Run the static server when the FileMaker file opens:

```
Send Event [ "cmd" ; "C:\path\to\start-server.cmd" ]
Pause/Resume Script [ Duration ( 2 ) ]
Go to Layout [ "WebViewer Layout" ]
Refresh Window
```

### 3. OnClose Script (stop the server)

Stop the static server when FileMaker closes:

```
Send Event [ "cmd" ; "C:\path\to\stop-server.cmd" ]
```

Configure in:

```
File > File Options > Script Triggers
```

## Updating the React application

When you edit files inside `src/`, follow this sequence:

```bash
npm run build
stop-server.cmd
start-server.cmd
```

Then refresh your FileMaker WebViewer.

If you skip either the rebuild or the restart, the WebViewer will continue to show the old version.

## Folder Structure

```
fm-react/
  src/                # React source code (editable)
  dist/               # Built static files (generated)
  start-server.cmd    # Starts static server (localhost:3000)
  stop-server.cmd     # Stops static server
  package.json
  vite.config.js
```

## Notes

* Do not use `npm run dev` inside FileMaker. Only the static build works in the WebViewer.
* The static server must be running before the WebViewer loads the page.
* Restart the server after each build to pick up changes.
