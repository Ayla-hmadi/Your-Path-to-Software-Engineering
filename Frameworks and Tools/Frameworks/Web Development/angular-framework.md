# Angular Framework

## Introduction
Angular is a comprehensive TypeScript-based web application framework maintained by Google. It provides a complete solution for building scalable web applications with features like two-way data binding, dependency injection, and modular architecture.

## Core Concepts

### Components
1. **Component Structure**
   ```typescript
   // user-profile.component.ts
   @Component({
       selector: 'app-user-profile',
       template: `
           <div class="profile">
               <h2>{{user.name}}</h2>
               <p>{{user.email}}</p>
           </div>
       `
   })
   export class UserProfileComponent {
       @Input() user: User;
       
       constructor() { }
   }
   ```

2. **Lifecycle Hooks**
   ```typescript
   export class DataComponent implements OnInit, OnDestroy {
       private subscription: Subscription;
       
       ngOnInit() {
           this.subscription = this.dataService
               .getData()
               .subscribe(data => this.processData(data));
       }
       
       ngOnDestroy() {
           this.subscription.unsubscribe();
       }
   }
   ```

### Services and Dependency Injection
```typescript
@Injectable({
    providedIn: 'root'
})
export class UserService {
    constructor(private http: HttpClient) { }
    
    getUsers(): Observable<User[]> {
        return this.http.get<User[]>('/api/users');
    }
    
    createUser(user: User): Observable<User> {
        return this.http.post<User>('/api/users', user);
    }
}

// Component using the service
@Component({...})
export class UserListComponent {
    users$: Observable<User[]>;
    
    constructor(private userService: UserService) {
        this.users$ = this.userService.getUsers();
    }
}
```

### Modules
```typescript
@NgModule({
    declarations: [
        AppComponent,
        UserProfileComponent,
        UserListComponent
    ],
    imports: [
        BrowserModule,
        HttpClientModule,
        RouterModule.forRoot(routes)
    ],
    providers: [
        UserService,
        { provide: API_URL, useValue: 'http://api.example.com' }
    ],
    bootstrap: [AppComponent]
})
export class AppModule { }
```

## Data Management

### Template Forms
```typescript
// Template
<form #userForm="ngForm" (ngSubmit)="onSubmit(userForm.value)">
    <input name="username" [(ngModel)]="user.username" required>
    <input name="email" [(ngModel)]="user.email" required email>
    <button type="submit" [disabled]="!userForm.valid">Submit</button>
</form>

// Component
export class UserFormComponent {
    user = {
        username: '',
        email: ''
    };
    
    onSubmit(formValue: any) {
        console.log('Form submitted:', formValue);
    }
}
```

### Reactive Forms
```typescript
export class UserReactiveFormComponent implements OnInit {
    userForm: FormGroup;
    
    constructor(private fb: FormBuilder) { }
    
    ngOnInit() {
        this.userForm = this.fb.group({
            username: ['', [Validators.required, Validators.minLength(4)]],
            email: ['', [Validators.required, Validators.email]],
            address: this.fb.group({
                street: [''],
                city: [''],
                zip: ['']
            })
        });
    }
    
    onSubmit() {
        if (this.userForm.valid) {
            console.log(this.userForm.value);
        }
    }
}
```

## Routing

### Route Configuration
```typescript
const routes: Routes = [
    { 
        path: 'users',
        component: UserListComponent,
        canActivate: [AuthGuard],
        children: [
            { 
                path: ':id',
                component: UserDetailComponent,
                resolve: {
                    user: UserResolver
                }
            }
        ]
    },
    {
        path: 'admin',
        loadChildren: () => import('./admin/admin.module')
            .then(m => m.AdminModule)
    }
];
```

### Route Guards
```typescript
@Injectable({
    providedIn: 'root'
})
export class AuthGuard implements CanActivate {
    constructor(
        private authService: AuthService,
        private router: Router
    ) { }
    
    canActivate(
        route: ActivatedRouteSnapshot,
        state: RouterStateSnapshot
    ): boolean | UrlTree {
        if (this.authService.isAuthenticated()) {
            return true;
        }
        return this.router.parseUrl('/login');
    }
}
```

## HTTP and APIs

### HTTP Client
```typescript
@Injectable({
    providedIn: 'root'
})
export class DataService {
    constructor(private http: HttpClient) { }
    
    getData(): Observable<any> {
        return this.http.get('/api/data').pipe(
            retry(3),
            catchError(this.handleError),
            map(response => this.processResponse(response))
        );
    }
    
    private handleError(error: HttpErrorResponse) {
        // Error handling logic
        return throwError(() => error);
    }
}
```

## Testing

### Component Testing
```typescript
describe('UserProfileComponent', () => {
    let component: UserProfileComponent;
    let fixture: ComponentFixture<UserProfileComponent>;
    
    beforeEach(async () => {
        await TestBed.configureTestingModule({
            declarations: [ UserProfileComponent ],
            providers: [ UserService ]
        }).compileComponents();
    });
    
    beforeEach(() => {
        fixture = TestBed.createComponent(UserProfileComponent);
        component = fixture.componentInstance;
        fixture.detectChanges();
    });
    
    it('should create', () => {
        expect(component).toBeTruthy();
    });
});
```

## Best Practices

### Project Structure
```text
src/
├── app/
│   ├── core/
│   │   ├── guards/
│   │   ├── interceptors/
│   │   └── services/
│   ├── shared/
│   │   ├── components/
│   │   ├── directives/
│   │   └── pipes/
│   └── features/
│       ├── users/
│       └── admin/
├── assets/
└── environments/
```

### Performance Optimization
1. **Change Detection**
   ```typescript
   @Component({
       selector: 'app-user-list',
       changeDetection: ChangeDetectionStrategy.OnPush
   })
   export class UserListComponent {
       @Input() users: User[];
   }
   ```

2. **Lazy Loading**
   ```typescript
   const routes: Routes = [{
       path: 'admin',
       loadChildren: () => import('./admin/admin.module')
           .then(m => m.AdminModule)
   }];
   ```

## Conclusion
Angular provides a robust framework for building enterprise-scale applications with strong typing, comprehensive tooling, and extensive features out of the box.
