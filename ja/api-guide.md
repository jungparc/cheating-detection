### Application Service > Cheating Detection > APIガイド

## 認証API

### 1. トークン(token)発行API

- 不正行為検知APIのリクエスト時に必要なトークン発行API

```
URL : /auth/token?appKey={appkey}&expiresIn={expiresIn}
METHOD : POST
Content-type : application/x-www-form-urlencoded;charset=utf-8
```

##### Request

| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| expiresIn | Integer | トークン有効時間(秒) | O |

| Query Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| userId | String | ユーザーID(受験生番号) | O |
| examNo | String | 試験番号 | O |
| deviceType | String | デバイス区分(pc: PC, mo: Mobile ) | O |
| webAuth | JSON | WebAuth認証データ | O |
| webAuth.userId | String | ユーザーID(受験生番号) | O |
| webAuth.token | String | **<span style="color:#e11d21">顧客が発行したWebAuth認証トークン</span>** | O |
| webAuth.via | String | その他情報 | X |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード(0：成功、その他：失敗) |
| header.resultMessage | String | リクエスト結果メッセージ |
| data.tokenType | String | トークンタイプ、 bearerで固定 |
| data.accessToken | String | ユーザーアクセストークン値 |
| data.expiresIn | Integer | アクセストークン満了時間(秒) |

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

### 2. トークンキャンセルAPI

- 発行されたトークンのキャンセル\(強制的に無効にするために\) API

``` yaml
URL : /auth/revoke
METHOD : POST
X-Region-Code : KR1
Content-type : application/json;charset=utf-8
```

##### Request

| Body Parameter | Type | Desc | Required |
| :---: | :---: | --- | :---: |
| token | String | キャンセルするアクセストークン値 | O |

##### ResponseBody

| Key | Type | desc |
| :---: | --- | --- |
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード(0：成功、それ以外：失敗) |
| header.resultMessage | String | リクエスト結果メッセージ |
| data.accessToken | String | キャンセルしたユーザーアクセストークン値 |

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

## 不正行為検知リクエストAPI

### 1. 行動検知リクエストAPI

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
| camLocation | String | カメラ位置情報(side(側面)、front(正面)) | O |
| reqTime | long | リクエスト時間(timestamp 10桁)(秒単位まで) | O |


| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | 画像ファイル | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| examNo | String | 試験番号 | O |
| userId | String | ユーザーID(受験生番号) | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |

### 2. 音声検知リクエストAPI

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
| reqTime | long | リクエスト時間(timestamp 10桁)(秒単位まで) | O |

| Body Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| file | Binary | 音声ファイル<br>(サポート形式 .wav, .wave, .webm)<br>(推奨16bit、16,000 sampling rate、mono channel) | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| examNo | String | 試験番号 | O |
| userId | String | ユーザーID(受験生番号) | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード(0：成功、それ以外：失敗) |
| header.resultMessage | String | リクエスト結果メッセージ |

## NHN Proctor API

### 1. Proctorイベント収集API

```
URL : /nhn-cht-prt/v1.0/proctor/event, /api/v1.0/proctor/event(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | クライアントタイプ「Proctor」固定 | O |

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| userId | String | ユーザーID(受験生番号) | O |
| examNo | String | 試験番号 | O |
| proctorVersion | String | NHN Proctorアプリバージョン情報 | O |
| eventTime | Number | イベント発生時間 | O |
| deviceID | String | UUID形式のデバイス識別子 - アプリインストール時に発行 | O |
| sessionID | String | UUID形式のセッションID - ブラウザローディング時に発行 | O |
| platform | String | OS情報 | O |
| eventSource | String | イベントソース( 「P<span style="color:#222222">roctor」固定</span>) | O |
| event | JSON | イベント | O |
| event.status | String | initialize：ログイン、 begintTest：試験開始、endTest：試験終了 <br><span style="color:#e11d21">\* Window / Mac</span> | O |
| event.<span style="color:#222222">keyboard</span> | String | タスクの切り替えを試行(<span style="color:#222222">Attempting switch program.)</span><br><span style="color:#222222"><span style="color:#e11d21">\* Window</span></span> | X |
| event.mouse | String | <span style="color:#222222">試験領域外、マウス移動検知(試験場以外の領域に移動ができない状況ですが例外が発生した場合)</span><br><span style="color:#222222"><span style="color:#e11d21">\* </span><span style="color:#222222"><span style="color:#e11d21">Window </span></span></span> | X |
| event.<span style="color:#9876aa"><span style="color:#000000">additionalEvent</span></span> | String | その他イベント情報 | X |

<span style="color:#e11d21">**\* イベントのうち1つは必須** </span>

サンプル

* 1つのイベントリクエスト

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |

### 2. Proctor指標収集API

```
URL : /nhn-cht-prt/v1.0/proctor/collect, /api/v1.0/proctor/collect(deprecated)
METHOD : POST
X-CD-Client-Type : Proctor
Content-type : application/json;charset=utf-8
```

##### Request

| Header Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| X-CD-Client-Type | String | クライアントタイプ「Proctor」固定 | O |

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| <span style="color:#222222">installApp</span> | JSON | インストールプロセス収集項目 | O |
| <span style="color:#222222">installApp.di</span>splayName | String | アプリケーション名 | O |
| <span style="color:#222222">installApp.di</span>splayVersion | String | バージョン情報 | O |
| <span style="color:#222222">installApp.p</span>ublisher | String | 製作社名 | O |
| extInfo | JSON | <span style="color:#222222">追加情報</span> | X |

サンプル

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |

## ユーザー情報登録API

### 中視線情報登録API

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
| file | Binary | 画像ファイル | O |

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| examNo | String | 試験番号 | O |
| userId | String | ユーザーID(受験生番号) | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |

## 設定照会API

### 1. 機器制御設定照会

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
| appKey | String | 統合AppkeyまたはサービスAppkey | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |
| data.appKey | String | 統合AppkeyまたはサービスAppkey |
| data.blockMonitorYn | String | 追加モニター遮断設定 |
| data.blockSwitchTaskYn | String | タスクの切り替え遮断設定 |
| data.blockScreenYn | String | フルスクリーン離脱防止設定 |
| data.blockProgramYn | String | アプリケーション/プログラム遮断設定 |

### 2. 顔検出設定照会

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/face
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |
| data.appKey | String | 統合AppkeyまたはサービスAppkey |
| data.faceDetectionYn | String | 顔認識使用設定 |
| data.faceDetectionCount | Integer | 検知された顔の数 |
| data.faceTopAngle | Integer | 顔の角度(上) |
| data.faceBottomAngle | Integer | 顔の角度(下) |
| data.faceLeftAngle | Integer | 顔の角度(左) |
| data.faceRightAngle | Integer | 顔の角度(右) |

### 3. 顧客URL設定照会

```
URL : /nhn-cht-cfg/v1.0/appkeys/{appKey}/configuration/url
METHOD : GET
X-CD-Client-Type : Proctor
Content-type : */*
```

##### Request

| Path Parameter | Type | Desc | Required |
| :---: | :---: | :---: | :---: |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |

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
| header.isSuccess | boolean | リクエスト成否 |
| header.resultCode | Integer | リクエスト結果コード |
| header.resultMessage | String | リクエスト結果メッセージ |
| data.appKey | String | 統合AppkeyまたはサービスAppkey |
| data.serviceUrl | String | 顧客サービスURL |
| data.webAuthUrl | String | ユーザー認証連動URL |
| data.webhookUrl | String | 不正行為を検知した時の送信URL |

## レスポンスコード

### 1. ResultCode

| Code | 区分 | 説明 |
| --- | --- | --- |
| 0 | 成功 | SUCCESS |
| -20001 | エラー | トークン満了時に発生 |
| -20002 | エラー | トークンが有効ではない場合に発生 |
| -40000 | エラー | 該当オプション未設定エラー |
| -40003 | エラー | 画像保存情報登録失敗 |
| -50000 | エラー | 無効な形式のファイル |
| -50001 | エラー | ファイル不足 |
| -50002 | エラー | ファイルサイズ超過(1GB超過) |
| -50004 | エラー | ファイルの処理中にエラー発生 |
| -50005 | エラー | 添付ファイルフィールド不足 |
| -99999 | エラー | サーバーエラー |

### 2. HttpStatusCode

| Code | 区分 | コード名 | 説明 |
| --- | --- | --- | --- |
| 200 | 正常 | Ok | 正常 |
| 400 | エラー | Bad Request | 無効なリクエストの場合に発生 |
| 500 | エラー | Server Error | サーバーがメンテナンス中または障害が発生している時に発生 |

## 顧客設定URL API

### 1. WebAuthURL

- ユーザー認証のために顧客のWebAuthUrlに認証リクエスト**(コンソールでWebAuthURL設定必須)**
- ユーザー\(志願者\)の状態を定期的に確認し、本人であることを確認

```
URL : {webAuthUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

#### Request

| body Parameter | Type | desc | Required |
| --- | --- | --- | :---: |
| userId | String | ユーザーID(受験生番号) | O |
| token | String | WebAuth認証トークン | O |
| via | String | その他情報 | X |
| validation | String | 有効性チェック | 0 |

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
| resultCode | Integer | 認証結果コード(0：成功、それ以外：失敗) |
| resultMessage | String | 認証結果メッセージ |

sample
成功

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```

失敗

``` json
{
    "resultCode": "40000",
    "resultMessage": "トークン満了"
}
```

### 2. WebHookURL

- 分析した画像および音声ファイルから不正行為を検知した時、チート情報を伝達**(コンソールでWebhook URL設定必須)**

```
URL : {webhookUrl}
METHOD : POST
Content-type : application/json;charset=utf-8
```

**Request(画像)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | 有効性チェック | O |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| userId | String | ユーザーID(受験生番号) | O |
| examNo | String | 試験番号 | O |
| cheatGroup | String | チートグループ(RPOCTOR, GAZE, POSE, BACKGROUND, AUDIO) | O |
| cheatLevel | Integer | 不正行為レベル(0: Normal,<span style="color:#000000">1: Attention\_Low, 2: Attention\_Hight, 3: Warning)</span> | O |
| eventTime | Long | イベント発生時間(timestamp) | O |
| fileUrl | String | 画像ファイルまたは音声ファイル保存パス | O |
| cheatData | JSON | 不正行為情報 | O |
| cheatData.cheatInfo | JSON | 不正行為判断結果 | O |
| cheatData.cheatInfo.absence | boolean | 不在かどうか<br />- 視線追跡(顔認識を行うかどうか) <br />- 行動検知(人の数) | X |
| cheatData.cheatInfo.thirdPerson | boolean | 第三者を識別するかどうか(視線追跡使用時) | X |
| cheatData.cheatInfo.facePoseYawOut | boolean | 顔上下角度離脱(顔検出使用時) | X |
| cheatData.cheatInfo.facePosePitchOut | boolean | 顔左右角度離脱(顔検出使用時) | X |
| cheatData.cheatInfo.eyeGazeYawOut | boolean | 視線上下角度離脱(視線追跡使用時) | X |
| cheatData.cheatInfo.eyeGazePitchOut | boolean | 視線左右角度離脱(視線追跡使用時) | X |
| cheatData.cheatInfo.eyeGazeScreenOut | boolean | 視線スクリーン離脱(事前視線登録完了時) | X |
| cheatData.cheatInfo.unstableBackground | boolean | 背景の変更(身体以外のバックグラウンド変化使用時) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | 左手識別を行うかどうか(行動検知使用時) | X |
| <span style="color:#000000">cheatData.cheatInfo.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span> | <span style="color:#000000">boolean</span> | 右手識別を行うかどうか(行動検知使用時) | X |
| cheatData.gaze | JSON | 視線追跡情報 | X |
| cheatData.gaze.numFaces | Integer | 検知された顔の数 | X |
| cheatData.gaze.facePitch | Integer | 顔の上下角度 | X |
| cheatData.gaze.faceYaw | Integer | 顔の左右角度 | X |
| cheatData.gaze.eyePitch | Integer | 視線の上下角度 | X |
| cheatData.gaze.eyeYaw | Integer | 視線の左右角度 | X |
| cheatData.gaze.screenX | float | 視線X軸位置 | X |
| cheatData.gaze.screenY | float | 視線Y軸位置 | X |
| [<span style="color:#000000">chaetData.bg</span>](http://chaetData.bg)[] | List | 背景変化情報 | X |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.isChanged</span> | <span style="color:#000000">boolean</span> | <span style="color:#6a8759"><span style="color:#000000">背景変化 </span></span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span> | <span style="color:#000000">Long</span> | <span style="color:#000000">発生時間(timstamp 10桁)</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data</span> | JSON | <span style="color:#000000">背景検知詳細情報</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.bgChangeDetFlag</span> | <span style="color:#000000">boolean</span> | <span style="color:#000000">背景変化検知結果</span> | <span style="color:#000000">X</span> |
| [<span style="color:#000000">chaetData.</span>](http://chaetData.bg)[<span style="color:#000000">bg</span>](http://chaetData.bg)[]<span style="color:#000000">.data.allocFlag</span> | <span style="color:#000000"></span><span style="color:#222222">boolean</span> | 背景画像スペース割り当て(false：背景検知不可) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.pose[]</span> | List | 行動検知情報 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">leftHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 左手識別を行うかどうか(行動検知使用時) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">rightHandNotExistence</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 右手識別を行うかどうか(行動検知使用時) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">eventTime</span></span><span style="color:#000000"></span> | Long | イベント発生時間(検知リクエスト時間) | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data</span></span><span style="color:#000000"></span> | JSON | 行動検知詳細情報 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.numPerson</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#6a8759"><span style="color:#000000">検知された人の数</span></span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand</span></span><span style="color:#000000"></span> | JSON | 左手座標情報 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">左手領域のバウンディングボックス(境界ボックス)上部の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">左手領域のバウンディングボックス下の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">左手領域のバウンディングボックス左の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">左手領域のバウンディングボックス右の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.lHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 左手検知を行うかどうか | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand</span></span><span style="color:#000000"></span> | JSON | 右手座標情報 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">右手</span> 領域のバウンディングボックス上の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">右手</span> 領域のバウンディングボックス下の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000"><span style="color:#000000">右手</span> 領域のバウンディングボックス左の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">右手領域のバウンディングボックス右の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.rHand.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 右手検知を行うかどうか | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face</span></span><span style="color:#000000"></span> | JSON | 顔座標情報 | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">顔領域のバウンディングボックス上の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymin</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">顔領域のバウンディングボックス下の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.xmax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">顔領域のバウンディングボックス左の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.ymax</span></span><span style="color:#000000"></span> | <span style="color:#000000">Integer</span> | <span style="color:#000000">顔領域のバウンディングボックス右の頂点の座標情報</span> | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatData.<span style="color:#000000">pose[]</span>.</span><span style="color:#9876aa"><span style="color:#000000">data.face.isDetected</span></span><span style="color:#000000"></span> | <span style="color:#000000">boolean</span> | 顔検出を行うかどうか | <span style="color:#000000">X</span> |
| <span style="color:#000000">cheatConfig</span> | JSON | 設定情報 | O |
| <span style="color:#000000">cheatConfig.pose.poseEstimationYn</span> | boolean | 行動検知を行うかどうか | X |
| <span style="color:#000000">cheatConfig.pose.poseEstimationTime</span> | Integer | 左手/右手座標未識別時間(N秒)<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTrackingYn</span> | String | 視線(瞳孔)追跡を行うかどうか<span style="color:#000000"></span> | X |
| <span style="color:#000000">cheatConfig.gaze.gazeTopAngle</span> | Integer | 瞳孔の角度(上)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeBottomAngle | Integer | 瞳孔の角度(下)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeLeftAngle | Integer | 瞳孔の角度(左)<span style="color:#000000"></span> | X |
| cheatConfig.gaze.gazeRightAngle | Integer | 瞳孔の角度(右)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionYn | String | 顔認識を行うかどうか<span style="color:#000000"></span> | X |
| cheatConfig.face.faceDetectionThreshold | Integer | 検知顔数の基準値<span style="color:#000000"></span> | X |
| cheatConfig.face.faceTopAngle | Integer | 顔の角度(上)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceBottomAngle | Integer | 顔の角度(下)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceLeftAngle | Integer | 顔の角度(左)<span style="color:#000000"></span> | X |
| cheatConfig.face.faceRightAngle | Integer | 顔の角度(右)<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionYn | String | 身体以外のバックグラウンド変化を検知するかどうか<span style="color:#000000"></span> | X |
| cheatConfig.bg.bgDetectionTime | Integer | バックグラウンド変化検知時間(N秒)<span style="color:#000000"></span> | X |

リクエストサンプル(Cheating Detection - 画像)

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

**Request(音声)**

| ResponseBody | Type | desc | Required |
| --- | --- | --- | :---: |
| validation | String | 有効性チェック | O |
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| userId | String | ユーザーID(受験生番号) | O |
| examNo | String | 試験番号 | O |
| cheatGroup | String | チート区分(DEVICE、IMAGE、AUDIO) | O |
| eventTime | Long | イベント発生時間(timestamp) | O |
| fileUrl | String | 画像ファイルまたは音声ファイル保存パス | O |
| cheatLevel | Integer | 音声検知を行うかどうか(0：未検知、1：検知) | O |
| cheatData | JSON | 検知情報 | X |
| cheatData.voice | Long[] | 音声検知時間(秒) <br>例) [3,4] > 3、4秒に音声検知 | X |

##### リクエストサンプル(Cheating Detection - 音声検知発生)

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

##### リクエストサンプル(Cheating Detection - 音声未発生)

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
| appKey | String | 統合AppkeyまたはサービスAppkey | O |
| userId | String | ユーザーID(受験生番号) | O |
| examNo | String | 試験番号 | O |
| cheatGroup | String | チート区分(PROCTOR、IMAGE、AUDIO) | O |
| platformOs | String | <span style="color:#222222">OS情報</span> | O |
| eventTime | Long | イベント発生時間(timestamp) | O |
| cheatLevel | Integer | チート発生(1)固定 | O |
| cheatData | JSON | 不正行為情報 | O |
| cheatData.keyboard | String | キーボードイベント | X |
| cheatData.mouse | String | マウスイベント | X |
| cheatData.additional | String | その他のイベント | X |
| cheatConfig | JSON | 設定情報 | O |
| cheatConfig.blockMonitorYn | String | 追加モニター遮断を行うかどうか | O |
| cheatConfig.blockSwitchTaskYn | String | タスクの切り替えを遮断するかどうか | O |
| cheatConfig.blockScreenYn | String | フルスクリーン離脱を防止するかどうか | O |
| cheatConfig.blockProgramYn | String | アプリケーション/プログラムを遮断するかどうか | O |

リクエストサンプル(Cheating Detection)

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
| resultCode | Integer | 認証結果コード(0：成功、それ以外：失敗) |
| resultMessage | String | 認証結果メッセージ |

sample

``` json
{
    "resultCode": "0",
    "resultMessage": "Success"
}
```
