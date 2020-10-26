# Git常用命令

## 1、配置个人信息

```shell
// 1.获取git配置信息
git config --list 

// 2.设置名字 user.name 和 user.email(本地的不是github上的)
git config --global user.name "user"
git config --global user.email "user@gmail.com"  

// 3.生成git ssh-key密钥
cd ~/.ssh //检查是否生成过ssk密钥
ssh-keygen -t rsa -C "我的SSH密钥" //-c 为注释，回车3次即可生成，生成ssh 密钥后，可以到~/.ssh目录下查看相关文件，一般来说ssh 密钥会包含id_rsa和id_rsa.pub两个文件，分别表示生成的私钥和公钥

// 4.查看公钥 
cat ~/.ssh/id_rsa.pub //打印并将相应内容复制到源代码管理服务器即可实现git的无密码管理
```

## 2、创建本地版本库

```shell
// 1.git init:初始化一个目录为git可以管理的仓库
git init
```

## 3、连接远程库

```shell
git remote add origin git@ssh的地址
```



## 4、查看本地工作区、暂存区中文件的修改状态

```shell
// 1.红褐色：创建之后没有add，没提交，不在版本控制范围之内，这时候文件是红褐色的，需要先add文件；
// 2.绿色：add之后是文件绿色的，没有提交（commit）;
// 3.蓝色：原本有一个文件，改动过后没有提交（commit）是蓝色的，提交之后，变成正常颜色
git status
```

## 5、把文件添加到版本库

```shell
// 1.Git add 文件名称:添加文件到Git暂存区
git add 文件名称
```

## 6、将Git暂存区的代码提交到Git中

```shell
// 1.Git commit -m “message”:将Git暂存区的代码提交到Git中;
git commit -m “message”
```

## 7、上传到远程库

```shell
// 1.git push <远程主机名> <本地分支名>:<远程分支名>
git push -u origin master
```



## 8、回滚代码版本

```shell
// 1.Git checkout 文件名:将单个文件代码回滚到上次提交的版本；此回滚是先在Git暂存区中查找，如果暂存区中有则回滚暂存区中的代码；如果暂存区中没有则再去Git上回滚该代码；
git checkout 文件名

// 2.Git reset --hard HEAD^:回滚到上一个版本
git reset --hard HEAD^

// 3.回滚到指定的版本，HEAD对应为版本的唯一ID号(HEAD ID可以只写前6位)。
git reset --hard HEAD
```

## 9、查看Git提交版本的所有信息

```shell
// 1.Git log：查看Git提交版本的所有信息，后提交的在上面；
git log

//查看Git版本提交信息，值只显示版本唯一HEAD和提交备注信息(是Git log的简版),后提交的在上面；
git log --pretty=oneline
```

## 10、克隆

```shell
// 1.git clone [url]:下载一个项目和它的整个代码历史;
git clone [url]

// 2.git clone [url] -b branchname 表示:branchname分支名称；
git clone [url] -b branchname 
```






