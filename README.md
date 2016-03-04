# install essential packages
- install pip
' sudo apt-get install python-pip '
- install virtualenv
' sudo pip install virtualenv '



virtualenv env

cd env

bin/pip install django
bin/pip install djangorestframework
bin/pip install pygments  # We'll be using this for the code highlighting

bin/django-admin.py startproject tutorial
cd tutorial
../bin/django-admin.py startapp quickstart

../bin/python manage.py migrate #sync database
../bin/python manage.py createsuperuser #备注：用户名admin 密码password123

cd ..
vim tutorial/quickstart/serializers.py
# deploy django
# deploy django rest framework
## deploy django rest framework with model serializer
## deploy django rest framework with non-model serializer
# deploy django rest framework jwt
# deploy django rest framework with celery
