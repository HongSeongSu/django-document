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