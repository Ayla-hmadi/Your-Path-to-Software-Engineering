# Flutter Framework

## Introduction
Flutter is Google's UI toolkit for building natively compiled applications for mobile, web, and desktop from a single codebase using the Dart programming language.

## Core Concepts

### Widget System
1. **Stateless Widgets**
   ```dart
   class WelcomeCard extends StatelessWidget {
       final String userName;
       
       const WelcomeCard({Key? key, required this.userName}) : super(key: key);
       
       @override
       Widget build(BuildContext context) {
           return Card(
               child: Padding(
                   padding: const EdgeInsets.all(16.0),
                   child: Text('Welcome, $userName!'),
               ),
           );
       }
   }
   ```

2. **Stateful Widgets**
   ```dart
   class Counter extends StatefulWidget {
       @override
       _CounterState createState() => _CounterState();
   }
   
   class _CounterState extends State<Counter> {
       int _count = 0;
       
       void _increment() {
           setState(() {
               _count++;
           });
       }
       
       @override
       Widget build(BuildContext context) {
           return Column(
               children: [
                   Text('Count: $_count'),
                   ElevatedButton(
                       onPressed: _increment,
                       child: Text('Increment'),
                   ),
               ],
           );
       }
   }
   ```

### Layout System
```dart
class ProfileScreen extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return Scaffold(
            appBar: AppBar(title: Text('Profile')),
            body: Column(
                children: [
                    Container(
                        padding: EdgeInsets.all(16.0),
                        child: Row(
                            mainAxisAlignment: MainAxisAlignment.spaceBetween,
                            children: [
                                CircleAvatar(
                                    radius: 30,
                                    backgroundImage: NetworkImage('url'),
                                ),
                                Expanded(
                                    child: Padding(
                                        padding: EdgeInsets.symmetric(horizontal: 16.0),
                                        child: Column(
                                            crossAxisAlignment: CrossAxisAlignment.start,
                                            children: [
                                                Text('John Doe', style: Theme.of(context).textTheme.headline6),
                                                Text('john@example.com'),
                                            ],
                                        ),
                                    ),
                                ),
                            ],
                        ),
                    ),
                ],
            ),
        );
    }
}
```

## State Management

### Provider Pattern
```dart
class UserProvider extends ChangeNotifier {
    User? _user;
    
    User? get user => _user;
    
    void setUser(User user) {
        _user = user;
        notifyListeners();
    }
    
    Future<void> login(String username, String password) async {
        try {
            final user = await authService.login(username, password);
            setUser(user);
        } catch (e) {
            print('Login error: $e');
            throw e;
        }
    }
}

// Usage in widget
Consumer<UserProvider>(
    builder: (context, userProvider, child) {
        final user = userProvider.user;
        return Text(user?.name ?? 'Not logged in');
    },
)
```

### BLoC Pattern
```dart
class UserBloc extends Bloc<UserEvent, UserState> {
    final UserRepository userRepository;
    
    UserBloc({required this.userRepository}) : super(UserInitial()) {
        on<UserFetch>((event, emit) async {
            emit(UserLoading());
            try {
                final user = await userRepository.getUser(event.userId);
                emit(UserLoaded(user));
            } catch (e) {
                emit(UserError(e.toString()));
            }
        });
    }
}

// Usage in widget
BlocBuilder<UserBloc, UserState>(
    builder: (context, state) {
        if (state is UserLoading) {
            return CircularProgressIndicator();
        } else if (state is UserLoaded) {
            return Text(state.user.name);
        } else if (state is UserError) {
            return Text('Error: ${state.message}');
        }
        return Container();
    },
)
```

## Navigation

### Route Management
```dart
class AppRouter {
    static Route<dynamic> generateRoute(RouteSettings settings) {
        switch (settings.name) {
            case '/':
                return MaterialPageRoute(builder: (_) => HomeScreen());
            case '/profile':
                final args = settings.arguments as Map<String, dynamic>;
                return MaterialPageRoute(
                    builder: (_) => ProfileScreen(userId: args['userId']),
                );
            default:
                return MaterialPageRoute(
                    builder: (_) => ErrorScreen(),
                );
        }
    }
}

// Navigation
Navigator.pushNamed(
    context,
    '/profile',
    arguments: {'userId': '123'},
);
```

## Platform Integration

### Native Code Integration
```dart
// Platform channel definition
static const platform = MethodChannel('com.example.app/battery');

// Calling native code
Future<void> getBatteryLevel() async {
    try {
        final int result = await platform.invokeMethod('getBatteryLevel');
        setState(() {
            batteryLevel = result;
        });
    } on PlatformException catch (e) {
        print("Failed to get battery level: '${e.message}'.");
    }
}
```

## Testing

### Widget Testing
```dart
void main() {
    testWidgets('Counter increments smoke test', (WidgetTester tester) async {
        await tester.pumpWidget(MyApp());
        
        expect(find.text('0'), findsOneWidget);
        expect(find.text('1'), findsNothing);
        
        await tester.tap(find.byIcon(Icons.add));
        await tester.pump();
        
        expect(find.text('0'), findsNothing);
        expect(find.text('1'), findsOneWidget);
    });
}
```

## Best Practices

### Code Organization
```text
lib/
├── core/
│   ├── theme/
│   └── utils/
├── features/
│   ├── auth/
│   │   ├── data/
│   │   ├── domain/
│   │   └── presentation/
│   └── home/
├── shared/
│   ├── widgets/
│   └── models/
└── main.dart
```

### Performance Optimization
1. **Widget Optimization**
   - Use const constructors
   - Implement shouldRebuild
   - Cache expensive computations
   - Use ListView.builder for long lists
   - Optimize image loading

2. **Memory Management**
   ```dart
   class _MyWidgetState extends State<MyWidget> {
       StreamSubscription? _subscription;
       
       @override
       void initState() {
           super.initState();
           _subscription = stream.listen((_) {});
       }
       
       @override
       void dispose() {
           _subscription?.cancel();
           super.dispose();
       }
   }
   ```

## Conclusion
Flutter provides a comprehensive framework for building cross-platform applications with a single codebase, offering excellent performance and a rich widget ecosystem.
