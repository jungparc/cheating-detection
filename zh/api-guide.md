
## Application Service > Cheating Detection > API Guide
- This document explains the APIs required for using Face Recognition API.
## Common API Information

### Advance Preparation
- To use APIs, an integrated project Appkey or service Appkey is required.
- The service Appkey is located in the URL & Appkey menu on the top of the console.

### Common Request Information
- The security key needs to be authenticated in order to use APIs.

[API domain]

| Environment | Domain                         |
| ---- | ------------------------------ |
| Real | http://ctd-api.cloud.toast.com |

## Authenticate API

### API to issue access (tokens)

-  API to issue access tokens required when requesting API to detect cheating

```
URL : /auth/token?appKey={appkey}&expiresIn={expiresIn}
METHOD : POST
Content-type : application/json;charset=utf-8
```

##### Request

[URL Parameter]

| Name      | Type    | Description                           | Necessity |
| --------- | ------- | ------------------------------ | --------- |
| appKey    | String  | Integrated Appkey or Service Appkey | O         |
| expiresIn | Integer | Token Time Limit ((sec))   <br> 0 < expiresIn <= 7200          | O         |

[Request Body]

| Name           | Type   | Description                                                         | Necessity |
| -------------- | ------ | ------------------------------------------------------------ | --------- |
| userId         | String | User ID (student number)                                       | O         |
| examNo         | String | Exam number                                                    | O         |
| deviceType     | String | Device category (pc: PC, mo: Mobile )                               | O         |
| webAuth        | JSON   | WebAuth authentication data                                          | O         |
| webAuth.userId | String | User ID (student number)                                       | O         |
| webAuth.token  | String | **<span style="color:#e11d21">WebAuth authentication token issued by the client** | O         |
| webAuth.via    | String | Other Information                                                    | X         |

[request body example]

``` json
{
    "userId" : "user123",
    "examNo" : "21342",
    "deviceType" : "pc",
    "webAuth" : {
        "userId": "user123",
        "token": "ADs3Fsdfasdfnv23fkja34FX=",
        "via": ""
    }
}
```

##### Response

[Response Body]

| Name                 | Type    | Description                                |
| -------------------- | ------- | ----------------------------------- |
| header.isSuccessful  | Boolean | Request success                      |
| header.resultCode    | Integer | Request result code (0: success, others: failure) |
| header.resultMessage | String  | Request result message                    |
| data.tokenType       | String  | Fix to token type and bearer            |
| data.accessToken     | String  | User access tokens                    |
| data.expiresIn       | Integer | Access token expiry time(sec)             |

[Response body example]

``` json
{
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "Success"
    },
    "data" : {
        "tokenType" : "bearer",
        "accessToken" : "DfA3f3da3/34SF+edf6898D2343fsasdf3f=",
        "expireIn" : 600
    }
}
```

### API to cancel access tokens

-  API to cancel the access token issued \(to force quit\)

``` yaml
URL : /auth/revoke
METHOD : POST
Content-type : application/json;charset=utf-8
```

##### Request

[Request Body]

| Name        | Type   | Description             | Necessity |
| ----------- | ------ | ---------------- | --------- |
| accessToken | String | Access token to cancel | O         |

[request body example]
``` json
{
   "accessToken" : "XDadhaS3dvns34Fdfnf23=="
}
```

##### Response

[Response Body]

| Name                 | Type    | Description                                |
| -------------------- | ------- | ----------------------------------- |
| header.isSuccessful  | Boolean | Request success                      |
| header.resultCode    | Integer | Request result code (0: success, others: failure) |
| header.resultMessage | String  | Request result message                    |
| data.accessToken     | String  | User access tokens canceled             |

[Response body example]

``` JSON
{
    "header" : {
        "isSuccessful" : true,
        "resultCode" : 0,
        "resultMessage" : "Success"
    },
    "data" : {
        "accessToken" : "XDadhaS3dvns34Fdfnf23=="
    }
}
```

## Request Cheat Detection API

### API to request detect cheating
-  API to request behavior detection and analysis
```
URL : /nhn-behavior-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?camLocation={camLocation}&reqTime={reqTime}
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : multipart/form-data
```

##### Request

[Header Parameter]

| Name         | Type   | Description        | Necessity |
| ------------ | ------ | ----------- | --------- |
| X-Auth-Token | String | AccessToken | O         |

[URL Parameter]

| Name        | Type   | Description                                      | Necessity |
| ----------- | ------ | ----------------------------------------- | --------- |
| camLocation | String | Camera location data (side, front) | O         |
| reqTime     | long   | Request period (timestamp 10 digits)(including seconds)  | O         |

[Request Body]

| Name | Type   | Description                                                         | Necessity |
| ---- | ------ | ------------------------------------------------------------ | --------- |
| file | Binary | Image file <br>Image recommendation<br> side (Size : 1280 x 720, extension : jpg, jpeg) <br>front (Size : 640 x 480, extension : jpg, jpeg) | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |
| examNo | String | Exam number                      | O         |
| userId | String | User ID (student number)         | O         |

[request body example]
```
curl -X POST "{doamin}/nhn-behavior-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?camLocation={camLocation}&reqTime={reqTime}"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@testImage.jpeg;type=image/jpeg"
```


##### Response

[Response Body]

| Name                 | Type    | Description             |
| -------------------- | ------- | ---------------- |
| header.isSuccessful  | Boolean | Request success   |
| header.resultCode    | Integer | Request result code   |
| header.resultMessage | String  | Request result message |

[Response body example]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

### API to request detect voice
-  API to request voice detection and analysis
```
URL : /nhn-voice-det/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}?&reqTime={reqTime}
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : multipart/form-data
```

##### Request

[Header Parameter]

| Name         | Type   | Description        | Necessity |
| ------------ | ------ | ----------- | --------- |
| X-Auth-Token | String | AccessToken | O         |

[URL Parameter]

| Name    | Type | Description                                     | Necessity |
| ------- | ---- | ---------------------------------------- | --------- |
| reqTime | long | Request period (timestamp 10 digits)(including seconds) | O         |

[Request Body]

| Name | Type   | Description                                                         | Necessity |
| ---- | ------ | ------------------------------------------------------------ | --------- |
| file | Binary | Voice file<br>(supported formats: wav, wave, webm)<br>(recommends: 16bit, 16,000 sampling rate, mono channel) | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |
| examNo | String | Exam number                      | O         |
| userId | String | User ID (student number)         | O         |

[request body example]
```
curl -X POST "{domain}/nhn-voice-det/v1.0/appkeys/{appkey}/exam/{examNo}/users/{userId}?reqTime={reqTime}"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@fileName.wav;type=audio/wav"
```

##### Response

[Response Body]

| Name                 | Type    | Description                                 |
| -------------------- | ------- | ------------------------------------ |
| header.isSuccessful  | Boolean | Request success                       |
| header.resultCode    | Integer | Request result code (0: success, others: failure) |
| header.resultMessage | String  | Request result message                     |

[Response body example]

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

### API to Collect Proctor Event
-  API to collect events occurred in project
```
URL : /nhn-cht-prt/v1.0/proctor/event
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

[Header Parameter]

| Name             | Type   | Description                           | Necessity |
| ---------------- | ------ | ------------------------------ | --------- |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O         |

[Request Body]

| Name           | Type   | Description                                                         | Necessity |
| -------------- | ------ | ------------------------------------------------------------ | --------- |
| appKey         | String | Integrated Appkey or Service Appkey                               | O         |
| userId         | String | User ID (student number)                                       | O         |
| examNo         | String | Exam number                                                    | O         |
| proctorVersion | String | NHN Proctor App version information                                     | O         |
| eventTime      | Long   | Event occurrence time                                             | O         |
| deviceId       | String | Device identifier in UUID form -  Issued when installing the app                | O         |
| sessionId      | String | Session ID in UUID form - Issued when loading the browser                  | O         |
| platform       | String | OS information                                                      | O         |
| eventSource    | String | Event source (fixed to 'Proctor')                                 | O         |
| event          | JSON   | Event                                                       | O         |
| event.status   | String | initialize: log in, begintTest: begin test, endTest: end test<br><span style="color:#e11d21">\* Windows / Mac | O         |
| event.keyboard | String | (Attempts to switch programs.)<br><span style="color:#e11d21">\* Windows | X         |
| event.mouse    | String | Mouse movement detection outside the test area (when it is impossible to move to an area outside of the test area, but an exception occurs)<br><span style="color:#e11d21">* Windows  | X         |
| event.detection | String | mouseExit : When the mouse moves outside the test area (Cannot be sent in full screen & mirroring mode)) <br>fullScreenExit : When attempting to exit the full screen in full screen exit prevention mode <br>processSwitching : When attempting to switch operation in operation switch blocking mode <br><span style="color:#e11d21">\* Mac| X
| event.description | String | Additional description on the event <br><span style="color:#e11d21">\* Mac
| event.additionalEvent | String | Other event information | X |

<span style="color:#e11d21">**\* One of the events is required **

[request body example]

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

##### Response

[Response Body]

| Name                 | Type    | Description             |
| -------------------- | ------- | ---------------- |
| header.isSuccessful  | Boolean | Request success   |
| header.resultCode    | Integer | Request result code   |
| header.resultMessage | String  | Request result message |

[Response body example]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```

### API to Collect Proctor Indicators
- API to collect program information installed on the examinee's PC in Proctor
```
URL : /nhn-cht-prt/v1.0/proctor/collect
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

[Header Parameter]

| Name             | Type   | Description                           | Necessity |
| ---------------- | ------ | ------------------------------ | --------- |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O         |

[Request Body]

| Name                      | Type   | Description                    | Necessity |
| ------------------------- | ------ | ----------------------- | --------- |
| installApp                | JSON   | Collected items for installation process | O         |
| installApp.displayApp    | String | Application name       | O         |
| installApp.displayVersion | String | Version information               | O         |
| installApp.publisher      | String | Manufacturer name             | O         |
| extInfo                   | JSON   | Additional information               | X         |

[request body example]

``` json
{
    "installApp" : [
        {
            "displayApp" : "DropBox",
            "displayVersion" : "3.18.1",
            "publisher" : "Dropbox, Inc."
        },
        {
            "displayApp" : "Google Chrome",
            "displayVersion" : "40.9.2623.111",
            "publisher" : "Google, Inc."
        }
     ]
}
```

##### Response

[Response Body]

| Name                 | Type    | Description             |
| -------------------- | ------- | ---------------- |
| header.isSuccessful  | Boolean | Request success   |
| header.resultCode    | Integer | Request result code   |
| header.resultMessage | String  | Request result message |

[Response body example]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```


## Register User Information API

### API to Register Middle Gaze Information
 -  API to correct gaze information of the user (examinee) when detecting cheating by gaze tracking

```
URL : /nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze
METHOD : POST
X-Auth-Token : Bearer {accessToken}
Content-type : application/json;charset=utf-8
```

##### Request

[Header Parameter]

| Name         | Type   | Description        | Necessity |
| ------------ | ------ | ----------- | --------- |
| X-Auth-Token | String | AccessToken | O         |

[Request Body]

| Name | Type   | Description                                                         | Necessity |
| ---- | ------ | ------------------------------------------------------------ | --------- |
| file | Binary | Image file <br>Recommended (Size : 640 x 480, Extension : jpg, jpeg) | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |
| examNo | String | Exam number                      | O         |
| userId | String | User ID (student number) | O |Lock

[request body example]
```
curl -X POST "{domain}/nhn-behavior-reg/v1.0/appkeys/{appKey}/exam/{examNo}/users/{userId}/gaze"
-H "accept: application/json;charset=UTF-8"
-H "X-Auth-Token: Bearer {accessToken}"
-H "Content-Type: multipart/form-data"
-F "file=@testImage.jpeg;type=image/jpeg"
```

##### Response

[Response Body]

| Name                 | Type    | Description             |
| -------------------- | ------- | ---------------- |
| header.isSuccessful  | Boolean | Request success   |
| header.resultCode    | Integer | Request result code   |
| header.resultMessage | String  | Request result message |

[Response body example]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "Success"
    }
}
```



## View Settings API

### Search Device Control Setting

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/device
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

#####

##### Request

[Header Parameter]

| Name             | Type   | Description                           | Necessity |
| ---------------- | ------ | ------------------------------ | --------- |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |

##### Response

[Response Body]

| Name                   | Type    | Description                            |
| ---------------------- | ------- | ------------------------------- |
| header.isSuccessful    | Boolean | Request success                  |
| header.resultCode      | Integer | Request result code                  |
| header.resultMessage   | String  | Request result message                |
| data.appKey            | String  | Integrated Appkey or Service Appkey  |
| data.regionCode        | String  | Region code                       |
| data.blockMonitorYn    | String  | Whether blocked additional monitors or not           |
| data.blockSwitchTaskYn | String  | Whether blocked job switching or not             |
| data.blockScreenYn     | String  | Whether prevented exit from full screen        |
| data.blockProgramYn    | String  | Application/program blocked or not |

[Response body example]

``` json
{
    "header":{
        "isSuccessful":true,
        "resultCode": 0,
        "resultMessage": "Success"
    },
    "data": {
        "appKey": "bdyfjdff",
        "resionCode" : "KR1"
        "blockMonitorYn": "Y",
        "blockSwitchTaskYn" :"Y",
        "blockScreenYn" : "Y",
        "blockProgramYn": "Y"
    }
}
```


### Search Face Detection Setting

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/face
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

[Header Parameter]

| Name             | Type   | Description                           | Necessity |
| ---------------- | ------ | ------------------------------ | --------- |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |

##### Response

[Response Body]

| Name                        | Type    | Description                           |
| --------------------------- | ------- | ------------------------------ |
| header.isSuccessful         | Boolean | Request success                 |
| header.resultCode           | Integer | Request result code                 |
| header.resultMessage        | String  | Request result message               |
| data.appKey                 | String  | Integrated Appkey or Service Appkey |
| data.regionCode             | String  | Region code                      |
| data.faceDetectionYn        | String  | Whether to use face recognition            |
| data.faceDetectionThreshold | Integer | Number of faces detected                 |
| data.faceTopAngle           | Integer | Face angle (top)                  |
| data.faceBottomAngle        | Integer | Face angle (bottom)                  |
| data.faceLeftAngle          | Integer | Face angle (left)                  |
| data.faceRightAngle         | Integer | Face angle (bottom)                  |

[Response body example]

``` json
{
    "header": {
        "isSuccessful": true,
        "resultCode": 0,
        "resultMessage": "SUCCESS"
    },
    "data": {
        "appKey" : "bdyfjdff",
        "regionCode" : "KR1",
        "faceDetectionYn" : "Y",
        "faceDetectionThreshold" : 1,
        "faceTopAngle": 20,
        "faceBottomAngle" : 20,
        "faceLeftAngle" : 20,
        "faceRightAngle" : 20
    }
}
```


### Search Customer URL Setting

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/url
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

[Header Parameter]

| Name             | Type   | Description                           | Necessity |
| ---------------- | ------ | ------------------------------ | --------- |
| X-CD-Client-Type | String | Fix client type to ‘Proctor’ | O         |

[Path Variable]

| Name   | Type   | Description                           | Necessity |
| ------ | ------ | ------------------------------ | --------- |
| appKey | String | Integrated Appkey or Service Appkey | O         |

##### Response

[Response Body]

| Name                 | Type    | Description                           |
| -------------------- | ------- | ------------------------------ |
| header.isSuccessful  | Boolean | Request success                 |
| header.resultCode    | Integer | Request result code                 |
| header.resultMessage | String  | Request result message               |
| data.appKey          | String  | Integrated Appkey or Service Appkey |
| data.regionCode      | String  | Region code                      |
| data.serviceUrl      | String  | Customer service URL                |
| data.webAuthUrl      | String  | User authentication link URL           |
| data.webhookUrl      | String  | URL where cheating detection report is sent      |

[Response body example]

``` json
{
  "header": {
    "isSuccessful": true,
    "resultCode": 0,
    "resultMessage": "Success"
  },
  "data": {
    "appKey": "bdyfjdff",
    "regionCode": "KR1",
    "serviceUrl": "https://hook.nhnent.com/service",
    "webAuthUrl": "https://hook.nhnent.com/redirect",
    "webhookUrl": "https://hook.nhnent.com/webhook"
  }
}
```

## Response code

### ResultCode

| Code   | Classification | Message                          | Description                                                        |
| ------ | ---- | ------------------------------- | ----------------------------------------------------------- |
| 0      | Success | SUCCESS                         | Normally processed                                                   |
| -20001 | Error | Access Token Expired                  | When requesting with access tokens that have been expired or requested to be canceled |
| -20002 | Error | Invalid access token         | When requesting with invalid access tokens                         |
| -20003 | Error | WebAuthUrl Communication Error       | Occurs when unable to proceed communication(access) with WebAuthUrl             |
| -20004 | Error | WebAuth authentication Failure                | When failed to authenticate with WebAuth                                        |
| -20005 | Error | Error in access by unauthenticated users | Unable to authenticate due to X-Auth-Token field omitted                   |
| -20006 | Error | Error in access token type             | Occurs when the X-Auth-Token head bearer is omitted                       |
| -40000 | Error | Control option not set           | Occurs when the setting information of the requested AppKey does not exist in DB   |
| -40002 | Error | Gaze Registration Failure                  | When an error occurs while registering gaze registration API                 |
| -40003 | Error | Failed to register image save setting information | Occurs when OBS authentication is not received                              |
| -41000 | Error | Unauthenticated AppKey            | Occurs when the AppKey does not exist or is not activated           |
| -50000 | Error | Unsupported file format           | When an unsupported file is attached                         |
| -50001 | Error | The requested file is omitted                | When a file is not attached                                  |
| -50002 | Error | File size exceeded                | Occurs when the attached file exceeds 1G                            |
| -50005 | Error | Attached field omitted | When the form data of the attached file field is omitted
| -50008 | Error | Error in the requested value [{message}] | When the requested parameter is incorrect (Parameter information in message included) |
| -50009 | Error | Unsupported content-type | When the requested content-type is not supported |
| -50010 | Error | Unsupported HttpMethod | When the requested method is not supported |
| -50011 | Error | The file size is 0 or unable to identify a file | When the attached file is not uploaded normally or there is an error |
| -99999 | Error | Internal error | Incorrect request or when there is an unexpected server error |

### HttpStatusCode

| Code | Classification | Code name       | Description                                  |
| ---- | ---- | ------------ | ------------------------------------- |
| 200  | Normal | Ok           | Normal                                  |
| 404  | Error | Not Found    | Occurs when calling an URL that does not exist        |
| 500  | Error | Server Error | Occurs during the server maintenance or fault |

## Client Setup URL API

### WebAuthURL

- Request authentication with the customer’s WebAuthUrl for user authentication **(WebAuthURL setting required for the console screen)**
- Check user \(applicant\) status periodically to verify personal information

```
URL : {webAuthUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

#### Request

[Request Body]

| Name       | Type   | Description                                                         | Necessity |
| ---------- | ------ | ------------------------------------------------------------ | --------- |
| userId     | String | User ID (student number)                                       | O         |
| token      | String | WebAuth authentication token                                            | O         |
| via        | String | Other Information                                                    | X         |
| validation | String | Validity check <br> *Information that has been AES256 encoded the user ip, incoming time ts(Unix 13 digit Timestamp) value <br> Example) {"ip":"127.0.0.1", "ts": 1621840609833} JSON tested was AES256 encoded | X         |

[request body example]

``` json
{
    "userId": "user123",
    "token": "asdfasdfnv23fkja..",
    "via": "",
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

#### Response

[Response Body]

| Name          | Type    | Description                                |
| ------------- | ------- | ----------------------------------- |
| resultCode    | Integer | Authentication result code (0: success, others: failure) |
| resultMessage | String  | Authentication result message                    |

[Response body example]

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
    "resultMessage": "Token expired"
}
```

### WebHookURL

- Deliver cheating Information from the analyzed image and audio files **(Webhook URL setting required for the console screen)**

```
URL : {webhookUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

#### Request

[Request Body] GAZE, POSE, BACKGROUND, AUDIO

| Name                                      | Type    | Description                                                         | Necessity |
| ----------------------------------------- | ------- | ------------------------------------------------------------ | --------- |
| appKey                                    | String  | Integrated Appkey or Service Appkey                               | O         |
| userId                                    | String  | User ID (student number)                                       | O         |
| examNo                                    | String  | Exam number                                                    | O         |
| cheatGroup                                | String  | Cheat group( Respond as one of FRONT, SIDE, AUDIO )             | O         |
| cheatLevel                                | Integer | Cheating activity level (0 : Normal,1 : Attention\_Low, 2 : Attention\_High, 3 : Warning) | O         |
| eventTime                                 | Long    | Event occurrence time(timestamp)                                  | O         |
| fileUrl                                   | String  | Image or audio file storage path                         | O         |
| cheatData                                 | JSON    | Cheating activity information                                                | O         |
| cheatData.cheatInfo                       | JSON    | Cheating judgment result                                           | O         |
| cheatData.cheatInfo.absence               | Boolean | Absence<br />- Gaze tracking(Face recognition) <br />- Behavior detection(Number of person)
X | X         |
| cheatData.cheatInfo.thirdPerson           | Boolean | Third party identification (if gaze tracking is in use)                           | X         |
| cheatData.cheatInfo.eyeGazeYawOut         | Boolean | Gaze up/down angle departure (if gaze tracking is in use)                  | X         |
| cheatData.cheatInfo.eyeGazePitchOut       | Boolean | Gaze left/right angledeparture (if gaze tracking is in use)                  | X         |
| cheatData.cheatInfo.eyeGazeScreenOut      | Boolean | Gaze screen departure (if previous gaze registration is complete)                | X         |
| cheatData.cheatInfo.unstableBackground    | Boolean | Background change (if the detection of background change other than the body is in use)            | X         |
| cheatData.cheatInfo.leftHandNotExistence  | Boolean | Left hand detection (if motion detection is in use)                            | X         |
| cheatData.cheatInfo.rightHandNotExistence | Boolean | Right hand detection (if motion detection is in use)                          | X         |
| cheatData.gaze                            | JSON    | Eye tracking information                                               | X         |
| cheatData.gaze.numFaces                   | Integer | Number of faces detected                                               | X         |
| cheatData.gaze.facePitch                  | Integer | Face up/down angle                                               | X         |
| cheatData.gaze.faceYaw                    | Integer | Face left/right angle                                               | X         |
| cheatData.gaze.eyePitch                   | Integer | Gaze up/down angle                                               | X         |
| cheatData.gaze.eyeYaw                     | Integer | Gaze left/right angle                                               | X         |
| cheatData.gaze.screenX                    | float   | Gaze X-axis location                                                | X         |
| cheatData.gaze.screenY                    | float   | Gaze Y-axis location                                                | X         |
| chaetData.bg[]                            | List    | Background change information                                               | X         |
| chaetData.bg[].isChanged                  | Boolean | Background change                                               | X         |
| chaetData.bg[].eventTime                  | Long    | Occurrence time (timestamp 10 digits)                                   | X         |
| chaetData.bg[].data                       | JSON    | Background detection details                                          | X         |
| chaetData.bg[].data.bgChangeDetFlag       | Boolean | Background change detection result                                     | X         |
| chaetData.bg[].data.allocFlag             | Boolean | Background image space allocation(false : background detection failed)            | X         |
| cheatData.pose[]                          | List    | Motion detection information                                               | X         |
| cheatData.pose[].leftHandNotExistence     | Boolean | Left hand detection (if motion detection is in use)                            | X         |
| cheatData.pose[].rightHandNotExistence    | Boolean | Right hand detection (if motion detection is in use)                          | X         |
| cheatData.pose[].eventTime                | Long    | Event occurrence time (detection request time)                             | X         |
| cheatData.pose[].data                     | JSON    | Motion detection details                                          | X         |
| cheatData.pose[].data.numPerson           | Integer | Number of people detected                                             | X         |
| cheatData.pose[].data.lHand               | JSON    | Left hand coordinate information                                               | X         |
| cheatData.pose[].data.lHand.xmin          | Integer | Coordinates of the upper vertex of the (bounding box) of the left hand area     | X         |
| cheatData.pose[].data.lHand.ymin          | Integer | Coordinates of the lower vertex of the bounding box of the left hand area     | X         |
| cheatData.pose[].data.lHand.xmax          | Integer | Coordinates of the left vertex of the bounding box of the left hand area                | X         |
| cheatData.pose[].data.lHand.ymax          | Integer | Coordinates of the right vertex of the bounding box of the left hand area              | X         |
| cheatData.pose[].data.lHand.isDetected    | Boolean | Left hand detection                                               | X         |
| cheatData.pose[].data.rHand               | JSON    | Right hand coordinate information                                             | X         |
| cheatData.pose[].data.rHand.xmin          | Integer | Coordinates of the upper vertex of the bounding box of the right hand area               | X         |
| cheatData.pose[].data.rHand.ymin          | Integer | Coordinates of the lower vertex of the bounding box of the right hand area             | X         |
| cheatData.pose[].data.rHand.xmax          | Integer | Coordinates of the left vertex of the bounding box of the right hand area               | X         |
| cheatData.pose[].data.rHand.ymax          | Integer | Coordinates of the right vertex of the bounding box of the right hand area            | X         |
| cheatData.pose[].data.rHand.isDetected    | Boolean | Right hand detection                                             | X         |
| cheatData.pose[].data.face                | JSON    | Face coordinate information                                               | X         |
| cheatData.pose[].data.face.xmin           | Integer | Coordinates of the upper vertex of the bounding box of the face area                | X         |
| cheatData.pose[].data.face.ymin           | Integer | Coordinates of the lower vertex of the bounding box of the face area              | X         |
| cheatData.pose[].data.face.xmax           | Integer | Coordinates of the left vertex of the bounding box of the face area                | X         |
| cheatData.pose[].data.face.ymax           | Integer | Coordinates of the right vertex of the bounding box of the face area              | X         |
| cheatData.pose[].data.face.isDetected     | Boolean | Face detection                                               | X         |
| cheatConfig                               | JSON    | Settings                                                    | O         |
| cheatConfig.pose.poseEstimationYn         | Boolean | Motion detection usage                                          | X         |
| cheatConfig.pose.poseEstimationTime       | Integer | Duration of not identifying left/right hand coordinates (N sec)                            | X         |
| cheatConfig.gaze.gazeTrackingYn           | String  | Eye (pupil) tracking usage                                    | X         |
| cheatConfig.gaze.gazeTopAngle             | Integer | Pupil angle (upper)                                                | X         |
| cheatConfig.gaze.gazeBottomAngle          | Integer | Pupil angle (lower)                                                | X         |
| cheatConfig.gaze.gazeLeftAngle            | Integer | Pupil angle (left)                                                | X         |
| cheatConfig.gaze.gazeRightAngle           | Integer | Pupil angle (right)                                                | X         |
| cheatConfig.gaze.regUserGaze              | JSON    | Registered gaze information                                             | X         |
| cheatConfig.gaze.regUserGaze.numFaces     | Integer | Number of registered faces detected                                        | X         |
| cheatConfig.gaze.regUserGaze.facePitch    | Integer | Up/down angle of registered face                                        | X         |
| cheatConfig.gaze.regUserGaze.faceYaw      | Integer | Left/right angle of registered face                                        | X         |
| cheatConfig.gaze.regUserGaze.eyePitch     | Integer | Up/down angle of registered gaze                                         | X         |
| cheatConfig.gaze.regUserGaze.eyeYaw       | Integer | Left/right angle of registered gaze                                         | X         |
| cheatConfig.gaze.regUserGaze.screenX      | Integer | X-coordinate of registered gaze                                           | X         |
| cheatConfig.gaze.regUserGaze.screenY      | Integer | Y-coordinate of registered gaze                                           | X         |
| cheatConfig.face.faceDetectionYn          | String  | Whether to use face recognition                                          | X         |
| cheatConfig.face.faceDetectionThreshold   | Integer | Reference value for number of faces detected                                          | X         |
| cheatConfig.face.faceTopAngle             | Integer | Face angle (top)                                                | X         |
| cheatConfig.face.faceBottomAngle          | Integer | Face angle (bottom)                                                | X         |
| cheatConfig.face.faceLeftAngle            | Integer | Face angle (left)                                                | X         |
| cheatConfig.face.faceRightAngle           | Integer | Face angle(right)                                                | X         |
| cheatConfig.face.regUserGaze              | JSON    | Registered gaze information                                             | X         |
| cheatConfig.face.regUserGaze.numFaces     | Integer | Number of registered faces detected                                        | X         |
| cheatConfig.face.regUserGaze.facePitch    | Integer | Up/down angle of registered face                                        | X         |
| cheatConfig.face.regUserGaze.faceYaw      | Integer | Left/right angle of registered face                                        | X         |
| cheatConfig.face.regUserGaze.eyePitch     | Integer | Up/down angle of registered gaze                                         | X         |
| cheatConfig.face.regUserGaze.eyeYaw       | Integer | Left/right angle of registered gaze                                         | X         |
| cheatConfig.face.regUserGaze.screenX      | Integer | X-coordinate of registered gaze                                           | X         |
| cheatConfig.face.regUserGaze.screenY      | Integer | Y-coordinate of registered gaze                                           | X         |
| cheatConfig.bg.bgDetectionYn              | String  | Whether the detection of background change other than the body is used                        | X         |
| cheatConfig.bg.bgDetectionTime            | Integer | Background change detection time (N sec)                               | X         |
| validation                                | String  | Validity check <br> *Information that has been AES256 encoded the user ip, incoming time ts(Unix 13 digit Timestamp) value <br> Example) {"ip":"127.0.0.1", "ts": 1621840609833} JSON tested was AES256 encoded | X         |

[Request body example] POSE, GAZE

``` json
{
    "appKey":"AQJ33tPUaI9Y4lc2IrjX",
    "userId":"usertTest",
    "examNo":"12345",
    "cheatGroup":"POSE",
    "cheatLevel":0,
    "eventTime": 1621828945,
    "fileUrl":"https://alp-api-storage.cloud.toast.com/v1/AUTH_3434343434534234234/cheatingdetection/IMAGE/demoWebTest/test/front/20210707123901682.jpg",
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
    },
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

``` Json
{
    "appKey":"AQJ33tPUaI9Y4lc2IrjX",
    "userId":"userTestId",
    "examNo":"12345",
    "cheatGroup":"GAZE",
    "cheatLevel":3,
    "eventTime": 1621828940,
    "fileUrl":"https://alp-api-storage.cloud.toast.com/v1/AUTH_3434343434534234234/cheatingdetection/IMAGE/demoWebTest/diablo3/test/20210707123901682.jpg",
    "cheatData":{
        "cheatInfo":{
            "absence":true,
            "thirdPerson":false,
            "eyeGazeYawOut":false,
            "eyeGazePitchOut":false,
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
        "gaze":{
            "gazeTrackingYn":"Y",
            "gazeTopAngle":24,
            "gazeBottomAngle":24,
            "gazeLeftAngle":24,
            "gazeRightAngle":24,
            "regUserGaze": {
                "numFaces": 0,
                "facePitch": 0,
                "faceYaw": 0,
                "faceDistance": 0,
                "eyePitch": 0,
                "eyeYaw": 0,
                "screenX": 0,
                "screenY": 0
            }
        },
        "face":{
            "faceDetectionYn":"Y",
            "faceDetectionThreshold":1,
            "faceTopAngle":25,
            "faceBottomAngle":25,
            "faceLeftAngle":25,
            "faceRightAngle":25,
            "regUserGaze": {
                "numFaces": 0,
                "facePitch": 0,
                "faceYaw": 0,
                "faceDistance": 0,
                "eyePitch": 0,
                "eyeYaw": 0,
                "screenX": 0,
                "screenY": 0
            }
        }
    },
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

[Request Body] AUDIO

| Name            | Type    | Description                                                         | Necessity |
| --------------- | ------- | ------------------------------------------------------------ | --------- |
| appKey          | String  | Integrated Appkey or Service Appkey                               | O         |
| userId          | String  | User ID (student number)                                       | O         |
| examNo          | String  | Exam number                                                    | O         |
| cheatGroup      | String  | Cheat classification (Fix AUDIO)                                       | O         |
| eventTime       | Long    | Event occurrence time(timestamp)                                  | O         |
| fileUrl         | String  | Image or audio file storage path                         | O         |
| cheatLevel      | Integer | Audio detection (0 : undetected, 1 : detected)                           | O         |
| cheatData       | JSON    | Detection information                                                    | X         |
| cheatData.voice | Long[]  | Audio detection time (sec )<br> ex ) [3,4] > audio detection at 3 and 4 sec          | X         |
| validation      | String  | Validity check <br> *Information that has been AES256 encoded the user ip, incoming time ts(Unix 13 digit Timestamp) value <br> Example) {"ip":"127.0.0.1", "ts": 1621840609833} JSON tested was AES256 encoded | X         |

[request body example]

Voice detection
``` json
{
    "appKey": "AQJ33tPUaI9Y4lc2IrjX",
    "userId": "usertTest",
    "examNo": "12345",
    "cheatGroup": "AUDIO",
    "senderTime": 1621828948,
    "fileUrl": "https://alp-api-storage.cloud.toast.com/v1/AUTH_3434343434534234234/cheatingdetection/AUDIO/demoWebTest/test/20210707123916831.webm",
    "cheatLevel" : 1,
    "cheatData" : {
        "voice": [1,2,3]
    },
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg==",
}
```

Voice not detected

``` json
{
    "appKey":"AQJ33tPUaI9Y4lc2IrjX",
    "userId": "usertTest",
    "examNo":"12345",
    "cheatGroup":"AUDIO",
    "senderTime": 1621828948,
    "fileUrl":"https://toast.cloud.com/20210518193737738.wav",
    "cheatLevel" : 0,
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

[Request Body] PROCTOR

| Name                          | Type    | Description                                                         | Necessity |
| ----------------------------- | ------- | ------------------------------------------------------------ | --------- |
| appKey                        | String  | Integrated Appkey or Service Appkey                               | O         |
| userId                        | String  | User ID (student number)                                       | O         |
| examNo                        | String  | Exam number                                                    | O         |
| cheatGroup                    | String  | Cheat classification (Fix PROCTOR)                                     | O         |
| platformOs                    | String  | OS information                                                      | O         |
| eventTime                     | Long    | Event occurrence time(timestamp)                                  | O         |
| cheatLevel                    | Integer | Cheating occurrence (1) fixed                                      | O         |
| cheatData                     | JSON    | Cheating activity information                                                | O         |
| cheatData.keyboard            | String  | Keyboard event                                                | X         |
| cheatData.mouse               | String  | Mouse event                                                | X         |
| cheatData.additional          | String  | Other event                                                  | X         |
| cheatConfig                   | JSON    | Settings                                                    | O         |
| cheatConfig.blockMonitorYn    | String  | Whether blocked additional monitors or not                                        | O         |
| cheatConfig.blockSwitchTaskYn | String  | Whether blocked job switching or not                                          | O         |
| cheatConfig.blockScreenYn     | String  | Whether prevented exit from full screen                                     | O         |
| cheatConfig.blockProgramYn    | String  | Application/program blocked or not                              | O         |
| validation                    | String  | Validity check <br> *Information that has been AES256 encoded the user ip, incoming time ts(Unix 13 digit Timestamp) value <br> Example) {"ip":"127.0.0.1", "ts": 1621840609833} JSON tested was AES256 encoded | X         |

[request body example]

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
    },
    "validation" : "LrXE8YJolAdgNiAKikontAb8aj8YkFf3vl+3oM6hdMVDE5bcmbzNgA9aV4y/ZDLdDpTsEsNtKqzcCxnYZMy2lg=="
}
```

[Response Body]

| Name          | Type    | Description                                |
| ------------- | ------- | ----------------------------------- |
| resultCode    | Integer | Authentication result code (0: success, others: failure) |
| resultMessage | String  | Authentication result message                    |

[Response body example]

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```