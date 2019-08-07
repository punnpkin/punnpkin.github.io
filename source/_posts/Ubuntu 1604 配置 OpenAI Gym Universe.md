---
layout:     post
title:      Ubuntu 1604 配置 OpenAI Gym Universe 
category:   Linux
---

配个环境真是不容易，困难重重，到处是坑。。。

#### Anaconda 的安装

更新源以及安装依赖：

```shell
$ sudo apt update
$ sudo apt install golang python3-dev python-dev libcupti-dev libjpeg-turbo8-dev make tmux htop chromium-browser git cmake zlib1g-dev libjpeg-dev xvfb libav-tools xorg-dev python-opengl libboost-all-dev libsdl2-dev swig
```

下载 Anaconda：https://www.anaconda.com/download/ ，安装使用

```shell
$ bash AnacondaXXXXXXX-86_64.sh
```

出现许可，键入 `q` 跳过，输入 `yes` 表示同意，稍后是一些安装位置信息，回车即可。

<!--more-->

安装之后需要重新开启终端或运行以下命令是配置生效：

```shell
$ source .bashrc
```

创建环境

```shell
$ conda create -n universe python=3.6 pip anaconda
```

激活环境

```shell
$ conda activate universe
```

ps. 如果需要退出虚拟环境，使用：

```shell
(universe)$ conda deactivate
```

如果要移除环境，使用：

```shell
$ conda env remove -n universe
```

在虚拟环境内安装额外的包：

```shell
(universe)$ conda install pip six libgcc swig
```

然后安装 OpenCV：

```shell
(universe)$ conda install opencv
```



#### Docker 的安装

其实看到这里的时候一脸懵逼，不知道装这个干啥，后来想明白是为了跑一些图形化的程序方便一点

先按照国际惯例，更新缓存后安装依赖

```shell
$ sudo apt update
$ sudo apt install \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```

使用中科大源来安装 Docker，首先添加 `GPG` 秘钥：

```shell
$ curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
```

向 `source.list` 中添加 Docker 软件源(不是 DockerHub 源)

```shell
$ sudo add-apt-repository \
    "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
```

再次更新缓存，安装 `docker-ce` 

```shell
$ sudo apt update
$ sudo apt install docker-ce
```

启动并加入自启：

```shell
$ sudo systemctl enable docker
$ sudo systemctl start docker
```

需要把 `docker` 用户加入用户组，这样不用每次执行时都要 `sudo`

```shell
$ sudo groupadd docker
$ sudo usermod -aG docker $USER
```

退出界面重新登录（最好重启一下），执行测试：

```shell
$ docker run hello-world
```

会输出欢迎信息等。

配置国内镜像加速，在 `/etc/docker/daemon.json` 中写入如下内容

```json
{
  "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn/"]
}
```



#### Gym

终于到了 OpenAI Gym， 首先确认在 universe 这个虚拟环境里，使用 git 获取 Gym 的代码：

```shell
(universe)$ cd ~
(universe)$ git clone https://github.com/openai/gym.git
(universe)$ cd gym
```

用 `vim` 打开 `setup.py` ，注释掉（在这里卡了半天）：

```shell
#'mujoco': ['mujoco_py>=1.50', 'imageio'],
#'robotics': ['mujoco_py>=1.50', 'imageio'],
```

MuJoCo 好像是关节机器人用的，其实暂时用不上。回到刚刚的目录里，先确认一下 `pip` 的位置：

```shell
(universe)$ pip -V
```

如果输出信息类似于：

```
pip 19.2.1 from /home/your-name/.local/lib/python2.7/site-packages/pip (python 2.7)
```

说明 `pip` 是全局的没有改过来，理论上说 `conda`创建虚拟环境的时候指定 `pip` 就可以使用局部 `pip`，这里我找了很多地方，并没有一个合适的解答，只能暂时使用文件路径的形式安装：

```shell
(universe)$../anaconda3/envs/universe/bin/pip install -e '.[all]'
```



#### Universe

Universe 的安装就比较简单了：

```shell
(universe)$ cd ~
(universe)$ git clone https://github.com/openai/universe.git
(universe)$ cd universe
(universe)$ ../anaconda3/envs/universe/bin/pip install -e .
```



---



#### 测试一下

单独测试 Gym ，在 `gym_test.py` 中写入：

```python
import gym

env = gym.make('CarRacing-v0')
env.reset()
for _ in range(1000):
    env.render()
    env.step(env.action_space.sample())
```

保存后执行：

```shell
(universe)$ python gym_test.py
```

结果：

![1565179939499](/images/RL/1565179939499.png)



接下来测试 `universe` ，这里又是一个大坑：

```python
# universe_test.py
import gym
import universe  # register the universe environments
from universe import wrappers
 
env = gym.make('gym-core.PongDeterministic-v0')
env = wrappers.experimental.SafeActionSpace(env)
env.configure(remotes=1)
 
observation_n = env.reset()
 
while True:
  action_n =  [env.action_space.sample() for ob in observation_n]
  observation_n, reward_n, done_n, info = env.step(action_n)
  env.render()
```

运行的时候报错 `ModuleNotFoundError: No module named 'gym.benchmarks'` ，找了半天说是高版本的 gym 移除了这个方法，反正就是到处是坑呗。

这里还得重来一遍：

```shell
(universe)$ ./anaconda3/envs/universe/bin/pip uninstall gym
(universe)$ ./anaconda3/envs/universe/bin/pip install gym==0.9.5
```

加载这个例子比较会下载一个比较大的镜像（目测快 1G 了，心疼我的流量），另外它一点都不炫酷

![1565182373556](/images/RL/1565182373556.png)



