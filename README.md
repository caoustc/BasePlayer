# BasePlayer


# 1.添加依赖

```java
repositories {
    maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }

    maven { url 'https://dl.bintray.com/caoustc/maven/' }
}

基于FFMPEG4.0和androidx 
implementation 'com.caoustc.library:playerview:4.0.1.0'
implementation 'com.caoustc.library:ijkplayerbase:0.8.8.1'
七牛播放器（暂不可用）
implementation 'com.caoustc.library:qiniusdk:0.0.1.0'
ovoparkplayer
implementation 'com.caoustc.library:ovoparksdk:0.0.0.4'
exoplayer
implementation 'com.caoustc.library:exoplayer:0.0.0.4'


基于FFMPEG4.0版本
implementation 'com.caoustc.library:playerview:4.0.0.9'
implementation 'com.caoustc.library:ijkplayerbase:0.8.8.1'

七牛播放器
implementation 'com.caoustc.library:qiniusdk:0.0.0.4'
exoplayer
implementation 'com.caoustc.library:exoplayer:0.0.0.4'
ovoparkplayer
implementation 'com.caoustc.library:ovoparksdk:0.0.0.4'
```

更新历史
```
4.0.1.0
1.全部支持androidx
2.选择开启openeles解决部分9.0手机上出现的无法倍速播放的问题，但是会导致刚播放时，画面卡顿几秒，没有声音。大概3秒后恢复正常，4.0.0.2版本开启，在这个版本关闭，有相应需求的选择开启。猜测应该是视频在等音频。（一般从封装中读出的视频pts会大于音频pts，如果采取音频为主时钟，就需要视频重复已经播放的帧来等待音频。）
ps1：再出现前几秒卡顿fps低的情况，可以考虑开启KEY_VIDEO_AUDIO_ALIGNED，采用视频为主时钟，不过有可能导致音画不同步
ps2：使用七牛和exoplayer的时候没有这样的问题，猜测它们可能是使用的视频为主时钟

4.0.0.9
1.优化初始化方式，无需再调用初始化方法

4.0.0.8 
1.ijkplayer去除nobuff标识，加快首屏加载速度
```

```
4.0.0.6
1.修改ijkplayer源码为自定义ijkplayerbase，如使用官方版本请删除
2.添加加密rtsp播放支持，rtsp://用户名：密码@地址   PS：rtsp://admin:admin123@172.30.16.20
3.升级七牛播放器2.1.8版本,优化了啥我也母鸡，修复了设置参数没有生效的问题，添加每次播放reset播放器的逻辑，待验证是否可行
4.ijkplayer添加获取当前帧的方法getCurrentFrame()，仅软解可用
5.优化了ijkplayer首屏速度，PlayOptions.KEY_FIND_STREAM_MORE参数开启丢弃操作，加快速度
```

```
4.0.0.4
1.添加arm64-v8a
2.优化参数配置,针对rtsp首屏耗时做了部分处理
3.添加setPreload方法预加载，对点播有效果，直播效果可能较差

4.0.0.3
1.添加七牛播放器支持
2.优化ijkplayer播放器，添加部分参数配置
```

# 2.基本使用
Application里添加初始化ijkplayer播放器</br>
```java
初始化方式参见3.1
PlanManager.init(this);

//供2播放方式使用
//是否使用默认的网络状态事件生产者，默认否
PlanManager.setUseDefaultNetworkEventProducer(true);

```

### 1.添加BaseVideoView方式

```xml
<com.caoustc.video.playerbase.widget.BaseVideoView
        android:id="@+id/baseVideoView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="#000000"/>
```
开启播放，添加监听回调
```java
mVideoView.setDataUrl(DataUtils.VIDEO_URL_01);
mVideoView.start();

mVideoView.setOnPlayerEventListener(new OnPlayerEventListener() {
    @Override
    public void onPlayerEvent(int eventCode, Bundle bundle) {
        switch (eventCode) {
            case OnPlayerEventListener.PLAYER_EVENT_ON_VIDEO_RENDER_START:
                "start play"
                break;
            case OnPlayerEventListener.PLAYER_EVENT_ON_PLAY_COMPLETE:
                "complete play"
                break;
        }
    }
});

mVideoView.setOnErrorEventListener(new OnErrorEventListener() {
    @Override
    public void onErrorEvent(int eventCode, Bundle bundle) {

    }
});

mVideoView.start();

mVideoView.resume();

mVideoView.pause();

mVideoView.stop();

mVideoView.stopPlayback();

//设置请求头，ijkplayer内部已经做了处理
DataSource dataSource = new DataSource();
HashMap<String, String> headers = new HashMap<>();
headers.put("xxx", "xxx");
dataSource.setData(url);
dataSource.setExtra(headers);
mVideoView.setDataSource(dataSource);

//@ijkplayer setDataSource
while(var4.hasNext()) {
      Entry<String, String> entry = (Entry)var4.next();
      sb.append((String)entry.getKey());
      sb.append(":");
      String value = (String)entry.getValue();
      if (!TextUtils.isEmpty(value)) {
          sb.append((String)entry.getValue());
      }
      sb.append("\r\n");
      this.setOption(1, "headers", sb.toString());
      this.setOption(1, "protocol_whitelist", "async,cache,crypto,file,http,https,ijkhttphook,ijkinject,ijklivehook,ijklongurl,ijksegment,ijktcphook,pipe,rtp,tcp,tls,udp,ijkurlhook,data");
}
```

### 2.AVPlayer添加方式
//TODO

### 3.Ijkplayer参数设置
参照PlayOptions 参数配置
```java
public static final int MEDIA_CODEC_SW_DECODE = 0;
public static final int MEDIA_CODEC_HW_DECODE = 1;

public static final int IJK_LOG_UNKNOWN = 0;
public static final int IJK_LOG_DEFAULT = 1;
public static final int IJK_LOG_VERBOSE = 2;
public static final int IJK_LOG_DEBUG = 3;
public static final int IJK_LOG_INFO = 4;
public static final int IJK_LOG_WARN = 5;
public static final int IJK_LOG_ERROR = 6;
public static final int IJK_LOG_FATAL = 7;
public static final int IJK_LOG_SILENT = 8;

public static final String KEY_PREPARE_TIMEOUT = "timeout";
public static final String KEY_MEDIACODEC = "mediacodec";
public static final String KEY_RTSP_TRANSPORT = "rtsp_transport";
public static final String KEY_RECONNECT = "reconnect";
public static final String KEY_MAX_CDELAY = "max_delay";
public static final String KEY_FRAMEDROP = "framedrop";

//通过修改
public static final String KEY_PROBESIZE = "probesize";
public static final String KEY_ANALYZE_MAX_DURATION = "analyzemaxduration";
public static final String KEY_ANALYZE_DURATION = "analyzeduration";

public static final String KEY_LOG_LEVEL = "log-level";
public static final String KEY_MAX_BUFF_SIZE = "max_buffer_size";
public static final String KEY_SKIP_LOOP_FILTER = "skip_loop_filter";
public static final String KEY_MAX_FPS = "max_fps";
public static final String KEY_FPS = "fps";

public static final String KEY_KEEP_MEDIA_TYPE = "keep_media_type";

//如果遇到很长时间才能播放成功，可以尝试禁止解码音频或修改同步方式，不行就自求多福吧
public static final String KEY_DECODING_VIDEO = "decoding_video"; //视频解码开关，0解码，1否
public static final String KEY_DECODING_AUDIO = "decoding_audio"; //音频解码开关，0解码，1否
public static final String KEY_VIDEO_AUDIO_ALIGNED = "video_audio_align"; //音视频同步模式0，默认音频 1，视频同步模式

//强制开启live模式
bundle.putInt(PlayOptions.KEY_LIVE_MODEL, 0);

//以下只是参考，为功能开启的示例
Bundle bundle = new Bundle();
bundle.putInt(PlayOptions.KEY_LOG_LEVEL, PlayOptions.IJK_LOG_ERROR);
bundle.putInt(PlayOptions.KEY_MEDIACODEC, PlayOptions.MEDIA_CODEC_HW_DECODE);
bundle.putInt(PlayOptions.KEY_RECONNECT, 3);
bundle.putInt(PlayOptions.KEY_RTSP_TRANSPORT, 0);
bundle.putInt(PlayOptions.KEY_MAX_BUFF_SIZE, 1024);
bundle.putInt(PlayOptions.KEY_MAX_CDELAY, 5000);
bundle.putInt(PlayOptions.KEY_FRAMEDROP, 5);
bundle.putInt(PlayOptions.KEY_ANALYZE_MAX_DURATION, 50000);
bundle.putInt(PlayOptions.KEY_ANALYZE_DURATION, 50000);
bundle.putInt(PlayOptions.KEY_DECODING_AUDIO, 1);
bundle.putInt(PlayOptions.KEY_DECODING_VIDEO, 1);
bundle.putInt(PlayOptions.KEY_OPENSLES, 1);
bundle.putInt(PlayOptions.KEY_VIDEO_AUDIO_ALIGNED, 1);
bundle.putInt(PlayOptions.KEY_FIND_STREAM_MORE, 1);
mVideoView.option(IjkPlayer.PLAN_ID, bundle);

```

### 4.播放器常用设置
```java
切换textureview surfaceview模式
mVideoView.setRenderType(IRender.RENDER_TYPE_SURFACE_VIEW);
mVideoView.setRenderType(IRender.RENDER_TYPE_TEXTURE_VIEW);

//view圆角 圆形（5.0以上）
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
   mVideoView.setRoundRectShape(PUtil.dip2px(this, 25));
   mVideoView.setOvalRectShape();
}
//恢复原形
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
   mVideoView.clearShapeStyle();
}
//设置view画面
mVideoView.setAspectRatio(AspectRatio.AspectRatio_16_9);
mVideoView.setAspectRatio(AspectRatio.AspectRatio_4_3);
mVideoView.setAspectRatio(AspectRatio.AspectRatio_FILL_PARENT);
mVideoView.setAspectRatio(AspectRatio.AspectRatio_MATCH_PARENT);
mVideoView.setAspectRatio(AspectRatio.AspectRatio_FIT_PARENT);
mVideoView.setAspectRatio(AspectRatio.AspectRatio_ORIGIN);

//设置播放速度
mVideoView.setSpeed(1f);

//设置播放声音，只用于开启关闭声音，本地声音控制使用Android自带方法，参见GestureCover
mVideoView.setVolume(0f, 0f);

//预加载，类似处理方式，还可以考虑start之后pause的方式
（不建议使用）
mVideoView.setPreload(new DataSource(url));
new Handler().postDelayed(new Runnable() {
    @Override
    public void run() {
        mVideoView.start();
    }
}, 5000);
```

### 5.返回类型
```java
    /**
     * when decoder set data source
     */
    int PLAYER_EVENT_ON_DATA_SOURCE_SET = -99001;

    /**
     * when surface holder update
     */
    int PLAYER_EVENT_ON_SURFACE_HOLDER_UPDATE = -99002;

    /**
     * when surface update
     */
    int PLAYER_EVENT_ON_SURFACE_UPDATE = -99003;

    /**
     * when you call {@link IPlayer#start()}
     */
    int PLAYER_EVENT_ON_START = -99004;

    /**
     * when you call {@link IPlayer#pause()}
     */
    int PLAYER_EVENT_ON_PAUSE = -99005;

    /**
     * when you call {@link IPlayer#resume()}
     */
    int PLAYER_EVENT_ON_RESUME = -99006;

    /**
     * when you call {@link IPlayer#stop()}
     */
    int PLAYER_EVENT_ON_STOP = -99007;

    /**
     * when you call {@link IPlayer#reset()}
     */
    int PLAYER_EVENT_ON_RESET = -99008;

    /**
     * when you call {@link IPlayer#destroy()}
     */
    int PLAYER_EVENT_ON_DESTROY = -99009;

    /**
     * when decoder start buffering stream
     */
    int PLAYER_EVENT_ON_BUFFERING_START = -99010; //开始缓冲中 开始缓冲

    /**
     * when decoder buffering stream end
     */
    int PLAYER_EVENT_ON_BUFFERING_END = -99011; //缓冲结束

    /**
     * when decoder buffering percentage update
     */
    @Deprecated
    int PLAYER_EVENT_ON_BUFFERING_UPDATE = -99012;

    /**
     * when you call {@link IPlayer#seekTo(int)}
     */
    int PLAYER_EVENT_ON_SEEK_TO = -99013;

    /**
     * when seek complete
     */
    int PLAYER_EVENT_ON_SEEK_COMPLETE = -99014;

    /**
     * when player start render video stream
     */
    int PLAYER_EVENT_ON_VIDEO_RENDER_START = -99015; //视频开始，准备渲染

    /**
     * when play complete
     */
    int PLAYER_EVENT_ON_PLAY_COMPLETE = -99016;

    /**
     * on video size change
     */
    int PLAYER_EVENT_ON_VIDEO_SIZE_CHANGE = -99017;

    /**
     * on decoder prepared
     */
    int PLAYER_EVENT_ON_PREPARED = -99018;

    /**
     * on player timer counter update {@link TimerCounterProxy}
     * if timer stopped, you could not receive this event code.
     */
    int PLAYER_EVENT_ON_TIMER_UPDATE = -99019;

    /**
     * on get video rotation.
     */
    int PLAYER_EVENT_ON_VIDEO_ROTATION_CHANGED = 99020; //视频方向改变，视频选择信息

    /**
     * when player start render audio stream
     */
    int PLAYER_EVENT_ON_AUDIO_RENDER_START = -99021; //音频开始

    /**
     * when audio decoder start
     */
    int PLAYER_EVENT_ON_AUDIO_DECODER_START = -99022;

    /**
     * when audio seek rendering start
     */
    int PLAYER_EVENT_ON_AUDIO_SEEK_RENDERING_START = -99023;

    /**
     * network bandwidth
     * not support now
     */
    @Deprecated
    int PLAYER_EVENT_ON_NETWORK_BANDWIDTH = -99024; //网络带宽，网速方面，已废弃

    /**
     * bad interleaving
     */
    int PLAYER_EVENT_ON_BAD_INTERLEAVING = -99025;

    /**
     * not support seek ,may be live.
     */
    int PLAYER_EVENT_ON_NOT_SEEK_ABLE = -99026; //不可设置播放位置，直播方面

    /**
     * on meta data update
     */
    int PLAYER_EVENT_ON_METADATA_UPDATE = -99027;

    /**
     * Failed to handle timed text track properly.
     */
    int PLAYER_EVENT_ON_TIMED_TEXT_ERROR = -99028;

    /**
     * Subtitle track was not supported by the media framework.
     */
    int PLAYER_EVENT_ON_UNSUPPORTED_SUBTITLE = -99029; //不支持字幕

    /**
     * Reading the subtitle track takes too long.
     */
    int PLAYER_EVENT_ON_SUBTITLE_TIMED_OUT = -99030;  //字幕超时

    /**
     * on play status update
     */
    int PLAYER_EVENT_ON_STATUS_CHANGE = -99031;


    /**
     * if you set data provider for player, call back this method when provider start load data.
     */
    int PLAYER_EVENT_ON_PROVIDER_DATA_START = -99050;

    /**
     * call back this method when provider load data success.
     */
    int PLAYER_EVENT_ON_PROVIDER_DATA_SUCCESS = -99051;

    /**
     * call back this method when provider load data error.
     */
    int PLAYER_EVENT_ON_PROVIDER_DATA_ERROR = -99052;

    int PLAYER_EVENT_AV_READ_FRAME_ERROR = -90001; //解析视频帧出现问题，会出现卡住或者跳帧，只做提醒

    int PLAYER_EVENT_ON_STARTED_AS_NEXT = -90002;//播放下一条

    int PLAYER_EVENT_ON_VIDEO_TRACK_LAGGING = -90004;//视频日志跟踪
```

参照Ijkplayer的错误码
```java
    int ERROR_EVENT_DATA_PROVIDER_ERROR = -88000;//获取播放地址出错

    //A error that causes a play to terminate
    int ERROR_EVENT_COMMON = -88011;  //其他错误都会归到common中

    int ERROR_EVENT_UNKNOWN = -88012; //未知错误

    int ERROR_EVENT_SERVER_DIED = -88013;//服务挂掉，视频中断，一般是视频源异常或者不支持的视频类型。

    int ERROR_EVENT_NOT_VALID_FOR_PROGRESSIVE_PLAYBACK = -88014;//数据错误,没有有效的回收

    int ERROR_EVENT_IO = -88015;//IO 错误

    int ERROR_EVENT_MALFORMED = -88016;

    int ERROR_EVENT_UNSUPPORTED = -88017;  //数据不支持

    int ERROR_EVENT_TIMED_OUT = -88018;    //数据超时

    int ERROR_EVENT_MEDIA_INTERRUPT = -10000; //播放出错，很多原因都能导致，比如码流解析，网络问题等等
```

# 3.其他功能使用
## 1.解码器的切换
（已废弃）

暂时添加了SysMediaPlayer Ijkplayer Exoplayer Ovoparkplayer QiniuPlayer五种模式</br>
SysMediaPlayer默认存在，无需add</br>
暂只支持切换为统一的播放器模式，不支持多播放器同时存在

```java
设置默认播放器模式
PlanManager.setDefaultPlanId(IjkPlayer.PLAN_ID);
```

```

### 1.1七牛播放器使用</br>
部分七牛自己view的方法不再提供，一些配置参照七牛文档
[link](https://developer.qiniu.com/pili/sdk/1210/the-android-client-sdk#5)</br>
AVOptions设置沿用option方法，传递字段与七牛文档相同，可自定义配置
info error回调同上，部分返回码使用统一返回码，不常使用的使用七牛自己的返回码
```java
默认配置
AVOptions options = new AVOptions();
//sb 七牛 2.1.6设置自动会黑屏
//options.setInteger(AVOptions.KEY_MEDIACODEC, AVOptions.MEDIA_CODEC_AUTO);
options.setInteger(AVOptions.KEY_FAST_OPEN, 1);
options.setInteger(AVOptions.KEY_OPEN_RETRY_TIMES, 5);
options.setInteger(AVOptions.KEY_LOG_LEVEL, 2);
options.setInteger(AVOptions.KEY_PREPARE_TIMEOUT, 10 * 1000);
options.setInteger(AVOptions.KEY_CACHE_BUFFER_DURATION, 500);
options.setInteger(AVOptions.KEY_MAX_CACHE_BUFFER_DURATION, 4000);
```
```java
Bundle bundle = new Bundle();
bundle.putString(AVOptions.KEY_DNS_SERVER, "");
// DNS 缓存设置
// 若不设置此项，则每次播放未缓存的域名时都会进行 DNS 解析，并将结果缓存
// 参数为 String[]，包含了要缓存 DNS 结果的域名列表
// SDK 在初始化时会解析列表中的域名，并将结果缓存
bundle.putStringArray(AVOptions.KEY_DOMAIN_LIST, "");
// 解码方式:
// codec＝AVOptions.MEDIA_CODEC_HW_DECODE，硬解
// codec=AVOptions.MEDIA_CODEC_SW_DECODE, 软解
// codec=AVOptions.MEDIA_CODEC_AUTO, 硬解优先，失败后自动切换到软解
// 默认值是：MEDIA_CODEC_SW_DECODE
bundle.putInt(AVOptions.KEY_MEDIACODEC, "");
// 若设置为 1，则底层会进行一些针对直播流的优化
bundle.putInt(AVOptions.KEY_LIVE_STREAMING, "");
// 快开模式，启用后会加快该播放器实例再次打开相同协议的视频流的速度
bundle.putInt(AVOptions.KEY_FAST_OPEN, "");
// 打开重试次数，设置后若打开流地址失败，则会进行重试
bundle.putInt(AVOptions.KEY_OPEN_RETRY_TIMES, "");
// 预设置 SDK 的 log 等级， 0-4 分别为 v/d/i/w/e
bundle.putInt(AVOptions.KEY_LOG_LEVEL, "");
// 打开视频时单次 http 请求的超时时间，一次打开过程最多尝试五次
// 单位为 ms
bundle.putInt(AVOptions.KEY_PREPARE_TIMEOUT, "");
// 默认的缓存大小，单位是 ms
// 默认值是：500
bundle.putInt(AVOptions.KEY_CACHE_BUFFER_DURATION, "");
// 最大的缓存大小，单位是 ms
// 默认值是：2000，若设置值小于 KEY_CACHE_BUFFER_DURATION 则不会生效
bundle.putInt(AVOptions.KEY_MAX_CACHE_BUFFER_DURATION, "");
// 是否开启直播优化，1 为开启，0 为关闭。若开启，视频暂停后再次开始播放时会触发追帧机制
// 默认为 0
bundle.putInt(AVOptions.KEY_LIVE_STREAMING, "");
// 设置拖动模式，1 位精准模式，即会拖动到时间戳的那一秒；0 为普通模式，会拖动到时间戳最近的关键帧。默认为 0
bundle.putInt(AVOptions.KEY_SEEK_MODE, "");
// 设置 DRM 密钥
bundle.putByteArray(AVOptions.KEY_DRM_KEY, "");
// 设置偏好的视频格式，设置后会加快对应格式视频流的打开速度，但播放其他格式会出错
// m3u8 = 1, mp4 = 2, flv = 3
bundle.putInt(AVOptions.KEY_PREFER_FORMAT, "");
// 开启解码后的视频数据回调
// 默认值为 0，设置为 1 则开启
bundle.putInt(AVOptions.KEY_VIDEO_DATA_CALLBACK, "");
// 开启解码后的音频数据回调
// 默认值为 0，设置为 1 则开启
bundle.putInt(AVOptions.KEY_AUDIO_DATA_CALLBACK, "");
// 设置开始播放位置
// 默认不开启，单位为 ms
bundle.putInt(AVOptions.KEY_START_POSITION, "");
```
```java
获取七牛player实例，配置其他方法
PLMediaPlayer mediaPlayer = ((QiniuPlayer)mVideoView.getPlayer().getInternalPlayer()).getMediaPlayer();
```


## 2.组件视图
添加自定义组件，也可以自行实现控制
LoadingCover，ControllerCover，GestureCover，CompleteCover，ErrorCover等
```java
//添加
ReceiverGroup receiverGroup = new ReceiverGroup(groupValue);
receiverGroup.addReceiver(KEY_LOADING_COVER, new LoadingCover(context));
receiverGroup.addReceiver(KEY_CONTROLLER_COVER, new ControllerCover(context));
receiverGroup.addReceiver(KEY_GESTURE_COVER, new GestureCover(context));
receiverGroup.addReceiver(KEY_COMPLETE_COVER, new CompleteCover(context));
receiverGroup.addReceiver(KEY_ERROR_COVER, new ErrorCover(context));

mVideoView.setReceiverGroup(ReceiverGroup);

//移除
mReceiverGroup.removeReceiver(DataInter.ReceiverKey.KEY_CONTROLLER_COVER);

```
自定义覆盖层cover组件，继承自基类BaseCover
```java
public class CustomCover extends BaseCover {
    public CustomCover(Context context) {
        super(context);
    }

    @Override
    public void onPlayerEvent(int eventCode, Bundle bundle) {
        //...
    }
    
    @Override
    public void onErrorEvent(int eventCode, Bundle bundle) {
        //...
    }

    @Override
    public void onReceiverEvent(int eventCode, Bundle bundle) {
	//...
    }

    @Override
    public View onCreateCoverView(Context context) {
        return View.inflate(context, R.layout.layout_custom_cover, null);
    }
    //......	
}
```

## 3.多路视频播放
### 1.多播放器View模式
```java
//覆写BaseVideoView 实现播放和停止的时机
mRecycler = findViewById(R.id.recycler);

mRecycler.setHasFixedSize(true);
int column = 2;
mRecycler.setLayoutManager(new GridLayoutManager(this, column, LinearLayoutManager.HORIZONTAL, false));
int mScreenUseW = PUtil.getScreenW(this);
mAdapter = new GridVideoAdapter(this, mScreenUseW / column);
mAdapter.setListView(mRecycler);
mAdapter.setOnListListener(this);
mRecycler.setItemViewCacheSize(8);

ViewGroup.LayoutParams params = mRecycler.getLayoutParams();
//params.width = mScreenUseW;
params.height = mScreenUseW;
mRecycler.setLayoutParams(params);

GridPagerSnapHelper helper = new GridPagerSnapHelper();
helper.setRow(column).setColumn(column);
helper.attachToRecyclerView(mRecycler);

List<VideoBean> dataList = DataUtils.getVideoList();
dataList = GridPagerUtils.transformAndFillEmptyData(new TwoRowDataTransform<VideoBean>(column), dataList);

mAdapter.setList(dataList);
mRecycler.setAdapter(mAdapter);

```
```java
GridPagerSnapHelper 使用RecyclerView实现一屏多个Item的效果
TwoRowDataTransform，ThreeRowDataTransform修改数据顺序
grid默认排序
1 4 7
2 5 8
3 6 9
修改数据为正常浏览顺序
1 2 3
4 5 6
7 8 9

其他顺序继承AbsRowDataTransform实现
```

### 2.多播放器切换view模式
```java
播放器切换
mVideoView.switchDecoder(PlanConstants.PLAN_ID_IJK);
mVideoView.switchDecoder(PlanConstants.PLAN_ID_EXO);
mVideoView.switchDecoder(PlanConstants.PLAN_ID_OVOPARK);
mVideoView.switchDecoder(PlanConstants.PLAN_ID_QINIU);
```
