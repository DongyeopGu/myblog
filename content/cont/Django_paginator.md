---
title: "Django Paginator"
weight: 15
chapter: true
pre: "<b>3. </b>"
tags : ["django", "paginator", "pagination"]
---

### Paginator 사용하기

---

> 게시판 같은 곳에서 리스트의 양이 많아 질 경우 나누어 보여주기 위해서 사용
>
> 여기 적힌 예에서 model은 Review 이고 forms는 ReviewForm이다.
>
> bootstrap을 사용했음.

#### 1. views.py에 내용 추가하기

```python
from django.core.paginator import Paginator

def review_list(request):
    reviews = Review.objects.order_by('-pk')
    paginator = Paginator(reviews, 5) #한 페이지 당 몇개 씩 보여줄 지 지정
    page_number = request.GET.get('page')
    page_obj = paginator.get_page(page_number)
    reviews_all = get_list_or_404(Review) #전체 개수를 보기 위해
    total_count = len(reviews_all)	# 리뷰의 총 개수 
    total_page = total_count//5		# 리뷰 총 개수 / 한 페이지에 보여줄 수
    page_range = range(1,total_page+1)
    context = {
        'reviews': reviews,
        'page_obj': page_obj,
        'page_range': page_range,
    }
    return render(request, '앱이름/review_list.html', context)
```

#### 2. 리스트를 보여줄 html파일 수정

```html
{% for review in page_obj %}
            <div class="card mb-3 border-info" onclick="location.href='/community/{{ review.pk }}/'" style="cursor:pointer">
                <div class="card-header">
                    {{ review.movie_title }}
                </div>
                <div class="card-body">
                    <h5 class="card-title">{{ review.title }}</h5>
                    <p class="card-text">{{ review.content }} </p>
                    <a href="{% url 'reviews:detail' review.pk %}" class="btn btn-info float-right">Go Detail</a>
                    <p class="card-text">{{ review.created_at|date:"y.m.d P" }}</h3>
                </div>
            </div>
        {% endfor %}

    <!-- Paginator !-->
<nav aria-label="Page navigation">
        <ul class="pagination">
        {% if page_obj.has_previous %}
            <li class="page-item"><a class="page-link" href="?page={{ page_obj.previous_page_number }}">Previous</a></li>
        {% else %}
            <li class="page-item"><a class="page-link" href="#">Previous</a></li>
        {% endif %}
        {% for page in page_range %}
            {% if page_obj.number == page %}
                    <li class="page-item active"><a class="page-link" href="?page={{ page }}">{{ page }}<span class="sr-only">(current)</span></a></li>
                {% else %}
                    <li class="page-item"><a class="page-link" href="?page={{ page }}">{{ page }}</a></li>
                {% endif %}
        {% endfor %}
        {% if page_obj.has_next %}
            <li class="page-item"><a class="page-link" href="?page={{ page_obj.next_page_number }}">Next</a></li>
        {% else %}
            <li class="page-item"><a class="page-link" href="#">Next</a></li>
        {% endif %}
        </ul>
</nav>
```

- 현재 페이지에 따라 표시를 다르게 하기 위하여 `{% if page_obj.number == page %}`을 사용함

---

1. [Django 기초](https://dongyeopgu.github.io/cont/django_start.html)
2. [Django CRUD](https://dongyeopgu.github.io/cont/django_crud.html)
3. [Django User(1)](https://dongyeopgu.github.io/cont/django_login.html)
4. [Django User(2)](https://dongyeopgu.github.io/cont/django_change_user.html)
5. [Django Comment](https://dongyeopgu.github.io/cont/django_comment.html)
6. [Django M:N](https://dongyeopgu.github.io/cont/django_like_follow.html)
7. [Django REST Framework](https://dongyeopgu.github.io/cont/django_rest_api.html)





