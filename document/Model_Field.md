#Model Field Reference

##Field options
다음 인수는 모든 필드 유형에서 사용할 수 있습니다.(모두 선택 사항입니다.)

###null
####Field.null
True면 Django는 빈 값을 NULL로 데이터 베이스에 저장합니다. 기본값은 False입니다.

빈 문자열 값은 항상 NULL이 아닌 빈 문자열로 저장되므로 CharField 및 TextField와 같은 문자열 기반 필드에서는 null을 사용하지 마세요. 문자열 기반 필드에 null = True가 있으면 no data에 대해 두가지 값이 있음을 의미합니다. NULL및 빈 문자열 대부분의 경우, no data에 대해 가능한 두가지 값을 갖는 것은 불필요합니다. Django 규칙은 NULL이 아닌 빈 문자열을 사용하는 것입니다.

문자열 기반 및 비문자열 기반 필드의 경우 null 매개 변수가 데이터베이스 저장소에만 영향을 주기 때문에 양식에서 빈값을 허용하려면 blank = True로 설정해야합니다.

BooleanField로 null값을 허용하려면 대신 NullBooleanField를 사용하십시오.

****
###blank
####Field.blank
True면 필드는 비워 둘 수 있습니다. 기본값은 False입니다.

이것은 null과 다릅니다. null은 순전히 데이터베이스와 관련된 반면 blank는 유효성 검사와 관련이 있습니다. 필드에 blank = True가 있으면 양식 유효성 검사에서 빈 값을 입력 할 수 있습니다. 필드에 blank = False 가 있으면 필드가 필요합니다.
****
###choices
####Field.choices
이 필드의 선택 항목으로 사용할 정확한 두 항목 (예 : [(A, B), (A, B) ...])의 반복 가능 항목으로 구성된 반복 가능한 항목 (예 : 목록 또는 튜플)입니다. 이것이 주어지면, 기본약식 위젯은 표준 텍스트 필드 대신 선택 항목을 가진 선택 상자가 됩니다.

각 튜플의 첫 번째 요소는 모델에 설정할 실제 값이고 두 번째 요소는 사람이 읽을 수있는 이름입니다.

```python
	YEAR_IN_SCHOOL_CHOICES = (
		('FR', 'Freshman'),
		('SO", "Sophomore'),
		('JR', 'Junior'),
		('SR', 'Senior'),
	)
```
일반적으로 모델 클래스 내에서 선택 사항을 정의하고 각 값에 대해 적절한 이름의 상수를 정의하는 것이 가장 좋습니다.

```python
	from django.db import models
	
	class Student(models.Model):
		FRESHMAN = 'FR'
		SOPHOMORE = 'SO'
		JUNIOR = 'JR'
		SENIOR = 'SR'
		YEAR_IN_SCHOOL_CHOICES = (
			(FRESHMAN, 'Freshman'),
			(SOPHOMORE, 'Sophomore'),
			(JUNIOR, 'Junior'),
			(SENIOR, 'Senior'),
		)
		year_in_school = models.CharField(
			max_length=2,
			choices=YEAR_IN_SCHOOL_CHOICES,
			default=FRESHMAN,
		)
		
		def is_upperclass(self):
			return self.year_in_school in (self.JUNIOR, self.SENIOR)
```
모델 클래스 외부에서 선택 목록을 정의한 다음 참조 할 수 있지만 모델 클래스 내부의 각 선택 항목에 대한 선택 사항과 이름을 정의하면 해당 정보를 사용하는 클래스와 모든 정보가 유지되고 선택 사항을 쉽게 참조 할 수 있습니다 ( 예 : Student.SOPHOMORE는 Student 모델을 가져온 곳이면 어디에서나 사용할 수 있습니다.

조직 목적으로 사용될 수있는 명명 된 그룹으로 사용 가능한 선택 사항을 수집 할 수도 있습니다.

```python
	MEDIA_CHOICES = (
	    ('Audio', (
		    ('vinyl', 'Vinyl'),
		    ('cd', 'CD'),
		)
	    ),
	    ('Video', (
		    ('vhs', 'VHS Tape'),
		    ('dvd', 'DVD'),
		)
	    ),
	    ('unknown', 'Unknown'),
	)
```
각 튜플의 첫 번째 요소는 그룹에 적용 할 이름입니다. 두 번째 요소는 2- 튜플의 반복 가능이며 각 2- 튜플에는 옵션에 대한 값과 사람이 읽을 수있는 이름이 들어 있습니다. 그룹화 된 옵션은 단일 목록 내의 그룹화되지 않은 옵션과 결합 될 수 있습니다 (이 예에서는 알 수없는 옵션).

선택 항목이있는 각 모델 필드에 대해 Django는 필드의 현재 값에 대한 사람이 읽을 수있는 이름을 검색하는 메소드를 추가합니다. 데이터베이스 API 문서에서 get_FOO_display ()를 참조하십시오.

선택 사항은 모든 반복 가능한 객체 일 수 있습니다. 반드시 목록이나 튜플 일 필요는 없습니다. 이를 통해 선택 사항을 동적으로 구성 할 수 있습니다. 그러나 자신이 역동적 인 선택을 해킹하는 경우 ForeignKey가있는 적절한 데이터베이스 테이블을 사용하는 것이 좋습니다. 선택 사항은 많은 경우 변경되지 않는 정적 데이터를 의미합니다.

blank = False가 기본값과 함께 필드에 설정되어 있지 않으면 "---------"가 포함 된 레이블이 선택 상자와 함께 렌더링됩니다. 이 동작을 무시하려면 None을 포함하는 선택 항목에 튜플을 추가합니다. 예 : (None, 'Your String for Display'). 또는 CharField에서와 같이 None 대신 빈 문자열을 사용할 수 있습니다.
****
###db_colum
####Field.db_colum
이 필드에 사용할 데이터베이스 열의 이름. 이것이 주어지지 않으면 장고는 필드의 이름을 사용합니다.

데이터베이스 열 이름이 SQL 예약어이거나 Python 변수 이름에서 허용되지 않는 문자 (특히 하이픈)를 포함하면 OK입니다. Django는 배후에서 열과 테이블 이름을 인용합니다.
****
###db_index
####Field.db_index
값이 True면, 이 필드에 의해 데이터베이스 인덱스가 작성 됩니다.
****
###db_tablespace
####Field.db_tablespace
이 필드가 색인화되어 있는 경우, 이 필드 색인에 사용할 데이터베이스 테이블스페이스의 이름이다.  기본값은 프로젝트의 DEFAULT_INDEX_TABLESPACE 설정 (설정된 경우) 또는 모델의 db_tablespace (있는 경우)입니다. 백엔드가 인덱스의 테이블 공간을 지원하지 않으면이 옵션은 무시됩니다.
****
###default
####Field.default
필드의 기본값입니다. 값 또는 호출 가능 객체 일수 있습니다. 호출 가능하면 새로운 객체가 생성 될 때마다 호출됩니다.

기본값은 변경 가능한 오브젝트(모델 인스턴스, 목록, 세트 등) 일 수 없습니다. 이는 해당 오브젝트의 동일한 인스턴스에 대한 참조가 모든 새 모델 인스턴스에 대한 참조가 모든 새 모델 인스턴스의 기본값으로 사용되기 때문입니다. 대신 원하는 기본값을 호출 가능 코드로 래핑하십시오. 

```python
	def contact_default():
	    return {"email": "to1@example.com"}

	contact_info = JSONField("ContactInfo", default=contact_default)
```
람다는 마이그레이션을 통해 직렬화 할 수 없으므로 기본값과 같은 필드 옵션에는 사용할 수 없습니다. 다른 주의사항에 대해서는 해당 설명을 참조.

모델 인스턴스에 매핑되는 ForeignKey와 같은 필드의 경우 기본값은 모델 인스턴스 대신 참조하는 필드 값(to_field가 설정되지 않은 경우 pk)이어야 합니다.

기본값은 새 모델 인스턴스가 만들어지고 값이 필드에 제공되지 않을 때 사용됩니다. 필드가 기본 키인 경우 필드가 없음으로 설정된 경우 기본값이 사용됩니다.

****
###editable
####Field.editable
False 인 경우 필드는 관리자 또는 다른 ModelForm에 표시되지 않습니다. 또한 모델 유효성 검사 도중 건너 뜁니다. 기본값은 True입니다.
****
###error_messages
####Field.error_messages
error_messages 인수를 사용하면 필드에서 발생시키는 기본 메시지를 대체 할 수 있습니다. 덮어 쓰려는 오류 메시지와 일치하는 키가있는 사전을 전달하십시오.

오류 메시지 키에는 null, blank, invalid, invalid_choice, unique 및 unique_for_date가 포함됩니다. 추가 오류 메세지 키는 아래 필드 유형 섹션의 각 필드에 지정됩니다.

****
###help_text
####Field.help_text
폼 위젯과 함계 표시되는 추가 help텍스트. 폼에서 필드를 사용하지 않아도 문서화에 유용합니다.

이 값은 자동 생성 양식에서 HTML 이스케이프 처리되지 않습니다. 원하는 경우 help_text에 HTML을 포함시킬 수 있습니다.

	```python
	help_text="Please use the following format: <em>YYYY-MM-DD</em>."
	```

또는 일반텍스트와 django.utils.html.escape()를 사용하여 HTML 특수 문자를 이스케이프 처리 할 수 있습니다. 교차 사이트 스크립팅 공격을 피하기 위해 신뢰 할 수 없는 사용자로부터 온 도움말 텍스트를 이스케이프 처리 해야합니다.
****
###primary_key
####Field.primary_key
True면 필드는 모델의 기본 키입니다.

모델의 모든 필드에 대해 primary_key = True를 지정하지 않으면 Django는 기본 키를 보유 할 자동 필드를 자동으로 추가합니다. 기본 기본 키 동작을 덮어 쓰지 않으려면 모든 필드에서 primary_key = True를 설정할 필요가 없습니다. 

primary_key = True는 null = False 및 unique = True를 의미합니다. 하나의 기본 키만 객체에 허용됩니다.

기본 키 필드는 읽기 전용입니다. 기존 개체의 기본 키 값을 변경 한 다음 저장하면 이전 개체와 함께 새 개체가 만들어집니다.

****
###unique
####Field.unique
True면 필드는 테이블 전체에서 고유해야합니다.

이것은 데이터베이스 레벨 및 모델 검증에 의해 시행됩니다. 고유 한 필드에 중복 값이있는 모델을 저장하려고하면 모델의 save () 메소드에 의해 django.db.IntegrityError가 발생합니다.

이 옵션은 ManyToManyField, OneToOneField 및 FileField를 제외한 모든 필드 유형에 유효합니다.

unique이 True이면 인덱스 생성을 의미하므로 db_index를 지정할 필요가 없습니다.
****
###unique_for_date
####Field.unique_for_date
이것을 DateField 또는 DateTimeField의 이름으로 설정하여이 필드가 날짜 필드의 값에 대해 고유해야합니다. 예를 들어, unique_for_date = "pub_date"라는 필드 제목이 있으면 Django는 같은 제목과 pub_date를 가진 두 개의 레코드를 입력 할 수 없습니다. DateTimeField를 가리 키도록 설정하면 필드의 날짜 부분 만 고려됩니다. 게다가 USE_TZ가 True 일 때, 개체가 저장 될 때 현재 시간대에서 검사가 수행됩니다.

이는 모델 유효성 검사 중에 Model.validate_unique ()에 의해 적용되지만 데이터베이스 수준에서는 적용되지 않습니다. model_validate_unique ()는 unique_for_date 제약 조건이 ModelForm의 일부가 아닌 필드를 포함하면 (예 : 필드 중 하나가 exclude에 나열되거나 editable = False 인 경우) 해당 특정 제약 조건에 대한 유효성 검사를 건너 뜁니다.
****
###unique_for_month
####Field.unique_for_month
unique_for_date와 같지만 월을 기준으로 필드가 고유해야합니다.
****
###unique_for_year
####Field.unique_for_year
unique_for_date 및 unique_for_month와 같습니다.
****
###verbose_name
####Field.verbose_name
사람이 읽을 수있는 이름입니다. 자세한 이름이 주어지지 않으면 Django는 밑줄을 공백으로 변환하여 필드의 속성 이름을 사용하여 장황한 이름을 자동으로 만듭니다. Verbose 필드 이름을 참조하십시오.
****
###validators
####Field.validators
이 필드에 대해 실행하는 유효성 검사기 목록입니다. 자세한 내용은 유효성 검사기 설명서를 참조하십시오.
####Registering and fetching lookups
필드는 조회 등록 API를 구현합니다. API를 사용하여 필드 클래스에 사용할 수있는 조회와 필드에서 조회를 가져 오는 방법을 사용자 정의 할 수 있습니다.
****
##Field types

###AutoField
####class AutoField(**options)
사용 가능한 ID에 따라 자동으로 증가하는 IntegerField입니다. 보통 이것을 직접 사용할 필요는 없습니다. 별도로 지정하지 않으면 기본 키 필드가 자동으로 모델에 추가됩니다. 자동 기본 키 필드를 참조하십시오.
****
###BigIntegerField
####class BigIntegerField(**options)
-9223372036854775808에서 9223372036854775807까지의 숫자를 맞출 수 있다는 점을 제외하고는 IntegerField와 매우 흡사 한 64 비트 정수입니다.이 필드의 기본 양식 위젯은 TextInput입니다.
****
###BinaryField
####class BinaryField(**options)
원시 이진 데이터를 저장하는 필드입니다. 바이트 할당 만 지원합니다. 이 입력란에는 기능이 제한되어 있습니다. 예를 들어, BinaryField 값에 대한 쿼리 집합을 필터링 할 수 없습니다. 또한 BinaryField를 ModelForm에 포함시키는 것도 불가능합니다.
****
###BooleanField
####class BooleanField(**options)
True/False 필드.
이 필드의 기본 양식 위젯은 CheckboxInput입니다.
null 값을 받아 들일 필요가 있다면 대신 NullBooleanField를 사용하십시오.
Field.default가 정의되어 있지 않으면 BooleanField의 기본값은 None입니다.
****
###CharField
####class CharField(max_length=None, **options)
작은 ~ 큰 크기의 문자열을 나타내는 문자열 필드입니다.
많은 양의 텍스트의 경우 TextField를 사용하십시오.
이 필드의 기본 양식 위젯은 TextInput입니다.
CharField에는 하나의 추가 필수 인수가 있습니다.
####CharField.max_length
필드의 최대 길이 (문자 수)입니다. max_length는 데이터베이스 레벨과 Django의 유효성 검사에서 적용됩니다.
****
###CommaSeparatedIntegerField
####class CommaSeparatedIntegerField(max_length=None, **options)
콤마로 구분 된 정수 필드. CharField와 마찬가지로 max_length 인수가 필요하며 여기에 언급된 데이터베이스 이식성에 대한 참고 사항을 주의해야합니다.
****
###DateField
####class DateField(auto_now=False, auto_now_add=False, **options)
Python에서 datetime.date 인스턴스로 표현되는 날짜입니다. 추가로 몇 가지 선택적 인수가 있습니다.
####DateField.auto_now
개체가 저장 될 때마다 지금 필드를 자동으로 설정합니다. "마지막으로 수정 된"타임 스탬프에 유용합니다. 현재 날짜는 항상 사용됩니다. 재정의 할 수 있는 기본값이 아닙니다.
이 필드는 Model.save ()를 호출 할 때 자동으로 업데이트됩니다. 이 필드는 QuerySet.update ()와 같은 다른 방법으로 다른 필드를 업데이트 할 때 업데이트되지 않지만 이와 같은 업데이트에서 필드의 사용자 지정 값을 지정할 수 있습니다.
####DateField.auto_now_add
객체가 처음 생성 될 때 자동으로 필드를 지금으로 설정합니다. 타임 스탬프 생성에 유용합니다. 현재 날짜는 항상 사용됩니다. 정의 할 수있는 기본값이 아닙니다. 따라서 객체를 만들 때 이 필드의 값을 설정하더라도 무시됩니다. 이 필드를 수정하려면 auto_now_add = True 대신 다음을 설정하십시오.

* For DateField: default=date.today - from datetime.date.today()
* For DateTimeField: default=timezone.now - from django.utils.timezone.now()

이 필드의 기본 양식 위젯은 TextInput입니다. 관리자는 JavaScript 캘린더와 "오늘"에 대한 단축키를 추가합니다. 추가 invalid_date 오류 메시지 키가 포함됩니다. 

auto_now_add, auto_now 및 default 옵션은 상호 배타적입니다. 이러한 옵션을 조합하면 오류가 발생합니다.

****
###DateTimeField
####class DateTimeField(auto_now=False, auto_now_add=False, **options)
Python에서 datetime.datetime 인스턴스로 표현되는 날짜와 시간. DateField와 동일한 추가 인수를 사용합니다.

이 필드의 기본 양식 위젯은 단일 TextInput입니다. 관리자는 JavaScript 바로 가기가있는 두 개의 별도 TextInput 위젯을 사용합니다.

****
###DecimalField
####class DecimalField(max_digits=None, decimal_places=None, **options)
고정 소수점 이하의 십진수로 파이썬에서 Decimal 인스턴스로 나타냅니다. 두 가지 필수 인수가 있습니다.
####DecimalField.max_digits
숫자에 허용되는 최대 자릿수입니다. 이 수는 decimal_places보다 크거나 같아야합니다.
####DecimalField.decimal_places
숫자와 함께 저장할 소수점 이하 자릿수입니다.
예를들어, 최대 999자리를 소수점 이하 2자리로 저장하려면 다음을 사용하십시오.
	
	models.DecimalField(..., max_digits=5, decimal_places=2)

소수점 이하 10 자리의 해상도로 약 10 억 개의 숫자 저장

	models.DecimalField(..., max_digits=19, decimal_places=10)

이 필드의 기본 양식 위젯은 localize가 False 일 때 NumberInput이고 그렇지 않으면 TextInput입니다.
****
###DurationField
####class DurationField(**options)
Timedelta가 Python으로 모델링 한 시간주기 저장 필드. PostgreSQL에서 사용되는 데이터 유형은 간격이며 Oracle에서는 데이터 유형이 INTERVAL DAY (9) TO SECOND (6)입니다. 그렇지 않으면 마이크로 초의 bigint가 사용됩니다.
****
###EmailField
####class EmailField(max_length=254, **options)
값이 유효한 전자 메일 주소인지 확인하는 CharField입니다. EmailValidator를 사용하여 입력의 유효성을 검사합니다.
****
###FileField
####class FileField(upload_to=None, max_length=100, **options)
파일 업로드 필드.
두 개의 선택적 인수가 있습니다.
####FileField.upload_to
이 속성은 업로드 디렉토리와 파일 이름을 설정하는 방법을 제공하며 두 가지 방법으로 설정할 수 있습니다. 두 경우 모두 값은 Storage.save () 메서드에 전달됩니다.

문자열 값을 지정하면 strftime () 형식이 포함될 수 있습니다.이 형식은 업로드 된 파일이 주어진 디렉토리를 채우지 않도록 파일 업로드 날짜 / 시간으로 대체됩니다.

	```python
	class MyModel(models.Model):
	    # file will be uploaded to MEDIA_ROOT/uploads
	    upload = models.FileField(upload_to='uploads/')
	    # or...
	    # file will be saved to MEDIA_ROOT/uploads/2015/01/30
	    upload = models.FileField(upload_to='uploads/%Y/%m/%d/')
	```
기본 FileSystemStorage를 사용하는 경우 문자열 값은 MEDIA_ROOT 경로에 추가되어 업로드 된 파일이 저장 될 로컬 파일 시스템의 위치를 ​​형성합니다. 다른 저장소를 사용하는 경우 해당 저장소의 설명서에서 업로드 처리 방법을 확인하십시오.

upload_to는 함수와 같은 호출 가능 함수 일 수도 있습니다. 이것은 파일 이름을 포함하여 업로드 경로를 얻기 위해 호출됩니다. 이 호출 가능 객체는 두 개의 인수를 받아 들여 스토리지 시스템에 전달되는 유닉스 스타일 경로 (슬래시 포함)를 반환해야합니다.


|Argument|Description|
|---|---|
|instance|FileField가 정의 된 모델의 인스턴스입니다. 보다 구체적으로, 이것은 현재 파일이 첨부되는 특정 인스턴스입니다.|
||대부분의 경우이 개체는 아직 데이터베이스에 저장되지 않았으므로 기본 자동 필드를 사용하는 경우 기본 키 필드의 값이 아직 없을 수 있습니다.|
|filename|원래 파일에 주어진 파일 이름. 최종 목적지 경로를 결정할 때 고려 될 수도 있고 고려되지 않을 수도 있습니다.|

####FileField.storage
파일의 저장 및 검색을 처리하는 저장 개체입니다. 이 객체를 제공하는 방법에 대한 자세한 내용은 파일 관리를 참조하십시오.

이 필드의 기본 양식 위젯은 ClearableFileInput입니다.

모델에서 FileField 또는 ImageField (아래 참조)를 사용하면 몇 단계가 수행됩니다.

* 설정 파일에서 Django가 업로드 된 파일을 저장할 디렉토리의 전체 경로로 MEDIA_ROOT을 정의해야합니다. (성능을 위해 이러한 파일은 데이터베이스에 저장되지 않습니다.) MEDIA_URL을 해당 디렉토리의 기본 공개 URL로 정의하십시오. 이 디렉터리에 웹 서버의 사용자 계정이 쓸 수 있는지 확인하십시오.
* 모델에 FileField 또는 ImageField를 추가하고 upload_to 옵션을 정의하여 업로드 된 파일에 사용할 MEDIA_ROOT의 하위 디렉토리를 지정합니다.
* 데이터베이스에 저장되는 것은 모두 파일에 대한 경로입니다 (MEDIA_ROOT에 상대적 임). Django가 제공하는 편의 url 속성을 사용하고 싶을 것입니다. 예를 들어 ImageField의 이름이 mug_shot 인 경우 {{object.mug_shot.url}} 템플릿을 사용하여 이미지의 절대 경로를 가져올 수 있습니다.

예를 들어 MEDIA_ROOT이 '/ home / media'로 설정되고 upload_to가 'photos / % Y / % m / % d'로 설정되었다고 가정합니다. upload_to의 '% Y / % m / % d'부분은 strftime () 형식.
'% Y'는 네 자리 숫자 연도이고 '% m'은 두 자리 숫자 월이고 '% d'은 두 자리 날짜입니다. 2007 년 1 월 15 일에 파일을 업로드하면 / home / media / photos / 2007 / 01 / 15 디렉토리에 저장.

업로드 된 파일의 디스크상의 파일 이름이나 파일 크기를 검색하려면 name 및 size 속성을 각각 사용할 수 있습니다. 사용 가능한 속성 및 메서드에 대한 자세한 내용은 File 클래스 참조 및 파일 관리 항목 가이드를 참조하십시오.

업로드 된 파일의 상대 URL은 url 속성을 사용하여 얻을 수 있습니다. 내부적으로 이것은 기본 Storage 클래스의 url () 메서드를 호출합니다.

업로드 된 파일을 다룰 때마다 업로드 할 파일과 파일의 유형에주의를 기울여 보안상의 위험을 피하십시오. 업로드 된 모든 파일의 유효성을 검사하여 파일이 자신이 생각하는 것임을 확신합니다. 예를 들어, 누군가 맹목적으로 검증없이 파일을 웹 서버의 문서 루트에있는 디렉토리에 업로드하도록 허락한다면 누군가가 CGI 또는 PHP 스크립트를 업로드하고 사이트의 URL을 방문하여 해당 스크립트를 실행할 수 있습니다. 그것을 허용하지 마십시오.

또한 업로드 된 HTML 파일은 브라우저가 (서버가 아니더라도) 실행할 수 있기 때문에 XSS 또는 CSRF 공격과 동일한 보안 위협을 제기 할 수 있습니다.

FileField 인스턴스는 기본 최대 길이가 100자인 varchar 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 max_length 인수를 사용하여 최대 길이를 변경할 수 있습니다.

###FileField and FieldFile
####class FieldFile
모델에서 FileField에 액세스하면 FieldFile 인스턴스가 기본 파일에 액세스하기위한 프록시로 제공됩니다.

FieldFile의 API는 File의 API와 매우 다른 점이 하나 있습니다. 클래스에 의해 래핑 된 객체는 반드시 파이썬의 내장 파일 객체를 감싸는 래퍼일 필요는 없습니다. 대신 Storage.open () 메서드의 결과를 둘러싼 래퍼입니다.이 메서드는 File 객체 일 수도 있고 File API의 사용자 정의 저장소 구현 일 수도 있습니다. read () 및 write ()와 같이 File에서 상속 된 API 외에도 FieldFile에는 기본 파일과 상호 작용하는 데 사용할 수있는 몇 가지 메서드가 포함되어 있습니다.
####FielfFile.name
관련 FileField의 저장소 루트에서 상대 경로를 포함한 파일의 이름입니다.
####FieldFile.size
기본 Storage.size () 메서드의 결과입니다.
####FieldFile.url
기본 Storage 클래스의 url () 메서드를 호출하여 파일의 상대 URL에 액세스하는 읽기 전용 속성입니다.
####FieldFile.open(mode='rb')
지정된 모드에서이 인스턴스와 관련된 파일을 열거 나 다시 엽니다. 표준 파이썬 open () 메소드와는 달리, 파일 디스크립터를 리턴하지 않습니다.

기본 파일이 액세스 할 때 암시 적으로 열리므로 기본 파일에 포인터를 재설정하거나 모드를 변경하는 경우를 제외하고는이 메서드를 호출 할 필요가 없습니다.
####FieldFile.close()
표준 파이썬 file.close () 메소드와 유사하게 동작하고 이 인스턴스와 관련된 파일을 닫습니다.
####FieldFile.save(name, content, save=True)
이 메서드는 파일 이름과 파일 내용을 가져 와서 필드의 저장소 클래스에 전달한 다음 저장된 파일을 모델 필드와 연결합니다. 모델의 FileField 인스턴스에 파일 데이터를 수동으로 연결하려면 save() 메서드를 사용하여 해당 파일 데이터를 유지합니다.

두 개의 필수 인수를 취합니다 : name은 파일의 이름이고 content는 파일 내용을 포함하는 객체입니다. 선택적 save 인수는이 필드와 연관된 파일이 변경된 후에 모델 인스턴스가 저장되는지 여부를 제어합니다. 기본값은 True입니다.

content 인수는 Python의 내장 파일 객체가 아닌 django.core.files.File의 인스턴스 여야합니다. 다음과 같이 기존 Python 파일 객체에서 File을 생성 할 수 있습니다 .

```python
	from django.core.files import File
	# Open an existing file using Python's built-in open()
	f = open('/path/to/hello.world')
	myfile = File(f)
```
또는 다음과 같이 파이썬 문자열로 만들수 있습니다.

```python
	from django.core.files.base import ContentFile
	myfile = ContentFile("hello world")
```
####FieldFile.delete(save=True)
이 인스턴스와 관련된 파일을 삭제하고 필드의 모든 특성을 지 웁니다. 참고 :이 메서드는 delete ()가 호출 될 때 열리면 파일을 닫습니다.

선택적 save 인수는이 필드와 연관된 파일이 삭제 된 후에 모델 인스턴스가 저장되는지 여부를 제어합니다. 기본값은 True입니다.

모델을 삭제하면 관련 파일이 삭제되지 않습니다. 고아 파일을 정리해야하는 경우 직접 처리해야합니다.
****
###FilePathField
####class FilePathField(path=None, match=None, recursive=False, max_length=100, **options)
CharField는 파일 시스템의 특정 디렉토리에있는 파일 이름으로 제한됩니다. 세가지의 특수한 요소가 있고 그 중 첫번째 요소가 필요합니다.
####FilePathField.path
필수 사항. 이 FilePathField가 선택해야하는 디렉토리에 대한 절대 파일 시스템 경로. 예 : "/ home / images"
####FilePathField.match
선택요소. FilePathField가 파일 이름을 필터링하는데 사용할 정규 표현식입니다 (문자열). 정규 표현식은 전체 경로가 아닌 기본 파일 이름. 예 : "foo. * \. txt $". foo23.txt이지만 bar.txt 또는 foo23.png 파일은 일치하지 않습니다.
####FilePathField.recursive
선택요소. True 또는 False. 기본값은 False입니다. path의 모든 하위 디렉토리가 포함되어야하는지 여부를 지정합니다.
####FilePathField.allow_files
선택요소. True 또는 False. 기본값은 True입니다. 지정된 위치의 파일을 포함할지 여부를 지정합니다. this 또는 allow_folders는 True 여야합니다.
####FilePathField.allow_folders
선택요소. True 또는 False. 기본값은 False. 지정된 위치의 폴더를 포함할지 여부를 지정합니다. this 또는 allow_files는 True 여야합니다.

물론 이러한 인수를 함께 사용할 수 있습니다.

하나의 잠재적 인 문제는 전체 경로가 아닌 기본 파일 이름에 일치가 적용된다는 것입니다.

	```python
	FilePathField(path="/home/images", match="foo.*", recursive=True)
	```
/home/images/foo.png는 일치하지만 /home/images/foo/bar.png는 일치하지 않습니다. 기본 파일 이름 (foo.png 및 bar.png)에 적용되기 때문입니다.

FilePathField 인스턴스는 기본 최대 길이가 100자인 varchar 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 max_length 인수를 사용하여 최대 길이를 변경할 수 있습니다.

****
###FloatField
####class FloatField(**options)
float 인스턴스에 의해 Python으로 표현 된 부동 소수점 숫자.

이 필드의 기본 양식 위젯은 localize가 False 일 때 NumberInput이고 그렇지 않으면 TextInput입니다.

	FloatField vs. DecimalField
	FloatField 클래스는 때때로 DecimalField 클래스와 혼합됩니다. 둘 다 실수를 나타내지 만 그 수를 다르게 나타냅니다. FloatField는 내부적으로 Python의 float 유형을 사용하고 DecimalField는 Python의 Decimal 유형을 사용합니다. 두 함수의 차이점에 대한 정보는 십진수 모듈에 대한 Python 문서를 참조하십시오.

****
###ImageField
####class ImageField(upload_to=None, height_feild=None, width_field=None, max_length=100,**options)
FileField의 모든 특성과 메서드를 상속하지만 업로드 된 개체가 유효한 이미지인지 확인합니다.

FileField에서 사용할 수있는 특수 특성 외에도 ImageField에는 height 및 width 특성이 있습니다.

이러한 속성에 대한 쿼리를 용이하게하기 위해 ImageField에는 두 개의 추가 선택적 인수가 있습니다.

####ImagesField.height_field
모델 인스턴스가 저장 될 때마다 이미지 높이로 자동 채워지는 모델 필드의 이름입니다.
####ImageField.width_field
모델 인스턴스가 저장 될 때마다 이미지 너비가 자동으로 채워지는 모델 필드의 이름입니다.

ImageField 인스턴스는 기본 최대 길이가 100자인 varchar 열로 데이터베이스에 만들어집니다. 다른 필드와 마찬가지로 max_length 인수를 사용하여 최대 길이를 변경할 수 있습니다.

이 필드의 기본 양식 위젯은 ClearableFileInput입니다.

****
###IntegerField
####class IntegerField(**options)
정수. -2147483648에서 2147483647까지의 값은 장고가 지원하는 모든 데이터베이스에서 안전합니다. 이 필드의 기본 양식 위젯은 localize가 False 일 때 NumberInput이고 그렇지 않으면 TextInput입니다.

IPv6 주소 정규화는 RFC 4291 # section-2.2 섹션 2.2를 따르며, 그 섹션의 3 단락에서 제안 된 IPv4 포맷 (예 : ffff : 192.0.2.0)을 사용합니다. 예를 들어 2001 : 0 :: 0 : 01은 2001 :: 1로 표준화되고 :: ffff : 0a0a : 0a0a는 :: ffff : 10.10.10.10으로 정규화됩니다. 모든 문자는 소문자로 변환됩니다.
####GenericIPAddressField.protocol
지정된 프로토콜에 대한 유효한 입력을 제한합니다. 허용되는 값은 'both'(기본값), 'IPv4'또는 'IPv6'입니다. 일치는 대소 문자를 구분하지 않습니다.
####GenericIPAddressField.unpack_ipv4
:: ffff : 192.0.2.1과 같은 IPv4 매핑 된 주소의 압축을 풉니 다. 이 옵션을 사용하면 주소가 192.0.2.1로 압축 해제됩니다. 기본값은 사용하지 않습니다. 프로토콜이 'both'로 설정된 경우에만 사용할 수 있습니다.
****
###NullBooleanField
####class NullBooleanField(**options)
BooleanField와 같으나 옵션 중 하나로 NULL을 허용합니다. BooleanField 대신 null = True를 사용하십시오. 이 필드의 기본 양식 위젯은 NullBooleanSelect입니다.
****
###PositiveIntegerField
####class PositiveIntegerField(**options)
IntegerField와 같지만 양수 또는 0이어야합니다. 0에서 2147483647 사이의 값은 장고가 지원하는 모든 데이터베이스에서 안전합니다. 이전 버전과의 호환성을 위해 0 값이 허용됩니다.
****
###PositiveSmallIntegerField
####class PositiveSmallIntegerField(**options)
PositiveIntegerField와 같지만 특정 (데이터베이스 종속적 인) 지점에서만 값을 허용합니다. 0에서 32767 사이의 값은 장고가 지원하는 모든 데이터베이스에서 안전합니다.
****
###SlugField
####class SlugField(max_length=50, **options)
Slug는 신문 용어입니다. slug는 글자, 숫자, 밑줄 또는 하이픈 만 포함하는 짧은 레이블입니다. 일반적으로 URL에 사용됩니다.

CharField와 마찬가지로 max_length를 지정할 수 있습니다 (이 절에서 데이터베이스 이식성 및 max_length에 대한 참고 사항도 참조하십시오). max_length를 지정하지 않으면 Django는 기본 길이 인 50을 사용합니다.

Field.db_index True로 설정하는 것을 의미.

다른 값의 값을 기반으로 SlugField를 자동으로 미리 채우는 것이 유용한 경우가 많습니다. prepopulated_fields를 사용하여 관리자가 자동으로이 작업을 수행 할 수 있습니다.
####SlugField.allow_unicode
True이면 필드에 ASCII 문자 이외의 유니 코드 문자도 사용할 수 있습니다. 기본값은 False입니다.
****
###SmallIntegerField
####class SmallIntegerField(**options)
IntegerField와 같지만 특정 (데이터베이스 종속적 인) 지점에서만 값을 허용합니다. -32768에서 32767 사이의 값은 장고가 지원하는 모든 데이터베이스에서 안전합니다.
****
###TextField
####class TextField(**options)
큰 텍스트 필드. 이 필드의 기본 양식 위젯은 Textarea입니다.

max_length 속성을 지정하면 자동 생성 양식 필드의 Textarea 위젯에 반영됩니다. 그러나 모델 또는 데이터베이스 수준에서는 적용되지 않습니다. CharField를 사용하십시오.

****
###TimeField
####class TimeField(auto_now=False, auto_now_add=False, **options)
Python에서 datetime.time 인스턴스로 표현되는 시간입니다. DateField와 동일한 자동 채우기 옵션을 적용합니다.

이 필드의 기본 양식 위젯은 TextInput입니다. 관리자는 몇 가지 JavaScript 바로 가기를 추가.
****
###URLField
####class URLField(max_length=200, **options)
URL의 CharField입니다.

이 필드의 기본 양식 위젯은 TextInput입니다.

모든 CharField 서브 클래스와 같이 URLField는 선택적 max_length 인수를 취합니다. max_length를 지정하지 않으면 기본값 200이 사용됩니다.
****
###UUIDField
####class UUIDField(**options)
보편적으로 유일한 식별자를 저장하기위한 필드. 파이썬의 UUID 클래스를 사용합니다. PostgreSQL에서 사용되면 uuid 데이터 유형에 저장되고, 그렇지 않으면 char (32)에 저장됩니다.

범용 고유 식별자는 primary_key에 대한 AutoField 대신 사용할 수있는 좋은 방법입니다. 데이터베이스에서 UUID를 생성하지 않으므로 기본값을 사용하는 것이 좋습니다.

```python
	import uuid
	from django.db import models

	class MyUUIDModel(models.Model):
	    id = models.UUIDField(primary_key=True, default=uuid.uuid4, editable=False)
	    # other fields
```
UUID 인스턴스가 아니라 호출 가능 객체 (괄호 생략)가 기본값으로 전달됩니다.
****
###Relationship fields
Django는 또한 관계를 나타내는 일련의 필드를 정의합니다.
###ForeignKey
####class ForeignKey(othermodel, on_delete, **options)
다대일 관계. 위치 인수가 필요합니다. 모델이 관련 지을 수 있고있는 클래스입니다.

재귀 관계 (자체와 다 대일 관계가있는 객체)를 만들려면 models.ForeignKey ( 'self', on_delete = models.CASCADE)를 사용합니다.

아직 정의되지 않은 모델에서 관계를 작성해야하는 경우 모델 오브젝트 자체가 아닌 모델 이름을 사용할 수 있습니다.

```python
	from django.db import models

	class Car(models.Model):
	    manufacturer = models.ForeignKey(
		'Manufacturer',
		on_delete=models.CASCADE,
	    )
	    # ...

	class Manufacturer(models.Model):
	    # ...
	    pass
```

이 방식으로 추상 모델에 정의 된 관계는 모델이 구체적인 모델로 서브 클래 싱되고 추상 모델의 app_label과 관련이없는 경우 해결됩니다.

	products/models.py
```python
	from django.db import models

	class AbstractCar(models.Model):
	    manufacturer = models.ForeignKey('Manufacturer', on_delete=models.CASCADE)

	    class Meta:
		abstract = True
```

```python
	from django.db import models
	from products.models import AbstractCar

	class Manufacturer(models.Model):
	    pass

	class Car(AbstractCar):
	    pass

```
다른 응용 프로그램에 정의 된 모델을 참조하려면 전체 응용 프로그램 레이블로 모델을 명시 적으로 지정할 수 있습니다. 예를 들어 위의 제조업체 모델이 생산이라는 다른 애플리케이션에 정의되어있는 경우 다음을 사용해야합니다.
```python
	class Car(models.Model):
	    manufacturer = models.ForeignKey(
		'production.Manufacturer',
		on_delete=models.CASCADE,
	    )
```
이러한 종류의 참조는 두 응용 프로그램 간의 순환 가져 오기 종속성을 해결할 때 유용 할 수 있습니다.

데이터베이스 인덱스는 ForeignKey에 자동으로 작성됩니다. db_index를 False로 설정하여이 기능을 비활성화 할 수 있습니다. 조인이 아닌 일관성을 위해 외래 키를 작성하거나 부분 또는 다중 컬럼 색인과 같은 대체 색인을 작성할 경우 색인의 오버 헤드를 피할 수 있습니다.

####데이터베이스 표현
Django는 "_id"를 필드 이름에 추가하여 데이터베이스 열 이름을 만듭니다. 위의 예에서 Car 모델의 데이터베이스 테이블에는 manufacturer_id 열이 있습니다. (당신은 이것을 명시 적으로 변경할 수 있습니다. db_column 지정) 그러나 사용자 정의 SQL을 작성하지 않으면 코드가 데이터베이스 열 이름을 처리하지 않아도됩니다. 모델 개체의 필드 이름은 항상 처리됩니다.
****
###Arguments
ForeignKey는 관계가 작동하는 방식에 대한 세부 정보를 정의하는 다른 인수를 허용합니다.

####ForeignKey.on_delete¶
ForeignKey가 참조하는 객체가 삭제되면 Django는 on_delete 인수로 지정된 SQL 제약 조건의 동작을 에뮬레이션합니다. 예를 들어 nullable ForeignKey가 있고 참조 된 객체가 삭제 될 때 null로 설정되기를 원할 경우
	
```python
	user = models.ForeignKey(
	    User,
	    models.SET_NULL,
	    blank=True,
	    null=True,
	)
```
on_delete에 사용할 수있는 값은 django.db.models에 있습니다.

* CASCADE
	* 계단식 삭제. Django는 ON DELETE CASCADE SQL 제약 조건의 동작을 에뮬레이션하고 ForeignKey가 포함 된 객체도 삭제합니다.
* PROTECT
	* django.db.IntegrityError의 하위 클래스 인 ProtectedError를 발생시켜 참조 된 객체의 삭제를 방지합니다.
* SET_NULL
	* ForeignKey null을 설정하십시오. null가 True 인 경우에만 가능합니다.
* SET_DEFAULT
	* ForeignKey를 기본값으로 설정하십시오. ForeignKey의 기본값을 설정해야합니다.
* SET()
	* ForeignKey를 SET ()에 전달 된 값으로 설정하거나 호출 가능 객체가 전달 된 경우 호출 한 결과. 대부분의 경우 models.py를 가져올 때 쿼리를 실행하지 않으려면 호출 가능을 전달해야합니다.
```python
	from django.conf import settings
	from django.contrib.auth import get_user_model
	from django.db import models

	def get_sentinel_user():
	    return get_user_model().objects.get_or_create(username='deleted')[0]

	class MyModel(models.Model):
	    user = models.ForeignKey(
		settings.AUTH_USER_MODEL,
		on_delete=models.SET(get_sentinel_user),
	    )
```
* DO_NOTHING
	* 아무런 조치도 취하십시오. 데이터베이스 백엔드가 참조 무결성을 적용하면 데이터베이스 필드에 SQL ON DELETE 제약 조건을 수동으로 추가하지 않으면 IntegrityError가 발생합니다.


####ForeignKey.limit_choices_to
ModelForm 또는 admin을 사용하여이 필드를 렌더링 할 때이 필드에 사용할 수있는 선택 항목에 대한 제한을 설정합니다 (기본적으로 쿼리 세트의 모든 객체를 선택할 수 있음). 딕셔너리, Q 오브젝트 또는 호출 가능한 리턴 딕셔너리 또는 Q 오브젝트가 사용될 수 있습니다.
```python
staff_member = models.ForeignKey(
    User,
    on_delete=models.CASCADE,
    limit_choices_to={'is_staff': True},
)
```
ModelForm의 해당 필드가 is_staff = True 인 사용자 만 나열하도록합니다. 이것은 장고 관리자에게 도움이 될 수 있습니다.

예를 들어 Python datetime 모듈과 함께 사용하여 날짜 범위에 따라 선택을 제한하는 경우 호출 가능 형식이 유용 할 수 있습니다.
	
```python
	def limit_pub_date_choices():
	    return {'pub_date__lte': datetime.date.utcnow()}

	limit_choices_to = limit_pub_date_choices
```
limit_choices_to가 복잡한 쿼리에 유용한 Q 객체를 반환하면 모델의 ModelAdmin에서 필드가 raw_id_fields에 나열되지 않은 경우에만 관리자가 사용할 수있는 선택 항목에 영향을 미칩니다.

####ForeignKey.related_name
관련 객체에서이 객체에 대한 관계에 사용할 이름입니다. 또한 related_query_name (대상 모델의 역 필터 이름에 사용할 이름)의 기본값입니다. 전체 설명과 예제는 관련 오브젝트 문서를 참조하십시오. 추상 모델에 관계를 정의 할 때이 값을 설정해야합니다. 그렇게하면 특별한 구문을 사용할 수 있습니다.

Django가 뒤로 관계를 생성하지 않기를 원한다면, related_name을 '+'로 설정하거나 '+'로 끝내십시오. 예를 들어, 이렇게하면 User 모델이이 모델에 대한 역방향 관계를 갖지 않게됩니다.
```python
user = models.ForeignKey(
    User,
    on_delete=models.CASCADE,
    related_name='+',
)
```

####ForeignKey.related_query_name
대상 모델의 역방향 필터 이름에 사용할 이름입니다. 설정되어있는 경우 related_name 또는 default_related_name의 값이 기본값이며, 그렇지 않은 경우 기본값은 모델의 이름입니다.
```python
# Declare the ForeignKey with related_query_name
class Tag(models.Model):
    article = models.ForeignKey(
        Article,
        on_delete=models.CASCADE,
        related_name="tags",
        related_query_name="tag",
    )
    name = models.CharField(max_length=255)

# That's now the name of the reverse filter
Article.objects.filter(tag__name="important")
```
related_name과 마찬가지로 related_query_name은 일부 특수 구문을 통해 앱 레이블 및 클래스 보간을 지원합니다.
####ForeignKey.db_constraint
이 외래 키에 대해 데이터베이스에 제약 조건을 만들지 여부를 제어합니다. 기본값은 True이며, 거의 확실하게 원하는 것입니다. 이것을 False로 설정하면 데이터 무결성이 매우 나쁠 수 있습니다. 즉, 다음과 같은 시나리오가 있습니다.

****

* 유효하지 않은 기존 데이터가 있습니다.
* 당신은 당신의 데이터베이스를 파괴하고 있습니다.

이 값을 False로 설정하면 존재하지 않는 관련 객체에 액세스하면 해당 DoesNotExist 예외가 발생합니다.

####ForeignKey.swappable
이 ForeignKey가 스왑 가능 모델을 가리키는 경우 마이그레이션 프레임 워크의 반응을 제어합니다. 기본값 인 True이면 ForeignKey가 settings.AUTH_USER_MODEL (또는 다른 스왑 가능 모델 설정)의 현재 값과 일치하는 모델을 가리키면 관계가 설정에 대한 참조를 사용하여 마이그레이션에 저장됩니다. 모델을 직접적으로.

###ManyToManyField
####class ManyToManyField(othermodel, **options)
다대다 관계. 위치 지정 인수가 필요합니다. 모형이 관련된 클래스이며, 재귀 및 지연 관계를 포함하여 ForeignKey에서와 똑같이 작동합니다.
관련 객체는 필드의 RelatedManager를 사용하여 추가, 제거 또는 생성 할 수 있습니다.

####Database Representation
Django는 many-to-many 관계를 표현하기 위해 중간 조인 테이블을 생성합니다. 기본적으로이 테이블 이름은 다 대다 필드의 이름과 그 테이블을 포함하는 모델의 테이블 이름을 사용하여 생성됩니다. 
일부 데이터베이스는 특정 길이 이상의 테이블 이름을 지원하지 않으므로 이러한 테이블 이름은 자동으로 64 자로 잘리고 고유성 해시가 사용됩니다. 즉, author_books_9cdf4와 같은 테이블 이름을 볼 수 있습니다. 이것은 정상입니다. db_table 옵션을 사용하여 조인 테이블의 이름을 수동으로 제공 할 수 있습니다.
****
####Arguments
ManyToManyField는 관계 함수가 제어되는 방식을 제어하는 ​​추가 인수 세트 (모두 선택 가능)를 허용합니다.

####ManyToManyField.related_name
ForeignKey.related_name과 같다.
####ManyToManyField.related_query_name
ForeignKey.related_query_name과 같다.
####ManyToManyField.limit_choices_to
ForeignKey.limit_choices_to와 같다.

limit_choices_to는 through 매개 변수를 사용하여 지정된 사용자 지정 중간 테이블을 사용하여 ManyToManyField에서 사용될 때 아무런 영향을주지 않습니다.
####ManyToManyField.symmetrical
자체에 대한 ManyToManyFields의 정의에만 사용됩니다. 다음 모델을 고려하십시오.
```python
from django.db import models

class Person(models.Model):
    friends = models.ManyToManyField("self")
```
Django는이 모델을 처리 할 때 ManyToManyField가 있음을 식별하므로 Person 클래스에 person_set 속성을 추가하지 않습니다. 대신, ManyToManyField는 대칭이라고 가정합니다. 즉, 제가 당신의 친구라면, 당신은 내 친구입니다.

자기와의 다대다 관계에서 대칭을 원하지 않으면 대칭을 거짓으로 설정하십시오. 이렇게하면 Django가 역방향 관계에 대한 설명자를 추가하게되어 ManyToManyField 관계가 비대칭이 될 수 있습니다.

####ManyToManyField.through
Django는 다대다 관계를 관리하기위한 테이블을 자동으로 생성합니다. 그러나 중간 테이블을 수동으로 지정하려면 through 옵션을 사용하여 사용할 중간 테이블을 나타내는 Django 모델을 지정할 수 있습니다.

이 옵션의 가장 일반적인 용도는 추가 데이터를 다 대다 관계와 연관시키려는 경우입니다.

명시 적 모델을 지정하지 않은 경우, 연관을 보유하기 위해 작성된 테이블에 직접 액세스하는 데 사용할 수있는 암시 적 쓰루 모델 클래스가 여전히 존재합니다. 모델을 연결하는 세 개의 필드가 있습니다.

원본 및 대상 모델이 다른 경우 다음 필드가 생성됩니다.

* id : 관계의 기본 키.
* <containing_model> _id : ManyToManyField를 선언 한 모델의 ID입니다.
* <other_model> _id : ManyToManyField가 가리키는 모델의 ID입니다.

ManyToManyField가 동일한 모델에서 시작하여 동일한 모델을 가리키는 경우 다음 필드가 생성.

* id : 관계의 기본 키.
* from_ <model> _id : 모델 (즉, 소스 인스턴스)을 가리키는 인스턴스의 id입니다.
* to_ <model> _id : 관계가 가리키는 인스턴스의 ID (즉, 대상 모델 인스턴스).

이 클래스는 일반 모델과 같이 주어진 모델 인스턴스에 대한 관련 레코드를 쿼리하는 데 사용할 수 있습니다.

####ManyToManyField.through_fields
사용자 지정 매개 모델이 지정된 경우에만 사용됩니다. Django는 일반적으로 다대다 관계를 자동으로 설정하기 위해 사용할 중간 모델의 필드를 결정합니다. 그러나 다음 모델을 고려하십시오.
```python
from django.db import models

class Person(models.Model):
    name = models.CharField(max_length=50)

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(
        Person,
        through='Membership',
        through_fields=('group', 'person'),
    )

class Membership(models.Model):
    group = models.ForeignKey(Group, on_delete=models.CASCADE)
    person = models.ForeignKey(Person, on_delete=models.CASCADE)
    inviter = models.ForeignKey(
        Person,
        on_delete=models.CASCADE,
        related_name="membership_invites",
    )
    invite_reason = models.CharField(max_length=64)
```
Membership( 와 ) 에 두 개의 외래 키 가있어 관계가 애매 해지고 장고는 사용할 관계를 알 수 없습니다. 이 경우 위의 예제에서와 같이 Django가 사용해야하는 외래 키를 명시 적으로 지정해야합니다 .Personpersoninviterthrough_fields

through_fields2 튜플을 허용합니다 . 여기서는 정의 된 모델에 대한 외래 키의 이름 ( 이 경우)과 대상 모델에 대한 외래 키의 이름 ( 이 경우)입니다.('field1', 'field2')field1ManyToManyFieldgroupfield2person

다 대다 관계에 참여하는 모델 중 하나 (또는 ​​두 모델 모두)의 중개 모델에 둘 이상의 외래 키가있는 경우이를 지정 해야 합니다 through_fields. 이것은 또한 적용 재귀 관계 의 중간 모델을 사용하는 경우 두 개 이상의 외부 키는 모델에있다, 또는 당신은 명시 적으로 장고 사용해야하는이 지정하십시오.

중간 모델을 사용하는 재귀 관계는 항상 비대칭으로 정의됩니다. symmetrical=False 즉, "원본"과 "대상"이라는 개념이 있습니다. 이 경우 'field1'관계의 "출처"및 'field2'"목표" 로 취급됩니다 .

####ManyToManyField.db_table
다대다 데이터를 저장하기 위해 작성할 테이블의 이름. 이것이 제공되지 않으면 Django는 관계를 정의하는 모델의 테이블과 필드 자체의 이름을 기반으로 이름을 사용합니다.
####ManyToManyField.db_constraint
중개 테이블의 외래 키에 대해 데이터베이스에 제약 조건을 만들어야하는지 여부를 제어합니다. 기본값은이며 True, 거의 확실하게 원하는 것입니다. 이 값을 설정하면 False데이터 무결성이 매우 나빠질 수 있습니다. 즉, 다음과 같은 시나리오가 있습니다.

* 유효하지 않은 기존 데이터가 있습니다.
* 당신은 당신의 데이터베이스를 파괴하고 있습니다.

####ManyToManyField.swappable
ManyToManyField 스왑 가능 모델을 가리키는 경우 마이그레이션 프레임 워크의 반응을 제어합니다 . 그런 경우 True기본 - - (가) 다음 경우 ManyToManyField의 전류 값과 일치하는 모델 가리키는 settings.AUTH_USER_MODEL(또는 다른 스왑 모델 설정)의 관계를 직접적으로 모델에, 설정에 대한 참조를 사용하여 이전에 저장 될 것이다.

False모델을 항상 스왑 된 모델 (예 : 사용자 정의 사용자 모델 용으로 특별히 고안된 프로파일 모델 인 경우)을 가리켜 야한다고 확신하는 경우 에만이 설정을 무시하고 싶습니다 .

의심 스럽다면 기본값으로 두십시오 True.

ManyToManyField지원하지 않습니다 validators.

null 데이터베이스 레벨에서 관계를 요구할 방법이 없기 때문에 효과가 없습니다.

****
###OneToOneField
####class OneToOneField(othermodel, on_delete, parent_link=False, **options)
일대일 관계. 개념적으로 이는 고유 = True 인 ForeignKey와 비슷하지만 관계의 "역"측은 하나의 객체를 직접 반환합니다.

이는 어떤 방법으로 다른 모델을 "확장"하는 모델의 기본 키로 가장 유용합니다. 다중 테이블 상속은 하위 모델에서 상위 모델로 암시 적 일대일 관계를 추가하여 구현됩니다.

모델이 관련 될 클래스 인 하나의 위치 인수가 필요합니다. 이는 재귀 및 지연 관계와 관련된 모든 옵션을 포함하여 ForeignKey에서와 똑같이 작동합니다.

OneToOneField에 related_name 인수를 지정하지 않으면 Django는 현재 모델의 소문자 이름을 기본값으로 사용합니다.
```python
from django.conf import settings
from django.db import models

class MySpecialUser(models.Model):
    user = models.OneToOneField(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
    )
    supervisor = models.OneToOneField(
        settings.AUTH_USER_MODEL,
        on_delete=models.CASCADE,
        related_name='supervisor_of',
    )
```
최종 사용자 모델에는 다음과 같은 속성이 있습니다.
```terminal
>>> user = User.objects.get(pk=1)
>>> hasattr(user, 'myspecialuser')
True
>>> hasattr(user, 'supervisor_of')
True
```
관련 테이블의 항목이없는 경우 역방향 관계에 액세스 할 때 DoesNotExist 예외가 발생합니다. 예를 들어 사용자에게 MySpecialUser가 지정한 관리자가없는 경우.
```
>>> user.supervisor_of
Traceback (most recent call last):
    ...
DoesNotExist: User matching query does not exist.
```
또한 OneToOneField는 ForeignKey에서 허용하는 모든 추가 인수와 하나의 추가 인수를 허용합니다.
####OneToOneField.parent_link
True이면 다른 모델을 상속 한 모델에서이 필드를 서브 클래 싱하여 일반적으로 암시 적으로 생성되는 여분의 OneToOneField가 아니라 부모 클래스에 대한 링크로 사용해야 함을 나타냅니다.
****
###Field API reference
####class Field
필드는 데이터베이스 테이블 열을 나타내는 추상 클래스입니다. Django는 데이터베이스 테이블 (db_type ())을 생성하기 위해 필드를 사용하여 Python 타입을 데이터베이스 (get_prep_value ())와 그 역 (from_db_value ())으로 매핑합니다.

따라서 필드는 다른 Django API, 특히 모델과 쿼리 세트에서 기본적인 부분입니다.

모델에서 필드는 클래스 속성으로 인스턴스화되고 특정 테이블 열을 나타냅니다 (모델 참조). 그것은 null과 unique와 같은 속성과 Django가 필드 값을 데이터베이스 특정 값으로 매핑하는 데 사용하는 메소드를 가지고 있습니다.

Field는 RegisterLookupMixin의 하위 클래스이므로 Transform 및 Lookup을 QuerySets에 사용할 수 있도록 등록 할 수 있습니다 (예 : field_name__exact = "foo"). 모든 기본 제공 조회는 기본적으로 등록됩니다.

CharField와 같은 Django의 모든 내장 필드는 Field의 특정 구현입니다. 사용자 정의 필드가 필요한 경우 기본 제공 필드를 서브 클래스로 만들거나 필드를 처음부터 작성할 수 있습니다. 두 경우 모두 사용자 정의 모델 필드 작성을 참조하십시오.

####description
