#### IJKPlayer
IJK集成播放器，拥有亮度调整、音量调整、视频全屏播放。

#### FIX - 2024.3.5.1
1.更新编译为ff4.0--ijk0.8.8--20210426--001版本;  
2.Android 11以上高版本报错问题;  
3.音量、光亮、进度调节逻辑修改;  


#### [AAR]
[ijk-2024.3.5.1.aar](https://github.com/RelinRan/IJKPlayer/blob/master/ijk-2024.3.5.1.aar)
```
android {
    ....
    repositories {
    flatDir {
            dirs 'libs'
        }
    }
}

dependencies {
    implementation(name: 'IJKPlayer', ext: 'aar')
}

```
#### JitPack
项目/build.grade
```
allprojects {
    repositories {
		...
		maven { url 'https://jitpack.io' }
	}
}
```
项目/app/build.grade
```
dependencies {
	    implementation 'com.github.RelinRan:IJKPlayer:2024.3.5.1'
	}
```
#### 权限配置
```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```
#### AndroidManifest.xml
播放页面
```
<activity
    android:name=".xxx"
    android:configChanges="keyboardHidden|orientation|screenSize"></activity>
```
Application
```
android:usesCleartextTraffic="true"
```
文件操作
```
<provider
    android:name="androidx.core.content.FileProvider"
    android:authorities="${applicationId}.fileProvider"
    android:exported="false"
    android:grantUriPermissions="true"
    android:permission="android.permission.MANAGE_DOCUMENTS">
    <meta-data
        android:name="android.support.FILE_PROVIDER_PATHS"
        android:resource="@xml/path" />
    <intent-filter>
        <action android:name="android.content.action.DOCUMENTS_PROVIDER" />
    </intent-filter>
</provider>
```
path.xml
```
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <root-path
        name="root"
        path="/storage/emulated/0" />
    <files-path
        name="files"
        path="/storage/emulated/0/Android/data/${applicationId}/files" />
    <cache-path
        name="cache"
        path="/storage/emulated/0/Android/data/${applicationId}/cache" />
    <external-path
        name="external"
        path="/storage/emulated/0/Android/data/${applicationId}/external" />
    <external-files-path
        name="Capture"
        path="/storage/emulated/0/Android/data/${applicationId}/files/Capture" />
    <external-cache-path
        name="Pick"
        path="/storage/emulated/0/Android/data/${applicationId}/files/Pick" />
    <external-cache-path
        name="TBS"
        path="/storage/emulated/0/Android/data/${applicationId}/files/TBS" />
</paths>
```

#### xml布局
```
<androidx.ijk.view.IJKVideoView
    android:id="@+id/ijk"
    android:layout_width="match_parent"
    android:layout_height="220dp"
    android:background="@color/colorBlack"/>
```
#### 参数配置
```
//初始化建议配置在Application
IJK ijk = IJK.config();
//启用硬解码器
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "mediacodec", 1);
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "mediacodec-auto-rotate", 1);
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "mediacodec-handle-resolution-change", 1);
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "opensles", 1);
//启用硬解码器（如果设备支持）
//ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "videotoolbox", 1);
//设置探测缓冲区大小
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "probesize", 1024 * 10);
//设置最小缓冲帧数
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "min-frames", 100);
//设置最大缓冲区大小（默认是0，表示无限制）
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "max-buffer-size", 0);
//设置最小缓冲帧数
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "min-frames", 5);
//设置超时时间
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "timeout", 10000);
//设置启动时的探测时间
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "analyzeduration", 100L);
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "analyzemaxduration", 100L);
//关闭播放缓冲区
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "packet-buffering", 0L);
//立刻写出处理完的Packet
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "flush_packets", 1L);
//允许丢帧
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "framedrop", 1L);
//调用prepareAsync()方法后是否自动开始播放
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "start-on-prepared", 1L);
//优化进度跳转
ijk.option(IjkMediaPlayer.OPT_CATEGORY_PLAYER, "enable-accurate-seek", 1);
ijk.option(IjkMediaPlayer.OPT_CATEGORY_FORMAT, "fflags", "fastseek");
```
#### 播放视频
```
IJKVideoView ijk = findViewById(R.id.ijk);
//是否是直播源
ijk.setLiveSource(true);
//自定义全屏还是小屏幕显示，不设置就采用默认的逻辑；
ijk.setOnIJKVideoSwitchScreenListener(orientation -> {
    //TODO: 自定显示方式 
});
//播放视频
String url = "http://xxx";
//开始播放
String source = ijk.getDataSource();
if (TextUtils.isEmpty(source)) {
    ijk.setDataSource(url);
    ijk.start();
} else {
    ijk.reset();
    ijk.setDataSource(url);
    ijk.prepareAsync();
}
```

#### 颜色配置
```
<!--Seek圆点颜色-->
<color name="ijk_seek_dot">#DDDDDD</color>
<!--进度条进度颜色-->
<color name="ijk_seek_progress">#03DAC5</color>
<!--进度条背景颜色-->
<color name="ijk_seek_background">#E8E8E9</color>
<!--进度条缓冲进度颜色-->
<color name="ijk_seek_secondary_progress">#F0F0F1</color>
<!--声音/亮度背景颜色-->
<color name="ijk_voice_brightness_background">#80000000</color>
<!--声音/亮度文字颜色-->
<color name="ijk_voice_brightness_text_color">#FFFFFF</color>
<!--声音/亮度进度颜色-->
<color name="ijk_voice_brightness_progress_color">#03DAC5</color>
```
#### 尺寸配置
```
<!--音量亮度进度线宽-->
<dimen name="ijk_voice_brightness_stroke_width">2dp</dimen>
<!--音量亮度进度距离padding-->
<dimen name="ijk_voice_brightness_padding">3dp</dimen>
<!--进度宽度-->
<dimen name="ijk_loading_width">60dp</dimen>
<!--进度高度-->
<dimen name="ijk_loading_height">60dp</dimen>
```