# Ubuntu安装nvidia显卡驱动

1. **禁用nouveau驱动**：

   - Nouveau是开源的NVIDIA驱动程序，需要禁用以避免与官方驱动程序冲突。

   - 通过在

     ```
     /etc/modprobe.d/
     ```

     目录下创建一个黑名单配置文件来禁用它。例如，你可以创建一个名为

     ```
     blacklist-nouveau.conf
     ```

     的文件，并添加以下内容：

     ```
     blacklist nouveau
     options nouveau modeset=0
     ```

   - 然后，更新initramfs并重启：

     ```
     sudo update-initramfs -u
     sudo reboot
     ```

2. **安装驱动前的准备**：

   - 更新系统：

     ```
     sqlCopy codesudo apt update
     sudo apt upgrade
     ```

   - 安装必要的构建工具和头文件：

     ```
     sudo apt install build-essential dkms
     ```

3. **安装NVIDIA驱动**：

   - 可以使用Ubuntu的 ubuntu-drivers 工具自动安装推荐的驱动版本：
     ```
     sudo ubuntu-drivers autoinstall
     ```
   - 或者，你可以添加NVIDIA官方PPA，然后安装最新的驱动：

     ```
     sudo add-apt-repository ppa:graphics-drivers/ppa
     sudo apt update
     ubuntu-drivers devices # 查看可以安装的驱动
     sudo apt install nvidia-driver-xxx
     ```
     
     其中xxx是你想要安装的驱动版本号，例如对于RTX 3060，可能想安装较新的驱动版本，如 nvidia-driver-465或更高。

4. **重启系统**：

   - 安装完成后，重启你的系统以使变更生效。

5. **验证安装**：

   - 重启后，运行以下命令来检查驱动程序是否正确安装：

     ```
     nvidia-smi
     ```
     
- 这个命令将显示NVIDIA卡的状态和安装的驱动程序版本。

