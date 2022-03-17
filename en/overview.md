## Application Service > Cheating Detection > Overview


AI Proctor (face recognition, detection of background changes other than the body, body part detection, audio detection, eye tracking) and Application Proctor (blocking of additional monitors, full screen exit prevention, blocking of task switching, blocking of application/program) can be used to detect and control abnormal behaviors.

## <span style="color:#0052cc">Key Features</span>

Cheating Detection provides the following:

### **AI Proctor API**
Stores the images sent by the client in the Object Storage of the client, and send cheating events through the API based on the cheating detection criteria.

* Face recognition – number of faces, face angle (upper/lower, left/right)
* Audio detection – Determines whether to use audio detection
* Eye (pupil) tracking – eye (pupil) angle (upper/lower, left/right)
* Body part detection – Duration of not identifying body (right hand, left hand) coordinates
* Detection of changes other than the body -  detection time of background changes other than the body

### **Application Proctor SDK**
Can be implemented to integrate the Application Proctor SDK to the client’s system in order to control the users on the client's system.

* Additional monitor blocking – when additional monitors are detected in the user's environment, the second connected monitor is blacked out
* Full screen departure prevention – while using the NHN Proctor app, the user's browser is controlled as a full screen
* Task switching block – blocks task switching in order to prevent switching to other browsers or applications on the user's PC
* Application/program block – controls applications or programs running on the user’s PC

## <span style="color:#0052cc">Service target</span>

* In case a user wishes to implement an online testing service
* In case a user needs online supervision for real-time videos
* In case a user wishes to control the use of programs or applications of the user’s PC
* In case a user needs to monitor those working from home
* In case a user needs to monitor tasks dealing with important information

## <span style="color:#0052cc">Service use precautions</span>

* How to use AI Proctor
In order to use AI Proctor, storing data such as user images in the client’s Object Storage is necessary.
The user’s personal and sensitive data stored in Object Storage from the use of this service must be stored encrypted according to the Personal Information Protection Act.
(Separately encrypted storage required)

## <span style="color:#0052cc">Personal data processing guide</span>

* While using the AI Proctor, the client may collect personal information from users. Therefore, the customer of this service must inform a legal notice to their users as per the Personal Information Protection Act and acquire their consent regarding the matter.
Also during this process, work consignment relation regarding the processing of personal information may arise between the customer and NHN. The customer who assumes the position of consignor may enter into a consignment contract with the consignee, NHN, separately in writing, and post a privacy policy notice by referencing the following:

* Consignee: NHN Cloud
* Consignment description: providing Cheating Detection service


## <span style="color:#0052cc">Guide to collection of behavioral information online</span>

*	In the process of using the Cheating Detection service, NHN collects end users’ behavioral information online to provide customized services. Therefore, the client who use this service must notify end users of the following in the privacy policy, etc. in accordance with the relevant regulations, and the client is responsible for non-compliance of its users.
* The company allows the following advertising companies to collect user behavior information online and use it to provide customized services to users.

  *	A. Advertising company: NHN Cloud
  *	B. Collection method: collected through the program installed that is required to use the service
