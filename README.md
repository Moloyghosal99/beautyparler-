beauty-tailor-website/
├── api/               ← your Express app (formerly “backend”)
│   ├── package.json
│   ├── server.js
│   └── ...models/routes...
├── public/            ← React’s `public/`
├── src/               ← React’s `src/`
├── package.json       ← root package.json with both build & start scripts
└── vercel.json
{
  "name": "beauty-tailor-website",
  "private": true,
  "scripts": {
    "dev": "concurrently \"npm run server\" \"npm run client\"",
    "server": "node api/server.js",
    "client": "react-scripts start",
    "build": "react-scripts build",
    "start": "node api/server.js"
  },
  "dependencies": {
    "express": "...",
    "mongoose": "...",
    "cors": "...",
    "dotenv": "...",
    // React deps moved here too:
    "react": "...",
    "react-dom": "...",
    "react-scripts": "...",
    "axios": "...",
    "react-router-dom": "..."
  },
  "devDependencies": {
    "concurrently": "^7.0.0"
  }
}
{
  "version": 2,
  "builds": [
    {
      "src": "api/server.js",
      "use": "@vercel/node"
    },
    {
      "src": "package.json",
      "use": "@vercel/static-build",
      "config": { "distDir": "build" }
    }
  ],
  "routes": [
    { "src": "/api/(.*)", "dest": "/api/server.js" },
    { "src": "/(.*)",      "dest": "/index.html" }
  ]
}
// api/server.js
const express = require('express');
const path = require('path');
const app = express();
// ... your middleware, routes, DB setup ...

// Serve React build in production:
if (process.env.NODE_ENV === 'production') {
  app.use(express.static(path.join(__dirname, '../build')));
  app.get('*', (req, res) => {
    res.sendFile(path.join(__dirname, '../build', 'index.html'));
  });
}

module.exports = app;
