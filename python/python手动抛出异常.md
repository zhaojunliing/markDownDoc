```python
try:
    print('正常执行')
    # 根据业务逻辑判断，需要手动抛出异常
    raise Exception(print(a))
    #raise Exception('print(a)')#注意这两个的区别，这个带字符串，直接打印字符串里的内容，python把字符串的内容一字不差解析成了异常并打印出来
    print('正常结束')
except Exception as e:
    print('出现异常:', e)

print('OVER')
```

常见异常

```shell
# AttributeError 调用不存在的方法引发的异常

# EOFError 遇到文件末尾引发的异常

# ImportError 导入模块出错引发的异常

# IndexError 列表月越界引发的异常

# IOError I/O操作引发的异常，如打开文件出错等

# KeyError 使用字典中不存在的关键字引发的异常

# NameError 使用不存在的变量名引发的异常

# TabError 语句块缩进不正确引发的异常

# ValueError 搜索列表中不存在值引发的异常

# ZeroDivisionError 除数为零引发的异常
```

