# Development Cheat Sheets

## Git Commands

### Basic Operations
```bash
# Repository Operations
git init                    # Initialize repository
git clone <url>            # Clone repository
git remote add origin <url> # Add remote
git remote -v              # List remotes

# Core Commands
git add <file>             # Stage file
git add .                  # Stage all changes
git commit -m "message"    # Commit changes
git push origin <branch>   # Push to remote
git pull origin <branch>   # Pull from remote

# Branch Operations
git branch                 # List branches
git branch <name>          # Create branch
git checkout <branch>      # Switch branches
git merge <branch>         # Merge branch
git branch -d <branch>     # Delete branch
```

## Docker Commands

### Container Management
```bash
# Container Operations
docker run <image>         # Run container
docker ps                  # List running containers
docker ps -a               # List all containers
docker stop <container>    # Stop container
docker rm <container>      # Remove container

# Image Operations
docker images              # List images
docker pull <image>        # Pull image
docker build -t <name> .   # Build image
docker rmi <image>         # Remove image

# Docker Compose
docker-compose up          # Start services
docker-compose down        # Stop services
docker-compose logs        # View logs
```

## Linux Commands

### File Operations
```bash
# Navigation
ls                        # List files
cd <directory>           # Change directory
pwd                      # Print working directory
mkdir <directory>        # Create directory
rm <file>               # Remove file
rm -r <directory>       # Remove directory

# File Manipulation
cp <source> <dest>      # Copy file
mv <source> <dest>      # Move/rename file
touch <file>           # Create empty file
cat <file>             # Display file content
tail -f <file>         # Follow file content
```

## SQL Commands

### Database Operations
```sql
-- Table Operations
CREATE TABLE table_name (
    column1 datatype,
    column2 datatype
);

DROP TABLE table_name;
ALTER TABLE table_name ADD column_name datatype;

-- Data Operations
SELECT * FROM table_name;
INSERT INTO table_name (column1, column2) VALUES (value1, value2);
UPDATE table_name SET column1 = value1 WHERE condition;
DELETE FROM table_name WHERE condition;

-- Joins
SELECT * FROM table1
INNER JOIN table2 ON table1.id = table2.id;
```

## React Hooks

### Common Hooks
```javascript
// State Hook
const [state, setState] = useState(initialState);

// Effect Hook
useEffect(() => {
  // Side effects
  return () => {
    // Cleanup
  };
}, [dependencies]);

// Context Hook
const value = useContext(MyContext);

// Ref Hook
const myRef = useRef(initialValue);

// Callback Hook
const memoizedCallback = useCallback(
  () => {
    doSomething(a, b);
  },
  [a, b],
);
```

## Regular Expressions

### Common Patterns
```javascript
// Email validation
/^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$/

// URL validation
/^(https?:\/\/)?([\da-z\.-]+)\.([a-z\.]{2,6})([\/\w \.-]*)*\/?$/

// Phone number (US)
/^(\+\d{1,2}\s)?\(?\d{3}\)?[\s.-]\d{3}[\s.-]\d{4}$/

// Password strength
/^(?=.*[A-Za-z])(?=.*\d)[A-Za-z\d]{8,}$/
```

## HTTP Status Codes

### Common Codes
```
2xx Success
200 OK
201 Created
204 No Content

4xx Client Errors
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
409 Conflict

5xx Server Errors
500 Internal Server Error
502 Bad Gateway
503 Service Unavailable
```

## CSS Flexbox

### Container Properties
```css
.container {
  display: flex;
  flex-direction: row | column;
  justify-content: flex-start | center | flex-end | space-between;
  align-items: stretch | flex-start | center | flex-end;
  flex-wrap: nowrap | wrap;
}

.item {
  flex: 1;
  align-self: auto | flex-start | center | flex-end;
  order: 0;
}
```

## Terminal Shortcuts

### Command Line
```bash
# Navigation
Ctrl + A          # Move to beginning of line
Ctrl + E          # Move to end of line
Ctrl + U          # Clear line before cursor
Ctrl + K          # Clear line after cursor
Ctrl + R          # Search command history

# Process Management
Ctrl + C          # Interrupt process
Ctrl + Z          # Suspend process
Ctrl + D          # Exit shell
```

## VSCode Shortcuts

### Editor Commands
```
General
Ctrl + P         : Quick file open
Ctrl + Shift + P : Command palette
Ctrl + ,         : Settings

Editing
Ctrl + D         : Select next occurrence
Alt + ↑/↓        : Move line up/down
Ctrl + /         : Toggle comment
Ctrl + B         : Toggle sidebar
```

## Security Best Practices

### Web Security
```
1. Input Validation
- Sanitize all inputs
- Use parameterized queries
- Validate on server side

2. Authentication
- Use HTTPS
- Implement MFA
- Secure password storage
- Session management

3. Authorization
- Role-based access
- Principle of least privilege
- JWT best practices
```

## Testing Patterns

### Unit Testing
```javascript
// Jest Example
describe('Component', () => {
  beforeEach(() => {
    // Setup
  });

  it('should do something', () => {
    // Arrange
    // Act
    // Assert
  });

  afterEach(() => {
    // Cleanup
  });
});
```

## Performance Tips

### Web Performance
```
1. Loading
- Minimize HTTP requests
- Use CDN
- Enable compression
- Image optimization

2. Rendering
- Lazy loading
- Code splitting
- Tree shaking
- CSS optimization

3. Caching
- Browser caching
- Service workers
- Memory caching
- Database indexing
```
