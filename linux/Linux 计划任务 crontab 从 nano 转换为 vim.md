# Linux 计划任务 crontab 从 nano 转换为 vim

crontab 默认是 nano 编辑器，不好用，将 crontab 改为 vim 编辑器

直接定义变量指定 crontab 为 vim 编辑器

```shell
export EDITOR="/usr/bin/vim" ; crontab -e
```

或使用更简单的方式，直接从重新指定 crontab 的默认编辑器，执行以下命令，选择对应编辑器的编号即可

```shell
select-editor
```