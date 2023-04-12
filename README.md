# git 使用笔记

## 一、【Git创建本地仓库】

### 1、设置git用户名和邮箱
<details>
<summary>code</summary>

- 假设github的用户名：`zhangsan`，邮箱：`123456@163.com`

`git config --global user.name 'zhangsan'`  

`git config --global user.email '123456@163.com'`  

| 参数           | 说明               |
| :------------: | :----------------: |
| --global       | 全局设置(默认关联所有未指定的项目),信息存储在`~/.gitconfig`文件 |
| --local/(null) | 当前设置(只针对当前仓库有效)    |
</details>

### -- git添加/取消http/https协议代理（可选）--
<details>
<summary>code</summary>

*(1)设置加速代理为`https://ghproxy.com`*  
`git config --local url."https://ghproxy.com/https://github.com/".insteadOf https://github.com/`

*(2)设置`HTTP/HTTPS 协议`代理*  
 `git config --local http.proxy http://127.0.0.1:port`

*(3)设置`SOCKS5 协议`代理*  
`git config --local http.proxy socks5://127.0.0.1:port`

*(4)git 取消代理设置*  
`git config --local --unset http.proxy`
</details>

### 2、查看git设置信息
<details>
<summary>code</summary>

`git config --list`
</details>

### 3、初始化仓库
<details>
<summary>code</summary>

`git init`

- 当前目录下出现隐藏文件夹`.git`;设置信息存储在`.git/config`文件
</details>

### 4、查看git仓库大小
<details>
<summary>code</summary>

`git count-objects -vH`
</details>

### 5、添加文件到暂存区
<details>
<summary>code</summary>

- 添加单个文件(file.txt)  
`git add file.txt`

- 添加所有文件  
`git add .`
</details>

### 6、暂存区文件处理
<details>
<summary>code</summary>

#### (一)查看暂存区文件  
`git status -s`

- M README # 右边的 M 表示该文件被修改了但是还没放入暂存区  
MM Rakefile # 左边的 M 表示该文件被修改了并放入了暂存区；右边的 M 表示该文件被修改了但是还没放入暂存区  
A lib/git.rb # A表示新添加到暂存区中的文件  
?? LICENSE.txt # ??表示新添加的未跟踪文件

| **删除暂存区和工作区指定文件(file.txt)**  | **删除暂存区和工作区指定文件夹(file)**  | **删除暂存区所有文件(保留工作区文件)** |
| :---: | :---: | :---: |
| `git rm file.txt`  | `git rm -r file` | `git rm -rf --cached .` |

> --cached:表示删除git追踪文件,并不会删除工作区任何文件
</details>

### 7、提交文件到本地仓库
<details>
<summary>code</summary>

`git commit -m "提交描述"`

| 参数  | 说明  |
| :---: | :---: |
| -m      | 添加提交描述 |
| -a      | 对已经track的文件的改动越过暂存区，直接提交  |
| --amend | 修改替换最近一次提交记录 |
</details>

## 二、【git提交本地仓库到远程仓库】

### 1、查看本地仓库连接的远程仓库链接
<details>
<summary>code</summary>

`git remote -v`
</details>

### 2、添加/修改/删除远程仓库链接[remote命令] <a id="_link_remote"></a>
- **添加https链接(二选一)[公开仓库首选]** 
<details>
<summary>code</summary>

*(1)添加别名为origin 的github仓库https链接*  
`git remote add origin https://github.com/zhangsan/xxx.git`

*(2)添加别名为zhangsan 的github仓库https链接*  
`git remote add zhangsan https://github.com/zhangsan/xxx.git`

*(3)修改别名为zhangsan 的github仓库https链接*  
`git remote set-url zhangsan https://github.com/zhangsan/yyy.git`

*(4)删除本地仓库的远程仓库别名origin*  
`git remote remove origin`

*(5)删除本地仓库的远程仓库别名zhangsan*  
`git remote remove zhangsan`
</details>

- **添加ssh链接(二选一)[私有仓库首选]** <a id="_link_ssh"></a>
<details>
<summary>code</summary>

> 查看是否已生成过ssh密钥文件(存在 id_rsa.pub 或 id_dsa.pub 文件，请跳过创建密钥)  
>  `cd ~/.ssh && ls -a`

*(1)创建ssh密钥*  
`ssh-keygen -t rsa -C "123456@163.com"`

| 参数  | 说明  |
| :---: | :---: |
| -t    | 指定密钥类型，默认是 rsa  |
| -C    | 设置注释文字 |

*(2)把ssh key添加到github*
> 复制文件`~/.ssh/id_rsa.pub`的内容，把key添加到：github > settings > SSH and GPG keys > New SSH key > 粘贴保存

*(3)测试ssh连接*  
`ssh -T git@github.com`

> 如下面表示已经连接成功：
You've successfully authenticated, but GitHub does not provide shell access.

*(4)添加远程仓库的ssh链接, 别名为origin* 【zhangsan：GitHub用户名, xxx：仓库名】  
`git remote add origin git@github.com:zhangsan/xxx.git`

*(5)修改别名为zhangsan 的github仓库ssh链接*  
`git remote set-url zhangsan git@github.com:zhangsan/yyy.git`

> 后面的操作与添加https链接的类似，不再赘述！！
</details>

- **使用GitHub_TOKEN(PAT)添加远程仓库链接** <a id="_link_PAT"></a>
<details>
<summary>code</summary>

*(1)设置一个GitHub的GITHUB_TOKEN(PAT)密钥*
- 点击右侧图标[![PAT](https://github.githubassets.com/favicons/favicon.png)](https://github.com/settings/tokens/new) ，设置token密钥
- 在`Note`处填入一个名称 -> 在`Expiration`处选择一个有效期 -> 在`Select scopes`处依次勾选`repo`、`workflow`、`write:packages`和`delete:packages` -> 点击最下方`Generate token`
![PAT](./img/PAT.png)
- 只能显示一次，请复制保存好刚刚生成的GITHUB_TOKEN(PAT)密钥。

*(2)添加别名为origin 的github仓库链接*  
`git remote add origin https://GITHUB_TOKEN@github.com/zhangsan/xxx.git`

> 后面的操作与添加https链接的类似，不再赘述！！
</details>

### 3、增/删/查/改branch分支及替换分支文件[branch/checkout命令]
<details>
<summary>code</summary>

*(1)查看本地/远程分支*

| **本地分支**  | **远程分支**  | **本地和远程分支** |
| :---: | :---: | :--: |
| `git branch`  | `git branch -r` | `git branch -a` |

*(2)创建/修改/切换/合并/删除分支*

| **创建分支名为`test`**  | **修改分支名为`main`**  | **删除分支名为`test`** |
| :---: | :---: | :---: |
| `git branch test`  | `git branch -m main` <br/>or<br/> `git branch -M main` | `git branch -d test` |

| 参数  | 说明  |
| :---: | :---: |
| -f --force | 强制 |
| -d --delete | 删除  |
| -D | --delete --force的快捷键 |
| -m --move | 移动或重命名 |
| -M | --move --force的快捷键 |

- 切换分支[checkout命令]

| **以`main`分支为基础创建并切换到`dev`分支**  | **以当前分支为基础创建并切换到`dev`分支**  | **切换到main分支** |
| :---: | :---: | :--: |
| `git checkout -b dev main`  | `git checkout -b dev` | `git checkout main` |

> --orphan:创建孤立分支，没有父节点的历史提交的新分支，但存在父节点所有文件。  
-b:创建包含父节点所有文件和历史提交的分支

*(4)替换其他分支文件到当前分支上*  
- 将`test`分支上的`a.txt`文件替换到当前分支`main`上  
`git checkout test a.txt`

- 将`test`分支上的`a.txt`文件和`.github`文件夹替换到当前分支`main`上  
`git checkout test a.txt .github`

- 将`test`分支上的所有文件替换到当前分支`main`上  
`git checkout test .`
</details>

### 4、合并其他分支到当前分支[merge命令]
<details>
<summary>code</summary>

- 合并`dev`分支到当前分支，自动进行新的提交  
`git merge dev`

- 合并`dev`分支到当前分支中，但不自动进行新的提交  
`git merge --no-commit dev`

- 禁止快进式合并[阻止`dev`分支的历史记录合并到当前分支，方便后期让当前分支回滚]  
`git merge --no-ff dev`
</details>

### 5、推送到远程仓库[push命令]
<details>
<summary>code</summary>

 #### (一)本地的`main`分支推送到别名`origin`远程仓库的`main`分支  
`git push -u origin main`

#### (二)本地的`master`分支推送到别名`origin`远程仓库的`main`分支  
`git push origin master:main`

#### (三)本地仓库所有分支推送到远程仓库  
`git push --all origin`

#### (四)强制推送本地仓库到远程仓库  
`git push -u -f origin main`

#### (五)删除远程仓库`main`分支  
`git push origin :main` <br/>or<br/> `git push origin --delete main`
</details>

### 6、取回远程仓库代码[pull命令]
<details>
<summary>code</summary>

> 必须存在`remote`远程仓库链接，如果没有请查看[添加/修改/删除远程仓库链接[remote命令]](#_link_remote)

- 拉取远程仓库`origin`的`test`分支到本地的`main`分支  
`git pull origin test:main`
</details>

## 三、【git拉取远程仓库代码维护】

### 1、git拉取远程仓库到本地工作区[clone/pull命令]  
<details>
<summary>code</summary>

> clone：除了不能拉他人的私库以外，均可使用  
> pull：只有当前用户有权限的仓库并且本地工作区已经`git init`初始化才能使用。

*(1)使用https协议拉取远程仓库*  
- 第三方公开仓库使用clone  
`git clone https://github.com/zhangsan/xxx.git`

- git设置的用户名有权限的远程仓库可用pull命令[必须先`git init`初始化]  
`git pull https://github.com/zhangsan/xxx.git`

*(2)使用GITHUB_TOKEN(PAT)密钥拉取*  
> 创建GITHUB_TOKEN(PAT)密钥请参考[使用GitHub_TOKEN(PAT)添加远程仓库链接](#_link_PAT)

- 不使用代理  
`git clone https://GITHUB_TOKEN@github.com/zhangsan/xxx.git`

- 使用https://ghproxy.com代理  
`git clone https://GITHUB_TOKEN@ghproxy.com/github.com/zhangsan/xxx.git`

*(3)使用ssh协议拉取远程仓库*  
> 创建添加ssh密钥请参考[添加ssh链接(二选一)[私有仓库首选]](#_link_ssh)

`git clone git@github.com:zhangsan/xxx.git`

- #### --- 提醒 ---：  
`git clone --depth=1 https://github.com/zhangsan/xxx.git`

> --depth=1:表示只拉取最新一次历史提交记录(减少.git空间)，慎用
- 如果使用了--depth=1参数拉取远程仓库，使用`git push`会出现"shallow update not allowed"报错，请使用如下代码补齐`.git`信息即可  
`git fetch --unshallow https://github.com/zhangsan/xxx.git`
</details>

### 2、对仓库进行压缩合并/回退/变基/撤销操作[revert/reset/rebase命令]

#### (一)查看历史记录(可获取`commit_id`)
<details>
<summary>code</summary>

| **日志从新-旧排序**  | **日志从旧-新排序**  |  **仓库历史版本记录** |
| :---: | :---: | :---: |
| `git log` | `git log --reverse` | `git reflog` |
</details>

> 假设操作的commit_id为：abcd123def  
#### (二)撤销某次提交（所有log均保留），此次撤销会作为一次新的提交
<details>
<summary>code</summary>

`git revert abc123edf`

- 撤销多次提交(从abc123edf-uvw789xyz)  
`git revert -n abc123edf..uvw789xyz`
</details>

#### (三)回退仓库至历史提交
<details>
<summary>code</summary>

*(1)提交之后的修改不做保留，git status干净的工作区，即工作区所有文件被回退*  
`git reset --hard abc123def`

*(2)提交之后的修改会退回到暂缓区, 工作区所有文件不会发生变化*  
`git reset --soft commitid`

- 此时如果直接执行`git push -f`可以在不影响工作区下让远程仓库回退代码到`abc123def`位置，此时该位置会成为远程仓库最新一次提交  
</details>

#### (四)恢复工作区已修改的代码，保持与远程仓库一致
<details>
<summary>code</summary>

*(1)未添加到暂存区*

| **丢弃某个修改的文件(a.txt)** | **撤销某个修改的文件(a.txt)** | **撤销指定文件`a.txt`到`abc123def`版本** | **丢弃全部修改的文件(回到暂存区之前的样子,放弃所有修改)** |
|:---: | :---: | :---: | :---: |
| `git checkout -- a.txt` | `git checkout a.txt` | `git checkout abc123def a.txt` | `git checkout -- .` |

*(2)代码`git add`到缓存区，但未commit提交*

| **丢弃暂存区某个文件(a.txt)** | **丢弃全部暂存区的文件** |
| :---: | :---: |
| `git reset HEAD a.txt` | `git reset HEAD .` |

*(3)`git commit`到本地分支、但没有`git push`到远程*

| **回到`abc123def`版本** | **回到最新的一次提交** | **代码保留，回到 git add 之前** |
| :---: | :---: | :---: |
| `git reset --hard abc123def` | `git reset --hard HEAD^` | `git reset HEAD^` |
</details>

#### (五)合并压缩多次提交日志[变基操作]
<details>
<summary>code</summary>

*(1)合并`abc123def`版本以后的所有提交日志*(确保暂存区无未提交文件)  
`git rebase -i abc123def^`

- 弹出修改窗口 -> 按字母`i`键进入编辑模式 -> 将pick修改为s或f，然后按`esc`键输入`:wq`保存 -> 如有冲突请解决冲突后再执行`git rebase --continue`  

> squash  表示将该commit注释合并到上一个注释(旧)或者自定义一个新的注释(在新的窗口中修改)  
fixup  表示放弃当前commit注释(删除该commit注释)，保留上一个pick commit注释  

| **继续执行** | **取消合并** | **撤销当前多次合并提交** |
| :---: | :---: | :---: |
| `git rebase --continue` | `git rebase --abort` | `git rebase --skip` |
</details>

### 3、上游仓库管理
<details>
<summary>code</summary>

*(1)添加远程上游仓库*  
`git remote add upstream https://github.com/xxx/xxx.git`

*(2)暂存已修改代码*  
`git stash save`

*(3)同步更新上游仓库*  
`git pull upstream master`

*(4)恢复暂存已修改的代码*  
`git stash pop`

*(5)恢复与上游仓库`master`分支代码一致*  
`git reset --hard upstream/master`
</details>

## 四、【git标签tag的使用】  

> 假设操作的tag标签名为：v1.0  
### 1、tag标签基本用法
<details>
<summary>code</summary>

*(1)查看tag标签*

| **查看本地tag标签** | **查看本地`v1.0`tag标签的详细信息** | **查看远程`origin`所有tag标签** |
| :---: | :---: | :---: |
| `git tag -l` | `git show v1.0` | `git ls-remote --tags origin` <br/>or<br/> `git show-ref --tag` |

*(2)创建tag标签*

| **以最近一次提交创建本地`v1.0`标签** | **以历史commit_id为`abc123def`提交创建`v1.0`标签** |
| :---: | :---: |
| `git tag v1.0` | `git tag -a v1.0 abc123def` |

> -a：定义标签名(当仅有`-a`时建议与`-m`一同使用)
-m：创建附注内容
-f：强制覆盖已存在标签

*(3)删除tag标签*

| **删除本地`v1.0`标签** | **删除远程`origin`的`v1.0`标签** | **强制删除远程`origin`的`v1.0`标签** |
| :---: | :---: | :---: |
| `git tag -d v1.0` | `git push origin -d v1.0` <br/>or<br/> `git push origin :refs/tags/v1.0` | `git push origin :v1.0 -f` |

- 批量删除tag标签

| **删除本地所有标签** | **删除远程`origin`的所有标签** |
| :---: | :---: |
| `git tag -l \| xargs git tag -d` | `git show-ref --tag \| awk '{print $2}' \| xargs git push origin -d` <br/>or<br/> ` git ls-remote --tags origin \| awk '{print $2}' \| xargs git push origin -d`|

*(4)推送tag标签到远程仓库*(`-f`表示强制推送,谨慎使用)

| **推送本地`v1.0`标签到远程`origin`** | **推送本地所有标签到远程`origin`** | **推送本地`v1.0`标签到远程`origin`的`v1.1`标签** |
| :---: | :---: | :---: |
| `git push origin v1.0` | `git push origin --tags` | ` git push origin v1.0:v1.1 -f` |
</details>

### 2、检出标签代码
<details>
<summary>code</summary>

*(1)基于`v1.0`标签的代码创建并切换到`dev`分支*  
`git checkout -b dev v1.0`
</details>

