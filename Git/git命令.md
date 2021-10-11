[toc]

### 一、git常用命令

#### 1、初始化分支（`git init`）

- 在初始化分支后，需要提交内容后才能创建分支，否则会出现`fatal: 不是一个有效的对象名：'master'`错误

#### 2、添加远程仓库（`git remote`）
- 添加远程库

  `git remote add origin git@github.com:xxx/xxx.git`

  - 使用`token`

    `git remote add origin https://<token>@github.com/xxx/xxx.git `

  `origin`为取的名字

- 清除远程库

  `git remote rm origin`

#### 3、克隆远程库（`git clone`）

`git clone git@github.com:xxx/xxx.git`

#### 4、查看log（`git log`）
- `git log`：查看commit id

- `git reflog`：查看所有提交过的commit id，包括删除过的

#### 5、分支操作（`git branch`）

 提交代码前最好创建本地分支

- **新建分支**

  `git checkout -b <name>`（相当于以下两条命令）

  - `git branch dev`创建分支
    `git checkout dev`切换分支

- **重命名分支**

  `git branch -m <oldName> <newName>`

- **查看分支**

  `git branch -l`

  `git branch` 查看本地分支（`git branch -r` 查看远程分支，`git branch -a`查看所有分支）

- **切换分支**

  `git checkout <name>`

- **合并分支**

  - `git merge <name>`

    > 直接把两个分支再各自拉出一条线，然后连在一起
    >
    > 最后会有两次提交，有分叉，会产生`Merge branch xxx of xxx`提交

  - `git rebase`

    > 将当前分支与要合并的分支中不同的commits取消掉，临时保存起来，然后在要合并的分支中再把保存起来的commits patch上去，变成新的commits
    >
    > 最后就只剩合并后的分支，是一条直线，没有分叉，不会有`Merge branch xxx of xxx`提交

- **删除分支**

  `git branch -d <name>`

- **建立本地分支和远程分支的关联**

  `git branch --set-upstream-to=origin/<远程分支> <本地分支>`

#### 6、拉取代码（`fetch&pull`）

- `git fetch`：将远程commit拉取到本地

  > `fetch`不会直接将拉取的commit接到本地分支的最后，而是在本地最后一次==push的commit处==再拉取一个新的分支

- `git pull`（相当于以下两条命令）：拉取并合并分支
  - `git fetch`
  - `git merge`

> 用于从远程获取代码并合并本地的版本

`git pull`其实就是 `git fetch` 和 `git merge FETCH_HEAD`的简写

- `git pull origin <远程分支名>:<本地分支名>`
  - 拉取origin的远程分支与本地分支合并
  
- ==本地未修改==，只有master分支，直接更新

  - `git pull`
    - 若修改后直接pull，会产生冲突

- 本地分支有修改，多分支

  - ```
    git checkout <主分支>
    git pull
    git checkout <子分支>
    git merger <主分支>
    ```

#### 7、添加暂存库（`git add`）
- `git add` 把修改的文件提交到暂存区

-  `git add -A`  提交所有变化

-  `git add -u`  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)

- `git add .`  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件

#### 8、提交到本地仓库（`git commit`）
- `git commit -m "message"`
  - 使用`-m` 参数，可以直接在引号中输入备注
  - 不使用`-m`参数，会打开vim，在vim中编辑备注
- `git commit -a -m "message"`
  - `-a`参数：把还没有执行add命令的修改一起提交
- `git commit --amend`
  - 将多次提交合并为一次，追加此次提交到上一次提交的`commit id`中，而不会增加新的`commit id`

#### 9、提交到远程仓库（`git push`）
提交到远程分支

> origin 为添加远程仓库时所取名字

- 提交到远程分支
  - 提交指定分支：`git push origin <分支名>`
  
  - `git push -u origin`
  
    `-u`选项会指定一个默认主机，以后可以使用不带任何参数的`git push`
  
- 提交到`Gerrit`中
  - `git push origin HEAD:refs/for/<分支名>`
    - `origin` : 代表远程仓库
    - `HEAD`：本地当前分支
    - `<分支名>`: 需要push到远程的分支名
    - `refs/for`：代表需要经过`gerrit`上进行`review`，而不是直接提交到仓库中
- 强制提交，覆盖远程仓库代码
  - `git push --force origin <分支名> `

#### 10、回退版本（`git reset`）
- 取消在暂存区中的某个文件

  `git reset HEAD <filename>`

- 取消暂存当前路径下所有的文件

  `git reset HEAD .`

- 取消最近的n次提交或退至指定`commit id`

  `git reset HEAD~n`或`git reset <commit id>`

  - 将commit id修改的内容同步本地，==不包含提交==，只同步修改

  `git reset --hard HEAD~n`或`git reset <commit id>`

  - 将commit id修改的内容同步本地，==包含提交==

#### 11、移除（`git clean`）
- 移除工作区的某个文件

  `git clean -f <filename>`

- 移除工作区所有未追踪文件

  `git clean -df`

#### 12、撤销（`git checkout`）
- 撤销工作区（还未添加至暂存区）某个文件的修改

  `git checkout <filename>`

  `git checkout .`

- 撤销添加到暂存区的文件

  使用`git reset HEAD <filename>`命令

- 以及添加至本地仓库

  进行版本回退`git reset HEAD~1`或`git reset <commit id>`

#### 13、删除（`git rm`）
- 将文件从暂存区和工作区删除

  `git rm <filename>`

- 强行从暂存区和工作区中删除修改后的文件

  `git rm -f <filename>`

- 将文件从暂存区删除，但保留在工作区中（仅从跟踪清单中删除）

  `git rm --cached <filename>`

- 递归删除

  `git rm -r <directory>`

#### 14、查看文件差异（`git diff`）
- 查看修改后与原文件的不同

  `git diff`

- 查看已经暂存起来的文件和上次提交的文件的差异

  `git diff --cached`

  `git diff --staged`

  - 显示的是下一次commit时会提交到HEAD的内容(不带-a情况下)

- 查看简单的`diff`结果，可以加上`--stat`参数

  `git diff --stat`

- 图像化查看不同

  `git difftool`

  - `difftool`需要配置

- 本地分支与远程分支比较

  - 显示远程分支与本地分支差异

    `git diff <remote-branch> <local-branch>`

  - 仅显示远程分支与本地分支文件名差异

    `git diff --name-status <remote-name> <local-name>`

#### 15、stash（`git stash`）
- 将工作区的修改临时保存在暂存区

  `git stash`

- 将暂存区的内容恢复到工作区

  `git stash pop`

  `git stash apply`

- 查看暂存区有哪些临时修改

  `git stash list`

- 丢弃暂存区的修改

  `git stash drop stash@{$num}`

  `git stash clear`

#### 16、tag（`git tag`）
- 创建标签

  `git tag <tag name>`

  - 标签默认打在最新的`commit`（`HEAD`）上

- 指定要创建的标签的位置

  `git tag <tag name> <commit id>`

- 新建带有标签的tag

  `git tag -a <tag name> -m ""`

- 获取tag列表

  `git tag -l`

- 查看具体tag

  `git show <tag name>`

- 删除tag

  `git tag -d <tag name>`

### 二、git操作

#### 1、提交当前工作区内容至指定的Commit-id

> 在`typora`中需要重新打开才能看见提交的内容

1. 保存当前工作区内容

   `git stash`

2. 修改commit内容，将HEAD指针指向需要更改的commit上

   `git rebase <commit_id>^ --interactive`

   找到需要更改的`commit`，将行首的`pick`改为`edit`

3. 取出`stash`中的工作

   `git stash pop`

4. 添加至暂存库

   `git add <filename>`

5. 追加到指定的`commit`中

   `git commit --amed`

6. 移动HEAD指针到最新的`commit`处

   `git rebase --continue`

   若有冲突：

   - 解决冲突后add，再追加
   - 再用`git rebase --continue`将HEAD指针移动至最新的commit处

#### 2、git在命令行中正确显示中文文件名

在命令行输入`git config --global core.quotepath off`

> 将`core.quotepath`设为false，不会对`0x80`以上的字符进行quote

#### 3、`github`怎么邀请合作者加入到自己的项目

[`github`怎么邀请合作者加入到自己的项目](https://jingyan.baidu.com/article/948f5924f43f47d80ff5f9f9.html)

> 登录`github`->选择项目->setting->Manage access->通过邮件、用户名等添加用户
>
> 被邀请人在`github`账号头像处查看邀请信息

#### 4、`github`添加token

[关于`github`中的token的创建方法](https://www.pianshen.com/article/9592754254/)

> `github`用户setting ->Developer settings-->Personal access tokens-->Generate token

#### 3、拉取远程代码，但本地代码有修改

1. `git fetch`

2. `git rebase`

3. `git rebase --continue`

   若有冲突：

   - 解决冲突后add
   - 再用`git rebase --continue`将HEAD指针移动至最新的commit处

4. `git push`
