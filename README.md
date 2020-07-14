# Django_level0
copywrite : https://www.javatpoint.com/django-crud-application

### django CRUD
### django is easy

4. Database Setup

Create a database djangodb in mysql, and configure into the settings.py file of django project. See the example.
```pythom
// settings.py

DATABASES = {  
    'default': {  
        'ENGINE': 'django.db.backends.mysql',  
        'NAME': 'djangodb',  
        'USER':'root',  
        'PASSWORD':'mysql',  
        'HOST':'localhost',  
        'PORT':'3306'  
    }  
}  
```
5. Create a Model

Put the following code into models.py file.
```pythom
// models.py

from django.db import models  
class Employee(models.Model):  
    eid = models.CharField(max_length=20)  
    ename = models.CharField(max_length=100)  
    eemail = models.EmailField()  
    econtact = models.CharField(max_length=15)  
    class Meta:  
        db_table = "employee"  
6. Create a ModelForm
````

```python
// forms.py

from django import forms  
from employee.models import Employee  
class EmployeeForm(forms.ModelForm):  
    class Meta:  
        model = Employee  
        fields = "__all__" 
```        
7. Create View Functions

```python
// views.py


from django.shortcuts import render, redirect  
from employee.forms import EmployeeForm  
from employee.models import Employee  
# Create your views here.  
def emp(request):  
    if request.method == "POST":  
        form = EmployeeForm(request.POST)  
        if form.is_valid():  
            try:  
                form.save()  
                return redirect('/show')  
            except:  
                pass  
    else:  
        form = EmployeeForm()  
    return render(request,'index.html',{'form':form})  
def show(request):  
    employees = Employee.objects.all()  
    return render(request,"show.html",{'employees':employees})  
def edit(request, id):  
    employee = Employee.objects.get(id=id)  
    return render(request,'edit.html', {'employee':employee})  
def update(request, id):  
    employee = Employee.objects.get(id=id)  
    form = EmployeeForm(request.POST, instance = employee)  
    if form.is_valid():  
        form.save()  
        return redirect("/show")  
    return render(request, 'edit.html', {'employee': employee})  
def destroy(request, id):  
    employee = Employee.objects.get(id=id)  
    employee.delete()  
    return redirect("/show")  
8. Provide Routing

Provide URL patterns to map with views function.

// urls.py

from django.contrib import admin  
from django.urls import path  
from employee import views  
urlpatterns = [  
    path('admin/', admin.site.urls),  
    path('emp', views.emp),  
    path('show',views.show),  
    path('edit/<int:id>', views.edit),  
    path('update/<int:id>', views.update),  
    path('delete/<int:id>', views.destroy),  
]  

```
9. Organize Templates

Create a templates folder inside the employee app and create three (index, edit, show) html files inside the directory. The code for each is given below.

// index.html

```html

<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>Index</title>  
    {% load staticfiles %}  
    <link rel="stylesheet" href="{% static 'css/style.css' %}"/>  
</head>  
<body>  
<form method="POST" class="post-form" action="/emp">  
        {% csrf_token %}  
    <div class="container">  
<br>  
    <div class="form-group row">  
    <label class="col-sm-1 col-form-label"></label>  
    <div class="col-sm-4">  
    <h3>Enter Details</h3>  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Id:</label>  
    <div class="col-sm-4">  
      {{ form.eid }}  
    </div>  
  </div>  
  <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Name:</label>  
    <div class="col-sm-4">  
      {{ form.ename }}  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Email:</label>  
    <div class="col-sm-4">  
      {{ form.eemail }}  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Contact:</label>  
    <div class="col-sm-4">  
      {{ form.econtact }}  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-1 col-form-label"></label>  
    <div class="col-sm-4">  
    <button type="submit" class="btn btn-primary">Submit</button>  
    </div>  
  </div>  
    </div>  
</form>  
</body>  
</html>  
// show.html

<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>Employee Records</title>  
     {% load staticfiles %}  
    <link rel="stylesheet" href="{% static 'css/style.css' %}"/>  
</head>  
<body>  
<table class="table table-striped table-bordered table-sm">  
    <thead class="thead-dark">  
    <tr>  
        <th>Employee ID</th>  
        <th>Employee Name</th>  
        <th>Employee Email</th>  
        <th>Employee Contact</th>  
        <th>Actions</th>  
    </tr>  
    </thead>  
    <tbody>  
{% for employee in employees %}  
    <tr>  
        <td>{{ employee.eid }}</td>  
        <td>{{ employee.ename }}</td>  
        <td>{{ employee.eemail }}</td>  
        <td>{{ employee.econtact }}</td>  
        <td>  
            <a href="/edit/{{ employee.id }}"><span class="glyphicon glyphicon-pencil" >Edit</span></a>  
            <a href="/delete/{{ employee.id }}">Delete</a>  
        </td>  
    </tr>  
{% endfor %}  
    </tbody>  
</table>  
<br>  
<br>  
<center><a href="/emp" class="btn btn-primary">Add New Record</a></center>  
</body>  
</html>  
// edit.html

<!DOCTYPE html>  
<html lang="en">  
<head>  
    <meta charset="UTF-8">  
    <title>Index</title>  
    {% load staticfiles %}  
    <link rel="stylesheet" href="{% static 'css/style.css' %}"/>  
</head>  
<body>  
<form method="POST" class="post-form" action="/update/{{employee.id}}">  
        {% csrf_token %}  
    <div class="container">  
<br>  
    <div class="form-group row">  
    <label class="col-sm-1 col-form-label"></label>  
    <div class="col-sm-4">  
    <h3>Update Details</h3>  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Id:</label>  
    <div class="col-sm-4">  
        <input type="text" name="eid" id="id_eid" required maxlength="20" value="{{ employee.eid }}"/>  
    </div>  
  </div>  
  <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Name:</label>  
    <div class="col-sm-4">  
        <input type="text" name="ename" id="id_ename" required maxlength="100" value="{{ employee.ename }}" />  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Email:</label>  
    <div class="col-sm-4">  
        <input type="email" name="eemail" id="id_eemail" required maxlength="254" value="{{ employee.eemail }}" />  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-2 col-form-label">Employee Contact:</label>  
    <div class="col-sm-4">  
        <input type="text" name="econtact" id="id_econtact" required maxlength="15" value="{{ employee.econtact }}" />  
    </div>  
  </div>  
    <div class="form-group row">  
    <label class="col-sm-1 col-form-label"></label>  
    <div class="col-sm-4">  
    <button type="submit" class="btn btn-success">Update</button>  
    </div>  
  </div>  
    </div>  
</form>  
</body>  
</html>  
```
10. Static Files Handling

Create a folder static/css inside the employee app and put a css inside it. Download the css file here Click Here.
