## Application Service > Cheating Detection > API 가이드
- Cheating Detection API를 사용하는 데 필요한 API를 설명합니다.
## API 공통 정보

### 사전준비
- API 사용을 위해서는 프로젝트 통합 Appkey 또는 서비스 Appkey가 필요합니다.
- 서비스 Appkey는 콘솔 상단 URL & Appkey 메뉴에서 확인이 가능합니다.

### 요청 공통 정보
- API를 사용하기 위해서는 보안 키 인증 처리가 필요합니다.

[API 도메인]

 | 환경 | 도메인 |
 | --- | --- |
 | Real | https://cheating-detection.api.nhncloudservice.com |

## 인증 API

### 접근 토큰(token) 발급 API

- 부정행위 감지 API 요청 시 필요한 접근 토큰 발급 API

```
URL: /auth/token?appKey={appkey}&expiresIn={expiresIn}
METHOD: POST
Content-type: application/json;charset=utf-8
```

##### 요청

[URL Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| expiresIn | Integer | 토큰 유효 시간(초) <br> 0 < expiresIn <= 7200 | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| userId | String | 사용자 ID(수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| deviceType | String | 장비 구분(pc: PC, mo: Mobile) | O |
| webAuth | JSON | WebAuth 인증 데이터 | O |
| webAuth.userId | String | 사용자 ID(수험생 번호) | O |
| webAuth.token | String | **<span style="color:#e11d21">고객사에서 발급한 WebAuth 인증 토큰** | O |
| webAuth.via | String | 기타 정보 | X |

[요청 본문 예]

``` json
{
    "userId": "user123",
    "examNo": "21342",
    "deviceType": "pc",
    "webAuth": {
        "userId": "user123",
        "token": "ADs3Fsdfasdfnv23fkja34FX=",
        "via": ""
    }
}
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0: 성공, 이외: 실패) |
| header.resultMessage | String | 요청 결과 메시지 |
| data.tokenType | String | 토큰 타입, bearer로 고정 |
| data.accessToken | String | 사용자 접근 토큰 |
| data.expiresIn | Integer | 접근 토큰 만료 시간(초) |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "tokenType": "bearer",
        "accessToken": "DfA3f3da3/34SF+edf6898D2343fsasdf3f=",
        "expireIn": 600
    }
}
```

### 접근 토큰 취소 API

- 발급받은 접근 토큰 취소\(강제 만료를 위한\) API

``` yaml
URL: /auth/revoke
METHOD: POST
Content-type: application/json;charset=utf-8
```

##### 요청

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| accessToken | String | 취소할 접근 토큰 | O |

[요청 본문 예]
``` json
{
   "accessToken": "XDadhaS3dvns34Fdfnf23=="
}
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0: 성공, 이외: 실패) |
| header.resultMessage | String | 요청 결과 메시지 |
| data.accessToken | String | 취소한 사용자 접근 토큰 |

[응답 본문 예]

``` JSON
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "accessToken": "XDadhaS3dvns34Fdfnf23=="
    }
}
```

## 부정행위 감지 요청 API

### 행동 감지 요청 API
- 행동 감지 분석을 위한 요청 API
```
URL: /nhn-behavior-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?camLocation={camLocation}&reqTime={reqTime}
METHOD: POST
X-Auth-Token: Bearer {accessToken}
Content-type: multipart/form-data
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-Auth-Token | String | AccessToken | O |

[URL Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| camLocation | String | 카메라 위치 정보(side(측면), front(정면)) | O |
| reqTime | long | 요청 시간(timestamp 10자리)(초 단위까지) | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| file | Binary | 이미지 파일 <br>이미지 권장 사항 <br> side(Size: 640 x 360, 확장자: .jpg, .jpeg) <br>front(Size: 640 x 480, 확장자: .jpg, .jpeg) | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생 번호) | O |

[요청 본문 예]
```
curl -X POST "{doamin}/nhn-behavior-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?camLocation={camLocation}&reqTime={reqTime}"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@testImage.jpeg;type=image/jpeg"
```


##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

### 음성 감지 요청 API
- 음성 감지 분석을 위한 요청 API
```
URL: /nhn-voice-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?&reqTime={reqTime}
METHOD: POST
X-Auth-Token: Bearer {accessToken}
Content-type: multipart/form-data
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-Auth-Token | String | AccessToken | O |

[URL Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| reqTime | long | 요청 시간(timestamp 10자리)(초 단위까지) | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| file | Binary | 음성 파일<br>(지원 형식 .wav, .wave, .webm)<br>(권장 16bit, 16,000 sampling rate, mono channel) | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생 번호) | O |

[요청 본문 예]
```
curl -X POST "{domain}/nhn-voice-det/v1.0/appkeys/{appkey}/exam/{examNo}/users/{userId}?reqTime={reqTime}"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@fileName.wav;type=audio/wav"
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0: 성공, 이외: 실패) |
| header.resultMessage | String | 요청 결과 메시지 |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

## NHN Proctor API

### Proctor 이벤트 수집 API
- Proctor에서 발생하는 이벤트를 수집하는 API
```
URL: /nhn-cht-prt/v1.0/proctor/event
METHOD: POST
X-CD-Client-Type: Proctor
Content-type: application/json;charset=utf-8
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-CD-Client-Type | String | 클라이언트 타입 'Proctor' 고정 | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| proctorVersion | String | NHN Proctor 앱 버전 정보 | O |
| eventTime | Long | 이벤트 발생 시간 | O |
| deviceId | String | UUID 형태의 디바이스 식별자 - 앱 설치 시 발급 | O |
| sessionId | String | UUID 형태의 세션 ID - 브라우저 로딩 시 발급 | O |
| platform | String | OS 정보 | O |
| eventSource | String | 이벤트 소스('Proctor' 고정) | O |
| event | JSON | 이벤트 | O |
| event.status | String | initialize: 로그인, begintTest: 시험 시작 , endTest: 시험 종료<br><span style="color:#e11d21">\* Window / Mac | O |
| event.keyboard | String | 작업 전환 시도(Attempting switch program.)<br><span style="color:#e11d21">\* Window | X |
| event.mouse | String | 시험 영역 밖, 마우스 이동 감지(시험장 외의 영역으로 이동이 불가능한 상황이지만 예외가 발생한 경우)<br><span style="color:#e11d21">* Window | X |
| event.detection | String | mouseExit: 시험 화면 밖으로 마우스 이동 시(풀스크린 및 미러링 상태에서는 전송되지 않음) <br>fullScreenExit: 풀스크린 이탈 방지 상태에서 풀스크린 이탈 시도 시 <br>processSwitching: 작업 전환 차단 상태에서 작업 전환 시도 시 <br><span style="color:#e11d21">\* Mac| X
| event.description | String | 이벤트 부가 설명 <br><span style="color:#e11d21">\* Mac
| event.additionalEvent | String | 기타 이벤트 정보 | X |

<span style="color:#e11d21">**\* 이벤트 중 하나는 필수**

[요청 본문 예]

``` json
{
	"appKey": "your_app_key",
	"userId": "randy",
	"examNo": "21342",
	"proctorVersion": "1.0.0.1",
	"eventTime": 1619485194941,
	"deviceId": "9faed1a8-964f-4097-a420-c9d9f38ab693",
	"sessionId": "1a8aad31-cc10-49bc-848d-a02e05075bbd",
	"platform": "Windows 10(10.0)",
	"eventSource": "Proctor",
	"event": {
		"status": "initialize"
	}
}
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

### Proctor 지표 수집 API
- Proctor에서 응시자의 PC에 설치된 프로그램 정보를 수집하는 API
```
URL: /nhn-cht-prt/v1.0/proctor/collect
METHOD: POST
X-CD-Client-Type: Proctor
Content-type: application/json;charset=utf-8
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-CD-Client-Type | String | 클라이언트 타입 'Proctor' 고정 | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| installApp | JSON | 설치 프로세스 수집 항목 | O |
| installApp.displayApp | String | 애플리케이션 이름 | O |
| installApp.displayVersion | String | 버전 정보 | O |
| installApp.publisher | String | 제조사 이름 | O |
| extInfo | JSON | 추가 정보 | X |

[요청 본문 예]

``` json
{
    "installApp": [
        {
            "displayApp": "DropBox",
            "displayVersion": "3.18.1",
            "publisher": "Dropbox, Inc."
        },
        {
            "displayApp": "Google Chrome",
            "displayVersion": "40.9.2623.111",
            "publisher": "Google, Inc."
        }
     ]
}
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


## 사용자 정보 등록 및 카메라 검증 API

### 가운데 시선 정보 등록 API
 - 시선 추적 부정행위 감지 시에 사용자(응시자)의 시선 정보를 보정해주기 위한 API

```
URL: /nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze
METHOD: POST
X-Auth-Token: Bearer {accessToken}
Content-type: multipart/form-data
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-Auth-Token | String | AccessToken | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| file | Binary | 이미지 파일 <br>권장 사항(Size: 640 x 480, 확장자: .jpg, .jpeg) | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생 번호) | O |

[요청 본문 예]
```
curl -X POST "{domain}/nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@testImage.jpeg;type=image/jpeg"
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

### 측면 카메라 사전 검증 API

* 측면 카메라의 사전 점검을 위해 검증하는 API

```
URL: /nhn-pre-chk/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/side
METHOD: POST
X-Auth-Token: Bearer {accessToken}
Content-type: multipart/form-data
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-Auth-Token | String | AccessToken | O |

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| file | Binary | 이미지 파일<br>권장 사항(Size: 640 x 360, 확장자: .jpg, .jpeg) | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생 번호) | O |

[요청 본문 예]

```
curl -X POST "{domain}/nhn-pre-chk/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/side" 
-H "accept: application/json;charset=UTF-8" 
-H "X-Auth-Token: Bearer {accessToken}" 
-H "Content-Type: multipart/form-data" 
-F "file=@testImage.jpeg;type=image/jpeg"
```

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0: 성공, 이외: 실패) |
| header.resultMessage | String | 요청 결과 메시지 |
| data.status | Boolean | 카메라 사전 검증 결과(true: 정상, false: 이상 발견) |
| data.thirdPerson | Boolean | 제3자 존재 여부(true: 제3자 식별, false: 미식별) |
| data.absence | Boolean | 수험생 부재 여부(true: 수험생 부재 중, false: 수험생 식별) |
| data.leftHandExistence | Boolean | 왼손 식별 여부(true: 식별, false: 미식별) |
| data.rightHandExistence | Boolean | 오른손 식별 여부(true: 식별, false: 미식별) |
| data.faceExistence | Boolean | 얼굴 식별 여부(true: 식별, false: 미식별) |

[응답 본문 예] - 정상 확인 시

``` json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "Success"
	},
	"data": {
		"status": true,
		"thirdPerson": false,
		"absence": false,
		"leftHandExistence": true,
		"rightHandExistence": true,
		"faceExistence": true
	}
}
```

[응답 본문 예] - 부재 시

``` json
{
	"header": {
		"isSuccessful": true,
		"resultCode": 0,
		"resultMessage": "Success"
	},
	"data": {
		"status": false,
		"thirdPerson": false,
		"absence": true,
		"leftHandExistence": false,
		"rightHandExistence": false,
		"faceExistence": false
	}
}
```



## 설정 조회 API

### 기기제어 설정조회

```
URL: /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/device
METHOD: GET
X-CD-Client-Type: Proctor
Content-type: */*
```

#####

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-CD-Client-Type | String | 클라이언트 타입 'Proctor' 고정 | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.blockMonitorYn | String | 추가 모니터 차단 여부 |
| data.blockSwitchTaskYn | String | 작업 전환 차단 여부 |
| data.blockScreenYn | String | 풀 스크린 이탈 방지 여부 |
| data.blockProgramYn | String | 애플리케이션/프로그램 차단 여부 |

[응답 본문 예]

``` json
{
    "header":{
        "isSuccessful":true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "appKey": "bdyfjdff",
        "blockMonitorYn": "Y",
        "blockSwitchTaskYn":"Y",
        "blockScreenYn": "Y",
        "blockProgramYn": "Y"
    }
}
```


### 얼굴 감지 설정조회

```
URL: /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/face
METHOD: GET
X-CD-Client-Type: Proctor
Content-type: */*
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-CD-Client-Type | String | 클라이언트 타입 'Proctor' 고정 | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.faceDetectionYn | String | 얼굴 인식 사용 여부 |
| data.faceDetectionThreshold | Integer | 탐지된 얼굴 수 |
| data.faceTopAngle | Integer | 얼굴 각도(상) |
| data.faceBottomAngle | Integer | 얼굴 각도(하) |
| data.faceLeftAngle | Integer | 얼굴 각도(좌) |
| data.faceRightAngle | Integer | 얼굴 각도(우) |

[응답 본문 예]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "appKey": "bdyfjdff",
        "faceDetectionYn": "Y",
        "faceDetectionThreshold": 1,
        "faceTopAngle": 20,
        "faceBottomAngle": 20,
        "faceLeftAngle": 20,
        "faceRightAngle": 20
    }
}
```


### 고객 URL 설정조회

```
URL: /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/url
METHOD: GET
X-CD-Client-Type: Proctor
Content-type: */*
```

##### 요청

[Header Parameter]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| X-CD-Client-Type | String | 클라이언트 타입 'Proctor' 고정 | O |

[Path Variable]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |

##### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| header.isSuccessful | Boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.serviceUrl | String | 고객 서비스 URL |
| data.webAuthUrl | String | 사용자 인증 연동 URL |
| data.webhookUrl | String | 부정행위 감지 시 발송 URL |

[응답 본문 예]

``` json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  },
  "data": {
    "appKey": "bdyfjdff",
    "serviceUrl": "https://hook.nhnent.com/service",
    "webAuthUrl": "https://hook.nhnent.com/redirect",
    "webhookUrl": "https://hook.nhnent.com/webhook"
  }
}
```

## 응답 코드

### ResultCode

| 코드 | 구분 | 메시지 | 설명 |
| --- | --- | --- |--- |
| 0 | 성공 | SUCCESS | 정상 처리 |
| -20001 | 에러 | 접근 토큰 만료 | 접근 토큰이 만료시간 초과 또는 취소 요청된 토큰으로 요청 시 |
| -20002 | 에러 | 유효하지 않는 접근 토큰 | 유효하지 않은 접근 토큰으료 요청 시 |
| -20003 | 에러 | WebAuthUrl 통신 불가 오류 | WebAuthUrl로 통신 불가(접근 불가)한 경우에 발생 |
| -20004 | 에러 | WebAuth 인증 실패 | WebAuth 인증 실패 시 |
| -20005 | 에러 | 인증 되지 않은 사용자 접근 오류 | X-Auth-Token 필드 누락으로 인증 처리 불가 |
| -20006 | 에러 | 접근 토큰 타입 오류 | X-Auth-Token 헤더 Bearer 누락 시 발생 |
| -40000 | 에러 | 제어 옵션 미설정 에러 | 요청한 AppKey의 설정 정보가 DB에 존재 하지 않는 경우 발생 |
| -40002 | 에러 | 시선 등록 실패 | 시선 등록 API 요청 중 오류가 발생 했을 경우 |
| -40003 | 에러 | 이미지 저장 설정 정보 등록 실패 | OBS 인증 받지 못했을 경우 발생 |
| -41000 | 에러 | 인증되지 않은 AppKey | 존재하지 않는 앱키거나 활성화 되지 않은 경우 발생 |
| -50000 | 에러 | 맞지 않는 형식의 파일 | 지원하지 않는 파일을 첨부 했을 경우 |
| -50001 | 에러 | 요청 파일이 누락 | 파일이 첨부 되지 않은 경우 |
| -50002 | 에러 | 파일 사이즈 초과 | 첨부 파일이 1G 초과 인 경우 발생 |
| -50005 | 에러 | 첨부 필드 누락 오류 | 첨부 파일 필드가 Form Data 누락된 경우
| -50008 | 에러 | 요청 값 오류 [{message}] | 요청 파라미터가 잘못된 경우(메시지에 파라미터 정보 포함) |
| -50009 | 에러 | 지원되지 않는 Content-Type | 요청 Content-Type 이 지원되지 않는 경우 |
| -50010 | 에러 | 지원되지 않는 HttpMethod | 요청 Method 가 지원 되지 않은 경우 |
| -50011 | 에러 | 파일 사이즈 0 또는 파일 식별이 불가 | 첨부 파일이 정상적으로 업로드 되지 않았거나 오류가 있는 경우 |
| -99999 | 에러 | 내부 오류 | 잘못된 요청이거나 예기치 못한 서버 오류가 발생 했을 경우 |

### HttpStatusCode

| 코드 | 구분 | 코드명 | 설명 |
| --- | --- | --- | --- |
| 200 | 정상 | Ok | 정상 |
| 404 | 에러 | Not Found | 존재하지 않는 URL 호출 시 발생 |
| 500 | 에러 | Server Error | 서버가 점검 중이거나 장애인 경우 발생 |

## 고객사 설정 URL API

### WebAuthURL

- 사용자 인증을 위해 고객사의 WebAuthUrl로 인증 요청**(콘솔에서 WebAuthURL 설정 필수)**
- 사용자\(지원자\)의 상태를 주기적으로 확인해 본인임을 확인

```
URL: {webAuthUrl}
METHOD: POST
Content-type: application/json;charset=utf-8
```

#### 요청

[Request Body]

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| userId | String | 사용자 ID(수험생 번호) | O |
| token | String | WebAuth 인증 토큰 | O |
| via | String | 기타 정보 | X |
| validation | String | 유효성 체크 <br> *사용자 ip, 인입시간 ts(Unix 13 digit Timestamp) 값을 AES256 암호화한 정보 <br> 예) {"ip":"127.0.0.1", "ts": 1621840609833} JSON 텍스트를 AES256 암호화 | X |

[요청 본문 예]

``` json
{
    "userId": "user123",
    "token": "asdfasdfnv23fkja..",
    "via": "",
    "validation": "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

#### 응답

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| resultCode | Integer | 인증 결과 코드(0: 성공, 이외: 실패) |
| resultMessage | String | 인증 결과 메시지 |

[응답 본문 예]

성공

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```

실패

``` json
{
    "resultCode": "40000",
    "resultMessage": "토큰 만료"
}
```

### WebHookURL

- 분석한 이미지 및 음성 파일에서 부정행위 감시 지 치팅 정보 전달**(콘솔에서 Webhook URL 설정 필수)**

```
URL: {webhookUrl}
METHOD: POST
Content-type: application/json;charset=utf-8
```

#### 요청
[Request Body] FRONT, SIDE

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 그룹(FRONT, SIDE 중 하나 응답) | O |
| cheatLevel | Integer | 부정행위 레벨(0: Normal,1:Attention\_Low, 2: Attention\_Hight, 3: Warning) | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| fileUrl | String | 이미지 파일 또는 음성 파일 저장 경로 | O |
| cheatData | JSON | 부정행위 정보 | O |
| cheatData.cheatInfo | JSON | 부정행위 판단 결과 | O |
| cheatData.cheatInfo.absence | Boolean | 부재 여부<br />- 정면 탐지(얼굴 인식 사용 시) <br />- 측면 탐지(사람 수) | X |
| cheatData.cheatInfo.thirdPerson | Boolean | 제3자 식별 여부<br />- 정면 탐지(얼굴 인식 사용 시) <br />- 측면 탐지(사람 수) | X |
| cheatData.cheatInfo.faceYawOut | Boolean | 얼굴 상하 각도 이탈 여부(얼굴 인식 사용 시) | X |
| cheatData.cheatInfo.facePitchOut | Boolean | 얼굴 좌우 각도 이탈 여부(얼굴 인식 사용 시) | X |
| cheatData.cheatInfo.eyeGazeYawOut | Boolean | 시선 상하 각도 이탈 여부(시선 추적 사용 시) | X |
| cheatData.cheatInfo.eyeGazePitchOut | Boolean | 시선 좌우 각도 이탈 여부(시선 추적 사용 시) | X |
| cheatData.cheatInfo.unstableBackground | Boolean | 배경의 변경 여부(신체 외 백그라운드 변화 사용 시) | X |
| cheatData.cheatInfo.leftHandNotExistence |Boolean | 왼손 식별 여부(신체 부위 탐지 사용 시) | X |
| cheatData.cheatInfo.rightHandNotExistence |Boolean | 오른손 식별 여부(신체 부위 탐지 사용 시) | X |
| cheatData.gaze | JSON | 시선 추적 정보 | X |
| cheatData.gaze.numFaces | Integer | 감지된 얼굴 수 | X |
| cheatData.gaze.facePitch | Integer | 얼굴 상하 각도 | X |
| cheatData.gaze.faceYaw | Integer | 얼굴 좌우 각도 | X |
| cheatData.gaze.eyePitch | Integer | 시선 상하 각도 | X |
| cheatData.gaze.eyeYaw | Integer | 시선 좌우 각도 | X |
| chaetData.bg[] | List | 배경 변화 정보 | X |
| chaetData.bg[].isChanged | Boolean | 배경 변화 여부 | X |
| chaetData.bg[].eventTime | Long |발생 시간(timstamp 10자리) |X |
| chaetData.bg[].data | JSON | 배경 감지 상세 정보 |X |
| chaetData.bg[].data.bgChangeDetFlag | Boolean |배경 변화 여부 감지 결과 |X |
| chaetData.bg[].data.allocFlag | Boolean | 배경 이미지 공간 할당 여부(false: 배경 감지 불가) |X |
| cheatData.pose[] | List | 행동 탐지 정보 |X |
| cheatData.pose[].leftHandNotExistence |Boolean | 왼손 식별 여부(신체 부위 탐지 사용 시) |X |
| cheatData.pose[].rightHandNotExistence |Boolean | 오른손 식별 여부(신체 부위 탐지 사용 시) |X |
| cheatData.pose[].eventTime | Long | 이벤트 발생 시간(감지 요청 시간) |X |
| cheatData.pose[].data | JSON | 행동 탐지 상세 정보 |X |
| cheatData.pose[].data.numPerson |Integer | 탐지된 사람의 수 |X |
| cheatData.pose[].data.lHand | JSON | 왼손 좌표 정보 |X |
| cheatData.pose[].data.lHand.xmin |Integer |왼손 영역의 바운딩 박스(경계 박스) 위쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.lHand.ymin |Integer |왼손 영역의 바운딩 박스 아래쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.lHand.xmax |Integer |왼손 영역의 바운딩 박스 왼쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.lHand.ymax |Integer |왼손 영역의 바운딩 박스 오른쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.lHand.isDetected |Boolean | 왼손 감지 여부 |X |
| cheatData.pose[].data.rHand | JSON | 오른손 좌표 정보 |X |
| cheatData.pose[].data.rHand.xmin |Integer |오른손영역의 바운딩 박스 위쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.rHand.ymin |Integer |오른손영역의 바운딩 박스 아래쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.rHand.xmax |Integer |오른손영역의 바운딩 박스 왼쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.rHand.ymax |Integer |오른손 영역의 바운딩 박스 오른쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.rHand.isDetected |Boolean | 오른손 감지 여부 |X |
| cheatData.pose[].data.face | JSON | 얼굴 좌표 정보 |X |
| cheatData.pose[].data.face.xmin |Integer |얼굴 영역의 바운딩 박스 위쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.face.ymin |Integer |얼굴 영역의 바운딩 박스 아래쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.face.xmax |Integer |얼굴 영역의 바운딩 박스 왼쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.face.ymax |Integer |얼굴 영역의 바운딩 박스 오른쪽 꼭지점 좌표 정보 |X |
| cheatData.pose[].data.face.isDetected |Boolean | 얼굴 감지 여부 |X |
| cheatConfig | JSON | 설정 정보 | O |
| cheatConfig.pose.poseEstimationYn | Boolean | 신체 부위 탐지 사용 여부 | X |
| cheatConfig.pose.poseEstimationTime | Integer | 왼손/오른손 좌표 미식별 시간(N초) | X |
| cheatConfig.gaze.gazeTrackingYn | String | 시선(동공) 추적 사용 여부 | X |
| cheatConfig.gaze.gazeTopAngle | Integer | 동공 각도(상) | X |
| cheatConfig.gaze.gazeBottomAngle | Integer | 동공 각도(하) | X |
| cheatConfig.gaze.gazeLeftAngle | Integer | 동공 각도(좌) | X |
| cheatConfig.gaze.gazeRightAngle | Integer | 동공 각도(우) | X |
| cheatConfig.gaze.regUserGaze | JSON | 등록된 시선 정보 | X |
| cheatConfig.gaze.regUserGaze.numFaces | Integer | 등록된 감지된 얼굴 수 | X |
| cheatConfig.gaze.regUserGaze.facePitch | Integer | 등록된 얼굴 상하 각도 | X |
| cheatConfig.gaze.regUserGaze.faceYaw | Integer | 등록된 얼굴 좌우 각도 | X |
| cheatConfig.gaze.regUserGaze.eyePitch | Integer | 등록된 시선 상하 각도 | X |
| cheatConfig.gaze.regUserGaze.eyeYaw | Integer | 등록된 시선 좌우 각도 | X |
| cheatConfig.face.faceDetectionYn | String | 얼굴 인식 사용 여부 | X |
| cheatConfig.face.faceDetectionThreshold | Integer | 감지 얼굴 수 기준값 | X |
| cheatConfig.face.faceTopAngle | Integer | 얼굴 각도(상) | X |
| cheatConfig.face.faceBottomAngle | Integer | 얼굴 각도(하) | X |
| cheatConfig.face.faceLeftAngle | Integer | 얼굴 각도(좌) | X |
| cheatConfig.face.faceRightAngle | Integer | 얼굴 각도(우) | X |
| cheatConfig.bg.bgDetectionYn | String | 신체 이외의 변화 사용 여부 | X |
| cheatConfig.bg.bgDetectionTime | Integer | 백그라운드 변화 탐지 시간(N초) | X |

[요청 본문 예] FRONT, SIDE

``` json
{
	"appKey": "testAppKey",
	"examNo": "demoWebTest",
	"userId": "test",
	"cheatGroup": "FRONT",
	"camLocation": "front",
	"cheatLevel": 2,
	"eventTime": 1661483728,
	"fileUrl": "https://api-storage.cloud.toast.com/usercontainer/IMAGE/demoWebTest/test/FRONT/20220826121531000_1e7e9ade-42aa-48bc-899d-a0d54450913d.jpg",
	"cheatData": {
		"cheatInfo": {
			"absence": false,
			"faceYawOut": true,
			"facePitchOut": false,
			"eyeGazeYawOut": false,
			"eyeGazePitchOut": false,
			"unstableBackground": true,
			"exceedThreshold": false,
			"registeredGaze": true
		},
		"gaze": {
			"numFaces": 1,
			"facePitch": 0,
			"faceYaw": 26,
			"faceDistance": 144,
			"eyePitch": -5,
			"eyeYaw": -7
		},
		"face": {
			"numFaces": 1,
			"facePitch": -8.174861,
			"faceYaw": 24.84945
		},
		"bg": [{
			"eventTime": 1661483728,
			"data": {
				"bgChangeDetFlag": true,
				"allocFlag": true
			}
		}],
		"levels": {
			"gaze": 0,
			"bg": 1,
			"face": 2
		}
	},
	"cheatConfig": {
		"bg": {
			"appKey": "testAppKey",
			"bgDetectionYn": "Y",
			"bgDetectionTime": 10
		},
		"gaze": {
			"appKey": "testAppKey",
			"gazeTrackingYn": "Y",
			"gazeTopAngle": 5,
			"gazeBottomAngle": 15,
			"gazeLeftAngle": 10,
			"gazeRightAngle": 10,
			"regUserGaze": {
				"numFaces": 0,
				"facePitch": 0,
				"faceYaw": 0,
				"faceDistance": 0,
				"eyePitch": 0,
				"eyeYaw": 0
			}
		},
		"face": {
			"appKey": "testAppKey",
			"faceDetectionYn": "Y",
			"faceDetectionThreshold": 1,
			"faceTopAngle": 20,
			"faceBottomAngle": 20,
			"faceLeftAngle": 20,
			"faceRightAngle": 20
		}
	}
}
```

``` json
{
	"appKey": "testAppKey",
	"examNo": "demoWebTest",
	"userId": "test",
	"cheatGroup": "SIDE",
	"camLocation": "side",
	"cheatLevel": 3,
	"eventTime": 1661484082,
	"fileUrl": "https://api-storage.cloud.toast.com/usercontainer/IMAGE/demoWebTest/test/SIDE/20220826122123710_863921e0-aa46-4401-80de-65e3d8a2bf0e.jpg",
	"cheatData": {
		"cheatInfo": {
			"absence": false,
			"thirdPerson": false,
			"leftHandNotExistence": true,
			"rightHandNotExistence": true,
			"exceedThreshold": true,
			"preCheckSideCam": false
		},
		"pose": [{
				"leftHandNotExistence": true,
				"rightHandNotExistence": true,
				"data": {
					"numPerson": 1,
					"face": {
						"xmin": 330,
						"ymin": 251,
						"xmax": 448,
						"ymax": 374,
						"detected": true
					},
					"rhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					},
					"lhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					}
				},
				"eventTime": 1661484081
			},
			{
				"leftHandNotExistence": true,
				"rightHandNotExistence": true,
				"data": {
					"numPerson": 1,
					"face": {
						"xmin": 335,
						"ymin": 238,
						"xmax": 452,
						"ymax": 358,
						"detected": true
					},
					"rhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					},
					"lhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					}
				},
				"eventTime": 1661484074
			},
			{
				"leftHandNotExistence": true,
				"rightHandNotExistence": true,
				"data": {
					"numPerson": 1,
					"face": {
						"xmin": 332,
						"ymin": 251,
						"xmax": 449,
						"ymax": 374,
						"detected": true
					},
					"rhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					},
					"lhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					}
				},
				"eventTime": 1661484082
			},
			{
				"leftHandNotExistence": true,
				"rightHandNotExistence": true,
				"data": {
					"numPerson": 1,
					"face": {
						"xmin": 330,
						"ymin": 251,
						"xmax": 448,
						"ymax": 375,
						"detected": true
					},
					"rhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					},
					"lhand": {
						"xmin": -1,
						"ymin": -1,
						"xmax": -1,
						"ymax": -1,
						"detected": false
					}
				},
				"eventTime": 1661484076
			}
		]
	},
	"cheatConfig": {
		"pose": {
			"appKey": "testAppKey",
			"poseEstimationYn": "Y",
			"poseEstimationTime": 30
		}
	}
}
```

[Request Body] AUDIO

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 구분(AUDIO 고정) | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| fileUrl | String | 이미지 파일 또는 음성 파일 저장 경로 | O |
| cheatLevel | Integer | 음성 감지 여부(0: 미감지, 1: 감지) | O |
| cheatData | JSON | 감지 정보 | X |
| cheatData.voice | Long[] | 음성 감지 시간(초)<br>예) [3,4] > 3,4초에 음성 감지 | X |


[요청 본문 예]

음성 감지
``` json
{
	"appKey": "testAppKey",
	"examNo": "demoWebTest",
	"userId": "test",
	"cheatGroup": "AUDIO",
	"cheatLevel": 1,
	"eventTime": 1661489862,
	"fileUrl": "https://api-storage.cloud.toast.com/usercontainer/AUDIO/demoWebTest/test/20220826135742960_4d99727c-af6a-4fb5-900e-507460587284.webm",
	"cheatData": {
		"voice": [
			2
		]
	}
}
```

[Request Body] PROCTOR

| 이름 | 타입 | 설명 | 필수 여부 |
| --- | --- | --- | --- |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 구분(PROCTOR 고정) | O |
| platformOs | String | OS 정보 | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| cheatLevel | Integer | 치트 발생 여부(1) 고정 | O |
| cheatData | JSON | 부정행위 정보 | O |
| cheatData.keyboard | String | 키보드 이벤트 | X |
| cheatData.mouse | String | 마우스 이벤트 | X |
| cheatData.additional | String | 기타 이벤트 | X |
| cheatConfig | JSON | 설정 정보 | O |
| cheatConfig.blockMonitorYn | String | 추가 모니터 차단 여부 | O |
| cheatConfig.blockSwitchTaskYn | String | 작업 전환 차단 여부 | O |
| cheatConfig.blockScreenYn | String | 풀 스크린 이탈 방지 여부 | O |
| cheatConfig.blockProgramYn | String | 애플리케이션/프로그램 차단 여부 | O |
| validation | String | 유효성 체크 <br> *사용자 ip, 인입시간 ts(Unix 13 digit Timestamp) 값을 AES256 암호화한 정보 <br> 예) {"ip":"127.0.0.1", "ts": 1621840609833} JSON 텍스트를 AES256 암호화  | X |

[요청 본문 예]

``` json
{
    "appKey": "testAppKey",
    "userId": "usertTest",
    "platformOs":"Windows10",
    "examNo":"12345",
    "cheatGroup": "PROCTOR",
    "eventTime": 1621828940,
    "cheatLevel": 1,
    "cheatData": {
        "keyboard": "Attempting switch program."
    },
    "cheatConfig": {
        "appKey": "bdyfjdff",
        "blockMonitorYn": "Y",
        "blockSwitchTaskYn":"Y",
        "blockScreenYn":"Y",
        "blockProgramYn":"Y"
    },
    "validation": "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

[Response Body]

| 이름 | 타입 | 설명 |
| --- | --- | --- |
| resultCode | Integer | 인증 결과 코드(0: 성공, 이외: 실패) |
| resultMessage | String | 인증 결과 메시지 |

[응답 본문 예]

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```
