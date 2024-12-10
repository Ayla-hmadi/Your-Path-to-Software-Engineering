# React Framework

## Introduction
React is a JavaScript library for building user interfaces, particularly single-page applications. It's maintained by Meta (formerly Facebook) and a community of individual developers and companies.

## Core Concepts

### Components
1. **Functional Components**
   ```jsx
   function UserProfile({ name, email }) {
       return (
           <div className="profile">
               <h2>{name}</h2>
               <p>{email}</p>
           </div>
       );
   }

   // Usage
   <UserProfile name="John Doe" email="john@example.com" />
   ```

2. **Class Components**
   ```jsx
   class Counter extends React.Component {
       state = {
           count: 0
       };

       increment = () => {
           this.setState(prev => ({ count: prev.count + 1 }));
       };

       render() {
           return (
               <div>
                   <p>Count: {this.state.count}</p>
                   <button onClick={this.increment}>Increment</button>
               </div>
           );
       }
   }
   ```

### Hooks
1. **State Management**
   ```jsx
   function TodoApp() {
       const [todos, setTodos] = useState([]);
       const [input, setInput] = useState('');

       const addTodo = () => {
           setTodos([...todos, input]);
           setInput('');
       };

       return (
           <div>
               <input
                   value={input}
                   onChange={(e) => setInput(e.target.value)}
               />
               <button onClick={addTodo}>Add Todo</button>
               <ul>
                   {todos.map((todo, index) => (
                       <li key={index}>{todo}</li>
                   ))}
               </ul>
           </div>
       );
   }
   ```

2. **Side Effects**
   ```jsx
   function UserData({ userId }) {
       const [user, setUser] = useState(null);
       const [loading, setLoading] = useState(true);

       useEffect(() => {
           async function fetchUser() {
               const response = await fetch(`/api/users/${userId}`);
               const data = await response.json();
               setUser(data);
               setLoading(false);
           }
           fetchUser();
       }, [userId]);

       if (loading) return <div>Loading...</div>;
       return <div>{user.name}</div>;
   }
   ```

## State Management

### Context API
```jsx
const ThemeContext = React.createContext('light');

function ThemeProvider({ children }) {
    const [theme, setTheme] = useState('light');
    
    return (
        <ThemeContext.Provider value={{ theme, setTheme }}>
            {children}
        </ThemeContext.Provider>
    );
}

function ThemedButton() {
    const { theme, setTheme } = useContext(ThemeContext);
    return (
        <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
            Current theme: {theme}
        </button>
    );
}
```

### Redux Integration
```jsx
// Slice
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

// Component
function Counter() {
    const count = useSelector(state => state.counter.value);
    const dispatch = useDispatch();

    return (
        <div>
            <button onClick={() => dispatch(decrement())}>-</button>
            <span>{count}</span>
            <button onClick={() => dispatch(increment())}>+</button>
        </div>
    );
}
```

## Performance Optimization

### Memoization
1. **useMemo**
   ```jsx
   function ExpensiveComponent({ data }) {
       const processedData = useMemo(() => {
           return data.map(item => expensiveOperation(item));
       }, [data]);

       return <div>{processedData.join(', ')}</div>;
   }
   ```

2. **useCallback**
   ```jsx
   function ParentComponent() {
       const [count, setCount] = useState(0);

       const handleClick = useCallback(() => {
           setCount(c => c + 1);
       }, []);

       return <ChildComponent onClick={handleClick} />;
   }
   ```

### Code Splitting
```jsx
const LazyComponent = React.lazy(() => import('./LazyComponent'));

function App() {
    return (
        <Suspense fallback={<div>Loading...</div>}>
            <LazyComponent />
        </Suspense>
    );
}
```

## Testing

### Jest and React Testing Library
```jsx
import { render, fireEvent, screen } from '@testing-library/react';

test('increments counter', () => {
    render(<Counter />);
    const button = screen.getByText('+');
    fireEvent.click(button);
    expect(screen.getByText('1')).toBeInTheDocument();
});
```

## Best Practices

### Component Organization
```text
src/
├── components/
│   ├── common/
│   ├── features/
│   └── layout/
├── hooks/
├── context/
├── services/
└── utils/
```

### Error Boundaries
```jsx
class ErrorBoundary extends React.Component {
    state = { hasError: false };

    static getDerivedStateFromError(error) {
        return { hasError: true };
    }

    componentDidCatch(error, errorInfo) {
        logErrorToService(error, errorInfo);
    }

    render() {
        if (this.state.hasError) {
            return <h1>Something went wrong.</h1>;
        }
        return this.props.children;
    }
}
```

## Conclusion
React provides a powerful and flexible foundation for building modern web applications, with a rich ecosystem of tools and libraries to support development.
