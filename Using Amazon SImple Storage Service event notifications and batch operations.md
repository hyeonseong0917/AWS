## Using Amazon Simple Storage Service event notifications and batch operations



### 목표

- Amazon S3 배치 작업을 구현하여 S3객체에 대한 대규모 작업 수행
- 이벤트 기반 워크로드를 자동화하도록 Amazon S3 이벤트 알림 구성



### 개요

- Amazon S3 이벤트 알림 및 S3 배치 작업을 사용하여 S3스토리지 관리

- 대규모 객체 태그 설정
- S3 이벤트 알림 및 AWS Lambda를 사용하여 S3이벤트를 처리하도록 APP 설계



### 작업 1 : S3 배치 작업 생성

S3객체에 태그를 추가하는 배치 작업 생성



1 . images로 시작하는 버킷에서 image1.png 객체의 속성 에서 태그가 없음을 확인

2 . Batch Operations에서 작업 생성

3 .  assets 버킷을 manifest 객체로 선택, manifest.csv 파일을 경로로 선택

4 . 모든 개체 태그 교체 section에서 Key : phototype, Value : finished 입력

5 . S3BatchPutRole IAM 역할 선택 후 작업생성

{    "Version": "2012-10-17",    "Statement": [        {            "Action": [                "s3:PutObjectTagging",                "s3:PutObjectVersionTagging"            ],            "Resource": [                "arn:aws:s3:::images-82834469/*"            ],            "Effect": "Allow"        },        {            "Action": [                "s3:GetObject",                "s3:GetObjectVersion",                "s3:GetBucketLocation"            ],            "Resource": [                "arn:aws:s3:::assets-82834469/*"            ],            "Effect": "Allow"        },        {            "Action": [                "s3:PutObject",                "s3:GetBucketLocation"            ],            "Resource": [                "arn:aws:s3:::assets-82834469/*"            ],            "Effect": "Allow"        }    ] }

- 이미지 버킷의 객체에 태그 및 버전 태그 추가
- 객체 , 객체 버전 및 버킷 위치를 가져오고 버킷에 객체 업로드



### 작업 2 : S3 Batch Operation 작업 실행 및 작업 완료 확인

1 . 작업 ID 링크 선택하고 작업을 실행

2 . job으로 시작하는 폴더링크 선택

3 . result / 폴더링크 선택

4 . 이미지 버킷의 객체목록과 성공 메시지가 포함된 csv파일 확인

5 . images1.png의 속성 탭에 태그 섹션과 배치 운영 작업시 입력한 값 추가



### 작업 3 : AWS Lambda 함수 생성

1 . assets 버킷에서 function.zip의 S3 URL 복사

2 . IAM role을 S3LambdaRole로 하여 함수 생성

(IAM role)

{    "Version": "2012-10-17",    "Statement": [        {            "Action": [                "logs:PutLogEvents",                "logs:CreateLogGroup",                "logs:CreateLogStream"            ],            "Resource": [                "arn:aws:logs:*:*:*"            ],            "Effect": "Allow"        },        {            "Action": [                "s3:GetObject"            ],            "Resource": [                "arn:aws:s3:::images-82834469/*"            ],            "Effect": "Allow"        },        {            "Action": [                "s3:PutObject"            ],            "Resource": [                "arn:aws:s3:::images-82834469-resized/*"            ],            "Effect": "Allow"        }    ] }



- CloudWatch 이벤트, 로그 그룹 및 로그 스트림 생성
- 이미지 버킷에서 객체 다운로드
- 버킷에 객체 업로드



3 . 코드 소스 섹션에서 1의 S3 URL위치를 선택하여 업로드

4 . ImageResizeFunction 생성



### 작업 4 : AWS Lambda 함수를 호출하는 Amazon S3 이벤트 알림 생성

1 . images버킷의 속성탭에서 

일반 구성 - 이벤트 이름

이벤트 유형 - 모든 객체가 이벤트 생성

destination - Lambda Function

람다 기능 - 람다 함수

람다 함수 선택 - ImageResizeFunction 



=> Lambda 함수를 호출하기 위한 S3 이벤트알림 생성



### 작업 5 : 이미지를 업로드하여 버킷 알림 및 Lambda 함수 호출

1 . images 버킷에 image5.png 업로드

2 . images-resized 버킷에 resized표시된 object 업로드 확인

3 . ImageResizeFunction 함수의 모니터 탭에서 Invocations 그래프가 1을 나타내는지 확인

4 . Error count and success rate graph 에서 success rate가 100인지 확인



## Conclusion

- Amazon S3 객체에서 대규모 작업을 수행하기 위해 S3 Batch 작업을 구현함
- 이벤트 기반 워크로드를 자동화하도록 구성된 Amazon S3 이벤트 알림 구현

​	









