# Django Framework

## Introduction
Django is a high-level Python web framework that enables rapid development of secure and maintainable websites. It follows the model-template-view (MTV) architectural pattern and includes a robust ORM.

## Core Components

### Models
1. **Database Models**
   ```python
   from django.db import models
   
   class User(models.Model):
       username = models.CharField(max_length=100, unique=True)
       email = models.EmailField(unique=True)
       created_at = models.DateTimeField(auto_now_add=True)
       
       def __str__(self):
           return self.username
   
   class Profile(models.Model):
       user = models.OneToOneField(User, on_delete=models.CASCADE)
       bio = models.TextField(blank=True)
       avatar = models.ImageField(upload_to='avatars/', null=True)
   ```

2. **Model Relationships**
   ```python
   class Post(models.Model):
       author = models.ForeignKey(User, on_delete=models.CASCADE)
       title = models.CharField(max_length=200)
       content = models.TextField()
       tags = models.ManyToManyField('Tag', related_name='posts')
       
       class Meta:
           ordering = ['-created_at']
           
   class Comment(models.Model):
       post = models.ForeignKey(Post, on_delete=models.CASCADE)
       author = models.ForeignKey(User, on_delete=models.CASCADE)
       content = models.TextField()
   ```

### Views
1. **Function-Based Views**
   ```python
   from django.shortcuts import render, get_object_or_404
   from django.http import HttpResponse
   
   def post_list(request):
       posts = Post.objects.all()
       return render(request, 'blog/post_list.html', {'posts': posts})
   
   def post_detail(request, pk):
       post = get_object_or_404(Post, pk=pk)
       return render(request, 'blog/post_detail.html', {'post': post})
   ```

2. **Class-Based Views**
   ```python
   from django.views.generic import ListView, DetailView
   
   class PostListView(ListView):
       model = Post
       template_name = 'blog/post_list.html'
       context_object_name = 'posts'
       paginate_by = 10
       
       def get_queryset(self):
           return Post.objects.filter(status='published')
   
   class PostDetailView(DetailView):
       model = Post
       template_name = 'blog/post_detail.html'
   ```

### Templates
1. **Template Structure**
   ```html
   <!-- base.html -->
   <!DOCTYPE html>
   <html>
   <head>
       <title>{% block title %}{% endblock %}</title>
   </head>
   <body>
       {% include 'navbar.html' %}
       
       <div class="content">
           {% block content %}
           {% endblock %}
       </div>
       
       {% include 'footer.html' %}
   </body>
   </html>
   ```

2. **Template Tags and Filters**
   ```html
   {% extends 'base.html' %}
   
   {% block content %}
   <h1>{{ post.title }}</h1>
   <p>{{ post.content|linebreaks }}</p>
   
   {% for comment in post.comments.all %}
       <div class="comment">
           {{ comment.content }}
       </div>
   {% empty %}
       <p>No comments yet.</p>
   {% endfor %}
   {% endblock %}
   ```

## URL Configuration

### URL Patterns
```python
# urls.py
from django.urls import path, include
from . import views

app_name = 'blog'

urlpatterns = [
    path('', views.PostListView.as_view(), name='post_list'),
    path('post/<int:pk>/', views.PostDetailView.as_view(), name='post_detail'),
    path('post/new/', views.PostCreateView.as_view(), name='post_create'),
    path('api/', include('blog.api.urls')),
]
```

## Forms

### Form Handling
```python
from django import forms

class PostForm(forms.ModelForm):
    class Meta:
        model = Post
        fields = ['title', 'content', 'tags']
        
    def clean_title(self):
        title = self.cleaned_data['title']
        if len(title) < 5:
            raise forms.ValidationError("Title must be at least 5 characters long")
        return title

# View
def post_create(request):
    if request.method == 'POST':
        form = PostForm(request.POST)
        if form.is_valid():
            post = form.save(commit=False)
            post.author = request.user
            post.save()
            return redirect('blog:post_detail', pk=post.pk)
    else:
        form = PostForm()
    return render(request, 'blog/post_form.html', {'form': form})
```

## Authentication

### User Authentication
```python
from django.contrib.auth.decorators import login_required
from django.contrib.auth.mixins import LoginRequiredMixin

@login_required
def profile(request):
    return render(request, 'accounts/profile.html')

class PostCreateView(LoginRequiredMixin, CreateView):
    model = Post
    template_name = 'blog/post_form.html'
    fields = ['title', 'content']
    
    def form_valid(self, form):
        form.instance.author = self.request.user
        return super().form_valid(form)
```

## Middleware

### Custom Middleware
```python
class RequestTimingMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response
        
    def __call__(self, request):
        start_time = time.time()
        response = self.get_response(request)
        duration = time.time() - start_time
        response['X-Request-Duration'] = str(duration)
        return response
```

## Testing

### Unit Tests
```python
from django.test import TestCase
from django.urls import reverse

class PostTests(TestCase):
    def setUp(self):
        self.user = User.objects.create_user(
            username='testuser',
            password='testpass123'
        )
        self.post = Post.objects.create(
            title='Test Post',
            content='Test Content',
            author=self.user
        )
    
    def test_post_list_view(self):
        response = self.client.get(reverse('blog:post_list'))
        self.assertEqual(response.status_code, 200)
        self.assertContains(response, 'Test Post')
```

## Conclusion
Django provides a comprehensive framework for web development with Python, offering robust features for building secure and scalable applications.
