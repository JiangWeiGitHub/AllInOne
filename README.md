## install essential packages
- install pip<p>
`sudo apt-get install python-pip`
- install virtualenv<p>
`sudo pip install virtualenv`

PS: *all deployments after depand on virtualenv!*

***

## deploy all in one enviroment
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









 

bin/django-admin.py startproject tutorial
cd tutorial
../bin/django-admin.py startapp quickstart

../bin/python manage.py migrate #sync database
../bin/python manage.py createsuperuser #备注：用户名admin 密码password123

cd ..
vim tutorial/quickstart/serializers.py

## deploy django rest framework
### deploy django rest framework with model serializer
### deploy django rest framework with non-model serializer
## deploy django rest framework jwt
## deploy django rest framework with celery
