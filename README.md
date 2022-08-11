# BigEyes - The only surveillance system you'll ever need

### BigEyes is a simple and extremely customizable surveillance system. It recognizes unauthorized faces entering its field of view and alerts you accordingly.

### It is your personal CCTV system, and you can use it to monitor private rooms in your home, office, or any other place you want.

## Setup

Just run all the cells in `main.ipynb` in order!<br>
The last cell runs the bot, and the previous cells are for the setup, config, variables, modules, functions etc.

## What you can do with it

* Type "help" to see a list of commands and features.
----
* You can set up basic configuration by typing "config". You need to set this up before you can start activating the camera and detect people.
* This will ask you for your unauthorized intruder folder, in which all the pictures of the unauthorized people will be saved in the form `/path/to/that/folder/DD-MM-YYYY at HH-MM-SS.jpg` (24-hr formatted time as the picture's name).
* It will then ask you if it should audibly warn any unauthorized people coming in the area to walk out. You can answer with `yes`/`y` or `no`/`n`. If you answer with `yes`/`y`, it will play the recording `unauthorized.mp3` in this folder when an intruder walks in, otherwise it will not.
* Then it will ask you for how strict the face recognition should be - it's tolerance. You can reply with any number (decimals included) within 1-10. The lower the number, the stricter the recognition. The default is 6.
* If BigEyes is recognizing authorized people as intruders, you may want to lower the tolerance and if intruders are being recognized as authorized people, you may want to turn the tolerance higher.
----
* You can add authorized people to the system by typing "add" or "new". This will ask you for the name (unique identification) of the person you want to add, and then it will ask you for a file path to the picture of the person.
* If you add a picture with less than 1 or more than 1 detected faces, BigEyes will draw rectangles around the faces and show it to you, indicating that the picture is not a valid face picture. You need to enter a picture with only 1 face in it.
* The picture will then get saved in the `authorized_users` folder with the inputted name.
----
* You can setup webhooks using "webhook". This will ask you for the URL of the webhook.
* You can get a brief guide on how to set up webhooks and what a webhook is by typing "webhook help".
* Webhooks are a tool of the internet, served over HTTP/HTTPS. There are many types of HTTP requests like `GET`, `POST`, `PUT`, `DELETE`, etc. `GET` is the type of request you do to a website when you want to see its contents eg. going to Google through a browser, going to YouTube to watch videos etc.
* Webhooks deal with `POST` type requests, and they are used to *send* data to a website instead of recieve data. If you have a webhook setup, BigEyes sends a `POST` request to the webhook URL whenever it detects an unauthorized person. It sends the POST request with some JSON (JavaScript Object Notation) data, which is the data that BigEyes sends to the webhook. Essentially, the data that BigEyes will send on that webhook URL is a dictionary with this format:
```json
{
    "datetime": "DD-MM-YYYY at HH-MM-SS",
    "imagepath": "/YOUR/ALERTS/FOLDER/PATH/DD-MM-YYYY at HH-MM-SS.jpg",
}
```
`DD-MM-YYYY at HH-MM-SS` is the date and time of the alert in 24 hour format. `/YOUR/ALERTS/FOLDER/PATH/` is the path of your alerts folder you set during the initial basic configuration.
* Do you know what this means for us? It means that if, with a little bit of coding skills, you setup a webhook URL which could detect `POST` requests, you could code that webhook to do anything you want when an unauthorized person enters the area!
* You could code your website to turn on an internet-powered alarm/light in your house, send the data of the unauthorized user elsewhere etc. **With this feature, YOU have the power to punish the intruder in whichever tech-savvy way you prefer.**
----
* All that's left for you to do now is activate the camera using the "activate" command. Note that this command will not work if you haven't setup configuration. You can place your device right behind your door so that whenever someone enters, their face gets caught right on camera for detection!
* Upon a person walking in, if BigEyes detects the person as a familiar and authorized person, then nothing happens. The camera continues on as usual.
* Otherwise if BigEyes detects the person as an intruder, a series of events take place.
* First, the camera will take a picture of the intruder, draw a rectangle around the face and save it in your alerts folder as `DD-MM-YYYY at HH-MM-SS.jpg` (date and time, 24-hr format).
* Second, it gives you a system notification about that pesky intruder.
* Third, if you have a webhook setup, a POST request will be sent to the webhook to alert it.
* Fourth, if you chose "yes" to verbally alert the intruder, the system will play the recording `unauthorized.mp3` to scare them away! This recording plays over and over again till the camera no longer detects them.

## Technical details

* Its dependencies are `opencv-python` to use the camera, `face_recognition` to help recognize faces, `requests` to send HTTP requests to a setup webhook if any, `plyer` and `pyobjus` for system notifications and `playsound` to play `unauthorized.mp3` (if your config says so, of course).
* When the camera is activated, the live video frames are not shown on screen to prevent unnecessary lag. Instead the camera works in the background.
* Upon importing the modules and assigning variables, a list called `known_encodings` is created with all the mathematical details of the known faces in the `authorized_users` folder (distance between eyes, shape of nose, lips etc). This is used to recognize the faces in the camera's field of view by comparing it to the known faces.
* User configuration is stored in the file `config.json` so you can enter the configuration just once and use the same configuration again everytime you run BigEyes. When the config has not been setup, the file just contains an empty dictionary `{}`.
* `haarcascade_frontalface_default.xml` is a file storing a frontal face detector made by OpenCV. This is the brain of this model's face recognition.