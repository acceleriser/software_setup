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


## Apply Bootstrap to home page

## Export the project to GitHub  


## Webserver setup  
### Import Git version  
python3 -m venv venv  
source venv/bin/activate  
pip install -r requirements.txt  
python3 manage.py makemigrations  
python3 manage.py migrate  
python3 manage.py collectstatic  
python3 manage.py runserver  

