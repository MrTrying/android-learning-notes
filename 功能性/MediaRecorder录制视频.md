# MediaRecorder实现录制视屏

## 一、简介

`MediaRecorder` 包含记录 `Audio` 和 `Video` 的功能。

## 二、使用流程

```
MediaRecorder recorder = new MediaRecorder();
recorder.setAudioSource(MediaRecorder.AudioSource.MIC);
recorder.setOutputFormat(MediaRecorder.OutputFormat.THREE_GPP);
recorder.setAudioEncoder(MediaRecorder.AudioEncoder.AMR_NB);
recorder.setOutputFile(PATH_NAME);
recorder.prepare();
recorder.start();   // Recording is now started
//...
recorder.stop();
recorder.reset();   // You can reuse the object by going back to setAudioSource() step
recorder.release(); // Now the object cannot be reused
```

![MediaRecorder使用流程图](https://raw.githubusercontent.com/MrTrying/android-learning-notes/master/_pic/mediarecorder_state_diagram.gif)

由上图可以看出 `MediaRecorder` 初始化之后，参数的设置有一定的顺序：
1.先设置 `setAudioSource` 和 `setVideoSource`
2.然后，设置`setAudioEncoder`、`setVideoEncoder`、`setOutputFile`、`setVideoSize`、`setVideoFrameRate`、`setPreviewDisplay`，到这里位置`MediaRecorder`的配置就完成了
3.调用`prepare()` 准备录制
4.调用`start()` 开始录制
5.调用`stop()`停止录制
6.调用`release()`释放资源

其中所有过程中，只有在调用`release()`之后，其他的状态都可以调用`reset()`方法重置`MediaRecorder`对象。

注：想知道更多如何使用`MediaRecorder`记录视频的的相关信息，请参考`Camera`的`API`文档；
想知道更多如何使用`MediaRecorder`记录声音的相关信息，请参考`Audio Capture`的`API`文档。

## 三、方法说明

- `setAudioChannels (int numChannels)`			设置录制的音频通道数
- `setAudioSource (int audio_source)`			设置用于录制的音源
- `setAudioEncodingBitRate(int bitRate)`		设置录制的音频编码比特率
- `setAudioSamplingRate(int samplingRate)`		设置录制的音频采样率
- `setAudioSource(int audio_source)`			设置用于录制的音源
- `setAuxiliaryOutputFile(String path)`			辅助时间的推移视频文件的路径传递
- `setAuxiliaryOutputFile(FileDescriptor fd)`	在文件描述符传递的辅助时间的推移视频
- `setCamera(Camera c)`							设置一个recording的摄像头
- `setCaptureRate(double fps)`	  				设置视频帧的捕获率
- `setMaxDuration(int max_duration_ms)`			设置记录会话的最大持续时间（毫秒）
- `setMaxFileSize(long max_filesize_bytes)`		设置记录会话的最大大小（以字节为单位）
- `setOutputFile(FileDescriptor fd)`			传递要写入的文件的文件描述符
- `setVideoSource (int video_source)`			设置用于录制的视频源
- `setAudioEncoder (int audio_encoder)`			设置Audio的编码格式
- `setVideoEncoder (int video_encoder)`			设置Video的编码格式
- `setVideoEncodingBitRate(int bitRate)`		设置录制的视频编码比特率
- `setOutputFile (String path)`					设置输出文件的路径
- `setVideoSize (int width,int height)`			设置要捕获视频的宽高
- `setVideoFrameRate (int rate)`				设置要捕获视频的帧速率
- `setPreviewDisplay (Surface sv)`				设置设置显示记录媒体预览的`Surface`

## 四、编码格式和资源说明

- 视频编码格式：`default`，`H263`，`H264`，`MPEG_4_SP`
- 获得视频资源：`default`，`CAMERA`
- 音频编码格式：`default`，`AAC`，`AMR_NB`，`AMR_WB`
- 获得音频资源：`defalut`，`camcorder`，`mic`，`voice_call`，`voice_communication`,`voice_downlink`,`voice_recognition`, `voice_uplink`
- 输出方式：`amr_nb`，`amr_wb`,`default`,`mpeg_4`,`raw_amr`,`three_gpp`