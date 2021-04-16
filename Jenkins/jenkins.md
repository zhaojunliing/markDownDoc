# 使用Jenkins一键打包部署

## Jenkins简介

Jenkins是开源CI&CD软件领导者，提供超过1000个插件来支持构建、部署、自动化，满足任何项目的需要。我们可以用Jenkins来构建和部署我们的项目，比如说从我们的代码仓库获取代码，然后将我们的代码打包成可执行的文件，之后通过远程的ssh工具执行脚本来运行我们的项目。

## Jenkins的安装及配置



### Jenkins的配置

- 运行成功后访问该地址登录Jenkins，第一次登录需要输入管理员密码：http://127.0.0.1:8953/jenkins
- 在启动的过程中可以看到初始的密码已经放在下面的路径了

![image-20200423094523902](F:\github\markDownDoc\Jenkins\assets\image-20200423094523902.png)

![image-20200423094856199](F:\github\markDownDoc\Jenkins\assets\image-20200423094856199.png)

- 选择安装插件方式，这里我们直接安装推荐的插件：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_25.png)

- 进入插件安装界面，联网等待插件安装，时间比较长，请耐心等待。

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_26.png)

- 安装完成后，创建管理员账号：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_27.png)

- 进行实例配置，配置Jenkins的URL：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_28.png)

- 点击系统管理->插件管理，进行一些自定义的插件安装：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_14.png)

- 确保以下插件被正确安装：
  - 根据角色管理权限的插件：Role-based Authorization Strategy
  - 远程使用ssh的插件：SSH plugin

- 通过系统管理->全局工具配置来进行全局工具的配置，比如maven的配置：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_15.png)

- 新增maven的安装配置：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_16.png)

- 在系统管理->系统配置中添加全局ssh的配置，这样Jenkins使用ssh就可以执行远程的linux脚本了：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_17.png)

### 角色权限管理

> 我们可以使用Jenkins的角色管理插件来管理Jenkins的用户，比如我们可以给管理员赋予所有权限，运维人员赋予执行任务的相关权限，其他人员只赋予查看权限。

- 在系统管理->全局安全配置中启用基于角色的权限管理：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_18.png)

- 进入系统管理->Manage and Assign Roles界面：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_19.png)

- 添加角色与权限的关系：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_20.png)

- 给用户分配角色：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_21.png)

## 打包部署应用

### 在Jenkins中创建执行任务

- 首先我们需要新建一个任务：

![](F:\github\markDownDoc\Jenkins\assets\jenkins_use_02.png)

- 设置任务名称后选择构建一个Maven项目：

![image-20200423095641524](F:\github\markDownDoc\Jenkins\assets\image-20200423095641524.png)

- 然后在源码管理中添加我们的git仓库地址：https://192.168.30.105/svn/2018_develop/站段双重预防系统/07source/trunk/jeefh_zd_scyf

![image-20200423095942433](F:\github\markDownDoc\Jenkins\assets\image-20200423095942433.png)

- 此时需要添加一个凭据，也就是我们git仓库的账号密码：

![image-20200423100025314](F:\github\markDownDoc\Jenkins\assets\image-20200423100025314.png)

- 填写完成后选择该凭据，就可以正常连接仓库了；



- 之后我们需要添加一个构建
- ![image-20200423100228880](F:\github\markDownDoc\Jenkins\assets\image-20200423100228880.png)
- 之后添加一个部署：
- ![image-20200423101243464](F:\github\markDownDoc\Jenkins\assets\image-20200423101243464.png)

我们采用的是tomcat上传war包部署的方式。需要配置tomcat的用户名密码，保证可以访问。

![image-20200423100319514](F:\github\markDownDoc\Jenkins\assets\image-20200423100319514.png)

- 之后点击保存操作，我们的任务就创建完成了，在任务列表中我们可以点击运行来执行该任务；

![image-20200423100359482](F:\github\markDownDoc\Jenkins\assets\image-20200423100359482.png)

- 我们可以通过控制台输出来查看整个任务的执行过程：

![image-20200423100423453](F:\github\markDownDoc\Jenkins\assets\image-20200423100423453.png)

