# moviepy使用pyinstaller打包后执行报错

> 报错信息：AttributeError: module audio/video.fx.all has no attribute 'fadein'、crop



## 问题描述：

在开发moviepy的Python程序使用pyinstaller打包后执行，报了两个错：

1. AttributeError: module ‘moviepy.video.fx.all’ has no attribute ‘crop’
2. AttributeError: module ‘moviepy.audio.fx.all’ has no attribute ‘audio_fadein’

## 问题原因：

这两个错是因为moviepy包下子包audio.fx.all、video.fx.all对应的目录moviepy\audio\fx\all、moviepy\video\fx\all下的包文件__init__.py中，分别使用了如下的方式import模块：
**audio.fx.all子包**：

```python
import pkgutil

import moviepy.audio.fx as fx

__all__ = [name for _, name, _ in pkgutil.iter_modules(
    fx.__path__) if name != "all"]

for name in __all__:
    exec("from ..%s import %s" % (name, name))
```

**video.fx.all子包**：

```python
import pkgutil

import moviepy.video.fx as fx

__all__ = [name for _, name, _ in pkgutil.iter_modules(
    fx.__path__) if name != "all"]

for name in __all__:
    exec("from ..%s import %s" % (name, name))
```

## 解决办法：
1、将这2个__init__.py文件的上面所列最后两行注释掉；
2、在Python中将上述注释掉两行代码后前面的内容复制后加一个如下输出语句的代码：

```python
for name in __all__: 
	print("from  moviepy.video.fx import %s" % (name))
```

audio.fx.all子包替换后变为：

```python
import pkgutil

import moviepy.audio.fx as fx

__all__ = [name for _, name, _ in pkgutil.iter_modules(
    fx.__path__) if name != "all"]

#for name in __all__:
#    exec("from ..%s import %s" % (name, name))

from  moviepy.video.fx import audio_fadein
from  moviepy.video.fx import audio_fadeout
from  moviepy.video.fx import audio_left_right
from  moviepy.video.fx import audio_loop
from  moviepy.video.fx import audio_normalize
from  moviepy.video.fx import volumex
```

video.fx.all子包替换后变为：

```python
import pkgutil

import moviepy.video.fx as fx

__all__ = [name for _, name, _ in pkgutil.iter_modules(
    fx.__path__) if name != "all"]

#for name in __all__:
#    exec("from ..%s import %s" % (name, name))
    
from  moviepy.video.fx import accel_decel
from  moviepy.video.fx import blackwhite
from  moviepy.video.fx import blink
from  moviepy.video.fx import colorx
from  moviepy.video.fx import crop
from  moviepy.video.fx import even_size
from  moviepy.video.fx import fadein
from  moviepy.video.fx import fadeout
from  moviepy.video.fx import freeze
from  moviepy.video.fx import freeze_region
from  moviepy.video.fx import gamma_corr
from  moviepy.video.fx import headblur
from  moviepy.video.fx import invert_colors
from  moviepy.video.fx import loop
from  moviepy.video.fx import lum_contrast
from  moviepy.video.fx import make_loopable
from  moviepy.video.fx import margin
from  moviepy.video.fx import mask_and
from  moviepy.video.fx import mask_color
from  moviepy.video.fx import mask_or
from  moviepy.video.fx import mirror_x
from  moviepy.video.fx import mirror_y
from  moviepy.video.fx import painting
from  moviepy.video.fx import resize
from  moviepy.video.fx import rotate
from  moviepy.video.fx import scroll
from  moviepy.video.fx import speedx
from  moviepy.video.fx import supersample
from  moviepy.video.fx import time_mirror
from  moviepy.video.fx import time_symmetrize
```

