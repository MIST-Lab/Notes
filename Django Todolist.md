# Django Todolist

1. Setup the env (with python3 installed)

![Screen Shot 2019-02-23 at 12.36.47 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.36.47 PM.png)

2. init the project

   ![Screen Shot 2019-02-23 at 12.36.34 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.36.34 PM.png)

   ![Screen Shot 2019-02-23 at 12.36.10 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.36.10 PM.png)

3. Update the settings.py

   ![Screen Shot 2019-02-23 at 12.45.09 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.45.09 PM.png)

   ![Screen Shot 2019-02-23 at 12.45.23 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.45.23 PM.png)

   ![Screen Shot 2019-02-23 at 12.59.41 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 12.59.41 PM.png)

   ``` python
   # add this to MIDDLEWARE & delete the STATICFILES_STORAGE line
   'whitenoise.middleware.WhiteNoiseMiddleware',
   ```

   

4. Build the models.py

   ```python
   from django.db import models
   
   # Create your models here.
   from django.utils import timezone
   
   class Category(models.Model): # The Category table name that inherits models.Model
       name = models.CharField(max_length=100) #Like a varchar
       class Meta:
           verbose_name = ("Category")
           verbose_name_plural = ("Categories")
       def __str__(self):
           return self.name #name to be shown when called
   
   class TodoList(models.Model): #Todolist able name that inherits models.Model
       title = models.CharField(max_length=250) # a varchar
       content = models.TextField(blank=True) # a text field 
       created = models.DateField(default=timezone.now().strftime("%Y-%m-%d")) # a date
       due_date = models.DateField(default=timezone.now().strftime("%Y-%m-%d")) # a date
       category = models.ForeignKey(Category, default="general",on_delete=models.DO_NOTHING) # a foreignkey
       class Meta:
           ordering = ["-created"] #ordering by the created field
       def __str__(self):
           return self.title #name to be shown when called
   ```

   ![Screen Shot 2019-02-23 at 1.01.30 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 1.01.30 PM.png)

   ![Screen Shot 2019-02-23 at 1.02.26 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 1.02.26 PM.png)

5. superuser account generate![Screen Shot 2019-02-23 at 1.03.58 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 1.03.58 PM.png)

6. Templates folders

   ```bash
   cd todolist
   mkdir templates
   touch index.html
   mkdir css
   cd css
   touch style.css
   ```

   ```html
   <!DOCTYPE html>
   <html >
   <head>
     <meta charset="UTF-8">
     <title>TodoApp - Create A Todo With Django</title>
     {% load static %}
     <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0-beta/css/bootstrap.min.css" integrity="sha384-/Y6pD6FV/Vv2HJnA6t+vslU6fwYXjCFtcEpHbNJ0lyAFsXTsjBbfaDjzALeQsN6M" crossorigin="anonymous">
     <link rel="stylesheet" type="text/css" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.7.0/css/font-awesome.min.css">
      <link rel="stylesheet" type="text/css" href="{% static 'css/style.css' %}">
   </head>
   <body>
   <div django-app="TaskManager">        
       <div class="container">
               <div class="content">
               <h1>TodoApp</h1>
               <p class="tagline">a Django todo app</p>
               <form action="" method="post">
               {% csrf_token %} <!-- csrf token for basic security -->
                   <div class="inputContainer">
                       <input type="text" id="description" class="taskName" placeholder="What do you need to do?" name="description" required>
                       <label for="description">Description</label>
                   </div>
                   <div class="inputContainer half last">
                       <i class="fa fa-caret-down selectArrow"></i>
                       <select id="category" class="taskCategory" name="category_select">
                       <option class="disabled" value="">Choose a category</option>
                       {% for category in categories %}
                           <option class="" value="{{ category.name }}" name="{{ category.name }}">{{ category.name }}</option>
                       {% endfor %}
                       </select>
                       <label for="category">Category</label>
                   </div>
                   <div class="inputContainer half last right">
                       <input type="date" id="dueDate" class="taskDate" name="date">
                       <label for="dueDate">Due Date</label>
                   </div>
                   <div class="row">
                       <button class="taskAdd" name="taskAdd" type="submit"><i class="fa fa-plus icon"></i>Add task</button>
                       <button class="taskDelete" name="taskDelete" formnovalidate="" type="submit" onclick="$('input#sublist').click();"><i class="fa fa-trash-o icon"></i>Delete Tasks</button>
                   </div>
               <ul class="taskList">
               {% for todo in todos %} <!-- django template lang - for loop -->
                   <li class="taskItem">
                       <input type="checkbox" class="taskCheckbox" name="checkedbox" id="{{ todo.id }}" value="{{ todo.id }}">
                       <label for="{{ todo.id }}"><span class="complete-">{{ todo.title }}</span></label>
                       <span class="category-{{ todo.category }}">{{ todo.category }}</span>
                       <strong class="taskDate"><i class="fa fa-calendar"></i>{{ todo.created }} - {{ todo.due_date }}</strong>
                   </li>
               {% endfor %}
               </ul><!-- taskList -->
               </form>
               </div><!-- content -->
    </div><!-- container -->
   </div>
   <script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>
   </body>
   </html>
   ```

   ```css
   /* basic reset */
   *, *:before, *:after {
       -webkit-box-sizing: border-box;
          -moz-box-sizing: border-box;
               box-sizing: border-box;
   }
   /* app */
   html {
       font-size: 100%;
   }
   body {
       background: #b1f6cb;
       font-family: 'Open Sans',sans-serif;
   }
   /* super basic grid structure */
   .container {
       width: 600px;
       margin: 0 auto;
       background: #ffffff;
       padding: 20px 0;
       -webkit-box-shadow: 0 0 2px rgba(0,0,0,0.2);
               box-shadow: 0 0 2px rgba(0,0,0,0.2);
   }
   .row {
       display: block;
       padding: 10px;
       text-align: center;
       width: 100%;
       clear: both;
       overflow: hidden;
   }
   .half {
       width: 50%;
       float: left;
   }
   .content {
       background: #fff;
   }
   /* logo */
   h1 {
       font-family: 'Rokkitt', sans-serif;
       color: #666;
       text-align: center;
       font-weight: 400;
       margin: 0;
   }
   .tagline {
       margin-top: -10px;
       text-align: center;
       padding: 5px 20px;
       font-size: 11px;
       font-weight: 600;
       text-transform: uppercase;
       color: #777; 
   }
   /* inputs */
   .inputContainer {
       height: 60px;
       border-top: 1px solid #e5e5e5;
       position: relative;
       overflow: hidden;
   }
   .inputContainer.last {
       border-bottom: 1px solid #e5e5e5;
       margin-bottom: 20px;
   }
   .inputContainer.half.last.right {
       border-left: 1px solid #efefef;
   }
   input[type="date"], input[type="text"], select {
       height: 100%;
       width: 100%;
       padding: 0 20px;
       position: absolute;
       top: 0;
       vertical-align: middle;
       display: inline-block;
       border: none;
       border-radius: none;
       font-size: 13px;
       color: #777;
       margin: 0;
       font-family: 'Open Sans', sans-serif;
       font-weight: 600;
       letter-spacing: 0.5px;
       -webkit-transition: background 0.3s;
               transition: background 0.3s;
   }
   input[type="date"] {
       cursor: pointer;
   }
   input[type="date"]:focus, input[type="text"]:focus, select:focus {
       outline: none;
       background: #ecf0f1;
   }
   ::-webkit-input-placeholder {
       color: lightgrey;
       font-weight: normal;
       -webkit-transition: all 0.3s;
               transition: all 0.3s;
   }
   ::-moz-placeholder {
       color: lightgrey;
       font-weight: normal;
       transition: all 0.3s;
   }
   ::-ms-input-placeholder {
       color: lightgrey;
       font-weight: normal;
       transition: all 0.3s;
   }
   input:-moz-placeholder {
       color: lightgrey;
       font-weight: normal;
       transition: all 0.3s;
   }
   input:focus::-webkit-input-placeholder {
       color: #95a5a6;
       font-weight: bold;
   }
   input:focus::-moz-input-placeholder {
       color: #95a5a6;
       font-weight: bold;
   }
   .inputContainer label {
       padding: 5px 20px;
       font-size: 11px;
       font-weight: 600;
       text-transform: uppercase;
       color: #777;
       display: block;
       position: absolute;
   }
   button {
       font-family: 'Open Sans', sans-serif;
       background: transparent;
       border-radius: 2px;
       border: none;
       outline: none;
       height: 50px;
       font-size: 14px;
       color: #fff;
       cursor: pointer;
       text-transform: uppercase;
       position: relative;
       -webkit-transition: all 0.3s;
       transition: all 0.3s;
       padding-left: 30px;
       padding-right: 15px;
   }
   .icon {
       position: absolute;
       top: 30%;
       left: 10px;
       font-size: 20px;
   }
   .taskAdd {
       background: #444;
       padding-left: 31px;
   }
   .taskAdd:hover {
       background: #303030;
   }
   .taskDelete {
       background: #e74c3c;
       padding-left: 30px;
   }
   .taskDelete:hover {
       background: #c0392b;
   }
   /* task styles */
   .taskList {
       list-style: none;
       padding: 0 20px;
   }
   .taskItem {
       border-top: 1px solid #e5e5e5;
       padding: 15px 0;
       color: #777;
       font-weight: 600;
       font-size: 14px;
       letter-spacing: 0.5px;
   }
   .taskList .taskItem:nth-child(even) {
       background: #fcfcfc;
   }
   .taskCheckbox {
       margin-right: 1em;
   }
   .complete-true {
       text-decoration:  line-through;
       color: #bebebe;
   }
   .taskList .taskDate {
       color: #95a5a6;
       font-size: 10px;
       font-weight: bold;
       text-transform: uppercase;
       display: block;
       margin-left: 41px;
   }
   .fa-calendar {
       margin-right: 10px;
       font-size: 16px;
   }
   [class*='category-'] {
       display: inline-block;
       font-size: 10px;
       background: #444;
       vertical-align: middle;
       color: #fff;
       padding: 10px;
       width: 75px;
       text-align: center;
       border-radius: 2px;
       float: right;
       font-weight: normal;
       text-transform: uppercase;
       margin-right: 20px;
   }
   .category- {
       background: transparent;
   }
   .category-Personal {
       background: #2980b9;
   }
   .category-Work {
       background: #8e44ad;
   }
   .category-School {
       background: #f39c12;
   }
   .category-Cleaning {
       background: #16a085;
   }
   .category-Other {
       background: #d35400;
   }
   footer {
       text-align: center;
       font-size: 11px;
       font-weight: 600;
       text-transform: uppercase;
       color: #777;
   }
   footer a {
       color: #f39c12;
   }
   /* custom checkboxes */
   .taskCheckbox {
       -webkit-appearance: none;
               appearance: none;
       -webkit-transition: all 0.3s;
               transition: all 0.3s;
       display: inline-block;
       cursor: pointer;
       width: 19px;
       height: 19px;
       vertical-align: middle;
   }
   .taskCheckbox:focus {
       outline: none;
   }
   .taskCheckbox:before, .taskCheckbox:checked:before {
       font-family: 'FontAwesome';
       color: #444;
       font-size: 20px;
       -webkit-transition: all 0.3s;
               transition: all 0.3s;
   }
   .taskCheckbox:before {
       content: '\f096';
   }
   .taskCheckbox:checked:before {
       content: '\f14a';
       color: #16a085;
   }
   /* custom select menu */
   .taskCategory {
       -webkit-appearance: none;
               appearance: none;
       cursor: pointer;
       padding-left: 16.5px; /*specific positioning due to difficult behavior of select element*/
       background: #fff;
   }
   .selectArrow {
       position: absolute;
       z-index: 10;
       top: 35%;
       right: 0;
       margin-right: 20px;
       color: #777;
       pointer-events: none;
   }
   .taskCategory option {
       background: #fff;
       border: none;
       outline: none;
       padding: 0 100px;
   }
   ```

7. Update the urls.py

   ```python
   from django.contrib import admin
   from django.urls import path
   from django.conf.urls import url
   from todolist.views import index
   
   urlpatterns = [
       path('admin/', admin.site.urls),
       url(r'^$', index, name="TodoList"),
   ]
   ```

8. Register the models in admin.py

   ```python
   from django.contrib import admin
   from . import models
   
   # Register your models here.
   class TodoListAdmin(admin.ModelAdmin):
       list_display = ("title",  "created", "due_date")
   class CategoryAdmin(admin.ModelAdmin):
       list_display = ("name",)
   admin.site.register(models.TodoList, TodoListAdmin)
   admin.site.register(models.Category, CategoryAdmin)
   ```

9. FINISHED

   ![Screen Shot 2019-02-23 at 1.42.57 PM](/Users/junchengzhu/Desktop/Screen Shot 2019-02-23 at 1.42.57 PM.png)

