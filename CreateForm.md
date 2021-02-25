# [Django] 導入Form
1. 新建templates:form.html
2. 記得要去settings.py確定是否有完成路徑設定
3. 在application 中創造 forms.py
```python
from django import forms

class FormName(forms.Form):
    name=forms.CharField()
    email=forms.EmailField()
    text=forms.CharField(widget=forms.Textarea)
````
4. 在view中建立form
```python=
from . import forms
def form_name_view(request):
    form=forms.FormName
    return render(request,'first_app/form_page.html',{'form':form})
```
5. Project 中的urls.py也要做調整
```python=
from first_app import views
urlpatterns = [
path('formpage/',views.form_name_view,name='form_page'),
```
6.路徑設定好了，接下來準備form頁的呈現
```html=
<!DOCTYPE html>
{% load static %}
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Forms</title>
    <link rel="stylesheet" href="{% static "css/mystyle.css" %}"/>
  </head>
  <body>
    <h1>Form</h1>
    <h2> Form to show you result</h2>
    <!--<img src="{% static "images/Swiss.jpg" %}" alt="Uh OH,ERROR"> -->
    <div class="container">
      {{form}}
    </div>
  </body>
</html>

```

7. 執行看看，並前往/formpage
```cmd=
python manage.py runserver
```
8. 
![](https://i.imgur.com/JXhp5ES.png)

9. 美化網站，引用Bootstrap在Header中
```htmlembedded=
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">

```
form_page.html
```htmlembedded=
<!DOCTYPE html>
{% load static %}
<html lang="en" dir="ltr">
  <head>
    <meta charset="utf-8">
    <title>Forms</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-BmbxuPwQa2lc/FVzBcNJ7UAyJxM6wuqIj61tLrc4wSX0szH/Ev+nYRRuWlolflfl" crossorigin="anonymous">
  </head>
  <body>
    <h1> Fill out the Form</h1>
    <!--<img src="{% static "images/Swiss.jpg" %}" alt="Uh OH,ERROR"> -->
    <div class="container">
      <form method="POST">
          {{form.as_p}}
          <input type="submit" class="btn btn-primary" value="Submit">
      </form>
    </div>

  </body>
</html>
```
10. {{form.as_p}}
它就是 Django 提供給 forms 顯示的一種方式
有三種：
* as_table,
* as_p,
* as_ul
代表我們的 form 要透過 HTML 的 ``<tr>`` or ``<p>`` or ``<li>`` 來裝飾

11. 執行網頁Submit時會發現出現錯誤
![](https://i.imgur.com/m93EOPw.png)

程式碼中加入：{% csrf_token %}
```htmlembedded=
<form method="POST">
          {{form.as_p}}
          {% csrf_token %}
          <input type="submit" class="btn btn-primary" value="Submit">
      </form>
```
{% csrf_token%}：CSRF=Cross-Site Request Forgery

Django framework需要CSRF token

如果沒有，就會無法運作

運作方式：

使用hidden input，是一組隨機碼，用來檢查是否跟使用者的網頁符合

12. 回到view.py設定一些POST後的動作
```python=
def form_name_view(request):
    form=forms.FormName()
    if request.method=='POST':
        form=forms.FormName(request.POST)
        if form.is_valid():
            print("validation IS SUCCESS")
            print("Name="+form.cleaned_data['name'])
            print("Email="+form.cleaned_data['email'])
            print("Text="+form.cleaned_data['text'])
    return render(request,'first_app/form_page.html',{'form':form})
```
cleaned_data:表單物件中的cleaned_data字典會提供給我們合法欄位(通過驗證的欄位)的數據，不過該屬性要在表單有進行過任何驗證手段後才存在(包含呼叫is_valid或是存取errors字典等)

此段是為了確保後端能否抓到我們前端所輸入的資料並且POST到後端接收。