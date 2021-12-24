## Application Service > Cheating Detection > Console User Guide
## How to sign up for the service
* The screen above appears when the Enable Service button is clicked
* The project can be set up on each setting tab after enabling the service.
![Screen shot 2021-07-12 오후 1 48 04](https://user-images.githubusercontent.com/1445289/125233164-fe4b4c80-e318-11eb-8f39-dd962cf24880.png)

## AI Proctor
### Face detection
* The cheating criteria can be set when using the face detection.
* Please select on/off to use the features you want.
* The default value can be modified from the original value if necessary, and the change will be applied upon modification.
![Screen shot 2021-06-28 오후 6 36 04](https://user-images.githubusercontent.com/1445289/123615143-fcc15500-d83f-11eb-9459-df0b1b4681e3.png)



### Eye (pupil) tracking
* When using the eye (pupil) tracking feature, you can set the criteria for cheating. A cheat event will be forwarded after detecting the up/down/left/right slant from the center of the eye.
* Please select on/off to use the features you want.
* The default value can be modified from the original value if necessary, and the change will be applied upon modification.
![Screen shot 2021-06-28 오후 6 36 09](https://user-images.githubusercontent.com/1445289/123615152-ff23af00-d83f-11eb-8bf3-605731d934a9.png)


### Body part detection
* The cheating criteria can be set when using the body part detection.
* Please select on/off to use the features you want.
* The default value can be modified from the original value if necessary, and the change will be applied upon modification.
![Screen shot 2021-06-28 오후 6 36 15](https://user-images.githubusercontent.com/1445289/123615192-06e35380-d840-11eb-80d6-c8e3a6fa5b33.png)


### Detection of changes other than the body
* The cheating criteria can be set when using the detection of background changes other than the body.
* Please select on/off to use the features you want.
* The default value can be modified from the original value if necessary, and the change will be applied upon modification.
![Screen shot 2021-06-28 오후 6 36 20](https://user-images.githubusercontent.com/1445289/123615205-0b0f7100-d840-11eb-8e81-fcf702779380.png)


### Audio detection
* The audio can be detected when using the audio detection.
* Please select on/off to use the features you want.
* The default value can be modified from the original value if necessary, and the change will be applied upon modification.
![Screen shot 2021-06-28 오후 6 36 24](https://user-images.githubusercontent.com/1445289/123615228-0ea2f800-d840-11eb-8b8b-fdb76a0498dd.png)

## Settings
### Basic settings
* Set the basic information of the service
* Service URL
  * The customer service URL to move from the Cheating Detection service.
  * The link with the relevant information gets delivered to the proctor app.

* Web Auth URL : URL to link for user authentication.
* Web hook URL: The web hook that receives an event when a cheating activity occurs.
![Screen shot 2021-06-28 오후 6 31 04](https://user-images.githubusercontent.com/1445289/123615256-15316f80-d840-11eb-88ce-6bd72803b0cf.png)


### Image storage setting
* Setting to store the images discovered through cheating detection
* Set up after enabling Object Storage in advance.
![Screen shot 2021-06-28 오후 6 30 49](https://static.toastoven.net/prod_cheating_detection/chd_imgsave.png)
* The following describes how to save/download logs using [NHN Cloud Object Storage](/Storage/Object%20Storage/en/Overview/)
   * **Access key** and **Secret key** can be verified with **Register and View EC2 Credential** using [AWS S3 API](/Storage/Object%20Storage/en/s3-api-guide/#_1).
   * **Bucket name** is the name of the object storage container where logs will be saved.
   * **Endpoint** and **Region** are information that manages the object storage where logs will be saved. For more information, see [Amazon S3 Compatibility API Guide - AWS SDK](/Storage/Object%20Storage/en/s3-api-guide#aws-sdk)
   * Once setup is complete, images will begin to be saved in the object storage.
   * After three or more failed upload attempts, the saved authentication information will be disabled. The details will be sent to the email address registered in the **Receive Result** (email) field.