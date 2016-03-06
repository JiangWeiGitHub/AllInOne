###### This repository includes something about python & django & rest framework & celery, enjoy ^_^!<p>

### install essential packages<p>
- install pip<p>
`sudo apt-get install python-pip`<p>
- install virtualenv<p>
`sudo pip install virtualenv`<p>
`PS: all deployments after depand on virtualenv!`<p>

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
`bin/django-admin.py startproject tutorial`<p>
- go into tutorial folder<p>
`cd tutorial`<p>
- create a django app named quickstart<p>
`../bin/django-admin.py startapp quickstart`<p>
- synchronize database<p>
`../bin/python manage.py migrate`<p>
- add superuser<p>
`../bin/python manage.py createsuperuser # name: admin & password: password123 & email can be null`<p>
`PS: If you didn't create a superuser, you could not access to django server's background.`<p>
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
`../bin/python manage.py runserver`
 + get information<p>
`curl -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/`
 + result<p>

        {
            \"count\": 1,
            \"next\": null,
            \"previous\": null,
            \"results\": [
                {
                    \"url\": \"http://127.0.0.1:8000/users/1/\",
                    \"username\": \"admin\",
                    \"email\": \"admin@admin.com\",
                    \"groups\": []
                }
            ]
        }

 + set information<p>
`curl -X POST -d username=newname -d email=newname@example.com -H 'Accept: application/json; indent=4' -u admin:password123 http://127.0.0.1:8000/users/`
 + result<p>

        {
            "count": 2,
            "next": null,
            "previous": null,
            "results": [
                {
                    "url": "http://127.0.0.1:8000/users/2/",
                    "username": "newname",
                    "email": "newname@example.com",
                    "groups": []
                },
                {
                    "url": "http://127.0.0.1:8000/users/1/",
                    "username": "admin",
                    "email": "admin@admin.com",
                    "groups": []
                }
            ]
        }

### hello world with django rest framework jwt<p>
###### From this demo, you can get user's information from restful api with token.<p>
###### Pre-Condition: you've already done with "hello world with django rest framework (use models)".<p>
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
`../bin/python manage.py runserver`
 + get token<p>
`curl -X POST -H "Content-Type: application/json" -d '{"username":"admin","password":"password123"}' http://localhost:8000/api-token-auth/`
 + get information with token<p>
`curl -H "Authorization: JWT eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImFkbWluIiwidXNlcl9pZCI6MSwiZW1haWwiOiIiLCJleHAiOjE0NTY5MDIwMjN9.HtqDs4FY322WXK6FeRUsyZGeDc-OMMpTIjQiLiKG9zw" http://localhost:8000/`

### hello world with django rest framework (non-models)<p>
###### From this demo, you can get user's information from restful api with token.<p>
###### Pre-Condition: you've already done with "hello world with django rest framework (use models)".<p>


### deploy django rest framework with celery
