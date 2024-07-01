本教程采用的配置为VMWARE+ubuntu-18.04.6+ns-3(3.28)

一、虚拟机的安装
1. 首先需要安装VMware Workstation Pro，并安装VMware Workstation Pro的虚拟机。
    a. 下载方式可以在联想应用商店下载（不用梯子而且速度很快）
    b. 或者在官网下载[Vmware官网](https://www.vmware.com/cn/products/workstation-pro/workstation-pro-evaluation.html)
    c.vmware的激活密令可以去百度搜索对应版本的激活密令。
2. 下载Ubuntu的镜像文件
    这里推荐去清华园下载 [清华园镜像](https://mirrors.tuna.tsinghua.edu.cn/)

3. 具体安装流程
    
    这里CSDN有一份比较好用的教程而且写的很详细。[教程](https://blog.csdn.net/weixin_52799373/article/details/124324077?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522170063026216800213026031%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=170063026216800213026031&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_positive~default-1-124324077-null-null.142^v96^pc_search_result_base2&utm_term=Vmware&spm=1018.2226.3001.4187)

    博主这里选用的是完整安装，因此可以去找一下两者的区别。
    

二、 NS-3的安装
    NS-3的安装可以分为两部分，第一部分为软件安装的依赖环境，第二部分为NS-3软件的正式安装与测试。（绝大部分都是卡在了软件的依赖环境）


1. 软件环境的依赖只需要按照如下步骤
   
```


    sudo apt-get install gcc g++ python python-dev -y
    sudo apt-get install mercurial -y
    sudo apt-get install bzr -y
    sudo apt-get install gdb valgrind -y
    sudo apt-get install gsl-bin libgsl-dev libgsl23 -y
    sudo apt-get install flex bison libfl-dev -y
    sudo apt-get install g++-3.4 gcc-3.4 -y
    sudo apt-get install tcpdump -y
    sudo apt-get install sqlite sqlite3 libsqlite3-dev -y
    sudo apt-get install libxml2 libxml2-dev -y
    sudo apt-get install libgtk2.0-0 libgtk2.0-dev -y
    sudo apt-get install vtun lxc -y
    sudo apt-get install uncrustify -y
    sudo apt-get install doxygen graphviz imagemagick -y
    sudo apt-get install texlive texlive-extra-utils texlive-latex-extra -y
    sudo apt-get install python-sphinx dia -y
    apt-get install gir1.2-goocanvas-2.0 python-gi python-gi-cairo python-pygraphviz python3-gi -y python3-gi-cairo python3-pygraphviz gir1.2-gtk-3.0 ipython ipython3 -y
    sudo apt-get install libboost-signals-dev libboost-filesystem-dev -y
    sudo apt-get install openmpi* -y
```


或将上述命令打包成.sh文件，在Linu下执行。在等待一段时间后就可以完整依赖环境的安装。

2. NS-3的安装

a. 首先返回home
```
cd
 git clone https://gitlab.com/nsnam/bake # 从gitlab克隆项目
 export BAKE_HOME=`pwd`/bake  # 将bake的路径加入系统路径
 export PATH=$PATH:$BAKE_HOME
 export PYTHONPATH=$PYTHONPATH:$BAKE_HOME
 cd /bake # 进入bake文件夹
 ./bake.py check # 检查依赖是否已经安装完成
```

如果没有报错，说明依赖已经安装完成。接下来就可以进行NS-3的安装了。

```
./bake.py configure -e ns-3.28 # 指定下载NS3的版本号
  ./bake.py show   
  ./bake.py download -vvv #下载
  ./bake.py build -vvv #build
```


b. 测试安装成功与否

进入刚才下载并build成功的ns3文件夹中
```
cd  ns-3.28
./waf configure --enable-examples --enable-tests
```
你应该可以看到一个报告，这个报告中会有一些not enabled的项目，我们只需要关注

Python API Scanning Support : enabled
Python Bindings : enabled

完成后执行下一步
```
./test.py
```

便会进入一个更长的测试，这个测试的时间很长，他会测试每个可能用到的文件和功能是否正常，若正常则返回PASS。我安装后只有一个测试失败，In64*64的测试失败后并不影响最终的结果

c. 测试例程
```
cd ns-3.27 

sudo ./waf  --run hello-simulator
``` 
如果此时输出hello-simulator则证明安装成功。