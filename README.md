# docker_django
使用docker-compose 测试


### build
```bash
➜  mysite git:(master) ✗ docker-compose build
db uses an image, skipping
Building web
Step 1/7 : FROM python:3
 ---> 968120d8cbe8
Step 2/7 : ENV PYTHONUNBUFFERED 1
 ---> Using cache
 ---> a43504e5d754
Step 3/7 : RUN mkdir /code
 ---> Using cache
 ---> b08af28be540
Step 4/7 : WORKDIR /code
 ---> Using cache
 ---> 5c9b5b78202f
Step 5/7 : ADD requirements.txt /code/
 ---> Using cache
 ---> 813d63e8e68d
Step 6/7 : RUN pip install -r requirements.txt
 ---> Using cache
 ---> 10e68c9f380d
Step 7/7 : ADD . /code/
 ---> 22f867a295e5
Removing intermediate container 201694422ea8
Successfully built 22f867a295e5
Successfully tagged mysite_web:latest
➜  mysite git:(master) ✗
```

### test
```bash
➜  mysite git:(master) ✗ docker images | grep mysite
mysite_web                                      latest              22f867a295e5        44 seconds ago      729MB
#可以启动时进行映射端口
➜  mysite git:(master) ✗ docker run -it --rm mysite_web sh
# python3 manage.py runserver 0.0.0.0:8000
Performing system checks...

System check identified no issues (0 silenced).

You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

August 16, 2017 - 09:42:59
Django version 1.11.4, using settings 'mysite.settings'
Starting development server at http://0.0.0.0:8000/
Quit the server with CONTROL-C.

```
