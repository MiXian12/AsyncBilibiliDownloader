# AsyncBilibiliDownloader
异步bilibili下载器，支持下载视频和番剧，基于`aiohttp`和`asyncio`的协程下载，速度飞快～

### 特点
- 轻量级，简单易用，只使用一个线程，开销很小。
- 使用协程同时从多个源下载（b站几乎所有视频都有至少一个备用源），使用了非阻塞的异步下载，发出请求后不用等待。因此速度很快，基本上能把带宽拉满。
- 使用非阻塞异步方式写文件，下载速度几乎不受磁盘读写速度影响。边下载边写文件，不是下好后存在内存中然后一次性写入，因此不管目标文件有多大，内存占用都很低（超出50MB时会有警告，一般很少超过50MB）。

### 测速
我的网络带宽大约为150Mbps，下图为测速结果：
![image](./imgs/speed_test.jpg)
这里使用[you-get](https://github.com/soimort/you-get)、[youtube-dl](https://github.com/ytdl-org/youtube-dl)、[Henryhaohao/Bilibili_video_download](https://github.com/Henryhaohao/Bilibili_video_download)和本下载器`max_tasks`为20，`chunk_size`为500KB, `timeout`为10秒）下载1080P视频[【敖厂长】1989年日本最早生存恐怖游戏 真的很恐怖吗?](https://www.bilibili.com/video/av89685634)（121.02MB）。平均速度如下：
![image](./imgs/compare.jpg)
可以看出，本下载器的的下载速度超过其他下载器20倍以上。

### 使用
- 如果下载视频，需实例化`VideoDownloader`类；如果下载番剧，需实例化`BangumiDownloader`类。
- 调用`run`方法即可开始下载。

### 参数
- `aid`: 视频av号。
- `ep_id`: 番剧单集编号。
- `quality`: 视频质量。可选值为：112/116、74、80、64、32、16分，分别对应1080P+、720P+、1080P、720P、480P、360P。
- `fname`: 保存文件名，格式为flv。
- `max_tasks`: 协程数。
- `chunk_size`: 单个协程下载的视频大小，单位字节。
- `sess_data`: 用户Cookies中的SESSDATA，对于需要大会员才能观看的视频必须传入大会员的SESSDATA。
- `timeout`: 单个协程下载的时间限制，超出将重试。

### 说明
- `max_tasks`和`chunk_size`均不宜设置过大。建议设置两者的乘积为网络带宽大小（单位字节）。
- 如果带宽无限大，理论上协程数加倍能使得下载速度加倍。但是带宽有限，协程数达到一定值下载速度趋于稳定。
- 建议使用Python3.6版本，Python3.7版本中有bug，可能会导致`aiohttp`中SSL验证失败，详见[https://github.com/aio-libs/aiohttp/issues/3535](https://github.com/aio-libs/aiohttp/issues/3535)。目前我也不太清楚如何修复……