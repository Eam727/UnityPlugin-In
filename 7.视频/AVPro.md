参考链接：[https://blog.csdn.net/dark00800/article/details/56015917](https://blog.csdn.net/dark00800/article/details/56015917)

# AVPro视频插件

### 一、插件介绍

RenderHeads提供了一款unity3d使用的具有强大功能的视频播放插件，不仅可以实现基础的播放功能，还能实现进度条拖放和速率调整，播放4K视频，360度全景视频等，并对不同的平台进行了优化。

### 二、基础组件

#### 1.Media Player

在Hierarchy视图右击或者选择菜单栏的GameObject菜单，然后选择AVPro Video->Media Player。 

**Media Player对象**包括基础设置，Media Properties（视频的图像和音频设置），Global Settings（全局设置），Preview（预览，只在Play时有效），Events（回调事件），Platform（多平台重写）和About（插件信息）。其中我们一般需要进行设置的只有基础设置和Events。

**基础设置**包括：文件路径，自动加载播放，循环音量等信息，这些都是一些视频的基础属性。需要注意一下的是Video Location这个选项，插件会根据Location的位置去查找相应的文件夹，这里推荐使用StreamingAssets文件夹，需要在项目的Assets目录下新建一个StreamingAssets文件夹，然后将视频拖进去，StreamingAssets中的文件不会被打包到程序资源包中，而是作为一个独立的目录自动生成在输出的程序目录的Data目录下。Recent选项可以快速选择之前选择过的文件，Browse选项可以快速选择电脑中的文件。 

**Events**是一个MediaPlayerEvent属性，继承了UnityEvent类，使用起来跟UGUI的Button控件的event类似，不同的是我们提供的方法需要有三个参数，MediaPlayer、MediaPlayerEvent.EventType和ErrorCode。其中EventType是播放器的一些状态，有如下几种：

- MetaDataReady：视频数据准备完成
- ReadyToPlay：视频加载完成，准备播放
- Started：视频开始播放
- FirstFrameReady：第一帧渲染完成
- FinishedPlaying：非循环播放的视频播放完毕

三个参数都需声明，但只需要对EventType进行判断，其余两个变量是情况使用。



	public void OnVideoEvent(MediaPlayer mp, MediaPlayerEvent.EventType et, ErrorCode er)
	{
		switch (et)
		{
	    case MediaPlayerEvent.EventType.ReadyToPlay:
	        Debug.Log("ReadyToPlay");
	        break;
	    case MediaPlayerEvent.EventType.FirstFrameReady:
	        Debug.Log("First frame ready");
	        break;
	    case MediaPlayerEvent.EventType.FinishedPlaying:
	        Debug.Log("Finished");
	        break;
		}
	}

**其他设置**：其他的设置一般不需要去更改。这里简单介绍一下：


- Media Properties：贴图的过滤、循环模式和Aniso level的设置，音频的立体声设置等。
- Global Settings：显示当前的开发平台，可以设置是否受TimeScale影响。
- Preview：视频播放时可以查看播放情况，包括拖动进度，重播，显示Alpha通道，查看当前帧的贴图，保存当前帧贴图等。
- PlatForm Overrides：可以重写相应平台播放的视频路径等。
- About：插件的相关信息，可以点击这一项中的按钮快捷跳转到开发者的网页。 

#### 2.AVPro Video

在Hierarchy视图右击或者选择菜单栏的GameObject菜单，然后选择UI->AVPro Video。 

然后将刚刚的Media Player对象拖给面板中的Media Player属性，Default Texture和No Default Display可以设置显示缩略预览图，Keep Aspect Ratio可以选择是否保持视频分辨率。其他的设置和UGUI的Image类似，不再赘述。

### 三、Demo学习

#### 1. 01_Demo_BackgroundShader

这个Demo介绍了**使用一个Shader控制在网格上播放视频**：

- 场景中添加一个Media Player组件，设置好参数
- 场景中添加一个Quad网格：3D Object-Quad，MeshRenderer里的Materials里添加使用一个新建的材质，这个材质使用的Shader就是这个指定的Shader：AVProVideo/Background/FullScreen.
- 这个Quad物体上添加一个ApplyToMesh组件，Media设置为刚才我们加的Media Player组件，Mesh拖入自己
- 注意：Quad物体要在摄像机范围内，一般Position设置为(0,0,0)，否则可能Game面板视频不显示。

#### 2. 02_Demo_imGui

这个Demo展示了GUI控制视频的播放与视频间的切换。

#### 3. 03_Demo_VideoControls

这个Demo展示了**在UGUI上播放视频，和控制视频**。

UGUI上播放使用：
- 场景中添加一个Media Player组件，设置好参数
- UICanvas下右击新建AVPro Video组件(参照上述基础组件2)，Media拖入上面的组件

视频播放控制：
假设PlayingPlayer是正在使用的播放器

- 播放：PlayingPlayer.Control.Play();
- 暂停：PlayingPlayer.Control.Pause();
- 重播：PlayingPlayer.Control.Rewind();
- 调整播放进度：PlayingPlayer.Control.Seek(选择的进度比值 * PlayingPlayer.Info.GetDurationMs());
- 调整音量：PlayingPlayer.Control.SetVolume(选择的进度比值);
- 调整是否自动播放：PlayingPlayer.m_AutoStart = 值
- 是否静音：PlayingPlayer.Control.MuteAudio(值);
- 播放一个视频文件：PlayingPlayer.OpenVideoFromFile(文件所在文件夹的定义方式  MediaPlayer.FileLocation.RelativeToStreamingAssetsFolder, PlayingPlayer.m_VideoPath, bool 是否自动播放);
- 关闭视频：PlayingPlayer.CloseVideo();

#### 4. 04_Demo_uGui

这个Demo展示了在UGUI上播放视频，和**在Text文本上渲染视频的内容**。

![](https://i.imgur.com/hFu8C1z.png)

- 场景中添加一个Media Player组件，设置好参数
- 在文本上加上Apply To Material组件，材质使用新建的材质，Shader使用UI/Default Font-Extra Texture；TexturePropertyName为_OverlayTex，Media拖入上面的组件。

#### 5. 05_Demo_Multiple

这个Demo展示了**多视频窗口**

![](https://i.imgur.com/JfGOra5.png)

添加按钮调用了如下函数，就是新建了一个物体挂了几个需要的脚本：
(同样的移除的话，就是停止要移除的视频，然后销毁物体)

    public void AddVideoClicked()
		{
			++m_NumVideosAdded;

			// New media player
			GameObject newMediaPlayerGameObject = new GameObject( "AVPro MediaPlayer " + m_NumVideosAdded.ToString() );
			MediaPlayer newMediaPlayer = newMediaPlayerGameObject.AddComponent<MediaPlayer>();
			newMediaPlayer.m_VideoPath = m_videoPath;
			newMediaPlayer.m_AutoStart = true;
			newMediaPlayer.m_Loop = true;
			newMediaPlayer.SetGuiPositionFromVideoIndex( m_NumVideosAdded - 1 );
			newMediaPlayer.SetDebugGuiEnabled( m_NumVideosAdded < 5 );

			// New uGUI object
			GameObject canvasPanel = GameObject.Find("Canvas/Panel");
			if( canvasPanel != null )
			{
				// New game object, and make it a child of the canvas panel
				GameObject newGuiGameObject = new GameObject( "AVPro Video uGUI " + m_NumVideosAdded.ToString() );
				newGuiGameObject.transform.parent = canvasPanel.transform;
				newGuiGameObject.SetActive( false );

				// Add the components
				newGuiGameObject.AddComponent<RectTransform>();
				newGuiGameObject.AddComponent<CanvasRenderer>();

				// Add the DisplayUGUI;
				DisplayUGUI newGuiObject = newGuiGameObject.AddComponent<DisplayUGUI>();
				newGuiObject._mediaPlayer = newMediaPlayer;
				newGuiObject._scaleMode = ScaleMode.StretchToFill;
				newGuiObject.rectTransform.localScale = Vector3.one; 
				newGuiObject.rectTransform.pivot = new Vector2( 0.0f, 1.0f );
				m_aAddedVideos.Add( newGuiObject );

				// Update layout
				UpdateVideosLayout();
			}
		}

#### 6. 06_Demo_FrameExtract

这个Demo展示了使用提取关键帧的方式保存视频关键帧作为图片文件。

#### 7. 07_Demo_Mapping3D

这个Demo展示了在方块、球等3D物体上播放同一个视频(在一个材质上播放)

![](https://i.imgur.com/pEyoEl0.png)

使用ApplyToMaterial组件，关联视频组，播件MediaPlayer，使用一个材质，让视频影响材质，然后材质挂在3D物体上，材质影响3D物体，播放视频。

#### 8. 08_Demo_360SphereVideo	
#### 9.09_Demo_360CubeVideo
360全景视频

#### 10.10_Demo_Transparency
这个Demo展示了带有透明通道的视频

#### 11.11_Demo_Subtitles

这个Demo展示了带有字幕文件的视频