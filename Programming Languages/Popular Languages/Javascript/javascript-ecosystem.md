# JavaScript Ecosystem and Libraries

## Frontend Frameworks

### React
1. **Core Features**
   ```javascript
   // Function components
   const UserProfile = ({ user }) => {
       const [isEditing, setIsEditing] = useState(false);
       
       useEffect(() => {
           // Side effects
           fetchUserData(user.id);
       }, [user.id]);
       
       return (
           <div>
               <h1>{user.name}</h1>
               {isEditing ? <EditForm /> : <DisplayProfile />}
           </div>
       );
   };
   ```

2. **State Management**
   ```javascript
   // Redux implementation
   const counterSlice = createSlice({
       name: 'counter',
       initialState: { value: 0 },
       reducers: {
           increment: state => {
               state.value += 1;
           },
           decrement: state => {
               state.value -= 1;
           }
       }
   });
   ```

### Vue.js
1. **Component Structure**
   ```javascript
   // Vue component
   export default {
       data() {
           return {
               message: 'Hello Vue!'
           };
       },
       methods: {
           handleClick() {
               this.message = 'Updated!';
           }
       },
       computed: {
           reversedMessage() {
               return this.message.split('').reverse().join('');
           }
       }
   };
   ```

2. **Vue Router**
   ```javascript
   const router = new VueRouter({
       routes: [
           { 
               path: '/users/:id', 
               component: UserDetails,
               props: true 
           },
           { 
               path: '/dashboard', 
               component: Dashboard,
               meta: { requiresAuth: true } 
           }
       ]
   });
   ```

## Backend Frameworks

### Express.js
1. **Route Handling**
   ```javascript
   const express = require('express');
   const app = express();

   app.get('/api/users', async (req, res) => {
       try {
           const users = await User.find();
           res.json(users);
       } catch (error) {
           res.status(500).json({ error: error.message });
       }
   });

   // Middleware
   app.use((req, res, next) => {
       console.log(`${req.method} ${req.url}`);
       next();
   });
   ```

2. **Authentication**
   ```javascript
   // JWT authentication
   const jwt = require('jsonwebtoken');

   const authMiddleware = (req, res, next) => {
       const token = req.headers.authorization?.split(' ')[1];
       if (!token) {
           return res.status(401).json({ error: 'No token provided' });
       }
       
       try {
           const decoded = jwt.verify(token, process.env.JWT_SECRET);
           req.user = decoded;
           next();
       } catch (error) {
           res.status(401).json({ error: 'Invalid token' });
       }
   };
   ```

### NestJS
1. **Controllers**
   ```typescript
   @Controller('users')
   export class UsersController {
       constructor(private usersService: UsersService) {}
       
       @Get()
       async findAll(): Promise<User[]> {
           return this.usersService.findAll();
       }
       
       @Post()
       @UsePipes(ValidationPipe)
       async create(@Body() createUserDto: CreateUserDto): Promise<User> {
           return this.usersService.create(createUserDto);
       }
   }
   ```

## Testing Libraries

### Jest
1. **Unit Testing**
   ```javascript
   describe('Calculator', () => {
       it('adds numbers correctly', () => {
           expect(add(2, 2)).toBe(4);
       });
       
       it('throws error for invalid input', () => {
           expect(() => add('2', '2')).toThrow();
       });
   });
   
   // Mock functions
   const mockFn = jest.fn();
   mockFn.mockReturnValue(42);
   ```

2. **Async Testing**
   ```javascript
   describe('UserService', () => {
       it('fetches user data', async () => {
           const userData = await UserService.fetchUser(1);
           expect(userData).toMatchObject({
               id: 1,
               name: expect.any(String)
           });
       });
   });
   ```

## Build Tools

### Webpack
1. **Configuration**
   ```javascript
   module.exports = {
       entry: './src/index.js',
       output: {
           path: path.resolve(__dirname, 'dist'),
           filename: '[name].[contenthash].js'
       },
       module: {
           rules: [
               {
                   test: /\.js$/,
                   use: 'babel-loader'
               },
               {
                   test: /\.css$/,
                   use: ['style-loader', 'css-loader']
               }
           ]
       },
       plugins: [
           new HtmlWebpackPlugin({
               template: './src/index.html'
           })
       ]
   };
   ```

### Vite
1. **Configuration**
   ```javascript
   // vite.config.js
   export default {
       plugins: [react()],
       build: {
           outDir: 'dist',
           rollupOptions: {
               external: ['react', 'react-dom']
           }
       },
       server: {
           proxy: {
               '/api': 'http://localhost:3000'
           }
       }
   };
   ```

## Utility Libraries

### Lodash
1. **Array Operations**
   ```javascript
   const _ = require('lodash');
   
   // Array manipulation
   const unique = _.uniq([1, 2, 2, 3]);
   const grouped = _.groupBy(users, 'role');
   const flattened = _.flatMap(array);
   ```

2. **Object Operations**
   ```javascript
   // Deep clone
   const cloned = _.cloneDeep(object);
   
   // Object manipulation
   const merged = _.merge({}, obj1, obj2);
   const picked = _.pick(object, ['prop1', 'prop2']);
   ```

## Data Management

### GraphQL (Apollo)
1. **Client Setup**
   ```javascript
   const client = new ApolloClient({
       uri: 'https://api.example.com/graphql',
       cache: new InMemoryCache()
   });
   
   // Query
   const GET_USERS = gql`
       query GetUsers {
           users {
               id
               name
               email
           }
       }
   `;
   ```

2. **Server Setup**
   ```javascript
   const typeDefs = gql`
       type User {
           id: ID!
           name: String!
           email: String!
       }
       
       type Query {
           users: [User!]!
           user(id: ID!): User
       }
   `;
   
   const resolvers = {
       Query: {
           users: () => User.find(),
           user: (_, { id }) => User.findById(id)
       }
   };
   ```

## Conclusion
JavaScript's ecosystem provides comprehensive solutions for both frontend and backend development, with robust tooling and libraries for testing, building, and managing applications.
