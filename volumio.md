## Volumio

### YouTube

Review discussion and explanation to set up the Google Developer API Key/token which is needed:
https://community.volumio.org/t/plugin-youtube-for-volumio/5990/270

Step by step instructions

- Get the youtube plugin: 
    `wget http://volumio.github.io/volumio-plugins/plugins/volumio/armhf/music_service/youtube/youtube.zip`
- Now we need to create a folder:
    `mkdir youtube`
- Now we are going to unzip the compressed file we just downloaded:
    `miniunzip youtube.zip -d youtube`

- Now change directory:
    `cd youtube`

- From here we cann install the plugin with the following command:
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