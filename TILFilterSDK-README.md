## 特效功能(美颜、美白、瘦脸、大眼、动效贴纸)

>* 功能说明
>* 费用说明
>* 工程配置
>* 功能调用
>* 集成到ILiveSDK

### 功能说明

sdK提供美颜、美白、红润、滤镜、大眼、瘦脸、动效贴纸、绿幕等功能，其中大眼、瘦脸、动效贴纸等以人脸识别为基础的功能是基于优图实验室的人脸识别技术和天天P图的美妆技术为基础开发的特权功能，通过跟优图和P图团队合作，将这些特效深度整合到图像处理流程中，以实现更好的视频特效。

### 费用说明

美颜、美白、红润等基础功能是免费的。基于人脸识别的功能由于采用了优图实验室的专利技术，授权费用约 50W/年（目前国内同类图像处理产品授权均在百万左右）。如有需要可以提工单或联系我们（louishliu QQ:7399644 ），商务同学会提供P图SDK，并替您向优图实验室申请试用 License。

### 工程配置

下载sdk

[点击此处下载TXMVideoPreprocessor（包含基础版本、高级版本）](http://dldir1.qq.com/hudongzhibo/ILiveSDK/TXMVideoPreprocessor_3.3.0.zip)

最新版本说明

> V3.3.0(2017-09-13)</br>
(1) 修复使用动效或者人脸识别功能导致图像压缩问题</br>
(2) 支持全架构</br>
(3) 增加渲染功能（如果不需要删除对应的.a即可）</br>
[查看更多版本更新记录](https://github.com/zhaoyang21cn/ILiveSDK_iOS_Suixinbo/blob/master/doc/TILFilterSDK_ChangeList.md)

</br>


引入依赖库

| | | 
:-----:|:-----:|
![](https://mc.qcloudimg.com/static/img/59cd6dad30234f60fc2bbb6006c98360/preprocessor_advance_project.png)|![](https://mc.qcloudimg.com/static/img/a6be8bfa51022800c8e5fb2022444d78/preprocessor_basic.png)|
高级版本|基础版本|


> 温馨提示：</br>
(1) 如果不使用滤镜，可以删除TXLiteAVVideoPreprocessorResource.bundle</br>
(2) 高级版本需要申请licence并拖到工程目录下，licence不能重命名，必须为YTFaceSDK.licence</br>
(3) 如需要更多动效资源，请联系商务，并将资源添加到Resource文件夹下</br>



工程配置
> 基础版本无须做工程配置，用默认配置即可</br>

| |
:-----:|
![](https://mc.qcloudimg.com/static/img/b164f01a39ec54e53c1b084d8e8308e5/preprocess_advance_config.png)|
![](https://mc.qcloudimg.com/static/img/57e160a173d903843891e0cc4f8f97d5/preprocessor_advance_bitcode.png)|
高级版本|


### 功能调用

预处理接口调用

```object-c
/*
* 预处理数据
* @param   sampleBuffer 帧数据（420f和y420）
* @param   orientation  人脸识别方向（无需人脸识别：TXE_ROTATION_0）
* @param   outputFormat 输出数据格式
* @return  int          是否调用成功，0成功，-1失败
*/
- (int)processFrame:(CMSampleBufferRef)sampleBuffer orientation:(TXERotation)orientation outputFormat:(TXEFrameFormat)outputFormat;

/*
* 预处理数据
* @param   data         帧数据
* @param   width        宽
* @param   height       高
* @param   orientation  人脸识别方向（无需人脸识别：TXE_ROTATION_0）
* @param   inputFormat  输入数据格式
* @param   outputFormat 输出数据格式
* @return  int          是否调用成功，0成功，-1失败
*/
- (int)processFrame:(Byte *)data width:(NSInteger)width height:(NSInteger)height orientation:(TXERotation)orientation inputFormat:(TXEFrameFormat)inputFormat outputFormat:(TXEFrameFormat)outputFormat;
```

数据处理完回调

```object-c
/*
* 设置代理
* @param   delegate   代理
*/
- (void)setDelegate:(id<TXIVideoPreprocessorDelegate>)delegate;

/*
* 设置通知（是否识别到人脸等通知）
* @param   notify   通知
*/
- (void)setNotify:(id<TXINotifyDelegate>)notify;

@protocol TXIVideoPreprocessorDelegate <NSObject>
@optional
/*
* 添加水印前回调
* @param   texture     纹理id
* @param   width       宽
* @param   height      高
* @return  处理后纹理id
*/
- (GLuint)willAddWatermark:(GLuint)texture width:(NSInteger)width height:(NSInteger)height;

/**
* 在OpenGL线程中回调，可以在这里释放创建的OpenGL资源
*/
- (void)onTextureDestoryed;

/*
* 数据处理回调
* @param   sampleBuffer 帧数据
* @param   timeStamp    时间戳
*/
- (void)didProcessFrame:(CMSampleBufferRef)sampleBuffer timeStamp:(UInt64)timeStamp;

/*
* 数据处理回调
* @param   bytes       帧数据
* @param   width       宽
* @param   height      高
* @param   format      输出数据格式
* @param   timeStamp   时间戳
*/
- (void)didProcessFrame:(Byte *)bytes width:(NSInteger)width height:(NSInteger)height format:(TXEFrameFormat)format timeStamp:(UInt64)timeStamp;

/**
* 人脸数据回调（启用了pitu模块才有效，开启pitu模块必须是打开动效或大眼瘦脸）
* @prama points 人脸坐标
*/
- (void)onDetectFacePoints:(NSArray *)points;
@end
```


基础功能

> 美颜风格、美颜、美白、红润、滤镜、水印、裁剪、旋转、缩放、镜像


```object-c
/*
* 设置输入裁剪区域
*/
@property (nonatomic, assign) CGRect cropRect;

/*
* 设置裁剪后旋转角度
*/
@property (nonatomic, assign) TXERotation rotateAngle;

/*
* 设置旋转后缩放大小
*/
@property (nonatomic, assign) CGSize outputSize;

/*
* 设置缩放后是否镜像
*/
@property (nonatomic, assign) BOOL mirror;

/*
* 设置水印是否镜像
*/
@property (nonatomic, assign) BOOL waterMirror;

/*
* 设置绿幕是否镜像
*/
@property (nonatomic, assign) BOOL greenMirror;

/*
* 是否异步回调（默认NO）
*/
@property (nonatomic, assign) BOOL isAsync;

/*
* 设置美颜风格
* @param   level    设置美颜类型，默认TXE_BEAUTY_TYPE_SMOOTH
*/
- (void)setBeautyStyle:(TXEBeautyStyle)style;

/*
* 设置美颜（0-10）
* @param   level    美颜程度，0表示原图
*/
- (void)setBeautyLevel:(NSInteger)level;

/*
* 设置美白（0-10）
* @param   level    美白程度，0表示原图
*/
- (void)setWhitenessLevel:(NSInteger)level;

/*
* 设置红润（0-10）
* @param   level    红润程度，0表示原图
*/
- (void)setRuddinessLevel:(NSInteger)level;

/*
* 设置水印
* @param   view  水印view
*/
- (void)setWaterMark:(UIView *)view;

```

可以使用sdk自带的滤镜资源，也可以自定义滤镜资源，通过设置融合度调整滤镜资源和图像的融合程度

```object-c
/*
* 设置滤镜
* @param   type  滤镜类型
*/
- (void)setFilterType:(TXEFilterType)type;

/*
* 设置滤镜
* @param   imagePath  滤镜资源路径
*/
- (void)setFilterImage:(NSString *)imagePath;

/*
* 设置滤镜融合度（0-10）
* @param   level    滤镜融合度
*/
- (void)setFilterMixLevel:(NSInteger)level;
```



高级功能

> 大眼、瘦脸、v脸、短脸、下巴、瘦鼻、绿幕、动效


```object-c
/*
* 设置绿幕
* @param   file  绿幕文件路径
*/
- (void)setGreenScreenFile:(NSString *)file;

/*
* 设置大眼（0-10）
* @param   level    大眼程度
*/
- (void)setEyeScaleLevel:(NSInteger)level;

/*
* 设置瘦脸（0-10）
* @param   level    瘦脸程度
*/
- (void)setFaceSlimLevel:(NSInteger)level;

/*
* 设置美型（0-10）
* @param   level    美型程度
*/
- (void)setFaceBeautyLevel:(NSInteger)level;

/*
* 设置V脸（0-10）
* @param   level    V脸程度
*/
- (void)setFaceVLevel:(NSInteger)level;

/*
* 设置下巴拉伸或收缩（-10-10，0为正常）
* @param   level    伸缩程度
*/
- (void)setChinLevel:(NSInteger)level;

/*
* 设置短脸（0-10）
* @param   level    短脸程度
*/
- (void)setFaceShortLevel:(NSInteger)level;

/*
* 设置瘦鼻（0-10）
* @param   level    瘦鼻程度
*/
- (void)setNoseSlimLevel:(NSInteger)level;

/*
* 设置动效
* @param   templatePath  动效资源路径
*/
- (void)setMotionTemplate:(NSString *)templatePath;

```

将动效资源解压在Resource目录下，通过资源路径设置动效

```object-c
/*
* 设置动效
* @param   templatePath  动效资源路径
*/
- (void)setMotionTemplate:(NSString *)templatePath;
```

使用绿幕需要先准备一个用于播放的mp4文件，通过调用以下接口即可开启绿幕效果

```object-c
/*
* 设置绿幕
* @param   file  绿幕文件路径
*/
- (void)setGreenScreenFile:(NSString *)file;
```


### 集成到ILiveSDK

1.对象创建

```object-c
//引入头文件
#import "TXCVideoPreprocessor.h"
//声明变量
@property (nonatomic, strong) TXCVideoPreprocessor *preProcessor;
@property (nonatomic, assign) Byte  *processorBytes;
//创建变量
self.preProcessor = [[TXCVideoPreprocessor alloc] init];
[self.preProcessor setDelegate:self];//TXIVideoPreprocessorDelegate
```

2.设置ILiveSDK数据回调

```object-c
// 进房前设置数据帧回调
[[ILiveRoomManager getInstance] setLocalVideoDelegate:self];//QAVLocalVideoDelegate
```

3.调用处理接口

```object-c
- (void)OnLocalVideoPreProcess:(QAVVideoFrame *)frameData
{
//设置美颜、美白、红润等参数
[self.preProcessor setBeautyLevel:5];
[self.preProcessor setRuddinessLevel:8];
[self.preProcessor setWhitenessLevel:8];
//开始预处理
[self.preProcessor processFrame:frameData.data width:frameData.frameDesc.width height:frameData.frameDesc.height orientation:TXE_ROTATION_0 inputFormat:TXE_FRAME_FORMAT_NV12 outputFormat:TXE_FRAME_FORMAT_NV12];
//将处理完的数据拷贝到原来的地址空间，如果是同步处理，此时会先执行（4）
if(self.processorBytes){
memcpy(frameData.data, self.processorBytes, frameData.frameDesc.width * frameData.frameDesc.height * 3 / 2);
}
}
```

4.回调中保存数据

```object-c
- (void)didProcessFrame:(Byte *)bytes width:(NSInteger)width height:(NSInteger)height format:(TXEFrameFormat)format timeStamp:(UInt64)timeStamp
{
self.processorBytes = bytes;
}
```
