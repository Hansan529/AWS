# AWS

도메인 호스팅에 관련한 문서

<details>
<summary>Route53</summary>

## Route53 서비스 페이지에서 호스팅 영역을 추가한다.

<img width="1200" alt="001" src="https://github.com/Hansan529/AWS/assets/115819770/51c54a28-328f-445a-8e3d-fb591a3cd8ed" />

<img width="845" alt="002" src="https://github.com/Hansan529/AWS/assets/115819770/b1e7bf2b-ea93-4e48-97a4-c967769a687b" />  

사용하고자 하는 도메인의 주소와 일치시켜야 한다.

</details>

<br />

<details>
<summary>S3</summary>

## S3 서비스 페이지에서 버킷을 만들기

<img width="815" alt="003" src="https://github.com/Hansan529/AWS/assets/115819770/98392d13-8d48-4969-a370-885d8bcb926b" />

버킷 이름도 마찬가지로 도메인의 주소와 일치해야 한다.

<img width="819" alt="004" src="https://github.com/Hansan529/AWS/assets/115819770/a90452c8-764d-47fa-b401-75b1bdc6a0c8" />

기본값은 체크가 되어 있는데, 체크를 해제해준다.

생성 완료 후, 해당 버킷에 접속해 **속성** 탭에서 아래로 스크롤 후, 정적 웹 사이트 호스팅을 활성화 시켜준다.

<img width="831" alt="005" src="https://github.com/Hansan529/AWS/assets/115819770/0e7091fc-b812-4141-bbba-c1657fd5599d" />

별도의 오류 문서가 없다면, 인덱스 문서와 동일하게 index.html로 설정 해주었다.

다음으로, **권한** 탭에서 버킷 정책을 설정한다.

버킷 정책 편집에서 편집을 통해 버킷 ARN을 복사한 뒤

[버킷 정책 생성 사이트](https://awspolicygen.s3.amazonaws.com/policygen.html) 를 통해 쉽게 생성 가능하다.

Action은 GetObject로 지정하면 된다.

<img width="850" alt="005_1" src="https://github.com/Hansan529/AWS/assets/115819770/b5be989f-78b1-4e5d-82aa-bc491344e6dd" />

    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "PublicReadGetObject",
                "Effect": "Allow",
                "Principal": "*",
                "Action": "s3:GetObject",
                "Resource": "arn:aws:s3:::{도메인 주소}/*"
            }
        ]
    }

도메인 주소를 수정하면 된다.

Route53의 호스팅 영역에서 레코드를 생성하여

<img width="1102" alt="006" src="https://github.com/Hansan529/AWS/assets/115819770/fb49f4a1-0a5e-493f-83c9-a754624b5719" />

- 레코드 유형 "A - IPv4 주소 및 일부 AWS 리소스로 트래픽 라우팅"
- 별칭 Toggle
- 트래픽 라우팅 대상 "S3 웹 사이트 엔드포인트에 대한 별칭"
- 버킷 생성한 리전 선택
- 버킷 선택

생성 후, S3 버킷에서 객체를 업로드하면 해당 도메인에 업로드한 객체를 볼 수 있다.

</details>

<br />

<details>
<summary>Lambda</summary>

## Lambda 함수

사용자 지정 함수 이름을 설정하주고, 함수를 생성한다.

<img width="1636" alt="007" src="https://github.com/Hansan529/AWS/assets/115819770/70e6d86a-3eda-4faa-8122-0480e6070d96" />

모니터링에서 `CloudWatch Logs 보기`가 있는데, 해당 로그 스트림에서 실행 로그를 볼 수 있다.

코드를 원하는대로 수정한 후 API Gateway 작업을 진행한다.

</details>

<br />

<details>
<summary>API Gateway</summary>

## API Gateway

<img width="834" alt="008" src="https://github.com/Hansan529/AWS/assets/115819770/80387c0e-e1e7-4cc3-b274-5d2e180da46e" />

Rest API를 생성할 것이다.

새 API와 API 이름을 설정해주고, API 생성 해준다.

그 후에 메서드 생성을 한다.

<img width="836" alt="009" src="https://github.com/Hansan529/AWS/assets/115819770/3e2a196a-ebf9-48f0-a7ca-228890c69672" />

Lambda를 사용 할 것이니까, Lambda 함수를 택하고, Lambda 프록시 통합을 체크한다.

그리고 Lambda 함수를 생성한 리전을 선택 후, 추가한 뒤 메서드 생성을 한다.

메인 페이지에서 API 배포를 클릭해 스테이지를 원하는 영문 이름으로 생성한다.

<img width="1200" alt="010" src="https://github.com/Hansan529/AWS/assets/115819770/258eedca-3519-40b3-8aa1-fed1480e20eb" />

다음과 같이 스테이지 생성이 완료되었다.

### API 키

<img width="1248" alt="011" src="https://github.com/Hansan529/AWS/assets/115819770/684a6932-9df3-473f-a785-f6312477bc66" />

API 키 생성을 해준 뒤, 해당 API를 컨트롤하기 위한 "사용량 계획" 을 생성해준다.

초당 최대 요청, 한번에 요청 횟수, 요청 할당량을 설정 할 수 있다.

이를 스테이지와 API 키와 연결해주면, 사용량 계획 설정이 마무리된다.

<img width="2039" alt="012" src="https://github.com/Hansan529/AWS/assets/115819770/8a115998-118a-4a9e-8d70-c371f95bbcf6" />

API 키를 요구하게 하여, POST 요청에서 x-api-key 헤더가 없을 경우 결과를 return 하지 않도록 할 수 있다.

</details>

<br />

<details>
<summary>ColudFront + HTTPS</summary>

## CloudFront를 통해 https 인증서 작업이 가능함

<!-- Lambda Function URL은 커스텀 도메인 맵핑이 불가능하며, WAF, 인증 서비스가 없음
하지만 CloudFront로 해결 가능하다.

**API Gateway REST API 보다 호출 비용이 저렴함**!!
APi Gateway REST API $3.5/million vs Cloudfront HTTPS $1.2/million -->

ACM 인증서는 `버지니아 북부` 리전에서만 생성한다. CloudFront가 버지니아 북부에서 사용되기 때문

### 인증서 발급

Certificate Manager에서 인증서 요청

<img width="1200" alt="013" src="https://github.com/Hansan529/AWS/assets/115819770/a6ebb591-0b96-454d-86ad-d89ebb93c07f" />

Route53에 호스팅한 도메인과, 와일드카드를 포함해 도메인을 추가

생성한 인증서에서 `Route 53에서 레코드 생성`, 그 후 해당 인증서 접속 후, **Route 53에서 레코드 생성** 추가 한 뒤 상태가 발급됨으로 변경될 때 까지 기다린다.

버킷에서 정적 웹 사이트 호스팅 엔드포인트 주소를 복사한 뒤에 CloudFront 서비스에서

**배포 생성**

<img width="838" alt="013_1" src="https://github.com/Hansan529/AWS/assets/115819770/2e793ef1-e9e9-48c6-a091-f0566a47a6f4" />

Origin domain 영역에 붙여넣기한다.

그 후, 기본 캐시 동작에서 `뷰어 프로토콜 정책: Redirect HTTP to HTTPS`,  
캐시 키 및 원본 요청에서 Legacy cache settings, 웹 애플리케이션 방화벽(WAF) 비활성화

설정: 대체 도메인 이름에 주소 추가, Custom SSL certificate에서 인증서를 선택한 뒤, 배포 생성을 해준다.

시간이 지난 후, 배포가 완료되면 기존 S3로 연결되는 엔드포인트를 Cloudfront로 변경해주는 작업을 진행 하는 작업이 남아있다.

<img width="372" alt="013_2" src="https://github.com/Hansan529/AWS/assets/115819770/64f420c5-517a-4b2c-bbf8-55a6868fea7a" />

변경 완료하면 HTTPS 작업 완료, 적용이 될 때 까지 시간이 걸릴 수 있음

</details>

<br />

## API Gateway 사용자 지정 도메인 이름 설정

<img width="1213" alt="014" src="https://github.com/Hansan529/AWS/assets/115819770/123548ba-f32a-4284-885e-d20e45cfba08" />

기존 API 게이트웨이에서 URL은 AWS에서 설정한 임의의 경로로 되어 있는데, 이를 커스텀 도메인으로 변경하기 위한 작업을 할 것이다.

<img width="1190" alt="015" src="https://github.com/Hansan529/AWS/assets/115819770/c406d86e-5800-4b40-a419-69b5c237ee4d" />

사용자 지정 도메인 이름을 작성하고, ACM 인증서를 적용 한 다음 도메인을 생성한다.

<img width="1200" alt="016" src="https://github.com/Hansan529/AWS/assets/115819770/51cd9cb7-e791-4891-90a1-0ab8b765ae78" />

API 매핑에서 API, 스테이지를 지정해준다.

API Gateway 도메인 이름을 Route53 호스팅 영역에서 해당 도메인에 레코드를 생성한다.

<img width="1200" alt="017" src="https://github.com/Hansan529/AWS/assets/115819770/75712906-6a08-4af6-9d0f-029a7bd6f45f" />