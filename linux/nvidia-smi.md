# GPU状态监测 nvidia-smi 命令详解

在进行深度学习实验时，GPU 的实时状态监测十分有必要。今天详细解读一下 nvidia-smi 命令。

![这里写图片描述](assets/20180201162332463)

上图是服务器上 GeForce GTX 1080 Ti 的信息，下面一一解读参数。
上面的表格中的红框中的信息与下面的四个框的信息是一一对应的：

**GPU**：GPU 编号；
**Name**：GPU 型号；
**Persistence-M**：持续模式的状态。持续模式虽然耗能大，但是在新的GPU应用启动时，花费的时间更少，这里显示的是off的状态；
**Fan**：风扇转速，从0到100%之间变动；
**Temp**：温度，单位是摄氏度；
**Perf**：性能状态，从P0到P12，P0表示最大性能，P12表示状态最小性能（即 GPU 未工作时为P0，达到最大工作限度时为P12）。
**Pwr:Usage/Cap**：能耗；
**Memory Usage**：显存使用率；
**Bus-Id**：涉及GPU总线的东西，domain:bus:device.function；
**Disp.A**：Display Active，表示GPU的显示是否初始化；
**Volatile GPU-Util**：浮动的GPU利用率；
**Uncorr. ECC**：Error Correcting Code，错误检查与纠正；
**Compute M**：compute mode，计算模式。

下方的 Processes 表示每个进程对 GPU 的显存使用率。

**第二个命令：nvidia-smi -L**
![这里写图片描述](assets/20180201164038649)

该命令用于列出所有可用的 NVIDIA 设备信息。