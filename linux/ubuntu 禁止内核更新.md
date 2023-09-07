1.　今天使用ubuntu 发现一直在登录界面循环，查资料表明：在使用apt 更新后，会导致内核更新，最终导致内核和驱动不匹配

2.　在解决上述问题之后，查看使用内核

```
1 sudo dpkg --get-selections | grep linux
```

3.　查看是正在使用内核

```
1 uname -a
```

4.　禁止内核更新

```
1 sudo apt-mark hold linux-image-4.4.0-21-generic
2 sudo apt-mark hold linux-image-extra-4.4.0-21-generic
```

5.　重启内核更新

```
1 sudo apt-mark unhold linux-image-4.4.0-21-generic
2 sudo apt-mark unhold linux-image-extra-4.4.0-21-generic
```