```shell

conda env list

conda create -n my_env_name python=3.6

conda activate python35

conda deactivate


安装32位python之前先需要设置set CONDA_FORCE_32BIT=1，这样环境可以切换到32位。可以通过conda info命令查看platform是否为32位进行确认。

set CONDA_FORCE_32BIT=1
conda create -n python37-32 python=3.7

```

