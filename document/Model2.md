###일대일 관계(One-to-one relationships)
일대일 관계를 정의하려면 OneToOneField를 사용!
다른 Field 유형과 마찬가지로 모델의 클래스 속성으로 포함하여 사용

객체의 기본키에서 객체가 어떤 방식으로 다른 객체를 확장 할 때 가장 유용

**OneToOneField**에는 위치 인수가 필요하다. ( 모델이 관련된 클래스 )

예를들면, 장소의 데이터베이스를 구축했다면 데이터베이스에 주소, 전화 번호등과 같은 표준적인 물건을 만들 수 있다. 그다음 식당의 데이터베이스를 구축하고 레스토랑 모델에서 해당 필드를 복제하는 대신 레스토랑을 OneToOneField Place 로 만들 수 있다.

ForeignKey와 마찬가지로 재귀 관계를 정의 할 수 있으며 아직 정의되지 않은 모델에 대한 참조를 만들 수 있습니다.

OneToOneField 필드는 parent_link 인수를 선택으로 받을 수 있다.

OneToOneField 클래스는 모델에서 자동으로 기본 키가됩니다. 원하는 경우에는 primary_key 인수를 수동으로 전달할 수 있지만 더 이상 필요하지 않습니다. 따라서 이제 단일 모델에 OneToOneField 유형의 여러 필드를 포함 할 수 있다.
****
###Models across files
모델을 다른 앱의 모델과 연결 할수 있습니다. 이렇게 하려면 모델이 정의 된 파일의 맨 위에 관련 모델을 가져옵니다.

	```python

	from django.db import models
	from geography.models import ZipCode

	class Restaurant(models.Model):
	# ...
	zip_code = models.ForeignKey(
		ZipCode,
		on_delete=models.SET_NULL,
		blank=True,
		null=True,
	 )

	```
***
###Field name restrictions
장고는 모델 필드 이름에 두가지 제한만 배치합니다.

1. 필드 이름은 파이썬 구문 오류가 발생하기 때문에 파이썬 예악어(python syntax error)가 될 수 없습니다.


		```python
		class Example(models.Model):
			pass =  models.IntergerField()
		```
	
2. 필드 이름은 Django의 쿼리 조회 구문이 작동하는 방식으로 한 줄에 하나 이상의 밑줄을 포함 할 수 없습니다.

		```python
		class Example(models.Model):
			foo__bar = models.IntergerField()
		```
그러나 이러한 제한 사항은 필드 이름이 데이터베이스 열 이름과 일치 할 필요가 없기 때문에 해결 될 수 있습니다.

Django는 모든 기본 SQL 쿼리의 모든 데이터베이스 테이블 이름과 열 이름을 이스케이프 처리하므로 SQL 예약어 (예 : join, where 또는 select)는 모델 필드 이름으로 사용할 수 있습니다. 특정 데이터베이스 엔진의 인용 구문을 사용합니다.
****
###Custom field types
기존 모델 필드 중 하나를 사용하여 용도에 맞게 사용할 수 없거나 덜 일반적으로 사용되는 데이터베이스 열 유형을 활용하려는 경우 자체 필드 클래스를 만들 수 있습니다.
****
###Meta options
내부 클래스 Meta를 사용하여 모델 메타 데이터를 제공하십시오.

	```python
	from django.db import models
	
	class Ox(models.Model):
		horn_length = models.IntegerField()
		
		class Meta:
			ordering = ["horn_legth"]
			verbose_name_plural = "oxen"
	```
모델 메타 데이터는 주문 옵션(ordering), 데이터베이스 테이블 이름(db_table) 또는 인간이 읽을 수 있는 단수 및 복수 이름(verbose_name, verbose_name_plural)과 같이 필드가 아닌 모든것. 아무것도 필요하지 않으면 클래스 메타를 모델에 추가하는 것은 선택사항.

****
###Model attributes
####objects
모델의 가장 중요한 속성은 Manager입니다. Django 모델에 데이터베이스 쿼리 작업이 제공되고 데이터베이스에서 인스턴스를 검색하는 데 사용되는 인터페이스입니다. 사용자 정의 관리자가 정의되지 않은 경우 기본 이름은 objects입니다. 관리자는 모델 클래스가 아닌 모델 인스턴스를 통해서만 액세스 할 수 있습니다.
****
###Model methods
모델에 사용자 정의 메소드를 정의하여 사용자 정의 row-level 기능을 객체에 추가함.
Manager 메소드는 "table-wide"작업을 수행하는 것이지만, 모델 메소드는 특정 모델 인스턴스에서 작동해야합니다.
 
 	```python
 	from django.db import models
 	
 	class Person(models.Model):
 		first_name = models.CharField(max_length=50)
 		last_name = models.CharField(max_length=50)
 		birth_date = models.DateField()
 		
 		def baby_boomer_status(self):
 			import datetime
 			if self.birth_date < datetime.date(1945, 8, 1):
 				return "Pre-boomer"
 			elif self.birth_date < datetime.date(1965, 1, 1):
 				return "Baby boomer"
        		else:
            		return "Post-boomer"
 	```
 	
 이 예제의 마지막 메서드는 속성입니다.
 모델 인스턴스 참조에는 각 모델에 자동으로 부여되는 메소드의 전체 목록이 있습니다.
 
 	__str__()
 	 모든 객체의 유니 코드 표현을 반환하는 Python "magic method"입니다. 이것은 모델 인스턴스를 강제로 문자열로 표시해야 할 때마다 Python과 Django가 사용하게 될 것입니다. 특히 이것은 대화 형 콘솔이나 관리자에 개체를 표시 할 때 발생합니다.
 	
 	get_absolute_url()
	Django는 객체의 URL을 계산하는 방법을 알려줍니다. Django는 관리 인터페이스에서 이것을 사용하며 언제든지 객체의 URL을 찾아야합니다.


****
###Overriding predefined model methods
커스터마이징 할 데이터베이스 동작을 캡슐화하는 또 다른 모델 메서드 집합이 있습니다. 특히 save () 및 delete () 작업 방식을 변경하려는 경우가 많습니다.

행동을 바꾸기 위해 이 방법을 (그리고 다른 어떤 모델 방법) 자유롭게 무시할 수 있습니다.

내장 된 메서드를 재정의하는 일반적인 사용 사례는 개체를 저장할 때마다 어떤 일이 발생하기를 원할 때입니다.

	```python
	from django.db import models
	
	class Blog(models.Model):
		name = models.CharField(max_length=100)
		tagline = models.TextField()
		
		def save(self, *args, **kwargs):
			do_something()
			super(Blog, self).save(*args, **kwargs):
			do_something_else()
	
	
	from django.db import models
	
	class Blog(models.Model):
		name = modles.CharField(max_length=100)
		tagline = models.TextField()
		
		def save(self, *args, **kwargs):
			if self.name == "Blog"
				return
			elsd:
				super(Blog, self).save(*args, **kwargs)
	```

수퍼 클래스 메소드를 호출하는 것을 기억하는 것이 중요합니다. super (Blog, self) .save (* args, * * kwargs) 비즈니스입니다. 객체가 여전히 데이터베이스에 저장되도록합니다. 수퍼 클래스 메서드를 호출하는 것을 잊어 버리면 기본 비헤이비어가 발생하지 않고 데이터베이스에 손을 대지 않습니다.

또한 모델 메서드에 전달할 수있는 인수를 전달하는 것이 중요합니다. 즉, * args는 * * kwargs 비트가하는 것입니다. Django는 때때로 빌트인 모델 메서드의 기능을 확장하여 새로운 인수를 추가합니다. 메소드 정의에서 * args, **kwargs를 사용하면 코드가 추가 될 때 해당 인수를 자동으로 지원한다는 보장을받습니다.

****
###모델 상속( Model Inheritance )
Django의 모델 상속은 파이썬에서 일반적인 클래스 상속이 작동하는 방식과 거의 동일하게 작동하지만 페이지 시작 부분의 기본 사항은 계속 따라야합니다. 이는 기본 클래스가 django.db.models.Model을 서브 클래스 화해야 함을 의미합니다.

부모 모델이 자신 만의 모델 (자체 데이터베이스 테이블 포함)이 될지 또는 부모가 자식 모델을 통해서만 볼 수있는 일반 정보의 소유자인지 여부를 결정해야합니다.

######장고에서 가능한 세 가지 스타일의 상속이 있습니다.
* 흔히 부모 클래스를 사용하여 각 하위 모델에 대해 입력하지 않으려는 정보를 보유하기를 원할 것입니다. 이 클래스는 단독으로 사용되지 않으므로 추상 기본 클래스는 사용자가 수행한 것.
* 기존 모델을 하위 클래스화하고 각 모델에 자체 데이터베이스 테이블을 갖기를 원한다면 다중 테이블 상속이 필요함.
* 마지막으로 모델 필드를 변경하지 않고 모델의 파이썬 동작만 수젖ㅇ하려는 경우 Proxy모델을 사용할 수 있다.

###추상 기본 클래스( Abstract base classes )
추상 기본 클래스는 몇가지 공통된 정보를 여러 다른 모델에 넣으려 할 떄 유용함. 당신은 당신의 기본 클래스를 선택하고 meta클래스에 abstract = True를 넣는다. 이 모델은 데이터베이스 테이블을 만드는데 사용되지 않습니다. 대신 다른 모델의 기본 클래스로 사용될 때 해당 필드는 하위 클래스의 필드에 추가됩니다. 자식의 이름과 같은 이름을 갖는 추상 기본 클래스의 필드를 갖는 것은 오류입니다.( 장고는 예외를 발생시킴 )

	 ```python
 	from django.db import models
 
 	class CommonInfo(models.Model):
 		name = models.CharField(max_length=100)
 		age = models.PositiveIntegerField()
 		
 		class Meta:
 			abstract = True
 		
 	class Student(CommonInfo):
 		home_group = models.CharField(max_length=5)
	 ```
	 
Student 모델에는 이름, 나이 및 home_group의 세가지 필드가 있습니다. CommonInfo 모델을 추상적인 기본 클래스이므로 일반 Django 모델로 사용할 수 없습니다. 데이터베이스 테이블을 생성하거나 관리자가 없으므로 직접 인스턴스화 하거나 저장할 수 없습니다.

많은 용도에서 이런 유형의 모델 상속은 당신이 정확히 원하는 것입니다. 이것은 파이썬 레벨에서 공통 정보를 분석하는 방법을 제공하면서 데이터베이스 레벨에서 하위 모델당 하나의 데이터베이스 테이블만 생성합니다.

####Meta 상속
추상 기본 클래스가 생성되면 Django는 기본 클래스에서 선언 한 Meta 내부 클래스를 속성으로 사용할 수 있게 합니다. 자식클래스가 자신의 메타 클래스를 선언하지 않으면 부모 클래스의 메타를 상속받습니다. 자식이 부모의 Meta클래스를 확장하려고하면 하위 클래스를 하위클래스로 만들수 있습니다.

	```python
	from django.db import models
	
	class CommonInfo(models.Model):
		class Meta:
			abstract = True
			ordering = ['name']
			
		class Student(CommonInfo):
			class Meta(CommonInfo.Meta):
				db_table = 'student_info'
	```

Django는 추상 기본 클래스의 Meta클래스를 조정합니다. Meta속성을 설치하기 전에 abstract = False로 설정합니다. 즉, 추상 기본 클래스의 자식은 자동으로 추상 클래 자체가 되지 않습니다. 물론 다른 추상 기본 클래스에서 상속받은 추상 기본 클래스를 만들 수 있습니다. 매번 abstract = True를 명시적으로 설정하는 것을 기억하면 됩니다.

일부 속성은 추상 기본 클래스의 메타 클래스에 포함하는 것이 타당하지 않습니다. 예를들면, db_table을 포함하는 것은 모든 자식클래스(자신의 메타를 지정하지 않은 클래스)가 동일한 데이터베이스 테이블을 사용한다는 것을 의미합니다

****
###멀티 테이블 상속
Django가 지원하는 모델 상속의 두 번째 유형은 계층 구조의 각 모델이 모두 하나의 모델일 떄입니다. 각 모델은 자체 데이터베이스 테이블에 해당하며 개별적으로 쿼리하고 생성 할 수 있습니다. 상속관계는 자식 모델과 부모(자동으로 생성 된 OneToOneField를 통해)사이의 링크를 도입.

	```python
	from django.db import models
	
	class Place(models.Model):
		name = models.CharField(max_length=50)
		address = models.CharField(max_length=80)
		
	class Restaurant(Place):
		serves_hot_dogs = models.BooleanField(default=False)
		serves_pizza = models.BooleanField(default=False)
	```

Place의 모든 필드는 Restaurant에서 사용할 수 있습니다. 단, 데이터는 다른 데이터베이스 테이블에 있습니다.

	>>> Place.objects.filter(name="Bob's Cafe")
	>>> Restaurant.objects.filter(name="Bob's Cafe")

Restaurant인 Place가 있는 경우 모델 이름을 소문자 버전을 사용하여 Place객체에서 Restaurant객체로 이동할수 있습니다.

	>>> p = Place.objects.get(id=12)
	# p가 Restaurant 객체인 경우 이것은 자식 클래스를 제공합니다.
	>>> p.restaurant
	
그러나 위 예제의 p가 Restaurant(Place객체로 직접 생성되었거나 다른 클래스의 부모)인 경우 p.restaurant를 참조하면 Restaurant.DoesNotExist 예외가 발생합니다.

#### 메타와 멀티 테이블 상속
멀티 테이블 상속 상황에서 자식 클래스가 부모의 메타 클래스에서 상속받는 것은 의미가 없습니다. 모든 메타 옵션은 이미 상위 클래스에 적용되었고 다시 적용하면 모순 된 행동 만 발생합니다 (이는 기본 클래스가 자체적으로 존재하지 않는 추상 기본 클래스의 경우와 대조적입니다). 따라서 자식 모델은 부모의 메타클래스에 액서스 할 수 없습니다. 그러나 아동이 부모로부터 행동을 물려봗는 제한된 경우가 몇가지 았습니다. 자식이 순서 지정 속성 또는 get_latest_by 속성을 지정하지 않는 경우는 부모로부터 순서를 상속합니다.

	```python
	lass ChildModel(ParentModel):
    		# ...
    		class Meta:
        		# Remove parent's ordering effect
        		ordering = []
	```

