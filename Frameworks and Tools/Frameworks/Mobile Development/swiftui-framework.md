# SwiftUI Framework

## Introduction
SwiftUI is Apple's modern UI framework for building user interfaces across all Apple platforms using Swift. It provides a declarative syntax and powerful data-driven features.

## Core Concepts

### Views and Modifiers
```swift
struct ContentView: View {
    var body: some View {
        VStack(spacing: 20) {
            Text("Hello, SwiftUI!")
                .font(.title)
                .foregroundColor(.blue)
                .padding()
            
            Image(systemName: "star.fill")
                .resizable()
                .frame(width: 50, height: 50)
                .foregroundColor(.yellow)
        }
        .background(Color.white)
        .cornerRadius(10)
        .shadow(radius: 5)
    }
}
```

### State Management
1. **State Property**
   ```swift
   struct CounterView: View {
       @State private var count = 0
       
       var body: some View {
           VStack {
               Text("Count: \(count)")
               Button("Increment") {
                   count += 1
               }
           }
       }
   }
   ```

2. **ObservableObject**
   ```swift
   class UserViewModel: ObservableObject {
       @Published var username = ""
       @Published var isLoggedIn = false
       
       func login() {
           // Perform login
           isLoggedIn = true
       }
   }
   
   struct UserView: View {
       @StateObject private var viewModel = UserViewModel()
       
       var body: some View {
           VStack {
               TextField("Username", text: $viewModel.username)
               Button("Login") {
                   viewModel.login()
               }
           }
       }
   }
   ```

## Layout System

### Stack Views
```swift
struct ProfileView: View {
    var body: some View {
        VStack(alignment: .leading, spacing: 10) {
            HStack {
                Image(systemName: "person.circle.fill")
                    .resizable()
                    .frame(width: 60, height: 60)
                
                VStack(alignment: .leading) {
                    Text("John Doe")
                        .font(.headline)
                    Text("iOS Developer")
                        .font(.subheadline)
                        .foregroundColor(.gray)
                }
            }
            
            Divider()
            
            Text("About")
                .font(.title2)
            Text("Lorem ipsum dolor sit amet...")
                .font(.body)
        }
        .padding()
    }
}
```

### Custom Layouts
```swift
struct GridLayout: Layout {
    var spacing: CGFloat = 8
    var columns: Int = 3
    
    func sizeThatFits(proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) -> CGSize {
        // Layout calculation
        let maxWidth = proposal.width ?? .infinity
        let spacing = CGFloat(columns - 1) * self.spacing
        let itemWidth = (maxWidth - spacing) / CGFloat(columns)
        
        // Calculate total height
        let rows = ceil(Double(subviews.count) / Double(columns))
        let height = rows * itemWidth + (rows - 1) * spacing
        
        return CGSize(width: maxWidth, height: height)
    }
    
    func placeSubviews(in bounds: CGRect, proposal: ProposedViewSize, subviews: Subviews, cache: inout ()) {
        // Place subviews in grid
    }
}
```

## Data Flow

### Environment Objects
```swift
class AppSettings: ObservableObject {
    @Published var isDarkMode = false
    @Published var fontSize: CGFloat = 14
}

struct RootView: View {
    @StateObject private var settings = AppSettings()
    
    var body: some View {
        ContentView()
            .environmentObject(settings)
    }
}

struct ContentView: View {
    @EnvironmentObject var settings: AppSettings
    
    var body: some View {
        Text("Hello")
            .font(.system(size: settings.fontSize))
            .preferredColorScheme(settings.isDarkMode ? .dark : .light)
    }
}
```

## Navigation

### NavigationStack
```swift
struct NavigationExample: View {
    var body: some View {
        NavigationStack {
            List(items) { item in
                NavigationLink(item.title) {
                    DetailView(item: item)
                }
            }
            .navigationTitle("Items")
            .navigationBarTitleDisplayMode(.large)
            .toolbar {
                ToolbarItem(placement: .navigationBarTrailing) {
                    Button("Add") {
                        // Add action
                    }
                }
            }
        }
    }
}
```

## Animations

### View Animations
```swift
struct AnimationExample: View {
    @State private var isExpanded = false
    
    var body: some View {
        VStack {
            RoundedRectangle(cornerRadius: 10)
                .fill(Color.blue)
                .frame(width: isExpanded ? 200 : 100,
                       height: isExpanded ? 200 : 100)
                .animation(.spring(), value: isExpanded)
            
            Button("Toggle") {
                withAnimation {
                    isExpanded.toggle()
                }
            }
        }
    }
}
```

## Testing

### Unit Testing
```swift
import XCTest
@testable import YourApp

class ViewModelTests: XCTestCase {
    var viewModel: ViewModel!
    
    override func setUp() {
        super.setUp()
        viewModel = ViewModel()
    }
    
    func testDataLoading() async {
        await viewModel.loadData()
        XCTAssertFalse(viewModel.items.isEmpty)
        XCTAssertNil(viewModel.error)
    }
}
```

## Best Practices

### Performance Optimization
1. **View Updates**
   ```swift
   struct OptimizedView: View {
       let items: [Item]
       
       var body: some View {
           List(items) { item in
               ItemRow(item: item)
                   .id(item.id) // Optimize updates
           }
       }
   }
   ```

2. **Memory Management**
   ```swift
   class ImageLoader: ObservableObject {
       @Published var image: UIImage?
       private var cancellable: AnyCancellable?
       
       deinit {
           cancellable?.cancel()
       }
       
       func loadImage(from url: URL) {
           cancellable = URLSession.shared.dataTaskPublisher(for: url)
               .map { UIImage(data: $0.data) }
               .replaceError(with: nil)
               .receive(on: DispatchQueue.main)
               .assign(to: \.image, on: self)
       }
   }
   ```

## Conclusion
SwiftUI provides a modern, declarative approach to building user interfaces for Apple platforms, with powerful features for state management and animations.
