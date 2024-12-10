# Electron Framework

## Introduction
Electron is a framework for building cross-platform desktop applications using web technologies (HTML, CSS, and JavaScript). It combines Chromium and Node.js into a single runtime.

## Core Architecture

### Main Process
```javascript
const { app, BrowserWindow } = require('electron');
const path = require('path');

function createWindow() {
    const mainWindow = new BrowserWindow({
        width: 1200,
        height: 800,
        webPreferences: {
            nodeIntegration: false,
            contextIsolation: true,
            preload: path.join(__dirname, 'preload.js')
        }
    });

    // Load the index.html file
    mainWindow.loadFile('index.html');
    
    // Open DevTools in development
    if (process.env.NODE_ENV === 'development') {
        mainWindow.webContents.openDevTools();
    }
}

app.whenReady().then(() => {
    createWindow();

    app.on('activate', () => {
        if (BrowserWindow.getAllWindows().length === 0) {
            createWindow();
        }
    });
});
```

### Preload Script
```javascript
// preload.js
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
    sendMessage: (message) => ipcRenderer.send('message', message),
    onResponse: (callback) => 
        ipcRenderer.on('response', (event, data) => callback(data)),
    openFile: () => ipcRenderer.invoke('dialog:openFile'),
    saveFile: (content) => ipcRenderer.invoke('dialog:saveFile', content)
});
```

## IPC Communication

### Between Processes
1. **Main Process**
   ```javascript
   const { ipcMain } = require('electron');

   ipcMain.on('message', (event, message) => {
       console.log('Received:', message);
       event.reply('response', 'Message received!');
   });

   ipcMain.handle('dialog:openFile', async () => {
       const { dialog } = require('electron');
       const result = await dialog.showOpenDialog({
           properties: ['openFile']
       });
       return result.filePaths[0];
   });
   ```

2. **Renderer Process**
   ```javascript
   // Using exposed API in renderer
   document.getElementById('sendBtn').addEventListener('click', async () => {
       window.electronAPI.sendMessage('Hello from renderer!');
   });

   window.electronAPI.onResponse((data) => {
       console.log('Response:', data);
   });

   async function openFile() {
       const filePath = await window.electronAPI.openFile();
       console.log('Selected file:', filePath);
   }
   ```

## System Integration

### Native Features
```javascript
const { app, dialog, shell } = require('electron');

// System dialogs
async function showDialog() {
    const result = await dialog.showMessageBox({
        type: 'question',
        buttons: ['Yes', 'No'],
        title: 'Confirmation',
        message: 'Are you sure?'
    });
    return result.response === 0;
}

// File system operations
const fs = require('fs').promises;

async function saveToFile(content, filePath) {
    try {
        await fs.writeFile(filePath, content);
        return true;
    } catch (error) {
        console.error('Failed to save file:', error);
        return false;
    }
}

// System tray
function createTray() {
    const tray = new Tray('icon.png');
    const contextMenu = Menu.buildFromTemplate([
        { label: 'Show App', click: () => mainWindow.show() },
        { label: 'Quit', click: () => app.quit() }
    ]);
    tray.setContextMenu(contextMenu);
}
```

## Security

### Best Practices
```javascript
// Secure window creation
const mainWindow = new BrowserWindow({
    webPreferences: {
        nodeIntegration: false,
        contextIsolation: true,
        sandbox: true,
        webSecurity: true,
        allowRunningInsecureContent: false
    }
});

// Content Security Policy
app.on('web-contents-created', (event, contents) => {
    contents.session.webRequest.onHeadersReceived((details, callback) => {
        callback({
            responseHeaders: {
                ...details.responseHeaders,
                'Content-Security-Policy': [
                    "default-src 'self' 'unsafe-inline' 'unsafe-eval';"
                ]
            }
        });
    });
});
```

## Application Updates

### Auto-Update Implementation
```javascript
const { autoUpdater } = require('electron-updater');

function setupAutoUpdater() {
    autoUpdater.checkForUpdatesAndNotify();

    autoUpdater.on('update-available', () => {
        dialog.showMessageBox({
            type: 'info',
            title: 'Update Available',
            message: 'A new version is available. Downloading...'
        });
    });

    autoUpdater.on('update-downloaded', () => {
        dialog.showMessageBox({
            type: 'info',
            title: 'Update Ready',
            message: 'Install and restart now?',
            buttons: ['Yes', 'Later']
        }).then((result) => {
            if (result.response === 0) {
                autoUpdater.quitAndInstall();
            }
        });
    });
}
```

## Build and Distribution

### Electron Builder Configuration
```javascript
// package.json
{
    "build": {
        "appId": "com.example.app",
        "mac": {
            "category": "public.app-category.developer-tools"
        },
        "win": {
            "target": ["nsis", "portable"]
        },
        "linux": {
            "target": ["AppImage", "deb"]
        },
        "publish": {
            "provider": "github",
            "owner": "username",
            "repo": "repository"
        }
    }
}
```

## Performance Optimization

### Memory Management
```javascript
// Renderer process memory monitoring
app.on('web-contents-created', (event, contents) => {
    contents.on('did-finish-load', () => {
        const memory = process.getProcessMemoryInfo();
        if (memory.private > 100 * 1024 * 1024) { // 100MB
            contents.reload();
        }
    });
});

// Clear cache periodically
function setupCacheClear() {
    setInterval(() => {
        mainWindow.webContents.session.clearCache();
    }, 1000 * 60 * 60); // Every hour
}
```

## Conclusion
Electron provides a powerful platform for building desktop applications using web technologies, with extensive native system integration capabilities and cross-platform support.
