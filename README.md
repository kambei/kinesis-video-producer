# VIDEO on Kinesis with Gstreamer

## Pre-requisites:

1. Following [this](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/examples-gstreamer-plugin.html#examples-gstreamer-plugin-docker) download the docker image:
```
docker pull 546150905175.dkr.ecr.us-west-2.amazonaws.com/kinesis-video-producer-sdk-cpp-amazon-linux:latest
```

<br>

---

## Without Dockerfile:

2. Run the image with: 
```
docker run -it --network="host" --device=/dev/video0 546150905175
.dkr.ecr.us-west-2.amazonaws.com/kinesis-video-producer-sdk-cpp-amazon-linux /bin/bash
```

3. In the container, set the environment variables using the following command:

```
export LD_LIBRARY_PATH=/opt/awssdk/amazon-kinesis-video-streams-producer-sdk-cpp/kinesis-video-native-build/downloads/local/lib:$LD_LIBRARY_PATH
export PATH=/opt/awssdk/amazon-kinesis-video-streams-producer-sdk-cpp/kinesis-video-native-build/downloads/local/bin:$PATH
export GST_PLUGIN_PATH=/opt/awssdk/amazon-kinesis-video-streams-producer-sdk-cpp/kinesis-video-native-build/downloads/local/lib:$GST_PLUGIN_PATH
```

4. Install brew:
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
And after the installation execute the suggested 3 commands!!!

4. Install gstream plugins (good and ugly):
```
brew install gst-plugins-good

brew install gst-plugins-ugly
```

5. Run the following command to start the stream (webcam is /dev/video0):
```
gst-launch-1.0 v4l2src do-timestamp=TRUE device=/dev/video0 ! videoconvert ! video/x-raw,format=I420,width=640,height=480,framerate=30/1 ! x264enc  bframes=0 key-int-max=45 bitrate=500 ! video/x-h264,stream-format=avc,alignment=au,profile=baseline ! kvssink stream-name="YOUR_STREAM_NAME" storage-size=512 access-key="YOUR_ACCESS_KEY" secret-key="YOUR_SECRET" aws-region="YOUR_REGION"
```

6. To stop the stream, press Ctrl+C


<br>

---

## With Dockerfile:

1. Following [this](https://docs.aws.amazon.com/kinesisvideostreams/latest/dg/examples-gstreamer-plugin.html#examples-gstreamer-plugin-docker) download the docker image:
```
docker pull 546150905175.dkr.ecr.us-west-2.amazonaws.com/kinesis-video-producer-sdk-cpp-amazon-linux:latest
```

2. Build the image with:
```
docker build -t kinesis-video-producer .
```

3. Run the image with: 
```
docker run -it --network="host" --device=/dev/video0 kinesis-video-producer /bin/bash
``` 

4. In the container, launch the video stream (from webcam to Kinesis):
```
gst-launch-1.0 v4l2src do-timestamp=TRUE device=/dev/video0 ! videoconvert ! video/x-raw,format=I420,width=640,height=480,framerate=30/1 ! x264enc  bframes=0 key-int-max=45 bitrate=500 ! video/x-h264,stream-format=avc,alignment=au,profile=baseline ! kvssink stream-name="YOUR_STREAM_NAME" storage-size=512 access-key="YOUR_ACCESS_KEY" secret-key="YOUR_SECRET" aws-region="YOUR_REGION"
```

<br>

---
