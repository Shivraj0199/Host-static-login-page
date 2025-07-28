## Hosting a fullstack app from scartch (React + Express on EC2 with HTTPS & Domain)

### **(if you have no project code use this steps)** 

## **Step 1: Create the Project Structure**

1. cd desktop
2. mkdir fullstack-app (on your local machine)
3. cd fullstack-app
4. Install the nodejs software on your local machine ``` (https://nodejs.org/en/download) ```
5. mkdir backend
6. cd backend
7. npm init -y
8. npm install express (Installing this we get all suporrted files)
9. nano server.js (Create file on your backend directory and paste the following code)

* Server.js
```
  const express = require('express');
const path = require('path');
const app = express();
const PORT = process.env.PORT || 5000;

app.use(express.json());

// Serve static React frontend
app.use(express.static(path.join(__dirname, '../frontend/build')));

// Example API route
app.get('/api/message', (req, res) => {
  res.json({ message: 'Hello from the Express backend!' });
});

// React fallback
app.get('*', (req, res) => {
  res.sendFile(path.join(__dirname, '../frontend/build/index.html'));
});

app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});

```

10. nano package.json (Update package.json with start script replace this file from following script)

* Replace with
```
{
  "name": "backend",
  "version": "1.0.0",
  "main": "server.js",
  "scripts": {
    "start": "node server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```
11. cd ../desktop
12. npx create-react-app frontend (npx is tool to create react app using classic way (CRA))
13. cd frontend
14. update package.json and add proxy ``` "proxy": "http://localhost:5000" ``` (Add this at the end of file before the lastcurle braceses)

```
{
  "name": "frontend",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "@testing-library/dom": "^10.4.1",
    "@testing-library/jest-dom": "^6.6.4",
    "@testing-library/react": "^16.3.0",
    "@testing-library/user-event": "^13.5.0",
    "react": "^19.1.0",
    "react-dom": "^19.1.0",
    "react-scripts": "5.0.1",
    "web-vitals": "^2.1.4"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": [
      "react-app",
      "react-app/jest"
    ]
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  },
"proxy": "http://localhost:5000"

}
```
15. cd /frontend/src/App.js (Modify react app to call API)

* Paste this
```
import React, { useEffect, useState } from 'react';

function App() {
  const [msg, setMsg] = useState('');

  useEffect(() => {
    fetch('/api/message')
      .then(res => res.json())
      .then(data => setMsg(data.message))
      .catch(err => console.log(err));
  }, []);

  return (
    <div style={{ textAlign: 'center', marginTop: '5rem' }}>
      <h1>React + Express Fullstack App</h1>
      <h2>{msg}</h2>
    </div>
  );
}

export default App;
```
16. npm run build
17. cd ../backend
18. npm install
19. npm start

## **Step 2: Launch EC2 & Connect via SSH**

1. 

