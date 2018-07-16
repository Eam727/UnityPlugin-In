**HdgRemoteDebug 插件用法：**

0.导入插件，把插件中RemoteDebugServer物体放到场景或者代码里在初始添加RemoteDebugServerFactory到场景物体下并设置不可销毁。

1 开启 DEVELOPMENT_BUILD 打包apk，安装到手机

2 在UnityEditor中打开RemoteDebug窗口 Window -> Hdg Remote Debug

3 在命令行中输入 adb forward tcp:12000 tcp:12000

4 在RemoteDebug窗口的ActivePlayer下拉框中输入IP和端口 127.0.0.1:12000 连接
