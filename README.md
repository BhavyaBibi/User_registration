# User_registration
this is for User Registration and Login Authentication in Django one of demo project

I will discuss how to handle user registration, login, and logout in a Django application.

Table of Contents:
· Initial Setup
· Creating the Register view
  ∘ views.py
  ∘ Templates
  ∘ regiseter.html
  ∘ urls.py
· Registration Form With Additional Fields
  ∘ forms.py
  ∘ views.py
  ∘ register.html
· Log in and Log out
  ∘ login.html
  ∘ home.html
· Conclusion

Initial Setup

django-admin startproject demo

virtualenv venv

python manage.py startapp users

python3.9 manage.py startapp users

python manage.py makemigratons
python manage.py migrate

python manage.py createsuperuser

Creating the Register view

views.py


from django.shortcuts import render, redirect
from django.contrib.auth import login, authenticate
from django.contrib import messages
from django.contrib.auth.forms import UserCreationForm

def home(request):
    return render(request, 'users/home.html')

def register(request):
    if request.method == 'POST':
        form = UserCreationForm(request.POST)
        if form.is_valid():
            form.save()

            messages.success(request, f'Your account has been created. You can log in now!')    
            return redirect('login')
    else:
        form = UserCreationForm()

    context = {'form': form}
    return render(request, 'users/register.html', context)

    Templates
We need the necessary html files for our views. Inside the users app, we will create our templates. Inside the users directory, we will create a directory called templates. Then inside the templates directory, we will create another directory named users. Here we will put the html files. In our case home.html and register.html.

register.html

<h3>Register here</h3>
<hr>

<form action="" method="POST">
    {% csrf_token %}
    {{ form.as_p }}

    <button type="submit">Register</button>
</form>

urls.py

from django.contrib import admin
from django.urls import path
from django.contrib.auth import views as auth_views

from users import views as user_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', user_views.home, name='home'),
    path('register/', user_views.register, name='register'),
]

python manage.py runserver

Registration Form With Additional Fields

forms.py

from django import forms
from django.contrib.auth.forms import UserCreationForm
from django.contrib.auth.models import User


class UserRegistrationForm(UserCreationForm):
    first_name = forms.CharField(max_length=101)
    last_name = forms.CharField(max_length=101)
    email = forms.EmailField()

    class Meta:
        model = User
        fields = ['username', 'first_name', 'last_name', 'email', 'password1', 'password2']

views.py


from django.shortcuts import render, redirect
from django.contrib.auth import login, authenticate
from django.contrib import messages
from .forms import UserRegistrationForm

def home(request):
    return render(request, 'users/home.html')

def register(request):
    if request.method == 'POST':
        form = UserRegistrationForm(request.POST)
        if form.is_valid():
            form.save()

            messages.success(request, f'Your account has been created. You can log in now!')    
            return redirect('login')
    else:
        form = UserRegistrationForm()

    context = {'form': form}
    return render(request, 'users/register.html', context)

register.html

<h3>Register here</h3>
<hr>

<form action="" method="POST">
    {% csrf_token %}

    {% for field in form %}
        <div>
            <p>{{ field.label }}: <br> {{ field }}</p> 

            {% for error in field.errors %}
                <small style="color: red">{{ error }}</small>
            {% endfor %}
        </div>
    {% endfor %}
    <button type="submit">Register</button>
</form>

Log in and Log out

from django.contrib import admin
from django.urls import path
from django.contrib.auth import views as auth_views

from users import views as user_views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', user_views.home, name='home'),
    path('register/', user_views.register, name='register'),
  
    path('login/', auth_views.LoginView.as_view(template_name='users/login.html'), name='login'),
    path('logout/', auth_views.LogoutView.as_view(template_name='users/logout.html'), name='logout'),
]

login.html

<h3>login</h3>

<form action="" method="POST">
    {% csrf_token %}
    {% for field in form %}
        <p>{{ field.label }}: <br> {{ field }}</p>
    {% endfor %}
    <button type="submit">Login</button>
</form>

<p>Don't have an account? <a href="{% url 'register' %}">Signup here</a></p>

LOGIN_REDIRECT_URL = ‘home’



home.html

{% if user.is_authenticated %}
    <div class="alert alert-success" role="alert">
        <h3>Welcome {{ user.username }}!</h3>
        <a href="{% url 'logout' %}">Logout?</a>
    </div>
{% else %}
    <div class="alert alert-success" role="alert">
        <h3>Welcome!</h3>
        <a href="{% url 'register' %}">Signup</a>
        <a href="{% url 'login' %}">Login</a>
    </div>
{% endif %}

Conclusion

And that’s pretty much it! User authentication is done. There is a lot we can do here though. Like user registration with email confirmation. Or having the option for a password reset. Or maybe add fields like date of birth, join date, etc. With some research, we can add those too.



