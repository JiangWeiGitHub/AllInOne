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

***

### hello world with django rest framework<p>
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
`PS: If you didn't create a superuser, you can not access to django server's background.`<p>
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
        from tutorial.quickstart.serializers import UserSerializer, GroupSerializer


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
        from tutorial.quickstart import views
        
        router = routers.DefaultRouter()
        router.register(r'users', views.UserViewSet)
        router.register(r'groups', views.GroupViewSet)
        
        # Wire up our API using automatic URL routing.
        # Additionally, we include login URLs for the browsable API.
        urlpatterns = [
            url(r'^', include(router.urls)),
            url(r'^api-auth/', include('rest_framework.urls', namespace='rest_framework')) # If you just use curl to test, you can rid of this row
        ]



## deploy django rest framework
### deploy django rest framework with model serializer
### deploy django rest framework with non-model serializer
## deploy django rest framework jwt
## deploy django rest framework with celery
