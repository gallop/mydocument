# git 的基本使用

## 0. git的基本使用

> git 的本地仓库 分为三个区：工作区、暂存区、本地库  
> 1. 将当前工作区改动的提交到 暂存区 用 git add 命令  
>    查看当前工作区已改动 但未提交到暂存区的文件列表 用 git status（只查带动的文件列表，详细改动哪些地方 用 git diff 命令）
> 2. git diff --cached 列出本地库和暂存区还未commit到本地库的详细变化
> 3. git commit -a 此命令将跳过git add 这步骤，直接将所有工作区的改动提交到本地库，执行完此命令，再执行git status  git diff  git diff --cached 都将返回空（无任何改变）
>

##### 0. 查看/修改用户名和邮箱地址

>用户名和邮箱地址是本地git客户端的一个变量，不随git库而改变。  
>每次commit都会用用户名和邮箱纪录。
>

1. 查看用户名和邮箱地址：

```
$ git config user.name

$ git config user.email
```
2. 修改/设置用户名和邮箱地址：

```
$ git config --global user.name "username"

$ git config --global user.email "email"
```
> --global，代表的是全局。如果你要修改当前全局的用户名和邮箱时，需要添加这个参数

##### 1. git 的基本操作

1. 在工作目录中初始化新仓库
  要对现有的某个项目开始用 Git 管理，只需到此项目所在的目录，执行：
  ```
  $ git init
  ```
2. 本地仓库的基本操作  
  ```
  $ git add text.txt  //将text文件变化从工作区提交到暂存区
  $ git commit -m 'initial project version' //将暂存区未提交到本地库文件变化提交到本地库
  $ git commit -a -m "initial project" //将工作区及暂存区未提交到本地库文件变化提交到本地库
  $ git status  // 列出工作区和暂存区(或暂存区和本地库)有变化的文件列表
  $ git diff    //列出工作区和暂存区有变化的文件的详细变化处
  $ git diff --cached  //列出暂存区和本地库详细的变化处
  ```

3. 从远程仓库克隆到本地
  ```
  $ git clone https://github.com/gallop/gallop-bigears.git
  ```
    这会在当前目录下创建一个名为gallop-bigears的目录，远程项目就会克隆到此目录下，如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：
  ```
  $ git clone https://github.com/gallop/gallop-bigears.git newproject
  ```
  唯一的差别就是，现在新建的目录成了 newproject，其他的都和上边的一样。

4. 忽略某些文件
  一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。我们可以创建一个名为 .gitignore 的文件，列出要忽略的文件模式。来看一个实际的例子：  
  一个 .gitignore 文件的例子：
  ```
# 此为注释 – 将被 Git 忽略
# 忽略所有 .a 结尾的文件
*.a
# 但 lib.a 除外
!lib.a
# 仅仅忽略项目根目录下的 TODO 文件，不包括 subdir/TODO
/TODO
# 忽略 build/ 目录下的所有文件
build/
# 会忽略 doc/notes.txt 但不包括 doc/server/arch.txt
doc/*.txt
# 忽略 doc/ 目录下所有扩展名为 txt 的文件
doc/**/*.txt
  ```
5. 删除文件
彻底删除一个文件，也就是同时从当前工作区、暂存区、本地库中删除  
```
$ git rm -f test.txt
$ git commit -m "删除一个文件"
```
如果我们只是想把文件从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录（工作区）中。换句话说，仅是从跟踪清单中删除。使用以下命令  
```
$ git rm --cached test.txt
$ git commit -m "将文件从跟踪清单中删除"
```
> 以上命令将test.txt 从暂存区和本地库删除，但在当前目录（工作区）中仍然存在  

    ```
    $ git rm log/\*.log
    ```
    注意到星号 * 之前的反斜杠 \，因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开（译注：实际上不加反斜杠也可以运行，只不过按照 shell 扩展的话，仅仅删除指定目录下的文件而不会递归匹配。上面的例子本来就指定了目录，所以效果等同，但下面的例子就会用递归方式匹配，所以必须加反斜杠。）。此命令删除所有 log/ 目录下扩展名为 .log 的文件。类似的比如：
    ```
    $ git rm \*~
    ```
    会递归删除当前目录及其子目录中所有 ~ 结尾的文件。
6. 修改文件名
```
$ git mv file_from file_to
```
此时查看状态信息，也会明白无误地看到关于重命名操作的说明：
```
$ git mv README.txt README
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        renamed:    README.txt -> README
```  

## 1. git 和 github 的连接使用  
1. 登陆github 创建SSH key
因为本地Git仓库和GitHub仓库之间的传输是通过SSH加密传输的，GitHub需要识别是否是你推送，GitHub只要知道了你的公钥，就可以确认只有你自己才能推送，所以需要配置ssh key。

  Git Bash，输入命令，创建SSH Key：
  ```
  $ ssh-keygen -t rsa -C "xxxxxxx@qq.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/gallop/.ssh/id_rsa):
Created directory '/c/Users/gallop/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /c/Users/gallop/.ssh/id_rsa.
Your public key has been saved in /c/Users/gallop/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:B7sfG2hEn220ZbbtMB3/qPFEHyyTE1yjWqHaMZZO1BQ 39100782@qq.com
The key's randomart image is:
+---[RSA 2048]----+
|           .oE.o |
|          . +.+ .|
|        o  B.=+. |
|       . +*+==++o|
|        S.+++==o+|
|       . + . .+*o|
|        + o . o +|
|       . . + =   |
|          o . .  |
+----[SHA256]-----+

  ```
2. 在GitHub上设置你生成的公钥  
到GitHub上，打开“Account settings”--“SSH Keys”页面，然后点击“Add SSH Key”，填上Title（随意写），在Key文本框里粘贴 id_rsa.pub文件里的全部内容。  
 id_rsa.pub 在 （/c/Users/gallop/.ssh目录中）
3. git bash里输入下面的命令登陆
```
$ ssh -T git@github.com
The authenticity of host 'github.com (13.250.177.223)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,13.250.177.223' (RSA) to the list of known hosts.
Hi gallop! You've successfully authenticated, but GitHub does not provide shell access
```

## 2. 给本地仓库添加远程远程仓库
要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 git remote add [shortname] [url]
```
$ git remote add mytest https://github.com/gallop/girl.git
$ git remote -v
mytest  https://github.com/gallop/girl.git (fetch)
mytest  https://github.com/gallop/girl.git (push)
```
>这里的mytest 为远程仓库地址的shortname

因为远程仓库已经存在并且有代码，所以新的本地仓库想push 得先把远程仓库的master分支pull下来

```
$ git pull mytest master master
From https://github.com/gallop/girl
 * branch            master     -> FETCH_HEAD
 * branch            master     -> FETCH_HEAD
fatal: refusing to merge unrelated histories
```
>当本地分支与远程分支没有共同祖先时，会出现   
>fatal: refusing to merge unrelated histories 的问题。
>
>解决方案
>可以使用 rebase 的方式来进行合并

```
$ git pull --rebase mytest master
From https://github.com/gallop/girl
 * branch            master     -> FETCH_HEAD
First, rewinding head to replay your work on top of it...
Applying: init
Applying: or your cmmhanges. Lines starting
Applying: 提交test文件
```
然后使用push 命令，就可以把本地仓库push 到远程仓库中了。
push命令格式： git push [remote-name] [branch-name]

```
$ git push mytest master
Enumerating objects: 24, done.
Counting objects: 100% (24/24), done.
Delta compression using up to 8 threads
Compressing objects: 100% (17/17), done.
Writing objects: 100% (23/23), 1.75 KiB | 199.00 KiB/s, done.
Total 23 (delta 9), reused 0 (delta 0)
remote: Resolving deltas: 100% (9/9), completed with 1 local object.
To https://github.com/gallop/girl.git
   33284c9..9f41313  master -> master
```
到此完成一个本地仓库添加到远程仓库的所有操作。

## 3、git commit之后，想撤销commit

代码如下：
```
git reset --soft HEAD^

```
>说明：  
HEAD^的意思是上一个版本，也可以写成HEAD~1  
如果你进行了2次commit，想都撤回，可以使用HEAD~2

### 几个参数：
--mixed
意思是：不删除工作空间改动代码，撤销commit，并且撤销git add . 操作
这个为默认参数,git reset --mixed HEAD^ 和 git reset HEAD^ 效果是一样的。


--soft  
不删除工作空间改动代码，撤销commit，不撤销git add .

--hard
删除工作空间改动代码，撤销commit，撤销git add .

注意完成这个操作后，就恢复到了上一次的commit状态。


### 如果commit注释写错了，只是想改一下注释，只需要：
git commit --amend

此时会进入默认vim编辑器，修改注释完毕后保存就好了。

## github上删除一个文件或者删除一个文件夹下的所有文件

```
$ git pull origin master                    # 将远程仓库里面的项目拉下来
$ dir                                                # 查看有哪些文件夹
$ git rm -r --cached target              # 删除target文件夹
$ git commit -m '删除了target'        # 提交,添加操作说明
$ git push -u origin master               # 将本次更改更新到github项目上去

```
