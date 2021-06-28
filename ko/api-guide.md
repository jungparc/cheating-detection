### Application Service > Cheating Detection > API 가이드

## 인증 API

### 1\. Token 발급 API

\- 부정 행위 감지 API 요청 시 필요한 Token 발급 API

``` yaml
URL : /auth/token?appKey={appkey}&expiresIn={expiresIn}
METHOD : POST
Content-type : application/x-www-form-urlencoded;charset=utf-8
```

##### Request

| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| expiresIn | Integer | 토큰 유효시간(초) | O |
<br>
| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| userId | String | 사용자 ID (수험생 번호) | O |
| examNo | String | 시험 번호 | O |
| deviceType | String | 장비 구분( pc: PC, mo: Mobile ) | O |
| webAuth | JSON | WebAuth 인증 데이터 | O |
| webAuth.userId | String | 사용자 ID (수험생 번호) | O |
| webAuth.token | String | **<span style="color:#e11d21">고객사에서 발급한 WebAuth 인증 토큰</span>** | O |
| webAuth.via | String | 기타 정보 | X |

body sample

``` json
{
    "userId" : "user123",
    "examNo" : "21342",
    "deviceType" : "pc",
    "webAuth" : {
        "userId": "user123",
        "token": "asdfasdfnv23fkja..",
        "via": ""
    }
}
```
<br>
##### Response

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0 , 성공 그외 실패) |
| header.resultMessage | String | 요청 결과 메시지 |
| data.tokenType | String | 토큰타입, bearer 로 고정 |
| data.accessToken | String | 사용자 엑세스 토큰 값 |
| data.expiresIn | Integer | 엑세스 토큰 만료 시간(초) |

``` json
{
	"header" : {
    	"isSuccessful" : true,
    	"resultCode" : 0,
    	"resultMessage" : "Success"
	},
    "data" : {
        "tokenType" : "bearer",
        "accessToken" : "r3rdf23f2f234ff234fgf2",
        "expireIn" : 21600
    }
}
```
<br>
### 2\. Token 취소 API

\- 발급 받은 Token을 취소\(강제 만료 시키기 위한\) API

``` yaml
URL : /auth/revoke
METHOD : POST
X-Region-Code : KR1
Content-type : application/json;charset=utf-8
```

##### Request

| Body Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| token | String | 취소할 엑세스 토큰 값 | O |
<br>
##### ResponseBody

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0 , 성공 그외 실패) |
| header.resultMessage | String | 요청 결과 메시지 |
| data.accessToken | String | 취소한 사용자 엑세스 토큰 값 |

``` JSON
{
    "header" : {
    	"successful" : true,
    	"resultCode" : 0,
    	"resultMessage" : "Success"
    },
    "data" : {
    	"accessToken" : "sadhasdvnfdfnf23=="
	}
}
```
<br>
## 부정행위 감지 요청 API

### 1\. 행동 감지 요청 API
<br>
```
URL : /nhn-behavior-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?location={location}&reqTime={reqTime}
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : multipart/form-data
```

##### Request

| Header | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-Auth-Token | String | AccessToken | O |


| Query Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| camLocation | String | 카메라 위치 정보(side(측면), front(정면)) | O |
| reqTime | long | 요청시간(timestamp 10자리)(초 단위까지) | O |


| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | 이미지 파일 | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생번호) | O |

##### Response body

``` json
{
  "header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  }
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
<br>
### 2\. 음성 감지 요청 API
<br>
```
URL : /nhn-voice-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?&reqTime={reqTime}
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : multipart/form-data
```

##### Request

| Header | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-Auth-Token | String | AccessToken | O |

| Query Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| reqTime | long | 요청시간(timestamp 10자리)(초 단위까지) | O |

| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | 음성 파일<br>(지원 포맷 wav, wave, webm)<br>(권장 16bit, 16000 sampling rate, mono channel) | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 Id(수험생 번호) | O |

#####

### Response

``` json
{
  "header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  }
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드(0 , 성공 그외 실패) |
| header.resultMessage | String | 요청 결과 메시지 |

## NHN Proctor API

### 1\. Proctor 이벤트 수집 API

``` yaml
URL : /nhn-cht-prt/v1.0/proctor/event, /api/v1.0/proctor/event(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request
<br>
| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | 클라이언 타입 'Proctor' 고정 | O |
<br>
| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생번호) | O |
| examNo | String | 시험 번호 | O |
| proctorVersion | String | NHN Proctor App 버전 정보 | O |
| eventTime | Number | 이벤트 발생시간 | O |
| deviceID | String | UUID형태의 디바이스 식별자 - 앱설치시 발급 | O |
| sessionID | String | UUID형태의 세션 ID - 브라우져 로딩시 발급 | O |
| platform | String | OS정보 | O |
| eventSource | String | 이벤트 소스( 'P<span style="color:#222222">roctor' 고정</span>) | O |
| event | JSON | 이벤트 | O |
| event.status | String | initialize : 로그인, begintTest: 시험 시작 , endTest : 시험 종료 <br><span style="color:#e11d21">\* Window / Mac</span> | O |
| event.<span style="color:#222222">keyboard</span> | String | 작업 전환 시도(<span style="color:#222222">Attempting switch program.)</span><br><span style="color:#222222"><span style="color:#e11d21">\* Window</span></span> | X |
| event.mouse | String | <span style="color:#222222">시험 영역 밖, 마우스 이동 감지 (시험장외의 영역으로 이동이 불가능 한 상황이지만 예외가 발생한 경우)</span><br><span style="color:#222222"><span style="color:#e11d21">\* </span><span style="color:#222222"><span style="color:#e11d21">Window </span></span></span> | X |
| event.<span style="color:#9876aa"><span style="color:#000000">additionalEvent</span></span> | String | 기타 이벤트 정보 | X |

<span style="color:#e11d21">**\* 이벤트 중 하나는 필수** </span>

샘플

* 하나의 이벤트 요청

``` json
{
    "appKey" : "your_app_key",
    "userId" : "randy",
    "examNo" : "21342",
    "proctorVersion" : "1.0.0.1",
    "eventTime" : 1619485194941,
    "deviceID" : "9faed1a8-964f-4097-a420-c9d9f38ab693",
    "sessionID" : "1a8aad31-cc10-49bc-848d-a02e05075bbd",
    "platform" : "Windows 10(10.0)",
    "eventSource" : "Proctor",
    "event" : {
	"status" : "initialize"
    }
}
```

##### Response body
<br>
``` json
{
  "header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  }
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
<br>
### 2\. Proctor 지표 수집 API

``` yaml
URL : /nhn-cht-prt/v1.0/proctor/collect, /api/v1.0/proctor/collect(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | 클라이언 타입 'Proctor' 고정 | O |
<br>
| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| <span style="color:#222222">installApp</span> | JSON | 설치 프로세스 수집 항목 | O |
| <span style="color:#222222">installApp.di</span>splayName | String | 어플리케이션 이름 | O |
| <span style="color:#222222">installApp.di</span>splayVersion | String | 버전 정보 | O |
| <span style="color:#222222">installApp.p</span>ublisher | String | 제조사 이름 | O |
| extInfo | JSON | <span style="color:#222222">추가 정보</span> | X |
<br>
샘플

``` json
{
 "installApp" : [
 	{
    	"displayName" : "DropBox",
		"displayVersion" : "3.18.1",
		"publisher" : "Dropbox, Inc."
    },
    {
    	"displayName" : "Google Chrome",
		"displayVersion" : "40.9.2623.111",
		"publisher" : "Google, Inc."
    }
 ]
}
```
<br>
##### Response body

``` json
{
  "header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  }
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
<br>
## 사용자 정보 등록 API

### 1\. 가운데 시선 정보 등록 API

``` yaml
URL : /nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : application/json;charset=utf-8
```

##### Request

| Header | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-Auth-Token | String | AccessToken | O |
<br>
| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | 이미지 파일 | O |
<br>
| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| examNo | String | 시험 번호 | O |
| userId | String | 사용자 ID(수험생번호) | O |
<br>
##### Response body

``` json
{
  "header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  }
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
<br>
## 설정 조회 API

### 1\. 기기제어 설정 조회

``` yaml
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/device
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

#####

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
<br>
##### Response body

``` json
{
	"header":{
		"successful" : true,
		"resultCode" : 0,
		"resultMessage" : "Success"
	},
	"data" : {
		"appKey" : "bdyfjdff",
		"blockMonitorYn" : "Y",
		"blockSwitchTaskYn" : "Y",
		"blockScreenYn" : "Y",
		"blockProgramYn" : "Y"
	}
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.blockMonitorYn | String | 추가 모니터 차단 여부 |
| data.blockSwitchTaskYn | String | 작업 전환 차단 여부 |
| data.blockScreenYn | String | 풀스크린 이탈 방지 여부 |
| data.blockProgramYn | String | 애플리케이션/프로그램 차단 여부 |
<br>
### 2\. 얼굴 감지 설정 조회

``` yaml
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/face
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
<br>
##### Response body

``` json
{
"header": {
    "successful": true,
    "resultCode": 0,
    "resultMessage": "SUCCESS"
},
"data": {
    "appKey" : "bdyfjdff",
    "faceDetectionYn" : "Y",
    "faceDetectionCount" : 1,
    "faceTopAngle": 20,
    "faceBottomAngle" : 20,
    "faceLeftAngle" : 20,
    "faceRightAngle" : 20
}
}
```

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.faceDetectionYn | String | 얼굴인식 사용 여부 |
| data.faceDetectionCount | Integer | 탐지된 얼굴 수 |
| data.faceTopAngle | Integer | 얼굴 각도(상) |
| data.faceBottomAngle | Integer | 얼굴 각도(하) |
| data.faceLeftAngle | Integer | 얼굴 각도(좌) |
| data.faceRightAngle | Integer | 얼굴 각도(하) |
<br>
### 3\. 고객 URL 설정 조회

``` yaml
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/url
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |

##### Response body

``` json
{
  "header": {
    "successful": true,
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

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | 요청 성공 여부 |
| header.resultCode | Integer | 요청 결과 코드 |
| header.resultMessage | String | 요청 결과 메시지 |
| data.appKey | String | 통합 Appkey 또는 서비스 Appkey |
| data.serviceUrl | String | 고객 서비스 URL |
| data.webAuthUrl | String | 사용자 인증 연동 URL |
| data.webhookUrl | String | 부정행위 감지 시 발송 URL |
<br>
<br>
## 응답 코드

### 1\. ResultCode

| Code | 구분 | 설명 |
| --- | --- | --- |
| 0 | 성공 | SUCCESS |
| -20001 | 에러 | 토큰 만료 시 발생 |
| -20002 | 에러 | 토큰이 유효하지 않은경우 발생 |
| -40000 | 에러 | 해당 옵션 미 설정 오류 |
| -40003 | 에러 | 이미지 저장 정보 등록 실패 |
| -50000 | 에러 | 맞지 않는 형식의 파일 |
| -50001 | 에러 | 파일 누락 시 |
| -50002 | 에러 | 파일 사이즈 초과 시(1G) |
| -50004 | 에러 | 파일 처리 중 오류 발생 |
| -50005 | 에러 | 첨부파일 필드 누락 |
| -99999 | 에러 | 서버 오류 |
<br>
### 2\. HttpStatusCode

| Code | 구분 | 코드명 | 설명 |
| --- | --- | --- | --- |
| 200 | 정상 | Ok | 정상 |
| 400 | 에러 | Bad Request | 잘못된 요청인 경우 발생 |
| 500 | 에러 | Server Error | 서버가 점검 중이거나 장애인 경우 발생 |
<br>
## 고객사 설정 URL API

### 1\. WebAuthURL

\- 사용자 인증을 위하여 고객사의 WebAuthUrl로 인증 요청 **(콘솔 화면에서 WebAuthURL 설정 필수)**
\- 사용자\(지원자\)의 상태를 주기적으로 체크하여 본인 확인
<br>
```
URL : {webAuthUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

### Request

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| userId | String | 사용자 ID(수험생번호) | O |
| token | String | WebAuth 인증 토큰 | O |
| via | String | 기타 정보 | X |
| validation | String | 유효성체크 | 0 |

sample

``` json
{
    "userId": "user123",
    "token": "asdfasdfnv23fkja..",
    "via": "",
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

### ResponseBody

| Key | Type | desc |
| --- | --- | --- |
| resultCode | Integer | 인증결과 코드(0 성공 그외 실패) |
| resultMessage | String | 인증결과 메시지 |

sample
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
<br>
### 2\. WebHookURL

\- 분석한 이미지 및 음성 파일에서 부정 행위 감시지 치팅 정보를 전달 **(콘솔 화면에서 Webhook URL 설정 필수)**
<br>
```
URL : {webhookUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

**Request(이미지)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | 유효성체크 | O |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 그룹(RPOCTOR, GAZE, POSE, BACKGROUND, AUDIO) | O |
| cheatLevel | Integer | 부정행위 레벨(0 : Normal,<span style="color:#000000">1 : Attention\_Low, 2 : Attention\_Hight, 3 : Warning)</span> | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| fileUrl | String | 이미지 파일 또는 음성 파일 저장 경로 | O |
| cheatData | JSON | 부정 행위 정보 | O |
| cheatData.cheatInfo | JSON | 부정 행위 판단 결과 | O |
| cheatData.cheatInfo.absence | boolean | 부재여부 (시선추적 (얼굴인식여부) 행동탐지(사람수) | X |
| cheatData.cheatInfo.thirdPerson | boolean | 제3자 식별 여부 (시선추적 사용 시) | X |
| cheatData.cheatInfo.facePoseYawOut | boolean | 얼굴 상하 각도 이탈 여부 (얼굴 감지 사용 시) | X |
| cheatData.cheatInfo.facePosePitchOut | boolean | 얼굴 좌우 각도 이탈 여부 (얼굴 감지 사용 시) | X |
| cheatData.cheatInfo.eyeGazeYawOut | boolean | 시선 상하 각도 이탈 여부 (시선 추적 사용 시) | X |
| cheatData.cheatInfo.eyeGazePitchOut | boolean | 시선 좌우 각도 이탈  여부 (시선 추적 사용 시) | X |
| cheatData.cheatInfo.eyeGazeScreenOut | boolean | 시선 스크린 이탈 여부(사전 시선 등록 완료 시) | X |
| cheatData.cheatInfo.unstableBackground | boolean | 배경의 변경 여부 (신체 외 백그라운드 변화 사용 시) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | 왼손 식별 여부 (행동 감지 사용 시) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | 오른손 식별 여부 (행동 감지 사용 시) | X |
| cheatData.gaze | JSON | 시선 추적 정보 | X |
| cheatData.gaze.numFaces | Integer | 감지된 얼굴 수 | X |
| cheatData.gaze.facePitch | Integer | 얼굴 상하 각도 | X |
| cheatData.gaze.faceYaw | Integer | 얼굴 좌우 각도 | X |
| cheatData.gaze.eyePitch | Integer | 시선 상하 각도 | X |
| cheatData.gaze.eyeYaw | Integer | 시선 좌우 각도 | X |
| cheatData.gaze.screenX | float | 시선 x 축 위치 | X |
| cheatData.gaze.screenY | float | 시선 y 축 위치 | X |
| [<span style="color:#000000">chaetData.bg</span>](http://chaetData.bg)[] | List | 배경 변화 정보 | X |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.isChanged</span> | <span style="color:#000000">boolean</span> | <span style="color:#6a8759"><span style="color:#000000">배경 변화 여부 </span></span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span> | <span style="color:#000000">Long</span> | <span style="color:#000000">발생 시간 (timstamp 10자리)</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data</span> | JSON | <span style="color:#000000">배경 감지 상세 정보</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.bgChangeDetFlag</span> | <span style="color:#000000">boolean</span> | <span style="color:#000000">배경 변화 여부 감지 결과</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.allocFlag</span> | <span style="color:#000000"></span><span style="color:#222222">boolean</span> | 배경 이미지 공간 할당 여부(false : 배경감지 불가) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.pose[]</span> | List | 행동 탐지 정보 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 왼손 식별 여부 (행동 감지 사용 시) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 오른손 식별 여부 (행동 감지 사용 시) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span><span style="color:#000000"></span> | Long | 이벤트 발생 시간(감지 요청 시간) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data</span></span><span style="color:#000000"></span> | JSON | 행동 탐지 상세 정보 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.numPerson</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#6a8759"><span style="color:#000000">탐지된 사람의 수</span></span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand</span></span><span style="color:#000000"></span> | JSON | 왼손 좌표 정보 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">왼손 영역의 바운딩박스 상 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">왼손영역의 바운딩박스 하 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">왼손 영역의 바운딩박스 좌 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">왼손 영역의 바운딩박스 우 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 왼손 감지 여부 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand</span></span><span style="color:#000000"></span> | JSON | 오른손 좌표 정보 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">오른손</span> 영역의 바운딩박스 상 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">오른손</span> 영역의 바운딩박스 하 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">오른손</span> 영역의 바운딩박스 좌 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">오른손 영역의 바운딩박스 우 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 오른손 감지 여부 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face</span></span><span style="color:#000000"></span> | JSON | 얼굴 좌표 정보 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">얼굴 영역의 바운딩박스 상 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">얼굴 영역의 바운딩박스 하 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">얼굴 영역의 바운딩박스 좌 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">얼굴 영역의 바운딩박스 우 꼭지점 좌표 정보</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 얼굴 감지 여부 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatConfig</span> | JSON | 설정 정보 | O |
| <span style="color:#000000">cheatConfig.pose.poseEstimationYn</span> | boolean | 행동 탐지 사용 여부 | X |
| <span style="color:#000000">cheatConfig.pose.poseEstimationTime</span> | Integer | 왼손/오른손 좌표 미 식별 시간(N초)<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTrackingYn</span> | String | 시선(동공) 추적 사용 여부<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTopAngle</span> | Integer | 동공 각도(상)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeBottomAngle | Integer | 동공 각도(하)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeLeftAngle | Integer | 동공 각도(좌)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeRightAngle | Integer | 동공 각도(우)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionYn | String | 얼굴인식 사용 여부<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionThreshold | Integer | 감지 얼굴 수 기준 값<span style="color:#000000"></span> | X |
| cheatConfig.face.faceTopAngle | Integer | 얼굴 각도(상)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceBottomAngle | Integer | 얼굴 각도(하)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceLeftAngle | Integer | 얼굴 각도(좌)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceRightAngle | Integer | 얼굴 각도(우)<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionYn | String | 신체 이외의 백그라운드 변화 사용 여부<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionTime | Integer | 백그라운드 변화 탐지 시간(N초)<span style="color:#000000"></span> | X |

요청 샘플(Cheating Detection - 이미지)
<br>
``` json
{
   "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg==",
   "appKey":"AQJ33tPUaI9Y4lc2IrjX",
   "userId":"usertTest",
   "examNo":"12345",
   "cheatGroup":"IMAGE",
   "cheatLevel":0,
   "eventTime": 1621828945,
   "fileUrl":"[https://toast.cloud.com/20210518193737738.jpeg](https://toast.cloud.com/20210518193737738.jpeg))",
   "cheatData":{
      "cheatInfo":{
         "absence":false,
         "thirdPerson":false,
         "unstableBackground":false,
         "leftHandNotExistence":false,
         "rightHandNotExistence":false
      }
   },
   "cheatConfig":{
      "bg":{
         "bgDetectionYn":"Y",
         "bgDetectionTime":20
      },
      "pose":{
         "poseEstimationYn":"Y",
         "poseEstimationTime":20
      }
   }
}
```

``` Json
{
   "appKey":"AQJ33tPUaI9Y4lc2IrjX",
   "userId":"userTestId",
   "examNo":"12345",
   "cheatGroup":"IMAGE",
   "cheatLevel":3,
   "eventTime": 1621828940,
   "fileUrl":"[https://toast.cloud.com/20210518185420495.png](https://toast.cloud.com/20210518185420495.png)",
   "cheatData":{
      "cheatInfo":{
         "absence":true,
         "thirdPerson":false,
         "facePoseYawOut":false,
         "facePosePitchOut":false,
         "eyeGazeYawOut":false,
         "eyeGazePitchOut":false,
         "unstableBackground":false
      },
      "gaze":{
         "numFaces":0,
         "facePitch":0,
         "faceYaw":0,
         "eyePitch":0,
         "eyeYaw":0,
         "screenX":0.0,
         "screenY":0.0
      }
   },
   "cheatConfig":{
      "bg":{
         "bgDetectionYn":"Y",
         "bgDetectionTime":1
      },
      "gaze":{
         "gazeTrackingYn":"Y",
         "gazeTopAngle":24,
         "gazeBottomAngle":24,
         "gazeLeftAngle":24,
         "gazeRightAngle":24
      },
      "face":{
         "faceDetectionYn":"Y",
         "faceDetectionThreshold":1,
         "faceTopAngle":25,
         "faceBottomAngle":25,
         "faceLeftAngle":25,
         "faceRightAngle":25
      }
   }
}
```
<br>
**Request(음성)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | 유효성체크 | O |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 구분(DEVICE, IMAGE, AUDIO) | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| fileUrl | String | 이미지 파일 또는 음성 파일 저장 경로 | O |
| cheatLevel | Integer | 음성 감지 여부( 0 : 미감지, 1 : 감지) | O |
| cheatData | JSON | 감지 정보 | X |
| cheatData.voice | Long[] | 음성 감지 시간(초) <br>ex) [3,4] > 3,4초에 음성 감지 | X |

##### 요청 샘플(Cheating Detection - 음성 감지 발생)
<br>
``` json
{
"validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg==",
"appKey": "AQJ33tPUaI9Y4lc2IrjX",
"userId": "usertTest",
"examNo": "12345",
"cheatGroup": "AUDIO",
"senderTime": 1621828948,
"fileUrl": "https://toast.cloud.com/20210518193737738.wav",
"cheatLevel" : 1,
"cheatData" : {
	"voice": [1,2,3]
}
}
```
<br>
##### 요청 샘플(Cheating Detection - 음성 미 발생)
<br>
``` json
{
"validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg==",
"appKey":"AQJ33tPUaI9Y4lc2IrjX",
"userId": "usertTest",
"examNo":"12345",
"cheatGroup":"AUDIO",
"senderTime": 1621828948,
"fileUrl":"https://toast.cloud.com/20210518193737738.wav",
"cheatLevel" : 0
}
```
<br>
#### **Request(Proctor)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| appKey | String | 통합 Appkey 또는 서비스 Appkey | O |
| userId | String | 사용자 ID(수험생번호) | O |
| examNo | String | 시험 번호 | O |
| cheatGroup | String | 치트 구분(PROCTOR, IMAGE, AUDIO) | O |
| platformOs | String | <span style="color:#222222">OS정보</span> | O |
| eventTime | Long | 이벤트 발생 시간(timestamp) | O |
| cheatLevel | Integer | 치트 발생 여부 (1) 고정 | O |
| cheatData | JSON | 부정 행위 정보 | O |
| cheatData.keyboard | String | 키보드 이벤트 | X |
| cheatData.mouse | String | 마우스 이벤트 | X |
| cheatData.additional | String | 기타 이벤트 | X |
| cheatConfig | JSON | 설정 정보 | O |
| cheatConfig.blockMonitorYn | String | 추가 모니터 차단 여부 | O |
| cheatConfig.blockSwitchTaskYn | String | 작업 전환 차단 여부 | O |
| cheatConfig.blockScreenYn | String | 풀스크린 이탈 방지 여부 | O |
| cheatConfig.blockProgramYn | String | 애플리케이션/프로그램 차단 여부 | O |

요청 샘플(Cheating Detection)

``` json
{
"appKey": "AQJ33tPUaI9Y4lc2IrjX",
"userId": "usertTest",
"platformOs" :"Windows10",
"examNo":"12345",
"cheatGroup": "PROCTOR",
"eventTime": 1621828940,
"cheatLevel": 1,
"cheatData": {
	"keyboard": "Attempting switch program."
},
"cheatConfig": {
"appKey" : "bdyfjdff",
"blockMonitorYn": "Y",
"blockSwitchTaskYn":"Y",
"blockScreenYn":"Y",
"blockProgramYn":"Y"
}
}
```
<br>
ResponseBody

| Key | Type | desc |
| --- | --- | --- |
| resultCode | Integer | 인증결과 코드(0 성공 그외 실패) |
| resultMessage | String | 인증결과 메시지 |

sample

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```
<br>
<br>
