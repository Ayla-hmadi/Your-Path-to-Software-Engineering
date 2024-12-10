# React Native Framework

## Introduction
React Native is a popular framework for building native mobile applications using JavaScript and React. It allows developers to create mobile apps that run natively on both iOS and Android platforms.

## Core Components

### Basic Components
```javascript
import { View, Text, StyleSheet, TouchableOpacity } from 'react-native';

const WelcomeScreen = () => {
    return (
        <View style={styles.container}>
            <Text style={styles.title}>Welcome to App</Text>
            <TouchableOpacity 
                style={styles.button}
                onPress={() => console.log('Pressed')}
            >
                <Text style={styles.buttonText}>Get Started</Text>
            </TouchableOpacity>
        </View>
    );
};

const styles = StyleSheet.create({
    container: {
        flex: 1,
        justifyContent: 'center',
        alignItems: 'center',
    },
    title: {
        fontSize: 24,
        marginBottom: 20,
    },
    button: {
        backgroundColor: '#007AFF',
        padding: 15,
        borderRadius: 8,
    },
    buttonText: {
        color: 'white',
        fontSize: 16,
    },
});
```

### Functional Components
```javascript
import React, { useState, useEffect } from 'react';
import { View, FlatList, ActivityIndicator } from 'react-native';

const UserList = () => {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        fetchUsers();
    }, []);

    const fetchUsers = async () => {
        try {
            const response = await fetch('https://api.example.com/users');
            const data = await response.json();
            setUsers(data);
        } catch (error) {
            console.error(error);
        } finally {
            setLoading(false);
        }
    };

    if (loading) {
        return <ActivityIndicator size="large" color="#0000ff" />;
    }

    return (
        <FlatList
            data={users}
            keyExtractor={item => item.id.toString()}
            renderItem={({ item }) => (
                <UserCard user={item} />
            )}
        />
    );
};
```

## Navigation

### React Navigation
```javascript
import { NavigationContainer } from '@react-navigation/native';
import { createStackNavigator } from '@react-navigation/stack';

const Stack = createStackNavigator();

const App = () => {
    return (
        <NavigationContainer>
            <Stack.Navigator initialRouteName="Home">
                <Stack.Screen 
                    name="Home" 
                    component={HomeScreen}
                    options={{
                        title: 'Home',
                        headerStyle: {
                            backgroundColor: '#f4511e',
                        },
                        headerTintColor: '#fff',
                    }}
                />
                <Stack.Screen 
                    name="Details" 
                    component={DetailsScreen} 
                />
            </Stack.Navigator>
        </NavigationContainer>
    );
};

// Screen with navigation
const HomeScreen = ({ navigation }) => {
    return (
        <View>
            <Button
                title="Go to Details"
                onPress={() => navigation.navigate('Details', { id: 1 })}
            />
        </View>
    );
};
```

## State Management

### Redux Integration
```javascript
// Store setup
import { configureStore } from '@reduxjs/toolkit';

const store = configureStore({
    reducer: {
        user: userReducer,
        posts: postsReducer,
    },
});

// Slice
import { createSlice } from '@reduxjs/toolkit';

const userSlice = createSlice({
    name: 'user',
    initialState: {
        data: null,
        loading: false,
        error: null,
    },
    reducers: {
        setUser: (state, action) => {
            state.data = action.payload;
        },
        setLoading: (state, action) => {
            state.loading = action.payload;
        },
    },
});

// Component usage
import { useSelector, useDispatch } from 'react-redux';

const UserProfile = () => {
    const user = useSelector(state => state.user.data);
    const dispatch = useDispatch();

    return (
        <View>
            <Text>{user?.name}</Text>
        </View>
    );
};
```

## Native Modules

### Bridge to Native Code
```javascript
// Native Module Definition (iOS)
@objc(CalendarManager)
class CalendarManager: NSObject {
    @objc
    func addEvent(_ name: String, location: String, date: NSNumber) {
        // Native implementation
    }
}

// JavaScript Interface
import { NativeModules } from 'react-native';
const { CalendarManager } = NativeModules;

// Usage
const scheduleEvent = async () => {
    try {
        await CalendarManager.addEvent(
            'Birthday Party',
            'Home',
            Date.now()
        );
    } catch (e) {
        console.error(e);
    }
};
```

## Performance Optimization

### Memory Management
```javascript
const Component = () => {
    const [data, setData] = useState([]);
    
    useEffect(() => {
        let mounted = true;
        
        const fetchData = async () => {
            const result = await API.getData();
            if (mounted) {
                setData(result);
            }
        };
        
        fetchData();
        
        return () => {
            mounted = false;
        };
    }, []);
};
```

### List Optimization
```javascript
const OptimizedList = () => {
    const renderItem = useCallback(({ item }) => (
        <ItemComponent item={item} />
    ), []);

    const keyExtractor = useCallback((item) => 
        item.id.toString(), []);

    return (
        <FlatList
            data={items}
            renderItem={renderItem}
            keyExtractor={keyExtractor}
            getItemLayout={(data, index) => ({
                length: ITEM_HEIGHT,
                offset: ITEM_HEIGHT * index,
                index,
            })}
            windowSize={5}
            maxToRenderPerBatch={5}
            initialNumToRender={10}
        />
    );
};
```

## Testing

### Jest and React Native Testing Library
```javascript
import { render, fireEvent } from '@testing-library/react-native';

describe('Button Component', () => {
    it('calls onPress when pressed', () => {
        const onPress = jest.fn();
        const { getByText } = render(
            <Button onPress={onPress} title="Press Me" />
        );
        
        fireEvent.press(getByText('Press Me'));
        expect(onPress).toHaveBeenCalled();
    });
});
```

## Conclusion
React Native provides a powerful framework for building cross-platform mobile applications with JavaScript, offering native performance and a rich ecosystem of libraries and tools.
