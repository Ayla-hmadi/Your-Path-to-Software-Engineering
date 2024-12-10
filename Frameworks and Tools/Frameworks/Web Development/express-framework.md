# Express.js Framework

## Introduction
Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications. It facilitates the rapid development of Node.js based web applications.

## Core Concepts

### Basic Application Structure
```javascript
const express = require('express');
const app = express();

// Middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.get('/', (req, res) => {
    res.send('Hello World!');
});

// Error handling
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).send('Something broke!');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server is running on port ${PORT}`);
});
```

### Routing
1. **Basic Routing**
   ```javascript
   // Route parameters
   app.get('/users/:id', (req, res) => {
       const userId = req.params.id;
       res.json({ userId });
   });

   // Query strings
   app.get('/search', (req, res) => {
       const query = req.query.q;
       res.json({ searchQuery: query });
   });

   // Multiple routes
   app.route('/book')
       .get((req, res) => {
           res.send('Get a book');
       })
       .post((req, res) => {
           res.send('Add a book');
       })
       .put((req, res) => {
           res.send('Update a book');
       });
   ```

2. **Router Module**
   ```javascript
   // users.routes.js
   const express = require('express');
   const router = express.Router();

   router.get('/', (req, res) => {
       res.json({ users: [] });
   });

   router.post('/', (req, res) => {
       const newUser = req.body;
       res.status(201).json(newUser);
   });

   module.exports = router;

   // app.js
   const userRoutes = require('./routes/users.routes');
   app.use('/api/users', userRoutes);
   ```

## Middleware

### Custom Middleware
```javascript
// Authentication middleware
const authMiddleware = (req, res, next) => {
    const token = req.headers.authorization;
    
    if (!token) {
        return res.status(401).json({ message: 'No token provided' });
    }
    
    try {
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch (error) {
        res.status(401).json({ message: 'Invalid token' });
    }
};

// Logging middleware
const loggerMiddleware = (req, res, next) => {
    console.log(`${req.method} ${req.path} - ${new Date()}`);
    next();
};

// Apply middleware
app.use(loggerMiddleware);
app.use('/api', authMiddleware);
```

### Error Handling
```javascript
// Error handling middleware
const errorHandler = (err, req, res, next) => {
    // Log error
    console.error(err.stack);

    // Custom error response
    if (err.name === 'ValidationError') {
        return res.status(400).json({
            type: 'ValidationError',
            message: err.message
        });
    }

    // Default error response
    res.status(500).json({
        message: 'Internal Server Error',
        error: process.env.NODE_ENV === 'development' ? err : {}
    });
};

app.use(errorHandler);
```

## Database Integration

### MongoDB with Mongoose
```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect(process.env.MONGODB_URI, {
    useNewUrlParser: true,
    useUnifiedTopology: true
});

// Define schema
const userSchema = new mongoose.Schema({
    username: { type: String, required: true, unique: true },
    email: { type: String, required: true },
    password: { type: String, required: true },
    createdAt: { type: Date, default: Date.now }
});

// User model
const User = mongoose.model('User', userSchema);

// Route handler with database interaction
app.post('/api/users', async (req, res, next) => {
    try {
        const user = new User(req.body);
        await user.save();
        res.status(201).json(user);
    } catch (error) {
        next(error);
    }
});
```

## Authentication

### JWT Authentication
```javascript
const jwt = require('jsonwebtoken');

// Login route
app.post('/api/login', async (req, res) => {
    const { username, password } = req.body;
    
    try {
        // Verify user credentials
        const user = await User.findOne({ username });
        if (!user || !await user.comparePassword(password)) {
            return res.status(401).json({
                message: 'Invalid credentials'
            });
        }
        
        // Generate token
        const token = jwt.sign(
            { userId: user._id },
            process.env.JWT_SECRET,
            { expiresIn: '24h' }
        );
        
        res.json({ token });
    } catch (error) {
        res.status(500).json({ message: error.message });
    }
});
```

## File Upload

### Handling File Uploads
```javascript
const multer = require('multer');

// Configure multer
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/');
    },
    filename: (req, file, cb) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
        cb(null, file.fieldname + '-' + uniqueSuffix);
    }
});

const upload = multer({ storage: storage });

// File upload route
app.post('/api/upload', upload.single('file'), (req, res) => {
    res.json({
        message: 'File uploaded successfully',
        file: req.file
    });
});
```

## Testing

### Unit Testing with Jest
```javascript
const request = require('supertest');
const app = require('../app');

describe('User API', () => {
    beforeEach(async () => {
        await User.deleteMany({});
    });

    test('should create a new user', async () => {
        const response = await request(app)
            .post('/api/users')
            .send({
                username: 'testuser',
                email: 'test@example.com',
                password: 'password123'
            });

        expect(response.statusCode).toBe(201);
        expect(response.body).toHaveProperty('username', 'testuser');
    });
});
```

## Conclusion
Express.js provides a minimal yet powerful framework for building web applications and APIs with Node.js, offering flexibility and a rich ecosystem of middleware and plugins.
