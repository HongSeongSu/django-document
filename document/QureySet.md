#QuerySet API reference
이 문서는 QuerySet API의 세부 사항을 설명합니다. 모델 및 데이터베이스 쿼리 가이드에 제시된 내용을 토대로 작성되었으므로이 문서를 읽기 전에 해당 문서를 읽고 이해하고 싶을 것입니다.

이 레퍼런스 전반에 걸쳐 우리는 데이터베이스 쿼리 가이드에 제시된 예제 웹 로그 모델을 사용.

###QuerySet이 평가 될 때
내부적으로 QuerySet은 실제로 데이터베이스에 도달하지 않고 구성, 필터링, 슬라이스 및 일반적으로 전달 될 수 있습니다. 쿼리 세트를 평가할 때까지 실제로 데이터베이스 활동이 발생하지 않습니다.

다음과 같은 방법으로 QuerySet을 평가.

* 되풀이. QuerySet은 반복 가능하며 처음 반복 할 때 QuerySet을 실행합니다. 예를 들어, 데이터베이스의 모든 항목의 제목을 인쇄합니다.
```python
for e in Entry.objects.all():
    print(e.headline)
```
참고 : 적어도 하나의 결과가 존재하는지 확인하기 만하면됨. exists ()를 사용하는 것이 더 효율적.

* 슬라이스. Limiting QuerySet에서 설명했듯이, QuerySet은 Python의 배열 슬라이싱 구문을 사용하여 슬라이스 할 수 있습니다. 평가되지 않은 QuerySet을 슬라이스하면 다른 평가되지 않은 QuerySet가 반환되지만 Django는 슬라이스 구문의 "step"매개 변수를 사용하면 데이터베이스 쿼리를 실행하고 목록을 반환. 평가 된 QuerySet을 슬라이스하면 목록도 반환됩니다.
 
 	또한 평가되지 않은 QuerySet을 슬라이스하는 경우 평가되지 않은 또 다른 QuerySet이 반환되지만 더 많은 필터를 추가하거나 순서를 수정하는 것은 허용되지 않습니다. 이는 SQL로 잘 변환되지 않으며 명확한 의미도 가지지 않기 때문.

*  Pickling / Caching. QuerySets를 산발 처리 할 때 관련된 내용에 대한 자세한 내용은 다음 섹션을 참조하십시오. 이 섹션에서 중요한 점은 결과가 데이터베이스에서 읽혀진다는 것.
* repr (). QuerySet은 repr ()을 호출 할 때 평가됩니다. 이것은 Python 인터프리터 인터프리터에서 편리하기 때문에 대화식으로 API를 사용할 때 결과를 즉시 볼 수 있습니다.
* len (). QuerySet은 len ()을 호출 할 때 평가됩니다. 예상 한대로 결과 목록의 길이를 반환합니다.
	참고 : 집합의 레코드 수를 결정할 필요가 있고 실제 개체가 필요하지 않은 경우 SQL의 SELECT COUNT (*)를 사용하여 데이터베이스 수준에서 카운트를 처리하는 것이 훨씬 효율적입니다. Django는 정확하게 count () 메소드를 제공합니다.
* ist (). QuerySet의 list ()를 호출하여 QuerySet을 강제 평가합니다. 예 :
```python
entry_list = list(Entry.objects.all())
```
* bool (). bool (), or, and 또는 if 문을 사용하는 것과 같이 부울 컨텍스트에서 QuerySet을 테스트하면 쿼리가 실행됩니다. 적어도 하나의 결과가 있으면 QuerySet은 True이고, 그렇지 않으면 False입니다. 예 :
```python
if Entry.objects.filter(headline="Test"):
   print("There is at least one Entry with the headline Test")
```
참고 : 적어도 하나의 결과 만 존재하는지 (실제 객체가 필요하지 않은지) 확인하려는 경우 exists ()를 사용하는 것이 더 효율적입니다.

###Pickling QuerySets
QuerySet을 pickle하면 pickling 전에 메모리에 모든 결과가로드됩니다. pickling은 일반적으로 캐싱의 선구자로 사용되며 캐싱 된 쿼리 세트가 다시로드 될 때 결과가 이미 사용되고 사용할 준비가되기를 원합니다 (데이터베이스 읽기는 캐싱의 목적을 무너 뜨리기까지 어느 정도 시간이 걸릴 수 있습니다). 즉, 쿼리 집합을 실행 취소 할 때 현재 데이터베이스에있는 결과가 아닌 쿼리 된 결과가 포함됩니다.

나중에 데이터베이스에서 QuerySet을 재 작성하기 위해 필요한 정보만을 pickle 경우 QuerySet의 쿼리 속성을 선택하십시오. 다음과 같은 코드를 사용하여 원래의 QuerySet을 다시로드 할 수 있습니다 (결과가로드되지 않음).
```
>>> import pickle
>>> query = pickle.loads(s)     # Assuming 's' is the pickled string.
>>> qs = MyModel.objects.all()
>>> qs.query = query            # Restore the original 'query'.
```
query 속성은 불투명 한 객체. 이는 쿼리 작성의 내부를 나타내며 공개 API의 일부가 아닙니다. 그러나 여기에 설명 된대로 속성의 내용을 pickle 및 unpickle하는 것은 안전하고 완벽하게 지원.
***
###QuerySet API
다음은 QuerySet의 정식 선언.
####class QuerySel(model=None, using=None)
일반적으로 QuerySet과 상호 작용할 때 필터를 연결하여 사용합니다. 이 작업을 수행하기 위해 대부분의 QuerySet 메소드는 새로운 쿼리 세트를 반환합니다. 이러한 방법은이 섹션의 뒷부분에서 자세히 설명합니다.

QuerySet 클래스에는 인트로 스펙트에 사용할 수있는 두 개의 공용 속성이 있습니다.

* **ordered**
	QuerySet이 순서가 맞으면 true입니다. 즉, 모델에 order_by () 절이 있거나 기본 순서가 있습니다. 그렇지 않으면 False.
* **db**
	이 쿼리가 지금 실행될 경우 사용할 데이터베이스입니다.

## 새로운 QuerySets을 반환하는 Methods
Django는 QuerySet에 의해 리턴 된 결과의 타입이나 SQL 쿼리가 실행되는 방식을 변경하는 다양한 QuerySet 세분화 메소드를 제공.
###filter()
####filter(**kwargs)
주어진 조회 매개 변수와 일치하는 개체를 포함하는 새로운 검색어 세트를 돌려줍니다.

조회 매개 변수 (** kwargs)는 아래 필드 조회에 설명 된 형식이어야합니다. 여러 매개 변수는 기본 SQL 문에서 AND를 통해 조인됩니다.

더 복잡한 쿼리 (예 : OR 문을 사용하는 쿼리)를 실행해야하는 경우 Q 개체를 사용할 수 있습니다.
****
###exclude()
####exclude**kwargs)
지정된 조회 매개 변수와 일치하지 않는 객체가 포함 된 새 QuerySet을 반환

조회 매개 변수 (** kwargs)는 아래 필드 조회에 설명 된 형식이어야합니다. 여러 매개 변수는 기본 SQL 문에서 AND를 통해 조인되며 모든 것은 NOT ()으로 묶입니다.

다음 예에서는 pub_date가 2005-1-3보다 늦고 제목이 "Hello"인 모든 항목을 제외.
```
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3), headline='Hello')
```
SQL 용어로 다음과 같이 반영됩니다.
```
SELECT ...
WHERE NOT (pub_date > '2005-1-3' AND headline = 'Hello')
```
다음 예에서는 pub_date가 2005-1-3보다 늦거나 모든 제목이 "Hello"인 모든 항목을 제외.
```
Entry.objects.exclude(pub_date__gt=datetime.date(2005, 1, 3)).exclude(headline='Hello')
```
SQL 용어로 다음과 같이 반영됩니다.
```
SELECT ...
WHERE NOT pub_date > '2005-1-3'
AND NOT headline = 'Hello'
```
두 번째 예제는 더 제한적.
더 복잡한 쿼리 (예 : OR 문을 사용하는 쿼리)를 실행해야하는 경우 Q 개체를 사용할 수 있음.
****
###annotate()
####annotate(*args, **kwargs)
제공된 쿼리 식 목록으로 QuerySet의 각 개체에 주석을 첨부합니다. 표현식은 간단한 값, 모델의 필드에 대한 참조 (또는 모든 관련 모델), 또는 QuerySet의 오브젝트와 관련된 오브젝트에 대해 계산 된 집계 표현식 (평균, 합계 등).

annotate ()에 대한 각 인수는 반환되는 QuerySet의 각 객체에 추가되는 주석입니다.

Django가 제공하는 집계 함수는 아래의 집계 함수에 설명되어 있습니다.

키워드 인수를 사용하여 지정된 주석은이 키워드를 주석의 별칭으로 사용합니다. 익명 인수에는 집계 함수의 이름과 집계중인 모델 필드를 기반으로 별칭이 생성됩니다. 단일 필드를 참조하는 집계 식만 익명 인수가 될 수 있습니다. 다른 모든 것은 키워드 인수 여야합니다.

예를 들어 블로그 목록을 조작하는 경우 각 블로그에서 몇 개의 항목이 작성되었는지 확인할 수 있습니다.
```
>>> from django.db.models import Count
>>> q = Blog.objects.annotate(Count('entry'))
# The name of the first blog
>>> q[0].name
'Blogasaurus'
# The number of entries on the first blog
>>> q[0].entry__count
42
```
블로그 모델은 entry__count 속성 자체를 정의하지 않지만 키워드 인수를 사용하여 집계 함수를 지정하면 주석의 이름을 제어 할 수 있습니다.
```
>>> q = Blog.objects.annotate(number_of_entries=Count('entry'))
# The number of entries on the first blog, using the name provided
>>> q[0].number_of_entries
42
```
****
###order_by()
####order_by(*fields)
기본적으로 QuerySet에 의해 반환 된 결과는 모델 메타의 정렬 옵션에 의해 주어진 순서 튜플에 의해 정렬됩니다. order_by 메소드를 사용하여 QuerySet 단위로이 값을 겹쳐 쓸 수 있습니다.
예:
```
Entry.objects.filter(pub_date__year=2005).order_by('-pub_date', 'headline')
```
참고 : order_by ( '?') 쿼리는 사용하는 데이터베이스 백엔드에 따라 값이 무겁고 느릴 수 있음.
다른 모델의 필드로 정렬하려면 모델 관계를 쿼리 할 때와 같은 구문을 사용하십시오. 즉, 필드 이름과 이중 밑줄 (__), 새 모델의 필드 이름 등이 포함됩니다. 원하는 모델을 추가 할 수 있습니다. 예 :
```
Entry.objects.order_by('blog__name', 'headline')
```
Django는 다른 모델과 관계가있는 필드로 정렬을 시도하면 관련 모델의 기본 순서를 사용하거나 Meta.ordering이 지정되지 않은 경우 관련 모델의 기본 키순으로 정렬합니다. 예를 들어, 블로그 모델에는 기본 순서가 지정되어 있지 않기 때문에 :
```
Entry.objects.order_by('blog')
```
...와 동일합니다.
```
Entry.objects.order_by('blog__id')
```
Blog가 ordering = [ 'name'] 인 경우 첫 번째 쿼리 세트는 다음과 같습니다.
```
Entry.objects.order_by('blog__name')
```
관련 필드의 _id를 참조하여 JOIN의 비용을 들이지 않고 관련 필드에서 쿼리 세트를 주문할 수도 있습니다.
```
# No Join
Entry.objects.order_by('blog_id')

# Join
Entry.objects.order_by('blog__id')
```
표현식에서 asc () 또는 desc ()를 호출하여 쿼리 식으로 정렬 할 수도 있습니다.
```
Entry.objects.order_by(Coalesce('summary', 'headline').desc())
```

****
###reverse()
####reverse()
reverse () 메서드를 사용하여 쿼리 세트의 요소가 반환되는 순서를 바꿉니다. reverse ()를 다시 호출하면 순서가 정상 방향으로 복원됩니다.

queryset에서 "마지막"다섯 항목을 검색하려면 다음을 수행 할 수 있습니다.
```
my_queryset.reverse()[:5]
```
이것은 파이썬에서 시퀀스의 끝에서부터 슬라이싱하는 것과 완전히 같지 않음에 유의하십시오. 위의 예제는 마지막 항목을 먼저 반환하고, 마지막에서 두 번째 항목을 반환합니다. 파이썬 시퀀스가 ​​있고 seq [-5 :]를 보면, 다섯 번째 마지막 항목이 먼저 보입니다. Django는 SQL에서 효율적으로 수행 할 수 없으므로 마지막부터 슬라이싱하는 액세스 모드를 지원하지 않습니다.

또한 reverse ()는 일반적으로 정의 된 순서가있는 QuerySet에서만 호출되어야합니다 (예 : 기본 순서를 정의하는 모델을 쿼리하거나 order_by ()를 사용할 때). 주어진 QuerySet에 대해 그러한 정렬이 정의되어 있지 않으면 reverse ()를 호출하면 실제 효과가 없습니다. reverse ()를 호출하기 전에 정렬이 정의되지 않았으며 나중에 정의되지 않습니다.

****
###distinct()
####distinct(*fields)
SQL 쿼리에서 SELECT DISTINCT를 사용하는 새 QuerySet을 반환합니다. 이렇게하면 조회 결과에서 중복 행이 제거됩니다.

기본적으로 QuerySet은 중복 행을 제거하지 않습니다. 실제로 Blog.objects.all ()과 같은 간단한 쿼리는 결과 행이 중복 될 가능성이 있기 때문에 거의 문제가되지 않습니다. 그러나 쿼리가 여러 테이블에 걸쳐있는 경우 QuerySet을 평가할 때 중복 결과를 얻을 수 있습니다. 그것은 distinct ()를 사용할 때입니다.