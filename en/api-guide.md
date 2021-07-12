## Application Service > Cheating Detection > API Guide

## Authenticate API

### 1. Issue Token API

- Issue Token API required to request Cheat Detection API

```
URL : /auth/token?appKey={appkey}&expiresIn={expiresIn}
METHOD : POST
Content-type : application/x-www-form-urlencoded;charset=utf-8
```

##### Request

| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| expiresIn | Integer | Token validity (sec) | O |

| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| userId | String | User ID (student number) | O |
| examNo | String | Exam number | O |
| deviceType | String | Device category(pc: PC, mo: Mobile ) | O |
| webAuth | JSON | WebAuth authentication data | O |
| webAuth.userId | String | User ID (student number) | O |
| webAuth.token | String | **<span style="color:#e11d21">WebAuth authentication token issued by the client</span>** | O |
| webAuth.via | String | Other Information | X |

##### Body sample

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

##### Response

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code (0, success or failure) |
| header.resultMessage | String | Request result message |
| data.tokenType | String | Token type, fixed to bearer |
| data.accessToken | String | User access token value |
| data.expiresIn | Integer | Access token expiry time(sec) |

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

### 2. Cancel Token API

- API to cancel \(force expire\) an issued token

``` yaml
URL : /auth/revoke
METHOD : POST
X-Region-Code : KR1
Content-type : application/json;charset=utf-8
```

##### Request

| Body Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| token | String | Access token value to be canceled | O |

##### ResponseBody

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code (0 = success; otherwise, failure) |
| header.resultMessage | String | Request result message |
| data.accessToken | String | Canceled user access token value |

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

## Request Cheat Detection API

### 1. Request Motion Detection API

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
| camLocation | String | Camera location data (side, front) | O |
| reqTime | long | Request period (timestamp 10 digits)(including seconds) | O |


| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | Image file | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| examNo | String | Exam Number | O |
| userId | String | User ID (student number) | O |

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |

### 2. Request Voice Detection API

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
| reqTime | long | Request period (timestamp 10 digits)(including seconds) | O |

| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | Voice file<br>(supported formats: wav, wave, webm)<br>(recommends: 16bit, 16000 sampling rate, mono channel) | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| examNo | String | Exam Number | O |
| userId | String | User ID (student number) | O |

##### Response

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code (0 = success; otherwise, failure) |
| header.resultMessage | String | Request result message |

## NHN Proctor API

### 1. Collect Proctor Event API

```
URL : /nhn-cht-prt/v1.0/proctor/event, /api/v1.0/proctor/event(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O |

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| userId | String | User ID (student number) | O |
| examNo | String | Exam number | O |
| proctorVersion | String | NHN Proctor App version information | O |
| eventTime | Number | Event occurrence time | O |
| deviceID | String | Device identifier in UUID format- issued at the time of app installation | O |
| sessionID | String | Session ID in UUID format - issued at the time of browser loading | O |
| platform | String | OS information | O |
| eventSource | String | Event source( fixed to 'P<span style="color:#222222">roctor' </span>) | O |
| event | JSON | Event | O |
| event.status | String | initialize: log in, begintTest: begin test , endTest: endTest <br><span style="color:#e11d21">\* Window / Mac</span> | O |
| event.<span style="color:#222222">keyboard</span> | String | Attempts to switch programs(<span style="color:#222222">Attempting switch program.)</span><br><span style="color:#222222"><span style="color:#e11d21">\* Window</span></span> | X |
| event.mouse | String | <span style="color:#222222">Mouse movement detection outside the test area (when it is impossible to move to an area outside of the test area, but an exception occurs</span><br><span style="color:#222222"><span style="color:#e11d21">\* </span><span style="color:#222222"><span style="color:#e11d21">Window </span></span></span> | X |
| event.<span style="color:#9876aa"><span style="color:#000000">additionalEvent</span></span> | String | Other event information | X |

<span style="color:#e11d21">**\* One of the events is required** </span>

Sample

* Request one event

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |

### 2. Collect Proctor Indicator API

```
URL : /nhn-cht-prt/v1.0/proctor/collect, /api/v1.0/proctor/collect(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O |

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| <span style="color:#222222">installApp</span> | JSON | Collected items for installation process | O |
| <span style="color:#222222">installApp.di</span>splayName | String | Application name | O |
| <span style="color:#222222">installApp.di</span>splayVersion | String | Version information | O |
| <span style="color:#222222">installApp.p</span>ublisher | String | Manufacturer name | O |
| extInfo | JSON | <span style="color:#222222">Additional information</span> | X |

Sample

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |

## Register User Information API

### Register Middle Gaze Information API

```
URL : /nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : application/json;charset=utf-8
```

##### Request

| Header | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-Auth-Token | String | AccessToken | O |

| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | Image file | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| examNo | String | Exam Number | O |
| userId | String | User ID (student number) | O |

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |

## View Settings API

### 1. View device control settings

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/device
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

#####

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |
| data.appKey | String | Integrated Appkey or Service Appkey |
| data.blockMonitorYn | String | Whether blocked additional monitors or not |
| data.blockSwitchTaskYn | String | Whether blocked job switching or not |
| data.blockScreenYn | String | Prevention of exiting full screen |
| data.blockProgramYn | String | Application/program blocked or not |

### 2. View face detection settings

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/face
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |
| data.appKey | String | Integrated Appkey or Service Appkey |
| data.faceDetectionYn | String | Face detection usage |
| data.faceDetectionCount | Integer | Number of faces detected |
| data.faceTopAngle | Integer | Face angle (top) |
| data.faceBottomAngle | Integer | Face angle (bottom) |
| data.faceLeftAngle | Integer | Face angle (left) |
| data.faceRightAngle | Integer | Face angle (bottom) |

### 3. View customer URL settings

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/url
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |

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
| header.isSuccess | boolean | Request success |
| header.resultCode | Integer | Request result code |
| header.resultMessage | String | Request result message |
| data.appKey | String | Integrated Appkey or Service Appkey |
| data.serviceUrl | String | Customer service URL |
| data.webAuthUrl | String | User authentication link URL |
| data.webhookUrl | String | URL where cheating detection report is sent |

## Response code

### 1. ResultCode

| Code | Classification | Description |
| --- | --- | --- |
| 0 | Success | SUCCESS |
| -20001 | Error | Generated at the time of token expiration |
| -20002 | Error | Occurs when the token is invalid |
| -40000 | Error | Error that occurs when the option is not set |
| -40003 | Error | Failed to register image save information |
| -50000 | Error | Unsupported file format |
| -50001 | Error | File missing |
| -50002 | Error | File size exceeded (1G) |
| -50004 | Error | File processing error |
| -50005 | Error | File attachment field missed |
| -99999 | Error | Server error |

### 2. HttpStatusCode

| Code | Classification | Code name | Description |
| --- | --- | --- | --- |
| 200 | Normal | Ok | Normal |
| 400 | Error | Bad Request | Occurs due to invalid request |
| 500 | Error | Server Error | Occurs during the server maintenance or fault |

## Client Setup URL API

### 1. WebAuthURL

- Request authentication with the customer’s WebAuthUrl for user authentication **(WebAuthURL setting required for the console screen)**
- Check user \(applicant\) status periodically to verify the user's identity

```
URL : {webAuthUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

#### Request

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| userId | String | User ID (student number) | O |
| token | String | WebAuth authentication token | O |
| via | String | Other Information | X |
| validation | String | Validation | 0 |

sample

``` json
{
    "userId": "user123",
    "token": "asdfasdfnv23fkja..",
    "via": "",
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

#### ResponseBody

| Key | Type | desc |
| --- | --- | --- |
| resultCode | Integer | Authentication result code (0 = success; otherwise, failure) |
| resultMessage | String | Authentication result message |

sample
Success

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```

Failure

``` json
{
    "resultCode": "40000",
    "resultMessage": "토큰 만료"
}
```

### 2. WebHookURL

- Deliver cheating information from the analyzed image and audio files **(Webhook URL setting required for the console screen)**

```
URL : {webhookUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

**Request(image)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | Validation | O |
| appKey | String | Integrated Appkey or Service Appkey | O |
| userId | String | User ID (student number) | O |
| examNo | String | Exam number | O |
| cheatGroup | String | Cheat group(RPOCTOR, GAZE, POSE, BACKGROUND, AUDIO) | O |
| cheatLevel | Integer | Cheating activity level(0: Normal,<span style="color:#000000">1: Attention\_Low, 2: Attention\_Hight, 3: Warning)</span> | O |
| eventTime | Long | Event occurrence time(timestamp) | O |
| fileUrl | String | Image or audio file storage path | O |
| cheatData | JSON | Cheating activity information | O |
| cheatData.cheatInfo | JSON | Cheating activity decision result | O |
| cheatData.cheatInfo.absence | boolean | Absence<br />- eye tracking (face detection) <br />- motion detection (number of people) | X |
| cheatData.cheatInfo.thirdPerson | boolean | Third party identification (if eye tracking is in use) | X |
| cheatData.cheatInfo.facePoseYawOut | boolean | Face up/down angle departure (if face detection is in use) | X |
| cheatData.cheatInfo.facePosePitchOut | boolean | Face left/right angle departure (if face detection is in use) | X |
| cheatData.cheatInfo.eyeGazeYawOut | boolean | Gaze up/down angle departure (if eye tracking is in use) | X |
| cheatData.cheatInfo.eyeGazePitchOut | boolean | Gaze left/right angle  departure (if eye tracking is in use) | X |
| cheatData.cheatInfo.eyeGazeScreenOut | boolean | Gaze screen departure (if previous gaze registration is complete) | X |
| cheatData.cheatInfo.unstableBackground | boolean | Background change (if the detection of background change other than the body is in use) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | Left hand detection (if motion detection is in use) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | Right hand detection (if motion detection is in use) | X |
| cheatData.gaze | JSON | Eye tracking information | X |
| cheatData.gaze.numFaces | Integer | Number of faces detected | X |
| cheatData.gaze.facePitch | Integer | Face up/down angle | X |
| cheatData.gaze.faceYaw | Integer | Face left/right angle | X |
| cheatData.gaze.eyePitch | Integer | Gaze up/down angle | X |
| cheatData.gaze.eyeYaw | Integer | Gaze left/right angle | X |
| cheatData.gaze.screenX | float | Gaze x-axis position | X |
| cheatData.gaze.screenY | float | Gaze y-axis position | X |
| [<span style="color:#000000">chaetData.bg</span>](http://chaetData.bg)[] | List | Background change information | X |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.isChanged</span> | <span style="color:#000000">boolean</span> | <span style="color:#6a8759"><span style="color:#000000">Background change </span></span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span> | <span style="color:#000000">Long</span> | <span style="color:#000000"><span style="color:#000000">Occurrence time (timestamp 10 digits)</span></span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data</span> | JSON | <span style="color:#000000">Background detection details</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.bgChangeDetFlag</span> | <span style="color:#000000">boolean</span> | <span style="color:#000000">Background change detection result </span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.allocFlag</span> | <span style="color:#000000"></span><span style="color:#222222">boolean</span> | Background image space allocation(false : background detection failed) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.pose[]</span> | List | Motion detection information | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | Left hand detection (if motion detection is in use) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | Right hand detection (if motion detection is in use) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span><span style="color:#000000"></span> | Long | Event occurrence time (detection request time) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data</span></span><span style="color:#000000"></span> | JSON | Motion detection details | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.numPerson</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#6a8759"><span style="color:#000000">Number of people detected</span></span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand</span></span><span style="color:#000000"></span> | JSON | Left hand coordinate information | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Top vertex coordinate of the bounding box of the left-hand area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Bottom vertex coordinate of the bounding box of the left-hand area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Left vertex coordinate of the bounding box of the left hand area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Right vertex coordinate of the bounding box of the left-hand area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | Left hand detection | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand</span></span><span style="color:#000000"></span> | JSON | Right hand coordinate information | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">Top vertex coordinate of the bounding box of the right-hand</span> area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">Bottom vertex coordinate of the bounding box of the right-hand</span> area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">Left vertex coordinate of the bounding box of the right-hand</span> area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Right vertex coordinate of the bounding box of the right-hand area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | Right hand detection | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face</span></span><span style="color:#000000"></span> | JSON | Face coordinate information | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Top vertex coordinate of the bounding box of the face area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Bottom vertex coordinate of the bounding box of the face area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Left vertex coordinate of the bounding box of the face area/span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">Right vertex coordinate of the bounding box of the face area</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | Face detection | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatConfig</span> | JSON | Settings | O |
| <span style="color:#000000">cheatConfig.pose.poseEstimationYn</span> | boolean | Motion detection usage | X |
| <span style="color:#000000">cheatConfig.pose.poseEstimationTime</span> | Integer | Duration of not identifying left/right hand coordinates (N sec)<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTrackingYn</span> | String | Eye (pupil) tracking usage<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTopAngle</span> | Integer | Pupil angle (upper)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeBottomAngle | Integer | Pupil angle (lower)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeLeftAngle | Integer | Pupil angle (left)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeRightAngle | Integer | Pupil angle (right)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionYn | String | Face detection usage<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionThreshold | Integer | Reference value of the number of detected faces<span style="color:#000000"></span> | X |
| cheatConfig.face.faceTopAngle | Integer | Face angle (top)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceBottomAngle | Integer | Face angle (bottom)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceLeftAngle | Integer | Face angle (left)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceRightAngle | Integer | Face angle(right)<<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionYn | String | Whether the detection of background change other than the body is used<<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionTime | Integer | Background change detection time (N sec)<span style="color:#000000"></span> | X |

Request sample(Cheating Detection - image)

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

**Request(audio)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | Validation | O |
| appKey | String | Integrated Appkey or Service Appkey | O |
| userId | String | User ID (student number) | O |
| examNo | String | Exam number | O |
| cheatGroup | String | Cheating category(DEVICE, IMAGE, AUDIO) | O |
| eventTime | Long | Event occurrence timetimestamp) | O |
| fileUrl | String | Image or audio file storage path | O |
| cheatLevel | Integer | Audio detection (0 : undetected, 1 : detected) | O |
| cheatData | JSON | Detection information | X |
| cheatData.voice | Long[] | Audio detection time (sec) <br>ex) [3,4] > audio detection at 3 and 4 sec | X |

##### Requested sample(Cheating Detection - audio detection occurrence)

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

##### Requested sample(Cheating Detection - no audio occurrence)

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

#### **Request(Proctor)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| appKey | String | Integrated Appkey or Service Appkey | O |
| userId | String | User ID (student number) | O |
| examNo | String | Exam number | O |
| cheatGroup | String | Cheating catego(PROCTOR, IMAGE, AUDIO) | O |
| platformOs | String | <span style="color:#222222">OS information</span> | O |
| eventTime | Long | Event occurrence time(timestamp) | O |
| cheatLevel | Integer | Cheating occurrence (1) fixed | O |
| cheatData | JSON | Cheating activity information | O |
| cheatData.keyboard | String | Keyboard event | X |
| cheatData.mouse | String | Mouse event | X |
| cheatData.additional | String | Other event | X |
| cheatConfig | JSON | Settings | O |
| cheatConfig.blockMonitorYn | String | Whether blocked additional monitors or not | O |
| cheatConfig.blockSwitchTaskYn | String | Whether blocked job switching or not | O |
| cheatConfig.blockScreenYn | String | Prevention of exiting full screen | O |
| cheatConfig.blockProgramYn | String | Application/program blocked or not | O |

Request sample(Cheating Detection)

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

ResponseBody

| Key | Type | desc |
| --- | --- | --- |
| resultCode | Integer | Authentication result code(0 = success; otherwise, failure) |
| resultMessage | String | Authentication result message |

sample

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```
