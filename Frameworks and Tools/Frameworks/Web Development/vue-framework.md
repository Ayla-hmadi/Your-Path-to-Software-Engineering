# Vue.js Framework

## Introduction
Vue.js is a progressive JavaScript framework for building user interfaces. It's designed to be incrementally adoptable and can scale between a library and a full-featured framework.

## Core Concepts

### Component Structure
1. **Single File Components**
   ```vue
   <!-- UserProfile.vue -->
   <template>
       <div class="profile">
           <h2>{{ user.name }}</h2>
           <p>{{ user.email }}</p>
           <button @click="updateProfile">Update</button>
       </div>
   </template>

   <script>
   export default {
       name: 'UserProfile',
       props: {
           user: {
               type: Object,
               required: true
           }
       },
       methods: {
           updateProfile() {
               this.$emit('update', this.user.id);
           }
       }
   }
   </script>

   <style scoped>
   .profile {
       padding: 20px;
       border: 1px solid #ddd;
   }
   </style>
   ```

2. **Composition API**
   ```vue
   <script setup>
   import { ref, computed, onMounted } from 'vue';

   const count = ref(0);
   const doubled = computed(() => count.value * 2);

   function increment() {
       count.value++;
   }

   onMounted(() => {
       console.log('Component mounted');
   });
   </script>
   ```

### Reactivity System
1. **Options API**
   ```javascript
   export default {
       data() {
           return {
               items: [],
               loading: false
           };
       },
       computed: {
           totalItems() {
               return this.items.length;
           }
       },
       watch: {
           items: {
               handler(newItems) {
                   localStorage.setItem('items', JSON.stringify(newItems));
               },
               deep: true
           }
       }
   }
   ```

2. **Composition API Reactivity**
   ```javascript
   import { ref, reactive, watch } from 'vue';

   const state = reactive({
       user: null,
       preferences: {}
   });

   const searchQuery = ref('');

   watch(searchQuery, async (newQuery) => {
       if (newQuery.length > 2) {
           const results = await searchAPI(newQuery);
           state.searchResults = results;
       }
   });
   ```

## State Management

### Vuex Integration
```javascript
// store/index.js
import { createStore } from 'vuex';

export default createStore({
    state: {
        user: null,
        tokens: []
    },
    mutations: {
        SET_USER(state, user) {
            state.user = user;
        }
    },
    actions: {
        async login({ commit }, credentials) {
            try {
                const user = await authAPI.login(credentials);
                commit('SET_USER', user);
            } catch (error) {
                console.error('Login failed:', error);
            }
        }
    },
    getters: {
        isAuthenticated: state => !!state.user
    }
});
```

### Pinia (Modern State Management)
```javascript
import { defineStore } from 'pinia';

export const useUserStore = defineStore('user', {
    state: () => ({
        user: null,
        preferences: {}
    }),
    getters: {
        fullName: (state) => 
            `${state.user?.firstName} ${state.user?.lastName}`
    },
    actions: {
        async fetchUser(id) {
            this.user = await api.getUser(id);
        }
    }
});
```

## Routing

### Vue Router Configuration
```javascript
import { createRouter, createWebHistory } from 'vue-router';

const router = createRouter({
    history: createWebHistory(),
    routes: [
        {
            path: '/',
            component: Home
        },
        {
            path: '/users/:id',
            component: UserProfile,
            props: true,
            beforeEnter: (to, from, next) => {
                // Route guard logic
                if (isAuthenticated()) next();
                else next('/login');
            }
        },
        {
            path: '/admin',
            component: () => import('./views/Admin.vue'),
            meta: { requiresAuth: true }
        }
    ]
});
```

## Performance Optimization

### Dynamic Imports
```javascript
// Router lazy loading
const routes = [
    {
        path: '/dashboard',
        component: () => import('./views/Dashboard.vue')
    }
];

// Component lazy loading
const MyAsyncComponent = defineAsyncComponent(() =>
    import('./components/MyComponent.vue')
);
```

### Keep-Alive Component
```vue
<template>
    <keep-alive>
        <component :is="currentView"></component>
    </keep-alive>
</template>

<script>
export default {
    data() {
        return {
            currentView: 'Home'
        };
    }
};
</script>
```

## Testing

### Unit Testing
```javascript
import { mount } from '@vue/test-utils';
import UserProfile from '@/components/UserProfile.vue';

describe('UserProfile', () => {
    it('renders user information', () => {
        const wrapper = mount(UserProfile, {
            props: {
                user: {
                    name: 'John Doe',
                    email: 'john@example.com'
                }
            }
        });

        expect(wrapper.text()).toContain('John Doe');
        expect(wrapper.text()).toContain('john@example.com');
    });

    it('emits update event', async () => {
        const wrapper = mount(UserProfile, {
            props: { user: { id: 1 } }
        });

        await wrapper.find('button').trigger('click');
        expect(wrapper.emitted().update[0]).toEqual([1]);
    });
});
```

## Best Practices

### Project Structure
```text
src/
├── assets/
├── components/
│   ├── common/
│   └── features/
├── composables/
├── router/
├── store/
└── views/
```

### Component Design
```vue
<!-- Good component design -->
<template>
    <div class="user-card">
        <slot name="header">
            <h2>{{ title }}</h2>
        </slot>
        <slot></slot>
        <slot name="footer"></slot>
    </div>
</template>

<script>
export default {
    name: 'UserCard',
    props: {
        title: {
            type: String,
            required: true,
            validator: value => value.length > 0
        }
    }
};
</script>
```

## Conclusion
Vue.js provides a flexible and powerful framework for building modern web applications, with excellent documentation and a supportive community.
