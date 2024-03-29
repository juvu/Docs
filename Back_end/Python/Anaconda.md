# Anaconda

Anaconda(森蚺)是一个包含180+的科学包及其依赖项的发行版本。其包含的科学包包括：conda, numpy, scipy, ipython notebook等.

* conda是包及其依赖项和环境的管理工具
    - 环境管理： 在conda中可以建立多个虚拟环境，用于隔离不同项目所需的不同版本的工具包，以防止版本上的冲突。
    - packages 管理： 可以使用 conda 来安装、更新 、卸载工具包 ，并且它更关注于数据科学相关的工具包。在安装 anaconda 时就预先集成了像 Numpy、Scipy、 pandas、Scikit-learn 这些在数据分析中常用的包。另外值得一提的是，conda 并不仅仅管理Python的工具包，它也能安装非python的包。比如在新版的 Anaconda 中就可以安装R语言的集成开发环境 Rstudio。

## packages

- Anaconda Navigator ：用于管理工具包和环境的图形用户界面，后续涉及的众多管理命令也可以在 Navigator 中手工实现。
- qtconsole ：一个可执行 IPython 的仿终端图形界面程序，相比 Python Shell 界面，qtconsole 可以直接显示代码生成的图形，实现多行代码输入执行，以及内置许多有用的功能和函数。
- spyder ：一个使用Python语言、跨平台的、科学运算集成开发环境。

## install

```sh
# download file from reference
bash Anaconda2-5.0.0.1-Linux-x86_64.sh

echo 'export PATH="/home/henry/anaconda3/bin:$PATH"' >> ~/.zshrc # bin目录加入PATH: ~/.bashrc /etc/profile 系统变量PATH
source ~/.zshrc

conda init zsh
# 更改镜像 可添加 Anaconda Python 免费仓库
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
# 设置搜索时显示通道地址
conda config --set show_channel_urls yes
```

## 使用

```sh
conda info -e|--envs # 显示已创建环境
conda update conda|anaconda|python

# package
conda search [--full-name] <package_full_name>

# env
conda env list # 显示所有的环境
conda list [-n python34|--revisions] # 查看某个指定环境的已安装包
conda create --name|n  py35 python=3.5 numpy pandas
conda install|update|remove [--name | -n  py35] numpy=1.10 scipy pandas
conda install --channel|-c conda-forge
conda upgrade|update --all   # 升级all
conda env export > environment.yaml  # 分享代码的时候，同时也需要将运行环境分享给大家，执行如下命令可以将当前环境下的 package 信息存入名为 environment 的 YAML 文件中
conda env create -f environment.yaml #  用对方分享的 YAML 文件来创建一摸一样的运行环境。
conda create --name <new_env_name> --clone <copied_env_name> # 复制环境

conda activate env_name # 进入名为 env_name 的环境
conda deactivate [env_name] # 退出当前环境

activate env_name # for Windows
deactivate [env_name] # for Windows

conda env remove -n env_name --all # 删除名为 env_name 的环境

python --version|V #查看版本
which -a python

# remove
rm -rf ~/anaconda3
```

## reference

* [offical](https://www.anaconda.com/download/)
* [source address](https://repo.continuum.io/archive/index.html)
* [tsinghua mirror](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)
* [conda](https://conda.io/docs/index.html)
* [conda-cheatsheet](https://conda.io/docs/_downloads/conda-cheatsheet.pdf)
