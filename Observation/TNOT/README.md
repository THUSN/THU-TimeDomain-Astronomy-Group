# TNOT 观测操作手册

## 1. 观测前准备

### 1.1 远程连接

打开远程控制软件“向日葵”，选择对应的观测电脑。登录信息暂按以下格式填写，实际内容从内部密码管理渠道获取：

- 识别码：799793344
- 验证码：nsqh800

<img width="1422" height="348" alt="1-1" src="https://github.com/user-attachments/assets/f3d9157d-176c-41a1-b4f7-2abe7ad12eec" />

连接上后如需登录：

- 账户：admin
- 密码：nsqh.800

登录后先确认桌面、网络和远程控制响应正常，再进行天气和设备检查。若远程画面卡顿或控制延迟明显，不要直接打开圆顶或镜盖。

### 1.2 天气确认

天气确认必须覆盖实时观测环境和观测时段预报。出现降雨、雷暴、云量快速恶化或无法确认的情况时，暂停开顶和开镜盖动作，如无法独自确认，可在群里反馈。

#### 1.2.1 登录南山观测环境监测系统

打开南山观测环境监测系统（119.78.162.37），查看：

1. 实时云量监测；

<img width="3322" height="1538" alt="1-2-1-1" src="https://github.com/user-attachments/assets/8936baec-4e31-4180-9ef2-bdd773391842" />

图中有无云差异明显，但需注意图像更新时间

2. 天气预报；
   <img width="1476" height="562" alt="1-2-1-2" src="https://github.com/user-attachments/assets/3a4e6e78-b096-473e-91aa-1aa6d372a9ac" />

全天云量图中白色为云，蓝色为晴，降雨/降雪标注较为直接

#### 1.2.2 Windy 云图

打开 Windy( www.windy.com )，定位到“乌鲁木齐市乌鲁木齐县甘沟乡”，分别查看“雨、雷暴”和“云”图层。沿计划观测时段拖动时间轴，确认降雨、雷暴和云层变化趋势。

<img width="3768" height="1796" alt="1-2-2" src="https://github.com/user-attachments/assets/8cc9aa6b-088a-4fb4-b8fd-8903907a1378" />

### 1.3 制定观测计划

目前根据王老师在群里发的当天观测计划制作TNOT电脑上ACP软件可读的版本

### 1.4 新建 Flat/Bias 文件夹

在E:\Data文件夹下创建今日数据文件夹如20260902，在文件夹内再分别创建Flat,Bias文件夹，以供后续拍平场使用
E:\Data文件夹在文件夹左侧快速访问中可方便找到

---

## 2. 开机

### 2.1 开启 CCD

使用 MaxIm DL Pro 6 连接 CCD：

1. 打开相机控制面板(Toggle Camera Control)，进入 `Setup`。
   <img width="1120" height="460" alt="2-1-1" src="https://github.com/user-attachments/assets/50bb1a05-51b1-4ac6-a984-e6a306e8892f" />

<img width="1480" height="1368" alt="2-1-2" src="https://github.com/user-attachments/assets/291c1e45-2025-4a19-955f-570bd489a7cf" />

2. 主 CCD 选择 `Camera 1`，进入 `Setup camera`。
3. 在高级选项'Advanced...'中点击 `Discover`，会自动选择 `DZ936_BV`。
4. 更改 `HS speed = 1`、`Pre-amp Gain = 2`
   <img width="1688" height="1442" alt="2-1-3" src="https://github.com/user-attachments/assets/6387e39b-0e9b-4a98-b4dd-2bfb1595b925" />
5. 点击两次 `OK` 保存设置，再点击 `Connect`连接相机。
6. 打开 `Setup-Cooler`，确认目标温度为`-70 °C`后点击 `Coolers-On`开启CCD制冷。
   <img width="1018" height="918" alt="2-1-4" src="https://github.com/user-attachments/assets/e3fbed65-96db-42d0-988f-685b9887a366" />

### 2.2 开启赤道仪

1. 确认赤道仪电源和急停状态正常。
2. 打开 Autoslew，查看电机状态和连接状态。
3. 打开 TheSkyX，在 `Telescope` 中连接望远镜，检查时间、地点和坐标信息。
4. 若电脑重启或重新打开 Autoslew 后需要校正，按现场规程在 TheSkyX 中完成连接、校正和 Park 检查，再继续后续操作。没有完成校正前，不要直接运行 ACP 自动任务。

> 图片：`![Autoslew 和 TheSkyX 开启赤道仪](media/02-02-mount.png)`
> 视频：`<video controls src="media/02-02-mount.mp4"></video>`

### 2.3 开启天窗、镜盖

确认天气合适、无降雨，且监控系统中没有新的天气或设备告警后：

1. 打开全天域控制系统，连接圆顶控制。
2. 开启天窗（圆顶），观察开启动作、位置反馈和现场监控。附件要求开顶后观察约 3–5 分钟；具体等待时间按台站规程执行。
3. 在 ACC 中确认镜筒、调焦、镜盖和冷却器状态。
4. 圆顶稳定、天气确认无误后，再打开镜盖。若位置反馈、动作声音或监控画面异常，立即停止并联系负责人。

> 图片：`![全天域控制系统和 ACC 开启天窗镜盖](media/02-03-dome-cover.png)`
> 视频：`<video controls src="media/02-03-dome-cover.mp4"></video>`

### 2.4 入夜拍平场本底

入夜后先按当晚校准方案拍摄 Flat 和 Bias，并将文件保存到 1.4 建立的目录：

1. 在 MaxIm DL Pro 6 中选择对应滤镜、曝光时间、binning 和保存路径。
2. 平场选择没有亮星的视场，按照入夜时段取数；附件给出的参考要求是 ADU 约 20000–30000、每个波段 5 张、指向有轻微变化、曝光时间大于 3 秒且小于 30 秒。
3. Bias（本底）的曝光、张数和命名按现行校准规程执行，不要用不清楚来源的旧文件替代当晚记录。
4. 打开代表性图像检查是否饱和、漏拍、文件名错位或写入错误，再开始正式观测。

> 图片：`![入夜平场本底](media/02-04-dusk-calibration.png)`
> 视频：`<video controls src="media/02-04-dusk-calibration.mp4"></video>`

---

## 3. 观测

### 3.1 上传观测列表

在 ACP 中执行自动观测任务：

1. 打开 ACP，连接望远镜和 CCD 相机。
2. 选择脚本 `AcquireImages.js`。
3. 载入已复核的 `txt` 观测列表，确认文件路径、数据目录和列表首行正确。
4. 检查望远镜状态、CCD 温度和镜盖状态，确认可以开始运动和曝光。
5. 点击 `Run`，记录任务开始时间和所用列表文件名。

这里的“上传”指将观测列表载入 ACP；正式运行前仍需在 ACP 界面复核一次参数。

> 图片：`![ACP 上传观测列表](media/03-01-acp-list.png)`
> 视频：`<video controls src="media/03-01-acp-list.mp4"></video>`

### 3.2 确认图像正常

任务开始后先检查测试曝光或第一组图像，再让任务持续运行。至少确认：

- 目标位置和指向没有明显错误；
- 滤镜、曝光时间、重复次数和文件名符合列表；
- 星象基本正常，没有明显拖线、严重失焦或异常背景；
- 图像没有大面积饱和，文件已经写入正确数据目录；
- CCD 温度、圆顶位置和赤道仪跟踪状态稳定。

运行中定时查看天气、圆顶、镜盖、滤镜切换、CCD 温度、图像落盘和 FWHM 等状态。发现报警或图像质量明显恶化时，先暂停或在 ACP 中执行 `Abort`，记录当前目标和原因，再决定是否恢复。

> 图片：`![ACP 图像检查](media/03-02-image-check.png)`
> 视频：`<video controls src="media/03-02-image-check.mp4"></video>`

---

## 4. 关机

### 4.1 出夜拍平场本底

正式观测结束或安全中止后，利用出夜时段按校准方案补拍 Flat 和 Bias。确认文件已写入 `Flat`、`Bias` 目录，并记录滤镜、曝光、张数、温度和文件范围。平场若出现亮星、饱和或明显异常，应标记为不合格并按规程重拍。

> 图片：`![出夜平场本底](media/04-01-dawn-calibration.png)`
> 视频：`<video controls src="media/04-01-dawn-calibration.mp4"></video>`

### 4.2 关闭镜盖、天窗

先在 ACP 中确认任务已正常结束或执行 `Abort`，等待最后一张图像写盘。确认天气和设备状态允许后，按现场联锁规程关闭镜盖，再关闭天窗（圆顶）；关闭过程中持续查看位置反馈。若发生卡顿、报警或反馈不一致，停止重复操作并联系负责人。

附件原始流程对镜盖、天窗和 Park 的先后顺序有不同写法，本小节先沿用当前章节顺序，正式版应在现场确认后固定唯一顺序。

> 图片：`![关闭镜盖和天窗](media/04-02-close-cover-dome.png)`
> 视频：`<video controls src="media/04-02-close-cover-dome.mp4"></video>`

### 4.3 CCD 温度处理

本小节沿用用户提供的编号；“CCD 降温”在关机流程中可能实际指停止制冷并升温，请现场确认术语。确认后：

1. 在 MaxIm DL Pro 6 中停止冷却，按设备规程让 CCD 回到规定温度范围。
2. 等待温度变化稳定，确认没有正在进行的读出或文件写入。
3. 未完成升温前不要直接关闭相机电源。

> 图片：`![CCD 温度处理](media/04-03-ccd-temperature.png)`
> 视频：`<video controls src="media/04-03-ccd-temperature.mp4"></video>`

### 4.4 望远镜回 Park 位

在确认镜盖和天窗状态安全、运动范围无障碍后，通过 ACP 或 TheSkyX 执行 `Park`。等待位置反馈完成，核对望远镜确实停在 Park 位，再继续断开软件连接。

> 图片：`![望远镜回 Park 位](media/04-04-park.png)`
> 视频：`<video controls src="media/04-04-park.mp4"></video>`

### 4.5 关闭 ACP、MaxIm DL Pro 6

确认任务停止、CCD 已完成温度处理、图像和日志均已写盘后：

1. 在 ACP 中断开望远镜和 CCD 相机连接，然后关闭 ACP。
2. 在 MaxIm DL Pro 6 中确认冷却器关闭、相机连接断开，再关闭软件。
3. 不要在图像写入或 CCD 仍处于冷却状态时强制结束软件或断电。

> 图片：`![关闭 ACP 和 MaxIm DL Pro 6](media/04-05-close-software.png)`
> 视频：`<video controls src="media/04-05-close-software.mp4"></video>`

### 4.6 关闭 Autoslew

在 TheSkyX 已断开、望远镜处于 Park 位后，按现场规程关闭或退出 Autoslew，并确认电机状态和软件状态已经记录。附件原始流程写的是将 Autoslew 置为红色（`Motor is OFF`）但不关闭软件；这与“关闭 Autoslew”的当前要求存在差异，正式版需由设备负责人确认后保留一种做法。

> 图片：`![关闭 Autoslew](media/04-06-close-autoslew.png)`
> 视频：`<video controls src="media/04-06-close-autoslew.mp4"></video>`

---

## 5. 数据传输

本节暂空，后续补充以下内容：

- 数据源目录和目标服务器/磁盘；
- 使用的传输工具和登录方式；
- 文件命名、数量、大小和校验方法；
- 传输失败时的重试、保留本地原始数据和联系人；
- 观测日志、Flat/Bias 与科学图像的交接方式。

> 图片：`![数据传输](media/05-data-transfer.png)`
> 视频：`<video controls src="media/05-data-transfer.mp4"></video>`

---


## 6. FAQ

### 6.1 天气不稳定，还能开顶吗？

不能仅凭单个页面判断。结合南山监测系统和 Windy 的观测时段趋势判断；无法确认时保持天窗和镜盖关闭，并记录原因。

### 6.2 向日葵连不上怎么办？

先确认远程电脑开机、网络和远程服务正常，再检查账号、识别码和验证码。远程控制延迟明显时，不执行圆顶、镜盖或望远镜运动操作。

### 6.3 MaxIm DL Pro 6 找不到 CCD 怎么办？

检查相机电源和连接，再按 `Setup → Setup camera → Advanced → Discover` 重新发现 `DZ936_BV`，核对 `Camera 1`、读出速度、前置放大增益和快门设置。仍无法连接时记录错误信息并联系负责人。

### 6.4 CCD 温度不稳定怎么办？

确认冷却器状态、目标温度和相机连接。温度未稳定前不要开始科学任务；保存监控画面和时间，按负责人意见等待、停止冷却或终止观测。

### 6.5 Autoslew 显示 `Motor is OFF` 怎么办？

先确认这是正常停机状态还是连接异常。需要重新连接时，按规程用 TheSkyX 完成连接和校正，不要跳过校正直接运行 ACP。

### 6.6 TheSkyX 不能正常指向怎么办？

停止自动任务，确认时间、地点、坐标和 `Park/Unpark` 状态。在安全区域做小范围验证；位置反馈与实际不一致时保持停止并联系负责人。

### 6.7 平场 ADU 超出范围或出现亮星怎么办？

将该组标记为不合格，不要混入正式数据。调整曝光时间或选择无亮星视场，按当晚校准规程重拍，并记录原因。

### 6.8 观测列表处理后参数不对怎么办？

回到原始列表检查括号、波段、时长、次数和目标名空格，重新生成后人工核对 `#Filter`、`#Interval` 和 `#Count`。复核完成前不要挂载 ACP。

### 6.9 ACP 运行中需要停止怎么办？

在 ACP 中执行 `Abort`，等待当前曝光和文件写入结束，记录当前目标及原因，再按关机流程继续。不要直接关闭 ACP、CCD 或计算机电源。

### 6.10 圆顶或镜盖动作异常怎么办？

立即停止自动任务，观察监控和报警信息，不要连续重复点击。保持当前安全状态，记录时间和画面，联系现场负责人按应急规程处理。

---

## 联系方式

请按照以下联系方式的顺序依次联系：

阿不：

- 电话：13579810861
- 微信：xinxin_xinxin_01

祝海畅：

- 电话：18790412166
- 微信：Tomcthulhu

胡正言：

闫圣钰：

- 电话：17707719670
- 微信：yanshengyu0311

## 操作视频教程录屏

https://cloud.tsinghua.edu.cn/d/8b98a16385b74b58b45a/

