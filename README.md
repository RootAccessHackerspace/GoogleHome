# GoogleHome
Google Home

Username: rootaccess

Password: HACKTHEPLANET

Google Home Documentation:
https://home-assistant.io/blog/2017/05/13/home-assistant-on-orange-pi-zero/

Audio Configuration:
https://www.cnx-software.com/2017/07/30/how-to-setup-an-orange-pi-zero-diy-smart-speaker-with-google-assistant-sdk/

# Steps: 
(Steps are briefly taken from the [audio configuration](https://www.cnx-software.com/2017/07/30/how-to-setup-an-orange-pi-zero-diy-smart-speaker-with-google-assistant-sdk/#setting-up-google-assistant-on-orange-pi-zero) above)
1. [Create a project on the Google Cloud Platform](https://console.cloud.google.com/project)
1. [Enable the Assistant API](https://console.developers.google.com/apis/api/embeddedassistant.googleapis.com/overview)
1. [Create an Oath Client ID](https://console.developers.google.com/apis/credentials/oauthclient)
1. Download the .json file containing the client secrets.
1. find Orange Pi ip address: http://angryip.org/
1. Transfer the client secret .json to your Orange Pi.
2. On your machine: `ssh rootaccess@IPAddress`
# On the Orange Pi
3. Run the following block of commands:
```
sudo apt-get update
sudo apt-get install python3-dev python3-venv
python3 -m venv env
env/bin/python -m pip install --upgrade pip setuptools
source env/bin/activate
```
4. Make sure that “Web & App Activity” with “Include Chrome browsing history and activity from websites and apps that use Google services”  is checked, Device Information and Voice & Audio Activity are enabled in the  [Activity Control Page](https://myaccount.google.com/activitycontrols). This might already be set, depending on what you've done in the past.
5. Now run `python -m pip install --upgrade google-assistant-library` on the Orange Pi to install the Google Assistant SDK, and `python -m pip install --upgrade google-auth-oauthlib[tool]` to install the authorization tool.
6. Run the authorization tool: `google-oauthlib-tool --client-secrets ~/client_secret_xxxx.apps.googleusercontent.com.json --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless`, where the client_secret\*.json file is what you transfered to the Orange Pi.
7. Now you can run the assistant! `google-assistant-demo`
# Having the assistant run automatically
*/etc/systemd/system/google-assistant-demo.service*:
```
[Unit]
Description=google assistant service
After=network.target ntpdate.service
 
[Service]
Type = idle
Environment=VIRTUAL_ENV=/home/pi/env/
Environment=PATH=/home/pi/env/bin:/usr/local/sbin:/usr/local
ExecStart=/home/pi/env/bin/google-assistant-demo
WorkingDirectory=/home/pi
StandardOutput=inherit
StandardError=inherit
Restart=always
User=pi
 
[Install]
WantedBy=multi-user.target
Alias=google-assistant.service
```

Enable and start service:
```
sudo systemctl enable google-assistant-demo.service
sudo systemctl start google-assistant-demo.service
```
