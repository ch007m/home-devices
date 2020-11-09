## Volumio

### YouTube

Review discussion and explanation to set up the Google Developer API Key/token which is needed:
https://community.volumio.org/t/plugin-youtube-for-volumio/5990/270

#### Step by step instructions using official plugin

- Get the youtube plugin: 

    `wget http://volumio.github.io/volumio-plugins/plugins/volumio/armhf/music_service/youtube/youtube.zip`

- Now we need to create a folder:

    `mkdir youtube`

- Now we are going to unzip the compressed file we just downloaded:

    `miniunzip youtube.zip -d youtube`

- Now change directory:

    `cd youtube`

- From here we can [install the plugin](https://volumio.github.io/docs/Plugin_System/Plugin_System_Overview.html) with the following command:
  **Remark**: If the plugin has already been installed, remove the entry from the plugins config file `/data/configuration/plugins.json`
  , next remove the folder `/data/plugins/music_service/youtube/` and dont forget to reboot.
  ```
  volumio plugin install
  This command will install the plugin on your device
  
  Compressing the plugin
  Plugin succesfully compressed
  Progress: 10
  Status :Downloading plugin
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip
  Progress: 30
  Status :Creating install location
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location
  Progress: 40
  Status :Unpacking plugin
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin
  Progress: 50
  Status :Checking for duplicate plugin
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin
  Progress: 60
  Status :Copying plugin to location
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin<br>Copying plugin to location
  Progress: 70
  Status :Installing necessary utilities
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin<br>Copying plugin to location<br>Installing necessary utilities
  Progress: 70
  Status :Installing necessary utilities
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin<br>Copying plugin to location<br>Installing necessary utilities<br>Installing youtube
  Progress: 90
  Status :Finalizing installation
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin<br>Copying plugin to location<br>Installing necessary utilities<br>Installing youtube<br>Finalizing installation
  Progress: 100
  Status :Youtube Playback Successfully Installed, Do you want to enable the plugin now?
  Downloading plugin at http://127.0.0.1:3000/plugin-serve/youtube.zip<br>END DOWNLOAD: http://127.0.0.1:3000/plugin-serve/youtube.zip<br>Creating install location<br>Unpacking plugin<br>Checking for duplicate plugin<br>Copying plugin to location<br>Installing necessary utilities<br>Installing youtube<br>Finalizing installation<br>Finalizing installation
  ```
- Now we need to update the node package - `ytdl-core`: 
  
  ```
  cd /data/plugins/music_service/youtube/
  npm install ytdl-core@latest
  npm WARN youtube@1.0.1 No repository field.
  
  + ytdl-core@4.0.3
  updated 4 packages in 16.455s
  ```
  
- Enable the Youtube the plugin using Volumio UI
- Click on the `log` button, and you will be redirected to Google to select/authorize your GMail account  

#### Step by step instructions using forked Youtube plugin

The previous instructions relies on the official plugin managed under the following github [project](https://github.com/volumio/volumio-plugins/tree/master/plugins/music_service/youtube)
and where the Google API Youtube node package is used to OAuth the user using your [Google Account](https://github.com/volumio/volumio-plugins/blob/master/plugins/music_service/youtube/index.js#L923)

Due to recent changes to the way that Youtube accepts remote applications to access their platform, the approach described with the official Volumio plugin will fail and this 
is the reason why it is needed to use a Google API key/ClientID/SecretID authorized to access the following scope:
```
YouTube Data API v3	.../auth/youtube.readonly	View your YouTube account
``` 

**Instructions**

```bash
ssh volumio@192.168.1.100
volumio@192.168.1.100's password:
                       ___
                      /\_ \                        __
         __  __    ___\//\ \    __  __    ___ ___ /\_\    ___
        /\ \/\ \  / __`\\ \ \  /\ \/\ \ /' __` __`\/\ \  / __`\
        \ \ \_/ |/\ \L\ \\_\ \_\ \ \_\ \/\ \/\ \/\ \ \ \/\ \L\ \
         \ \___/ \ \____//\____\\ \____/\ \_\ \_\ \_\ \_\ \____/
          \/__/   \/___/ \/____/ \/___/  \/_/\/_/\/_/\/_/\/___/

             Free Audiophile Linux Music Player - Version 2.0

          C 2015 Michelangelo Guarise - Volumio Team - Volumio.org


Volumio Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.

wget https://github.com/patrickkfkan/volumio-plugins/archive/master.zip
mkdir plugins
miniunzip master.zip -d plugins
cd plugins/volumio-plugins-master/plugins/music_service/
volumio plugin install

cd /data/plugins/music_service/youtube/
npm install ytdl-core@latest
```