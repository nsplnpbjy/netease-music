# netease-music
本项目是一个蹩脚的播放器，集合了搜索音乐、播放音乐、下载音乐、显示歌词和专辑封面等功能，需结合配套服务器使用。
配套服务器项目地址：https://github.com/nsplnpbjy/netease-music-copy

以下是使用方法：
首先更改请求路径，也就是baseUrl变量为你的服务器的地址和端口
### 制作dist
```
npm run build
```


以上步骤完成后

然后把dist文件夹复制到服务器上，并使用Nginx配置好端口转发
