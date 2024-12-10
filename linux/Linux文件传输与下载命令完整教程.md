# Linux文件传输与下载命令完整教程

## 1. `scp` 命令

### 1.1. 什么是 `scp`？

`scp`（**Secure Copy**）是一种用于通过 SSH 进行文件或目录安全复制的命令行工具。它支持在本地与远程服务器之间、两个远程服务器之间传输文件。

### 1.2. 基本语法

```bash
scp [options] [source] [destination]
```

- `source`：源文件或目录。
- `destination`：目标文件或目录。

### 1.3. 常见选项

- `-r`：递归复制目录及其内容。
- `-P`：指定远程服务器的 SSH 端口号（默认端口是22）。
- `-C`：压缩传输数据，以提高传输速度。
- `-i`：指定用于认证的 SSH 私钥文件。

### 1.4. 示例

#### 从远程服务器下载文件到本地

```bash
scp user@192.168.1.100:/home/user/file.txt /local/directory/
```

#### 从本地上传文件到远程服务器

```bash
scp /local/file.txt user@192.168.1.100:/home/user/
```

#### 递归复制目录

```bash
scp -r user@192.168.1.100:/home/user/directory/ /local/directory/
```

#### 使用自定义SSH端口

```bash
scp -P 2222 user@192.168.1.100:/home/user/file.txt /local/directory/
```

### 1.5. 处理密码验证

#### 手动输入密码

在默认情况下，`scp` 会在运行时提示你输入用户密码。

#### 使用 `sshpass` 自动输入密码

对于自动化任务，可以使用 `sshpass` 工具：

```bash
sshpass -p 'your_password' scp -r user@192.168.1.100:/home/user/directory/ /local/directory/
```

#### 使用 SSH 密钥认证（推荐）

1. 生成 SSH 密钥对：

   ```bash
   ssh-keygen -t rsa
   ```

2. 将公钥复制到远程服务器：

   ```bash
   ssh-copy-id user@192.168.1.100
   ```

3. 之后无需输入密码：

   ```bash
   scp -r user@192.168.1.100:/home/user/directory/ /local/directory/
   ```

------

## 2. `rsync` 命令

### 2.1. 什么是 `rsync`？

`rsync` 是用于高效传输和同步文件和目录的命令。与 `scp` 不同的是，`rsync` 只传输更新过的部分，并支持断点续传。

### 2.2. 基本语法

```bash
rsync [options] [source] [destination]
```

### 2.3. 常见选项

- `-a`：归档模式，保留文件的权限、时间戳、符号链接等。
- `-v`：显示详细输出。
- `-z`：压缩数据传输。
- `-r`：递归传输目录。
- `--progress`：显示传输进度。
- `-e`：指定远程传输协议（如 `ssh`）。

### 2.4. 示例

#### 从远程服务器同步文件到本地

```bash
rsync -avz user@192.168.1.100:/home/user/directory/ /local/directory/
```

#### 从本地同步文件到远程服务器

```bash
rsync -avz /local/directory/ user@192.168.1.100:/home/user/directory/
```

#### 使用 `ssh` 进行同步

```bash
rsync -avz -e "ssh -p 2222" /local/directory/ user@192.168.1.100:/home/user/directory/
```

### 2.5. 处理密码验证

同样可以使用 `sshpass` 或 SSH 密钥认证来处理密码验证：

```bash
sshpass -p 'your_password' rsync -avz /local/directory/ user@192.168.1.100:/home/user/directory/
```

------

## 3. `wget` 命令

### 3.1. 什么是 `wget`？

`wget` 是一个命令行工具，用于从网络上下载文件。它支持通过HTTP、HTTPS、FTP等协议下载文件，并支持断点续传。

### 3.2. 基本语法

```bash
wget [options] [url]
```

### 3.3. 常见选项

- `-O`：指定输出文件名。
- `-c`：断点续传。
- `-r`：递归下载。
- `--limit-rate`：限制下载速度。
- `--user` 和 `--password`：指定HTTP或FTP的用户名和密码。

### 3.4. 示例

#### 下载单个文件

```bash
wget http://example.com/file.zip
```

#### 下载并指定输出文件名

```bash
wget -O custom_name.zip http://example.com/file.zip
```

#### 断点续传

```bash
wget -c http://example.com/largefile.zip
```

#### 下载需要身份验证的文件

```bash
wget --user=username --password=your_password http://example.com/protected_file.zip
```

------

## 4. `curl` 命令

### 4.1. 什么是 `curl`？

`curl` 是一个命令行工具，用于通过 HTTP、HTTPS、FTP 等协议与服务器交互。它既可以下载文件，也可以发送HTTP请求。

### 4.2. 基本语法

```bash
curl [options] [url]
```

### 4.3. 常见选项

- `-O`：使用原始文件名下载。
- `-o`：指定下载文件的输出名。
- `-C`：断点续传。
- `-u`：指定用户名和密码。
- `-L`：跟随重定向。
- `-X`：指定请求方法（如GET、POST）。

### 4.4. 示例

#### 下载文件并保存为原始文件名

```bash
curl -O http://example.com/file.zip
```

#### 下载文件并指定输出文件名

```bash
curl -o custom_name.zip http://example.com/file.zip
```

#### 断点续传

```bash
curl -C - -O http://example.com/largefile.zip
```

#### 下载需要身份验证的文件

```bash
curl -u username:password -O http://example.com/protected_file.zip
```

#### 发送POST请求上传文件

```bash
curl -X POST -F "file=@/path/to/local/file" http://example.com/upload
```

------

## 5. `ftp` 命令

### 5.1. 什么是 `ftp`？

`ftp` 是一个交互式命令行工具，用于通过FTP协议进行文件传输。虽然相对老旧，仍然在某些特定场景中使用。

### 5.2. 基本语法

```bash
ftp [hostname]
```

### 5.3. 常见命令

- `get`：下载文件。
- `put`：上传文件。
- `mget`：批量下载文件。
- `mput`：批量上传文件。
- `ls`：列出目录内容。
- `cd`：切换远程目录。

### 5.4. 示例

#### 登录 FTP 服务器

```bash
ftp 192.168.1.100
```

输入用户名和密码登录后，使用以下命令传输文件。

#### 下载文件

```bash
get remote_file.txt local_file.txt
```

#### 上传文件

```bash
put local_file.txt remote_file.txt
```

#### 批量下载文件

```bash
mget *.txt
```

#### 批量上传文件

```bash
mput *.txt
```

------

## 6. 总结

以上命令涵盖了常见的文件传输和下载任务：

- **`scp`** 和 **`rsync`** 用于在本地与远程服务器之间进行文件传输。`rsync` 提供了更强大的同步和增量传输功能。
- **`wget`** 和 **`curl`** 用于从网络上下载文件，并且都支持断点续传和身份验证。
- **`ftp`** 提供了一种传统的文件传输协议，在某些旧系统中仍然使用。

为了自动化传输过程，你可以结合 `sshpass` 或 SSH 密钥认证来处理密码问题。如果你有更多的特定需求或遇到问题