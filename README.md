###### This repository includes something about python & django & rest framework, enjoy ^_^!<p>

### install essential packages<p>
- install pip<p>
`sudo apt-get install python-pip`<p>
- install virtualenv<p>
`sudo pip install virtualenv`<p>
`Ps: all deployments after depand on virtualenv!`<p>
`Caveat: virtualenv can not relocate some configuration files well, and the environment it has made is not relocatable well either, so using this repository's environment directly is not recommended!`<p>

***

### deploy all in one enviroment<p>
- use virtualenv<p>
`cd anywhere-you-want`<p>
`virtualenv env-name-you-point`<p>
`cd env-name-you-point`<p>
- install django<p>
`bin/pip install django`<p>
- install django rest framework<p>
`bin/pip install djangorestframework`<p>
- install pygments<p>
`bin/pip install pygments # We'll be using this for the code highlighting`<p>
- install django rest framework jwt<p>
`bin/pip  install djangorestframework-jwt`<p>

***

### hello world with django rest framework (use models)<p>
###### From this demo, you can get user's information from restful api.<p>
- create a django project named tutorial<p>
`bin/django-admin startproject tutorial`<p>
- go into tutorial folder<p>
`cd tutorial`<p>
- create a django app named quickstart<p>
`../bin/django-admin startapp quickstart`<p>
- synchronize database<p>
`../bin/python manage.py migrate`<p>
- add superuser<p>
`../bin/python manage.py createsuperuser # name: admin & password: password123 & email can be null`<p>
`Ps: If you didn't create a superuser, you could not access to django server's background.`<p>
- create & edit serializers.py<p>
`vim quickstart/serializers.py`<p>

        from django.contrib.auth.models import User, Group
        from rest_framework import serializers
        
        class UserSerializer(serializers.HyperlinkedModelSerializer):
            class Meta:
                model = User
                fields = ('url', 'username', 'email', 'groups')
        
        class GroupSerializer(serializers.HyperlinkedModelSerializer):
            class Meta:
                model = Group
                fields = ('url', 'name')

- edit views.py<p>
`vim quickstart/views.py`<p>

        from django.contrib.auth.models import User, Group
        from rest_framework import viewsets
        from quickstart.serializers import UserSerializer, GroupSerializer

        class UserViewSet(viewsets.ModelViewSet):
            """
            API endpoint that allows users to be viewed or edited.
            """
            queryset = User.objects.all().order_by('-date_joined')
            serializer_class = UserSerializer
        
        class GroupViewSet(viewsets.ModelViewSet):
            """
            API endpoint that allows groups to be viewed or edited.
            """
            queryset = Group.objects.all()
            serializer_class = GroupSerializer

- edit urls.py<p>
`vim tutorial/urls.py`<p>

        from django.conf.urls import url, include
        from rest_framework import routers
        from quickstart import views
        
        router = routers.DefaultRouter()
        router.register(r'users', views.UserViewSet)
        router.register(r'groups', views.GroupViewSet)
        
        # Wire up our API using automatic URL routing.
        # Additionally, we include login URLs for the browsable API.
        urlpatterns = [
            url(r'^', include(router.urls)),
            url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')) # If you only use curl to test, you can rid of this row.
        ]

- edit settings.py<p>
`vim tutorial/settings.py`<p>

        INSTALLED_APPS = (
            ...
            'rest_framework',
            'quickstart',
        )
        
        REST_FRAMEWORK = {
            'DEFAULT_PERMISSION_CLASSES': ('rest_framework.permissions.IsAdminUser',), # PS: IsAdminUser(have to login) AllowAny(do not need to login) IsAuthenticated(use in jwt)
            'PAGE_SIZE': 10
        }

- test<p>
 + run web server<p>
`../bin/python manage.py runserver`<p>
 + get information<p>
`curl -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/`<p>
 + result<p>
 `{ "count": 1, "next": null, "previous": null, "results": [ { "url": "http://127.0.0.1:8000/users/1/", "username": "admin", "email": "admin@admin.com", "groups": [] } ] }`<p>
 + set information<p>
`curl -X POST -d username=newname -d email=newname@example.com -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/`<p>
 + result<p>
 `{ "count": 2, "next": null, "previous": null, "results": [ { "url": "http://127.0.0.1:8000/users/2/", "username": "newname", "email": "newname@example.com", "groups": [] }, { "url": "http://127.0.0.1:8000/users/1/", "username": "admin", "email": "admin@admin.com", "groups": [] } ] }`<p>

### hello world with django rest framework jwt<p>
###### From this demo, you can get user's information from restful api with token.<p>
###### Pre-Condition: you've already done with "hello world with django rest framework (use models)".<p>
- copy project "tutorial" to "tutorial-jwt"<p>
`cp -rf tutorial tutorial-jwt`<p>
- enter tutorial-jwt folder<p>
`cd tutorial-jwt`<p>
- edit settings.py<p>
`vim tutorial/settings.py`<p>

        REST_FRAMEWORK = {
            'DEFAULT_PERMISSION_CLASSES': (
                'rest_framework.permissions.IsAuthenticated',
            ),
            'DEFAULT_AUTHENTICATION_CLASSES': (
                'rest_framework.authentication.SessionAuthentication',
                'rest_framework.authentication.BasicAuthentication',
                'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
            ),
        }

- edit urls.py<p>
`vim tutorial/urls.py`<p>

        urlpatterns = patterns(
            '',
            # ...
            url(r'^api-token-auth/', 'rest_framework_jwt.views.obtain_jwt_token'),
        )

- test<p>
 + run web server<p>
 `../bin/python manage.py runserver`<p>
 + get token<p>
 `curl -X POST -H "Content-Type: application/json" -d '{"username":"admin","password":"password123"}' http://localhost:8000/api-token-auth/`<p>
 + result<p>
 `{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwidXNlcl9pZCI6MSwiZW1haWwiOiJhZG1pbkBhZG1pbi5jb20iLCJleHAiOjE0NTczMTYzMjF9.E-0yM5UkAHxUaXM6LIimSM-33TJP0z4y-XMqDTY03lI"}`<p>
 + get information with token<p>
 `curl -H "Authorization: JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwidXNlcl9pZCI6MSwiZW1haWwiOiJhZG1pbkBhZG1pbi5jb20iLCJleHAiOjE0NTczMTYzMjF9.E-0yM5UkAHxUaXM6LIimSM-33TJP0z4y-XMqDTY03lI" http://localhost:8000/users/`<p>
 + result<p>
 `[{"url":"http://localhost:8000/users/2/","username":"newname","email":"newname@example.com","groups":[]},{"url":"http://localhost:8000/users/1/","username":"admin","email":"admin@admin.com","groups":[]}]`<p>

### hello world with django rest framework (non-models)<p>
###### From this demo, you can get user's information from restful api without "models" method.<p>
###### Pre-Condition: you've already done with "hello world with django rest framework (use models)".<p>
- copy project "tutorial" to "tutorial-none-models"<p>
`cp -rf tutorial tutorial-none-models`<p>
- enter tutorial-none-models folder<p>
`cd tutorial-none-models`<p>
- create a new app<p>
`../bin/django-admin startapp calculation`<p>
`Ps: If error occurs like "bad interpreter: No such file or directory", that means the enviroment you used is not fit the repository well, so you have to use another way to create this app, just like go into "env/bin" folder and type "django-admin startapp calculation", then type "mv calculation ../tutorial-none-models", finally go back "tutorial-none-models" folder!`<p>
- create & edit cal.py<p>
`vim vim calculation/cal.py`<p>

        class CalcClass(object):
        
            def __init__(self, *args, **kw):
                # Initialize any variables you need from the input you get
                pass
                
            def do_work(self):
                # Do some calculations here
                # returns a tuple ((1,2,3, ), (4,5,6,))
                result = ((1,2,3, ), (4,5,6,)) # final result
                return result

- edit views.py<p>
`vim calculation/views.py`<p>

        from rest_framework.views import APIView
        from rest_framework.response import Response
        from rest_framework import status
        
        from cal import CalcClass
        
        class MyRESTView(APIView):
        
            def get(self, request, *args, **kw):
                # Process any get params that you may need
                # If you don't need to process get params,
                # you can skip this part
                get_arg1 = request.GET.get('arg1', None)
                get_arg2 = request.GET.get('arg2', None)
        
                # Any URL parameters get passed in **kw
                myClass = CalcClass(get_arg1, get_arg2, *args, **kw)
                result = myClass.do_work()
                response = Response(result, status=status.HTTP_200_OK)
                return response

- edit urls.py<p>
`vim tutorial/urls.py`<p>

        ...
        from calculation.views import MyRESTView
        ...
        
        urlpatterns = [
        ...
            url(r'^hahaha/', MyRESTView.as_view(), name='my_rest_view'),
        ...
        ]

- test<p>
 + run web server<p>
 `../bin/python manage.py runserver`<p>
 + get information<p>
 `curl -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/hahaha/`<p>
 + result<p>
 `[ [ 1, 2, 3 ], [ 4, 5, 6 ] ]`<p>
