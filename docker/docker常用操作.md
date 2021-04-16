apt-get update

apt-get install portaudio19-dev -y

apt install libsndfile1 -y

apt install cmake -y

apt install vim -y



```
pip install -r requirements.txt -i https://mirror.baidu.com/pypi/simple

pip install pytest -i https://mirror.baidu.com/pypi/simple

pip install flask_cors -i https://mirror.baidu.com/pypi/simple
```







```
--将容器转换为镜像
docker commit containerid zjl/masr:v1

--将镜像打包成文件
docker save -o masr_v1.tar zjl/masr:v1

--将文件导入到镜像中
docker load --input masr_v1.tar

```



apt install gcc-4.8 g++-4.8
cd /usr/bin
mv gcc gcc_bak
mv g++ g++_bak
ln -s gcc-4.8 gcc
ln -s g++-4.8 g++



export CUDA_HOME=/usr/local/cuda-10.1/

export PATH=/usr/local/cuda-10.1/bin:$PATH
export LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH

export WARP_CTC_PATH="/workspace/share/warp-ctc/build"



```
pip install torch==1.6.0+cu101 torchvision==0.7.0+cu101 -f https://download.pytorch.org/whl/torch_stable.html
```



https://blog.csdn.net/ly18846826264/article/details/107076297/





cmake -version





cat /usr/local/cuda/version.txt

# 显示结果：CUDA Version 10.0.130



```
docker run --name masr_zjl --device=/dev/mem:/dev/mem:r --privileged --net=host -p 5000:5000 --env="DISPLAY" --env QT_X11_NO_MITSHM=1 -v ~/workspace/share:/workspace/share -v /etc/localtime:/etc/localtime -it --runtime=nvidia  --shm-size=15g zjl/masr:v1 
```