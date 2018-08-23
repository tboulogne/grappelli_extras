# grappelli_extras

[![Latest PyPI version](https://pypip.in/v/django-grappelli-extras/badge.png)](https://crate.io/packages/django-grappelli-extras/)

[![Number of PyPI downloads](https://pypip.in/d/django-grappelli-extras/badge.png)](https://crate.io/packages/django-grappelli-extras/)

# Requirements

* Python = 3
* Django >= 2.1
* django-grappelli >= 2.11.1

# Installation

* ```pip install django-grappelli-extras```

## settings.py

 * Put 'grappelli_extras' **before** 'grappelli' on INSTALLED_APPS

```python
# Your setting will look like:
INSTALLED_APPS = [
    'grappelli_extras',
    'grappelli',
    'import_export',
    'adminactions',
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    # continue with your apps
]
```

## urls.py

 * Put grappelli extras urls in 'urlpatterns':

```python
# Your urls will look like:
urlpatterns = [
    url('admin/', admin.site.urls),
    url('admin/ajax/', include('grappelli_extras.ajax_urls')),
    url('admin/extras/', include('grappelli_extras.extras_urls')),
    url('grappelli/', include('grappelli.urls')),

]
```


Available features:

* [Header navbar](#navbar)
Add a dynamic navbar that change according to user permissions

* [Add Links](#add-links)
Add addlink for each model in the nabvar according to user permissions.

* [Traslation](#translation)
Traslation Suport by Locales.

* [Ajax](#ajax)
An Ajax api to make queries to django OMR using generics views.

# Using Autocomplete generic view
```javascript


<script src="{% static 'ajax/grp-token.js' %}"></script>

<input type="text" id="complete-input">

<script>
    (function ($) {
        var completeEvent = function () {
            $(this).autocomplete({
                minLength: 2,
                source: "{% url 'ajax_autocomplete' %}?app_label=app&model=foo&column_name=name&column_value=code",
                select: function (i, o) {
                    // you can user obj to populate another inputs
                    console.log(o.item.obj);
                }
            });
        };
        $('#complete-input').on('keyup', completeEvent);
    })(grp.jQuery)
</script>


```

# Using GetCollection generic view
```javascript


<script src="{% static 'ajax/grp-token.js' %}"></script>

<script>
    (function ($) {
        $.ajax("{% url 'ajax_getCollection' %}", {
                method: "POST",
                data: {app_label: "app", model: "foo"},
                success: function (collection) {
                    console.log(collection);
                }
            })
    })(grp.jQuery)
</script>

```

# Using GetObject generic view
```javascript


<script src="{% static 'ajax/grp-token.js' %}"></script>

<script>
    (function ($) {
        $.ajax("{% url 'ajax_getObject' %}", {
                method: "POST",
                data: {app_label: "app", model: "foo", id: '1'},
                success: function (obj) {
                    console.log(obj);
                }
            })
    })(grp.jQuery)
</script>

```

# Using ObjectView generic view
Firts define the method returning a html string
```python
from grappelli_extras.models import base, base_entidad
from django.template.loader import render_to_string

class Foo(base_entidad):
    ...
    def render_as_table(self):
        return render_to_string("app/foo.html", {'obj': self})

```

In the html template render using django tags and filters as usual
```djangotemplate
<table class="grp-table">
    <thead>
    <tr>
        <th colspan="2">Foo Object</th>
    </tr>
    <tr>
        <th>Key</th>
        <th>Value</th>
    </tr>
    </thead>

    <tbody>
    <tr>
        <td>ID</td>
        <td>{{obj.id}}</td>
    </tr>
    <tr>
        <td>CODE</td>
        <td>{{obj.code}}</td>
    </tr>
    <tr>
        <td>NAME</td>
        <td>{{obj.name}}</td>
    </tr>
    </tbody>
</table>

```
After when you need to render this conten by Ajax
```javascript


<script src="{% static 'ajax/grp-token.js' %}"></script>

<div id="result"></div>

<script>
    (function ($) {
        $.ajax("{% url 'ajax_ObjectView' %}", {
                method: "POST",
                data: {app_label: "app", model: "foo", id: '1', view: 'render_as_table'},
                success: function (response) {
                    $('#result').html(response);
                }
            })
    })(grp.jQuery)
</script>

```

# Working with jQuery pluggins.
Let start with datatables.

In your template include the js script ajax token.js after jQuery to keep secure the request.
And include all css and js as you need.

```djangotemplate
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
{% load static %}

<table></table>


<script src="https://code.jquery.com/jquery-3.3.1.js"></script>
<script src="{% static 'ajax/token.js' %}"></script>
<script src="https://cdn.datatables.net/1.10.18/js/jquery.dataTables.min.js"></script>
<link rel="stylesheet" href="https://cdn.datatables.net/1.10.19/css/jquery.dataTables.min.css">

<script>

    $(document).ready(function () {
        $("table").DataTable({
            ajax: '{% url "ajax_getDataTables" %}?app_label=app&model=foo',
            columns: [{'data': 'id'}, {'data': 'code'}, {'data': 'name'}]
        });
    });

</script>
</body>
</html>

```

You can add filters like a json see doc.

* [Integration](#integration)*
Integration of adminactions, filebrowser, import_export modules.

* [Integration](#jquery)
Include a Jquery Plugin to creade modals with objects json getting data from django OMR.

## To run test project

```
cd ~/grappelli_extras/testapp
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

## Contributing

1. Fork it.
2. Create your feature branch. (`git checkout -b my-new-feature`)
3. Commit your changes. (`git commit -am 'Add some feature'`)
4. Push to the branch. (`git push origin my-new-feature`)
5. Create new Pull Request.

Thank You