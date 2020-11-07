## Volumio

### YouTube

Review discussion and explanation to set up the Google Developer API Key/token which is needed:
https://community.volumio.org/t/plugin-youtube-for-volumio/5990/270

Step by step instructions

- Get the youtube plugin
    
    wget http://volumio.github.io/volumio-plugins/plugins/volumio/armhf/music_service/youtube/youtube.zip

- Now we need to create a folder:
    
    mkdir youtube

Now we are going to unzip the compressed file we just downloaded:
miniunzip youtube.zip -d youtube

Now change directory:
cd ./youtube

From here we cann install the plugin with the following command:
volumio plugin install
```