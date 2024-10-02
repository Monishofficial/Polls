# Ex02 Django Polls
## Date: 02-10-2024

## AIM
To develop a Django application to implement polls.


## DESIGN STEPS

### STEP 1:
Clone the problem from GitHub

### STEP 2:
Create a new app in Django project

### STEP 3:
Enter the code for admin.py and models.py

### STEP 4:
Execute Django admin and create details for polls.

## PROGRAM

 admin.py
 ```python
from django.contrib import admin

from .models import Choice, Question

admin.site.register(Question)

 ```
views.py
```python
from django.http import HttpResponseRedirect, HttpResponse
from django.shortcuts import get_object_or_404, render
from django.urls import reverse
from django.views import generic

from .models import Choice, Question


class IndexView(generic.ListView):
    template_name = 'polls/index.html'
    context_object_name = 'latest_question_list'

    def get_queryset(self):
        return Question.objects.filter(
            pub_date__lte=timezone.now()
        ).order_by('-pub_date')[:5]


class DetailView(generic.DetailView):
    model = Question
    template_name = 'polls/detail.html'

    def get_queryset(self):
        return Question.objects.filter(pub_date__lte=timezone.now())


class ResultsView(generic.DetailView):
    model = Question
    template_name = 'polls/results.html'

def vote(request, question_id):
    question = get_object_or_404(Question, pk=question_id)
    try:
        selected_choice = question.choice_set.get(pk=request.POST['choice'])
    except (KeyError, Choice.DoesNotExist):
        return render(request, 'polls/detail.html', {
            'question': question,
            'error_message': "You didn't select a choice.",
        })
    else:
        selected_choice.votes += 1
        selected_choice.save()
        return HttpResponseRedirect(reverse('polls:results', args=(question.id,)))

def owner(request):
       return HttpResponse("Hello, world. d82c2d1a is the polls index.")
```
 models.py
 ```python

from django.db import models
import datetime

from django.utils import timezone

class Question(models.Model):
    question_text = models.CharField(max_length=200)
    pub_date = models.DateTimeField('date published')
    def __str__(self):
        return self.question_text
    def was_published_recently(self):
        return self.pub_date >= timezone.now() - datetime.timedelta(days=1)


class Choice(models.Model):
    question = models.ForeignKey(Question, on_delete=models.CASCADE)
    choice_text = models.CharField(max_length=200)
    votes = models.IntegerField(default=0)

    def __str__(self):
        return self.choice_text
 ```
urls.py
```python
from django.urls import path

from . import views

app_name = 'polls'
urlpatterns = [
    path('', views.IndexView.as_view(), name='index'),
    path('owner', views.owner, name='owner'),
    path('<int:pk>/', views.DetailView.as_view(), name='detail'),
    path('<int:pk>/results/', views.ResultsView.as_view(), name='results'),
    path('<int:question_id>/vote/', views.vote, name='vote'),
]
```


## OUTPUT
![Screenshot 2024-09-28 102249](https://github.com/user-attachments/assets/9df0d88a-4c97-4654-a57d-76ff033bb170)

![Screenshot 2024-09-28 102301](https://github.com/user-attachments/assets/83622d55-12a6-4ff0-82ed-4956b367f069)

![Screenshot 2024-09-28 102312](https://github.com/user-attachments/assets/9e41895f-8583-476c-8fa8-b985b427d1cf)

![Screenshot 2024-09-28 102354](https://github.com/user-attachments/assets/386a421f-62e5-496e-9a9d-eef6bdda4ac0)

## COURSERA GRADE
![Screenshot (137)](https://github.com/user-attachments/assets/3efd554e-2dfa-4c67-a57e-976530a174f4)

## RESULT
Thus the program for creating a polls using Django has been executed successfully.
