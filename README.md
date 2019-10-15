# Django란 무엇인가??
* 파이썬으로 만들어진 무료 오픈소스 웹 프레임워크 
* 빠르게 웹사이트를 개발할 수 있도록 돕는 구성요소로 이루어진 웹 프레임워크

## Django 프로젝트 생성 및 서버 실행
$ django-admin startproject 프로젝트명 . (명령어 끝에 . (점,마침표)를 꼭꼭 입력!!)
` $ django-admin startproject mysite . `

![jango](img/img1.PNG)

** manage.py : 스크립트로 사이트 관리를 도와주는 역할  
** settings.py : 웹사이트 설정이 있는 파일  
** urls.py : 프로젝트의 url를 관리할 수 있는 파일  

* 데이터 베이스를 생성하기 위한 명령어  
` $ python manage.py migrate `
* 웹 서버를 시작하는 명령어
` $ python manage.py runsever `  
[jango](./img/img2.PNG)

* http://127.0.0.1:8000/ 를 웹페이지 창에 입력 후 서버가 잘 실행 됐을시의 첫 화면  
[jango](./img/img3.PNG)

## Django 모델
** 객체 지향 프로그래밍 : 모델을 만들어 그 모델이 어떤 역할을 가지고 어떻게 행동해야 하는지 정의하여 서로 알아서 상호작용할 수 있도록 하는 것  
** 객체란? : 속성과 행동을 모아놓은 것

* 어플리케이션 생성 명령어  
` $ python manage.py startapp blog `  
** blog 디렉터리와 파일이 생성됨.  
[jango](./img/img4.PNG)

* 어플리케이션을 장고에 사용한다고 알려줌.  
-> mysite/settings.py 파일에 blog 추가  
[jango](./img/img5.PNG)

* 모든 모델 객체는 blog/models.py 파일에 선언하여 모델을 만듬.  
```
from django.conf import settings
from django.db import models
from django.utils import timezone


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    # blank= True 추가
    text = models.TextField(blank = True)
    created_date = models.DateTimeField(
            default=timezone.now)
    published_date = models.DateTimeField(
            blank=True, null=True)

# class 의 기본 메소드는 인스턴스(self) __str__(매직 메소드라 부름)
   
    def publish(self):
        self.published_date = timezone.now()
        self.save()

    def __str__(self):
        return self.title
```  

** models.CharField : 글자 수가 제한된 텍스트를 정의할 때 사용. 글 제목같이 짧은 문자열 정보를 저장할 때 사용함.(제한이 있어서 속도가 빠름)  
** models.TextField : 글자 수에 제한이 없는 긴 텍스트를 위한 속성.(제한이 없기 때문에 속도가 느림)  
** models.DateTimeField : 날짜와 시간을 의미.  
** models.ForeignKey : 다른 모델에 대한 링크를 의미.  

* 테이블 생성  
** 장고에게 모델에 추가 된것을 알려줘야함.  
` $ python manage.py makemigrations blog `  
[jango](./img/img6.PNG)  

** 실제 데이터베이스에 모델 추가를 반영  
` $ python manage.py migrate blog `  
[jango](./img/img7.PNG)  

## Django 관리자 
* 모델링한 글을 장고 관리자에 추가.  
-> blog/admin.py  
``` 
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
* http://127.0.0.1:8000/admin/ 로 관리자 페이지 확인.  
[jango](./img/img8.PNG) 

* 슈퍼 사용자 생성  
` $ python manage.py createsuperuser `  
[jango](./img/img9.PNG) 

* 생성 후 관리자 페이지엥서 슈퍼 사용자로 로그인 후 확인  
[jango](./img/img10.PNG) 

## templates 파일 안에 html 생성  
** templates : 서로 다른 정보를 일정한 형태로 표시하기 위해 재사용 가능한 파일
* blog/templates 폴더를 생성한다.
* templates/post_list.html 을 생성한다.
* post_list.html  
```
<!doctype html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport"
          content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
</head>
<body>
<h1>post_list</h1>
</body>
</html>  
```

## Django view 생성
* view : 웹플리케이션의 로직을 넣는 곳. 모델에서 필요한 정보를 받아와서 템플릿에 전달하는 역할 
* view.py   
```
from django.shortcuts import render

def post_list(request):
    return render(request, 'post_list.html')  
```
** request : url에 연결하면 url의 요청 정보가 request를 통해 전달됨.  
** 반환하고 싶은 post_list.html 파일의 이름을 작성.

## Django urls
* URL이란? 웹 주소
* mysite/urls.py : 프로젝트에 사용할 url을 추가 삭제 할 수 있음
* mysite/urls.py 에 path('post-list/', post_list) 추가  
```
from django.contrib import admin
from django.urls import path

from blog.views import post_list


urlpatterns = [
    path('admin/', admin.site.urls),
    path('post-list/', post_list),
] 
```

## setting.py 에 templates 초기 세팅
* setting.py 에 TEMPLATES 밑에 DIRS 안에 os.path.join(BASE_DIR,'templates') 추가  
** 장고에 templates를 사용한다고 알려주는 코드
* setting.py  

```
TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
        'DIRS': [
            os.path.join(BASE_DIR,'templates'), 
        ],  
```
* http://127.0.0.1:8000/post-list/ 화면 확인  
[jango](./img/img11.PNG) 

