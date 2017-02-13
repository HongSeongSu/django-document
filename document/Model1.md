#Django Models

###Models
모델은 데이터에 대한 단일 정보 소스입니다. 여기에는 저장중인 데이터의 필수 필드와 동작이 포함됩니다. 일반적으로 각 모델은 단일 데이터베이스 테이블에 매핑됩니다.

**기본사항**
	
* 각 모델은 django.db.models.Model을 하위 클래스로 묶는 Python 클래스
* 모델의 각 속성은 데이터베이스 필드를 나타냄
* 이런 것을 통해서 Django는 자동으로 생성된 데이터베이스 엑세스 API를 제공

####예제
이 예제는 first_name과 last_name을 가진 Person을 정의

	from django.db import models

	class Person(models.Model):
		first_name = models.CharField(max_length=30)
		last_name = models.CharField(max_length=30)
	
first_name과 last_name은 모델의 필드입니다. 각 필드는 클래스 속성으로 지정되며 각 속성은 데이터베이스 열에 매핑됩니다.

위의 Person 모델은 다음과 같은 데이터베이스 테이블을 생성합니다.

	CREATE TABLE myapp_person (
		"id" serial NOT NULL PRIMARY KEY,
		"first_name" varchar(30) NOT NULL,
		"last_name" varchar(30) NOT NULL
	);
	
####**기술노트**
* 테이블의 이름인 myapp_person은 일부 모델 메타 데이터에 자동으로 파생되지만 재정의가 될 수 있습니다.
* id 필드가 자동으로 추가되지만 동작은 무시 될수가 있습니다. 자동으로 기본 키 필드를 참조하세요.
* 이 예제의 CREATE TABLE SQL은 PostgreSQL 구문을 사용하여 포맷되지만 Django는 설정 파일에 지정된 데이터베이스 백엔드에 맞게 SQL을 사용.

###모델 사용
모델을 정의한 후에는 Django에게 해당 모델을 사용할 것이라고 알려야합니다. 설정 파일을 편집하고 INSTALLED_APPS 설정을 변경하여 models.py가 포함 된 모듈의 이름을 추가하십시오.

예를 들어, 응용 프로그램의 모델이 myapp.models 모듈 (manage.py startapp 스크립트로 응용 프로그램 용으로 작성된 패키지 구조)에있는 경우 INSTALLED_APPS는 부분적으로 다음을 읽어야합니다.

	INSTALLED_APPS = [
	#...
	'myapp',
	#...
	]
	
INSTALLED_APPS에 새 앱을 추가 할 때는 manage.py migrate를 실행하고, 선택적으로 manage.py makemigrations를 사용하여 마이그레이션을 수행하십시오.

###필드
모델의 가장 중요한 부분과 모델의 유일한 필수 부분은 정의하는 데이터베이스 필드의 목록입니다. 필드는 클래스 속성에 의해 지정됩니다. 모델 API와 충돌하는 필드 이름을 정리, 저장 또는 삭제하지 않도록 주의하십시오.

	from django.db import models

	class Musician(models.Model):
		first_name = models.CharField(max_length=50)
		last_name = models.CharField(max_length=50)
		instrument = models.CharField(max_length=100)

	class Album(models.Model):
		artist = models.ForeignKey(Musician, on_delete=models.CASCADE)
		name = models.CharField(max_length=100)
		release_date = models.DateField()
		num_stars = models.IntegerField()
		
###필드 타입
모델의 각 필드는 해당 Field 클래스의 인스턴스여야 합니다. 장고는 필드 클래스 유형을 사용하여 몇 가지를 결정합니다

* 데이터베이스에 저장할 데이터 종류
(예 : INTEGER, VARCHAR, TEXT)를 나타내는 열 유형입니다.
* 양식 필드를 렌더링 할 때 사용할 기본 HTML 위젯입니다
(예 : <,input type = "text">, <,select>).
* 장고 관리자 및 자동 생성 양식에서 사용되는 최소한의 유효성 확인 요구 사항.

###필드 옵션
각 필드는 필드 특정 인수 집합을 사용합니다. 예를 들어, CharField (및 해당 하위 클래스)에는 데이터를 저장하는 데 사용되는 VARCHAR 데이터베이스 필드의 크기를 지정하는 max_length 인수가 필요합니다.

* null : True이면 Django는 빈 값을 NULL로 데이터베이스에 저장. 기본값은 False.
* blank : True이면 입력란을 비워 둘 수 있습니다. 기본값은 False.
이것은 null과 다릅니다. null은 순전히 데이터베이스와 관련된 반면 blank는 유효성 검사와 관련이 있습니다. 필드에 blank = True가 있으면 양식 유효성 검사에서 빈 값을 입력 할 수 있습니다. 필드에 blank = False가 있으면 필드가 필요합니다.
* choices :이 필드의 선택 사항으로 사용할 2- 튜플의 반복 가능한 (예 : 목록 또는 튜플) 이것이 주어지면 기본 양식 위젯은 표준 텍스트 필드 대신 선택 상자가되며 주어진 선택 사항으로 선택 항목을 제한합니다.
