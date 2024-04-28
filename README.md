# vod-media
### 写在前言

闲着无聊，撸个项目，*项目并不打算开源,如果感兴趣可以联系出售*，请不要对此评头论足，谢谢！

### 技术选型

jwt minio rabbitmq mybatis-plus redis spring-boot hls m3u8 vue3 typescript

### 项目结构

vod-media-parent  
|-vod-media-api &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;**接口模块**  
|-vod-media-autogen &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;**mybatis-plus generator**  
|-vod-media-common       &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;**公共模块**  
|-vod-media-mobile       &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;**vue3+Typescript 前端页面**  
|-vod-media-server       &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp; **后端管理模块（未实现）**  
|-vod-media-worker       &ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;**视频切片模块（也会作为任务工程）**  

### 项目完成度（80%）

`已实现功能`
1. 用户注册、用户登录、忘记密码
2. 邮件发送验证码、重置密码链接
3. 视频分组、视频列表、视频上传、视频播放（不直接播放视频源文件，播放m3u8文件，视频加载播放速度提升不止一点点)  ![image](https://github.com/sunyouliu/vod-media/assets/168319680/a60d4430-4a2d-45ae-8856-efdea5ff0f06)

4. minio存储图片、视频源文件、视频流文件
5. rabbitmq处理业务耦合，在源视频上传后，通知对视频文件进行切片，生成m3u8和ts文件
6. 其它...
   
`演示`  
![1](https://github.com/sunyouliu/vod-media/blob/main/Screen-2024-04-28-103202.gif)
![2](https://github.com/sunyouliu/vod-media/blob/main/Screen-2024-04-28-104158.gif)  
`验证邮件`  
![image](https://github.com/sunyouliu/vod-media/assets/168319680/f5443c38-2463-44c8-b706-372808600cdf)
![image](https://github.com/sunyouliu/vod-media/assets/168319680/13223601-4ea0-4333-8d60-2246cec49e4b)
*邮件内容用的是定义好的html模板*

### 技术难点

### 问题
虽然说是很简单的项目，看起来没几个页面，但还是遇到了很多意想不到的问题
- druid-version 1.2.22, select alway true condition not allow
  问题原因出现在mapper.xml文件中写sql语句，习惯性写where 1=1语句；网络上有建议直接拿掉wall filter，这种遇到问题不解决问题，选择绕道的思路真是清奇；还有博文说是druid 1.2.22版本中的逻辑实现bug，需要调换配置；但如果新版本更新解决了bug，不是莫名其妙的埋了一个雷吗？我没有去查看源码实现，费时间；换个思路，是否有办法在写sql语句时拿掉1=1的优雅做法，果然mybatis是有提供的，可以使用<where></where>标签代替where，这解决了我的问题；  

- 图片等资源上传到minio上，但是要浏览文件通过`getPresignedObjectUrl`方法拿到的是time bound url，是会过期的url；我只是修改了对应bucket的Access Policy为public即可在浏览器中直接访问文件；
 ![image](https://github.com/sunyouliu/vod-media/assets/168319680/6011b758-ccbb-4b21-bdc5-f9a4932e41d8)
  比如你的minio是在http://localhost:9000端口上，你的bucket名称是images，访问文件的url地址为：http://localhost:9000/images/[objectName]  
  
- 配置Google Gmail SMTP Server向用户免费发送验证码短信，及重置密码链接，出现的一些错误，这个可能相对简单些，可以网络上查找下
  
- 415 Unsupported MediaType in Spring Application  
  在Typescript中上传文件，只是这个form表单中还有其他信息一起提交，有意思的是网络上很多的推荐做法是在spring-boot服务端分开接收这两部分信息，用@RequestPart来接收文件及其它信息，类似upload(@RequestPart("file") MultipartFile,@RequestPart("fileInfo") String fileInfo)，这里更有意思的是fileInfo是json串，需要再parse一番，那是否有更优雅的解决方案？在一个对象中同时接收文件信息及文件？答案是肯定的，使用@ModelAttribute注解；比如：upload(@Valid @ModelAttribute UploadRequest req)

- 选择本地视频文件并回显，即在未上传前进行加载预览问题（how to preload video before uploading)
- 截取视频第一帧作为视频封面问题(how to use first frame of the video as the video cover)

### 联系方式（请注明来源、来意）  
![image](https://github.com/sunyouliu/vod-media/assets/168319680/b6ec43ac-3d05-4921-a219-9f39bf4b603b)
