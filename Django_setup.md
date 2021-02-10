# Setup Django test driven development backend with a Bootstrap frontend 

## Resources
Awesome Django\
https://github.com/shahraizali/awesome-django\
https://github.com/wsvincent/awesome-django\
https://awesomedjango.org

Gitignore: use Toptal generator\
https://www.toptal.com/developers/gitignore
gitignore for Django:\
https://www.toptal.com/developers/gitignore/api/django


## Create virtual environment
Download Gecko Driver: https://github.com/mozilla/geckodriver/releases
```
cd expafin_com
pip3 install virtualenv
virtualenv venv
source venv/bin/activate
pip install selenium
```
Move the downloaded and unzipped Gecko driver into venv/bin folder  
Test Selenium:
```python
from selenium import webdriver
browser = webdriver.Firefox()
browser.get('http://localhost:8000')
```


## Install Django locally  
```
pip install django
django-admin startproject expafin_com
cd expafin_com
django-admin startapp expafin
python manage.py runserver
```

## Add first functional test
In expafin/tests.py add first test
```python
from django.test import TestCase
from selenium import webdriver

class FunctionalTestCase(TestCase):

    def setUp(self):
        self.browser = webdriver.Firefox()

    def test_there_is_homepage(self):
        browser.get('http://localhost:8000')
        self.assertIn('install', self.browser.page_source)

    def tearDown(self):
        self.browser.quit()
```
Run test
``` python
python manage.py test`
```

## Add first unit test
``` python
class UnitTestCase(TestCase):

    def test_home_homepage_template(self):
        response = self.client.get('/')
        self.assertTemplateUsed(response, 'expafin/home.html')
```
Run test
``` python
python manage.py test
```

## Add home page
in expafin_com/**settings.py** add "'expafin'," to INSTALLED_APPS
``` python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'expafin',
]
```
In expafin_com/**urls.py**:
* add "path('',views.home, name='home')," to urlpatterns
* add "from expafin import views" to functions imports
``` python
from django.contrib import admin
from django.urls import path
from expafin import views

urlpatterns = [
    path('admin/', admin.site.urls),
    path('',views.home, name='home'),
]
```
In _expafin/_**views.py**:
Define homepage view
``` python
def home(request):
    return render(request,'expafin/home.html')
```
Create template directory `expafin/templates/`
Create project template sub-directory `expafin/templates/expafin/`

Create homepage template `expafin/templates/expafin/home.html` with the text 'Hello!'\
_Run test again_

## Create portfolio database 
### Install pillow in the virtual environment
```
pip3 install pillow
```
### In expafin/**models.py** add model class
``` python
class Job(models.Model):
    # Images 
    image = models.ImageField(upload_to='images/')
    # Summary 
    summary = models.CharField(max_length=200)
    # Details
    details = models.CharField(max_length=500)
```
### Install PostgreSQL
#### Download and install PostgreSQL 
Download from https://www.postgresql.org/download/\
*Install example in Arch Linux:* 
``` sql
sudo pacman -S postgresql
sudo -iu postgres initdb --locale=en_GB.UTF-8 -E UTF8 -D /var/lib/postgres/data
systemctl enable --now postgresql
sudo -iu postgres
createuser --interactive

```
The first user should be the superuser, like "db_admin".  
Make sure the port is 5432 and server starts automatically

#### Create PostgreSQL superuser, initialise database cluster and database
Login to postgres
``` sql
psql -U postgres
initdb --locale=en_GB.UTF-8 -E UTF8 -D /var/lib/postgres/data
psql
CREATE ROLE db_admin LOGIN SUPERUSER;
\password postgres
CREATE DATABASE portfoliodb;
```
Password should be something like "django1234".  
It will be stored in a db.pass text file above the project directory.  
Include .pass files in gitignore to avoid password leakage.

## Connect project to database
Install database connectors
```
pip3 install psycopg2
pip3 install psycopg2-binary
```
In *settings.py* add connection parameters:
``` python
db_pass_file = "../db.pass"
with open(db_pass_file) as f:
    db_pass = f.read().strip()

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'portfoliodb',
        'USER': 'postgres',
        'PASSWORD': db_pass,
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

## Apply migrations
```
python3 manage.py makemigrations
python3 manage.py migrate
```

## Set admin
```
python3 manage.py createsuperuser
```
Test admin login: http://localhost:8000/admin/  
Add automated admin login test:
```python
    def test_can_login_to_admin(self):
        pass_file = "../admin.pass"
        with open(pass_file) as f:
            password = f.read().strip()

        user_file = "../username.pass"
        with open(user_file) as f:
            username = f.read().strip()

        self.browser.get('http://localhost:8000/admin/')

        self.browser.find_element_by_name('username').send_keys(username)
        self.browser.find_element_by_name('password').send_keys(password , '\n')
        self.browser.find_element_by_name('next').click()

        self.assertIn('Groups', self.browser.page_source)

```
*In admin.py:*
``` python
from django.contrib import admin
from .models import Job

# Register your models here.
admin.site.register(Job)
```

## Inject Django code into the home page
*In views.py add:*
``` python
from django.shortcuts import render
from .models import Job

# Create your views here.
def home(request):
    jobs = Job.objects
    return render(request,'expafin/home.html', {'jobs': jobs})
```
*In home.html add:*
``` html
<h1>Portfolio</h1>

{% for job in jobs.all %}
<div>{{ job.summary }}</div>
{% endfor %}
```

## Apply Bootstrap template to home page
### Download bootstrap theme ###
https://getbootstrap.com/docs/5.0/examples/album/  
Just copy the page source into home.html
### Download Bootstrap compiled CSS and JS
https://getbootstrap.com/docs/5.0/getting-started/download/  
Replace the compiled CSS reference into home.html:
``` html
<!-- Bootstrap core CSS -->
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1" crossorigin="anonymous">
```
Replace the JS at the end of the page:  
``` html
    <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/js/bootstrap.bundle.min.js" integrity="sha384-ygbV9kiqUc6oa4msXn9868pTtWMgiQaeYH7/t7LECLbyPA2x65Kgf80OJFdroafW" crossorigin="anonymous"></script>
  </body>
</html>
```

## Tweak Bootstrap template
Adjust home.html:  
- Head  
- Header  
- Footer  
- Select body template

## Add static
- create folder static inside project (expafin) folder alongside templates folder
- insert static template images into "static" folder
- declare static folder in settings.py 
``` python
import os
...
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')
```
- declare static urls in urls.py 
``` python
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
] + static(settings.STATIC_URL, document_root = settings.STATIC_ROOT)
```

## Add and collect static content
Add media files in the static project folder (the deep one, not the top static folder which is generated automatically).\
Download CSS and JS files from https://getbootstrap.com/docs/5.0/getting-started/download/ and https://popper.js.org/ \
Add CSS and JS folders into  the static project folder.\

Colect static: run in terminal:
```
python manage.py collectstatic
```
Load startic content: in home.html, below html tag:
```
{% load static %} 
```
and use {% static 'static_file' %} syntax whenever necessary.

## Specify static iamges folder
In settings.py add:
``` python
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR
```
in urls.py add media links; final results should look like:
``` python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('', views.home, name='home'),
] 
urlpatterns += static(settings.STATIC_URL, document_root = settings.STATIC_ROOT)
urlpatterns += static(settings.MEDIA_URL, document_root = settings.MEDIA_ROOT)

```

## Insert data from database
For images: {{ job.image.url }}  
For text: {{ job.detail }}  

## Adjust admin panel
In models.py add a model view function inside the model class definiton:
``` python
def __str__(self):
        return self.summary
```

## Add detail urls




## Webserver setup  
### Import Git version  
python3 -m venv venv  
source venv/bin/activate  
pip install -r requirements.txt  
python3 manage.py makemigrations  
python3 manage.py migrate  
python3 manage.py collectstatic  
python3 manage.py runserver  

