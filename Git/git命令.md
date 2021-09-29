[toc]

### 一、git常用命令

#### 1、初始化分支

- `git init`
  - 在初始化分支后，需要提交内容后才能创建分支，否则会出现`fatal: 不是一个有效的对象名：'master'`错误

#### 2、添加远程仓库

- 添加远程库

  `git remote add origin git@github.com:xxx/xxx.git`

  - 使用`token`

    `git remote add origin https://<token>@github.com/xxx/xxx.git `

  `origin`为取的名字

- 清除远程库

  `git remote rm origin`

#### 3、克隆远程库

`git clone git@github.com:xxx/xxx.git`

#### 4、查看log

- `git log`：查看commit id

- `git reflog`：查看所有提交过的commit id，包括删除过的


#### 5、分支操作

`git branch`


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

  `git merge <name>`

- **删除分支**

  `git branch -d <name>`
  
- **抓取分支**

  `git pull`

  - `git pull`失败，原因是没有指定本地分支与远程分支的链接

- **建立本地分支和远程分支的关联**

  `git branch --set-upstream-to=origin/<远程分支> <本地分支>`

#### 6、添加暂存库

`git add`

- `git add` 把修改的文件提交到暂存区

-  `git add -A`  提交所有变化

-  `git add -u`  提交被修改(modified)和被删除(deleted)文件，不包括新文件(new)

- `git add .`  提交新文件(new)和被修改(modified)文件，不包括被删除(deleted)文件

#### 7、提交到本地仓库

`git commit`

- `git commit -m "message"` 
  - 使用`-m` 参数，可以直接在引号中输入备注
  - 不使用`-m`参数，会打开vim，在vim中编辑备注
- `git commit --amend` 
  - 将多次提交合并为一次，追加此次提交到上一次提交的`commit id`中，而不会增加新的`commit id`

#### 8、提交到远程仓库

提交到远程分支

- `git push origin <分支名>`

提交到`Gerrit`中

- `git push origin HEAD:refs/for/<分支名>`
  - `origin` : 代表远程仓库
  - `HEAD`：本地当前分支
  - `<分支名>`: 需要push到远程的分支名
  - `refs/for`：代表需要经过`gerrit`上进行`review`，而不是直接提交到仓库中

#### 9、回退版本

`git reset`

- 取消在暂存区中的某个文件

  `git reset HEAD <filename>`

- 取消暂存当前路径下所有的文件

  `git reset HEAD .`

- 取消最近的n次提交或退至指定`commit id`

  `git reset HEAD~n`或`git reset <commit id>`

  - 将commit id修改的内容同步本地，==不包含提交==，只同步修改

  `git reset --hard HEAD~n`或`git reset <commit id>`
  
  - 将commit id修改的内容同步本地，==包含提交==

#### 10、移除

`git clean`

- 移除工作区的某个文件

  `git clean -f <filename>`

- 移除工作区所有未追踪文件

  `git clean -df`

#### 11、查看文件差异

- 查看修改后与原文件的不同

  `git diff`

- 查看已经暂存起来的文件和上次提交的文件的差异

  `git diff --cached`

  `git diff --staged`

  - 显示的是下一次commit时会提交到HEAD的内容(不带-a情况下)

- 查看简单的diff结果，可以加上--stat参数

  `git diff --stat`

- 图像化查看不同

  `git difftool`
  
  - `difftool`需要配置
  
- 本地分支与远程分支比较

  - 显示远程分支与本地分支差异

    `git diff <remote-branch> <local-branch>`

  - 仅显示远程分支与本地分支文件名差异

    `git diff --name-status <remote-name> <local-name>`

#### 12、stash

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

#### 13、撤销

- 撤销工作区（还未添加至暂存区）某个文件的修改

  `git checkout <filename>`

  `git checkout .`
  
- 撤销添加到暂存区的文件

  使用`git reset HEAD <filename>`命令

- 以及添加至本地仓库

  进行版本回退`git reset HEAD~1`或`git reset <commit id>`

#### 14、tag

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

### 二、git技巧

#### 1、提交当前工作区内容至指定的Commit-id

1. 保存当前工作区内容

   `git stash`

2. 修改commit内容，将HEAD指针指向需要更改的commit上

   `git rebase <commit_id>^ --interactive`

   找到需要更改的`commit`，将行首的`pick`改为`edit`

3. 取出`stash`中的工作

   `git stash pop`

4. 添加到暂存库

   `git add <filename>`

4. 追加到指定的`commit`中

   `git commit --amed`

5. 移动HEAD指针到最新的`commit`处

   `git rebase --continue`

   若有冲突：

   - 解决冲突后add，在追加
   - 再将HEAD指针移动至最新的commit处

