# Git

## 版本控制系统

1. **集中化**（e.g Svn）

   有一个集中管理的中央服务器，保存着所有文件的修改历史版本，而协同开发者通过客户端连接到这台服务器，从服务器上同步更新或上传自己的修改。

2. **分布式**（e.g Git）

   远程仓库同步所有版本信息到本地的每个用户，分三点阐述：

   - 用户在本地就可以查看所有的历史版本信息，但是偶尔要从远程更新一下，因为可能别的用户有文件修改提交到远程；

   - 用户即使离线也可以本地提交，push 推送到远程服务器才需要联网；

   - 每个用户都保存了历史版本，所以只要有一个用户设备没问题，就可以恢复数据。



## 介绍

Git 是免费，开源的分布式版本控制系统，可以有效，高速地处理从很小到非常大的项目版本管理。

- 4 大工作区域
  - `Workspace`：你电脑本地看到的文件和目录，在 Git 的版本控制下，构成了工作区；
  - `Index/Stage`：暂存区，一般存放在 .git 目录下，即 .git/index ，它又叫待提交更新区，用于临时存放你未提交的改动，比如，你执行的 git add ，这些改动就添加到这个区域；
  - `Repository`：本地仓库，你执行 git clone 地址，就是把远程仓库克隆到本地仓库，它是一个存放在本地的版本库，其中 HEAD 指向最新放入仓库的版本，当你执行 git commit ，文件改动就到本地仓库来了；
  - `Remote`：远程仓库，就是类似 github、码云等网站所提供的仓库，可以理解为远程数据交换的仓库。
- 正向工作流程
  - 从远程仓库拉去文件代码回来；
  - 在工作目录，增删改查文件；
  - 把改动的文件放入暂存区；
  - 将暂存区的文件提交本地仓库；
  - 将本地仓库的文件推送到远程仓库。
- 工作状态
  - `Untracked`：文件还没有加入到 git 库，还没参与版本控制，即未跟踪状态。这时候的文件，通过 git add 状态，可以变为 Staged 状态；
  - `Unmodified`：文件已经加入 git 库，但是还没修改，就是说版本库中的文件快照内容与文件夹中还完全一致。Unmodified 的文件如果被修改，就会变为 Modified，如果使用 git remove 移除版本库，则成为 Untracked 文件；
  - `Modified`：文件被修改了，就进入 modified 状态，文件这个状态通过 stage 命令可以进入 staged 状态；
  - `Staged`：暂存状态，执行 git commit 则将修改同步到库中，这时库中的文件和本地文件又变为一致，文件为 Unmodified 状态。



## 安装

官网下载安装 [Git](https://git-scm.com/downloads) 。

打开安装包后选择安装配置：

1. `Select Components` （选择组件）。

   由于所有必要组件都已默认勾选，所以直接下一步；

2. `Adjusting your PATH environment `（设置环境变量）。

   一般只用到 `Git Bash` 命令提示符，所以选择 `Use Git Bash only` ，然后下一步；

3. `Configuring the line ending conversions` （换行符处理）。

   GitHub 中的代码大部分以 Mac 或 Linux 的 `LF ( Line Feed ) ` 换行。但是 Windows 中是以 `CRLF ( Carriage Return + Line Feed )` 换行，会到在非对应的编辑器中无法正常显示。

   所以 Windows 系统推荐选择 `Checkout Windows-style, commit Unix-style line endings` 。这样换行符在导出时会自动转为 CRLF ，而提交时自动换位 LF 。

安装完 `Git Bash` 就会作为一个应用程序安装进系统。启动它会有一个 `Git Bash` 的命令提示符。如果是按以上流程安装，那么在 Windows 的附属命令提示符中无法运行 Git 。

`Git Bash` 照搬了很多 Bash 命令，如果习惯用 Linux 的人会更得心应手。



## 初始设置

*   设置姓名和邮箱地址

    ```bash
    git config --global user.name "Draven"
    git config --global user.email "your_email@example.com"
    ```

*   提高命令输出的可读性性

    ```bash
    git config --global color.ui auto
    ```

以上设置均可在 `~/.gitconfig` 中查看和修改。



## 创建账户

1. 在 [`GitHub`](https://github.com/) 上注册账户并登陆；

2. 设置头像。GitHub 的头像是通过 `Gravatar` 服务显示的，只要使用注册 GitHub 账户时使用的**邮箱**在 `Gravatar` 上设置头像，就会同步到 Github 。

3. 设置 `SSH Key` 。GitHub 上连接已有仓库时的认证，是通过使用 SSH 的公开密钥认证方式进行的。我们需要在本地创建 `SSH Key` ，并将其添加到 GitHub 。

   ``` bash
   ssh-keygen -t rsa -C "your_email@example.com"
   ```

   email 请使用你创建 GitHub 账号时使用的 email 。

   在本地命令提示符输入以上命令后，会让你确认保存的文件名（ 默认 id_rsa ）和输入密码。密码是未来在认证时需要输入的。

   输入完密码后，会在 `~/.ssh` 文件夹中生成两个文件（ `id_rsa` 和 `id_rsa.pub` ）。`id_rsa` 是私有密钥，`id_rsa.pub` 是公开密钥。

4. 添加公开密钥。在 GitHub 右上角  `Account Settings` 中选择 `SSH keys` 菜单并点击 `Add SSH Key` 。点击后填写 `title` ，`Key` 部分将本地 `id_rsa.pub` 的内容复制粘贴进去。添加成功后 GItHub 会发邮件提示，然后可以在本地使用以下命令确认是否成功：

   ``` bash
   ssh -T git@github.com
   ```



## 常用操作

### git init 

初始化仓库。

在目录下执行 git init 初始化仓库，成功以后在该目录下会生成 `.git` 目录，存储这管理当前目录内容所需的仓库数据。

在 Git 中我们将这个目录称为 **“附属于该仓库的工作树”**。文件的编辑等操作在工作树中进行，然后记录到仓库中，以此管理文件的历史快照。



### git remote

设置远程仓库。

**添加远程仓库 origin** ：

``` bash
git remote add orgin <your_repo_url>
```



### git clone

获取远程仓库。

在 GitHub 上复制仓库的 `SSH` URL ，然后**本地进行克隆**：

``` bash
git clone <your_repo_ssh_url>
```

`git clone` 后，系统会自动将 origin 设置成该远程仓库，并且我们本地会默认处于 master 分支下，与远程 master 分支在内容上完全一致。



### git pull

获取最新的远程分支。

``` bash
git pull origin <branch_name>
```




### git status 

查看仓库状态。

工作树和仓库在被操作过程中状态会不断发生变化，所以我们要经常使用这个命令查看状态。



### git add

向暂存区添加文件。

``` bash
git add <file_name>
```

只是在工作树中创建了文件，并不会被记入 Git 仓库的版本管理对象之中，我们使用 `git status` 查看状态时，会显示文件在 `Untracked files` 中。

使用 `git add` 将文件添加入暂存区，从而让文件记入 Git 仓库的版本管理对象中。



### git stash

将所有未提交的修改（工作区和暂存区）保存至堆栈中，后续再恢复。

情景1：当代码修改一半不能提交作为一个版本，但是又需要切换其他分支进行开发时，可以使用该命令先暂存当前修改。

情景2：当在错误的分支上进行修改，可以先保存，再切换回正确分支。

- **暂存所有未提交修改**：

  ``` bash
  git stash
  ```

- **暂存时添加备注**：

  ``` bash
  git stash save "save_message"
  ```

- **只暂存还没被 add 的文件**：

  ``` bash
  git stash -keep-index
  ```

- **查看缓存列表**：

  ``` bash
  git stash list
  ```

- **显示做了哪些改动**：

  默认展示第一个（即堆栈最上方的）：

  ``` bash
  git stash show
  ```

  如果要显示其它，需要单独指定 `stash@{$num}`，比如要显示第二个：

  ``` bash
  git stash show stash@{1}
  ```

  > 以下其它命令，如果没有指定存储，则规则与此一致，默认选中第一个。

- **应用存储**：

  ``` bash
  git stash apply
  ```

- **删除存储**：

  ``` bash
  git stash drop stash@{$num}
  ```

- **应用并删除存储**

  ``` bash
  git stash pop
  ```

- **删除所有存储**

  ``` bash
  git stash clear
  ```



### git commit

提交，将文件从暂存区保存到仓库的历史记录中，这样我们就能通过历史记录复原这些文件。

``` bash
git commit -m "commit_message"
```

如果只需一行提交信息，就添加 `-m "commit_message"` ，如果要提交多行信息，则直接执行 `git commit` ，执行后会启动编辑器。

- **中止提交**：在打开编辑器后，将提交信息留空并关闭编辑器，就会中止提交。

- **添加进暂存区并且提交**：

  在后面加上 `-am` 

  ``` bash
  git commit -am "your_commit_message"
  ```
  
- **修改上一条提交信息**：

  ``` bash
  git commit --amend
  ```
  
  执行完该命令后编辑器就会启动，可以在编辑器中修改并保存提交信息。



### git push

提交代码至远程仓库。

``` bash
git push origin <branch_name>
```

第一次提交代码，需要**进行登录验证**：

``` bash
git push -u origin <branch_name>
```



### git log

查看提交日志，只能查看以当前状态为终点的历史记录，即如果你当前状态是重置到了之前的某个提交，那么在那个提交以后的记录都看不到。

- **以图表形式查看记录**：

  ``` bash
  git log --graph
  ```

- **只显示提交信息的第一行**：

  ``` bash
  git log --pretty=short
  ```

- **只显示指定目录、文件的日志**：

  ``` bash
  git log <directory_or_file_name>
  ```

- **显示文件的改动**：

  文件的前后差别会体现在提交信息之后。再加上文件名可以单独查看某个文件的改动。

  ``` bash
  git log -p
  git log -p <file_name>
  ```



### git reflog 

查看操作日志，可以查看当前仓库执行过的操作日志。

如果你当前重置到了之前的某次提交，就可以通过这个命令查到之后其它操作和提交的记录。



### git diff

可以查看工作区、暂存区和最新提交之间的区别。

**查看和最新提交的区别**：

``` bash
git diff HEAD
```

可以养成一个好习惯，在 git commit 之前先执行 `git diff HEAD` 查看一下本次提交与上次提交的区别。



### git branch

- **显示分支列表**：

  ``` bash
  git branch
  ```

- **新建分支**：

  ``` bash
  git branch <new_branch_name>
  ```

- **删除分支**：

  ``` bash
  git branch -d <branch_name>
  ```

- **同时查看本地分支和远程分支**：

  ``` bash
  git branch -a
  ```



### git checkout

- **切换分支**：

  ``` bash
  git checkout <branch_name>
  ```

- **创建并切换到新分支**：

  ``` bash
  git checkout -b <new_branch_name>
  ```

- **切换到上一个分支**：

  ``` bash
  git checkout -
  ```

- **获取远程分支**：

  ``` bash
  git checkout -b <local_branch_name> origin/<branch_name>
  ```



### git merge

合并分支。

将 feature_A 分支合并到 master 分支，需要先切换到 master 分支，在 master 分支进行 merge ：

``` bash
git checkout master
git merge feature_A
```

git merge 默认是 `-ff` ，即快速合并 （ fast-forward）。快速合并只是粗暴的移动 HEAD 指针，即在上面的例子中，简单的把 feature_A 所有提交记录合并在 master 的记录中。如果合并后在 master 分支中回退上个记录，会回退到 feature_A 分支的倒数第二次提交，而不是 master 分支本来的上次合并或者提交的记录。

- `Squash merge`：

  ``` bash
  git merge --no-ff feature_A
  ```

  使用 `--no-ff` 关闭快速合并，即普通合并。那么会**把 fearture_A 分支的所有提交生成一个新的 commit **再合并到 master 分支上。此时在 master 分支上的历史记录就只有一条合并这个新 commit 的记录，回退上一个记录时就能回退到 master 分支上一次合并或者提交的记录。

合并后会要求输入合并信息，如果不想填写，直接按 `esc` + `:wq` 进行保存即可



### git rebase

- **合并多次 commit**：

  压缩历史，可以将当前提交合并到上一个提交中。

  有的时候我们提交完会发现有一些小错误忘记改了，然后改完又提交一次，此时就可以把两次提交合并成一次：

  ``` bash
  git rebase -i HEAD~2
  ```

  执行完该命令会显示最近的两次提交，假设如下：

  ``` bash
  pick hash_1 commit_message_1
  pick hash_2 commit_message_2
  ```

  最近的第 2 条记录是对第 1 条记录的补充修改，那么我们可以将第 2 条记录的 `pick` 改 为 `fixup` ：

  按 `s` 在鼠标位置插入修改 
  ``` bash
  pick hash_1 commit_message_1
  fixup hash_2 commit_message_2
  ```

  修改完，按 `esc` + `:wq` 退出编辑进行保存。

  保存后就会进行合并，再使用 `git log` 查看记录就会发现只有第 1 条记录，并且记录的 hash 已经改变，说明发生了更改。

- **分支合并（变基）**：

  同步主分支的最新改动。

  比如在主分支 master 检出一个 dev 分支，然后此时 master 分支上有其它 hotfix ，此时如果将 master merge 到 dev ，会污染了 commit 记录，此时可以在 dev 分支上 rebase master 分支。

  ``` bash
  # current branch：dev
  git rebase master
  ```
  
  1. Git 会把 dev 分支里面的每个 commit 取消掉；
  2. 把上面的操作临时保存成 `patch` 文件，存在 `.git/rebase` 目录下；
  3. 把 dev 分支更新到最新的 master 分支；
  4. 再把第 2 步的 `patch` 文件应用到 dev 分支上（即 commit 的记录变为在 master 最新的记录之后）。
  
  > **危险**：由于 rebase 操作会更变历史，所以你最好在肯定当前分支只有自己开发的时候再使用，否则同事 pull 拉取代码后可能会丢失本地的 commit 记录。
  
  即只要你的分支上需要 rebase 的所有 commit 历史还没有被 push 过，就可以安全的使用。

- **异常退出 vim 窗口需重新打开**：

  ``` bash
  git rebase --edit-todo
  ```

- **主动中断**：

  ``` bash
  git rebase --abort
  ```
  
- **被中断后继续**：

  ``` bash
  git rebase --continue
  ```



### git reset --hard

回溯历史版本。

需要提供指定历史版本对应的 hash 值：

``` bash
git reset --hard <hash>
```



### git tag

给某次提交打上标签。

> 标签规范：
>
> `v1.0.0` ：表示主版本号为 1、次版本号为 0 、修订版本号为 0 。

- **列出标签**

  `-l` / `--list` 列出已有标签，也可以加上正则进行筛选列出。

  ``` bash
  git tag -l
  git tag -l "v1.8.5*"
  ```

- **创建标签**

  标签有两种：**轻量标签（ `lightweight` ）**和 **附注标签（ `annotated` ）**。

  `轻量标签`很像一个不会改变的分支 - 它只是某个特定提交的引用。

  `附注标签` 是存储在 Git 数据库中的一个完整对象，包含打标签者的信息，etc。可以使用 `GNU Privacy Guard ( GPG )` 签名并校验的。

  > 通常建议创建附注标签，如果只是想要一个临时标签，或者不想保存这些信息，也可以使用轻量标签。

- **附注标签**

  `-a` 便可生成附注标签，`-m` 可以添加提交信息。

  `git show <tagName>` 可以查看对应 tag 的标签信息和提交信息。

  ``` bash
  git tag -a v1.4 -m "my version 1.4" 
  ```

  ``` bash
  git show v1.4
  ```

- **轻量标签**

  直接使用 `git tag` ，不添加 `-a` 等选项，便可生成轻量标签。

  ``` bash
  git tag v1.4-lw
  ```

- **后期打标签**

  根据以前提交记录的哈希值，可以给以前的提交打标签。

  ``` bash
  git tag -a v1.2 9fecb02
  ```

- **共享标签**

  默认情况下，`git push` 并不会传送标签到远程，创建完标签后必须显式地推送标签 `git push origin <tagName>` 。

  ``` bash
  git push origin v1.5
  ```

  一次性推送很多标签 `--tags`

  ``` bash
  git push origin --tags
  ```

- **删除标签**

  删除本地仓库标签 `-d` ：

  ``` bash
  git tag -d v1.4
  ```

  删除远程仓库标签，有两种方式：

  ``` bash
  git push origin :refs/tags/v1.4
  ```

  ``` bash
  git push origin --delete <tagName>
  ```

- **检出标签**

  使用 `git checkout` 命令，但是会使仓库指针处于 `分离头指针（ detached HEAD ）` 的状态，会有些不好的副作用。

  ``` bash
  git checkout v2.0.0
  ```

  > 在`分离头指针`状态下，如果做了某些更改再提交，标签不会发生变化，并且新提交不属于任何分支，将无法访问，除非通过确切的提交哈希才能访问。

  所以要修复旧版本错误，通常需要新建一个分支，再提交：

  ``` bash
  git checkout -b version2 v2.0.0
  ```

  



## .gitignore

Git 忽略文件。

1. 在工作区中创建 `.gitignore` 文件;

2. 文件内容：
   - /<file_name>/：指定文件被 Git 忽略，<file_name> =>  * 代表忽略所有文件，例：/node_modules/ ；
   - *.<file_type>：指定文件类型被 Git 忽略，例： *.json ；
   - !<file_name>：该文件不会被忽略，例：!.gitignore；
   - `#`：注释，`#` 开头后面写的东西不会被解析，例：`# 这是注释` 。

``` bash
# e.g .gitignore
# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
pnpm-debug.log*
lerna-debug.log*

node_modules
.DS_Store
dist
dist-ssr
coverage
*.local

/cypress/videos/
/cypress/screenshots/

# Editor directories and files
.vscode/*
!.vscode/extensions.json
.idea
*.suo
*.ntvs*
*.njsproj
*.sln
*.sw?

*.tsbuildinfo

```





## SSH

### https 和 SSH 的区别：

两者主要是所用协议不一样。

https 用 443 端口，可以对 repo 根据权限进行读写，只要有账号密码就可以进行操作。

SSH 则用的是 22 端口，也可以对 repo 根据权限进行读写，但是需要 ssh key 授权，这个 key 是通过 ssh key 生成器生成的，然后放在 github 上，作为授权的证据，这样的话不需要用户名和密码进行授权了。如果配置 ssh key 的时候设置了密码，则 push 的时候是需要输入密码的。

在电脑中使用 SSH key 的步骤：

- 检查电脑是否存在 SSH key ：

  ```
  // 桌面右键进入 git bash
  $cd ~/.ssh
  $ls
  ```

- 如果存在 id_rsa.pub 或 id_dsa.pub 文件，说明文件已经存在，跳过创建 ssh key 步骤（文件默认存在于 User/用户/.ssh ）；

- 创建 SSH key：

  ```
  ssh-keygen -t rsa -C 'youremail@qq.com'
  // 输入后不断 enter 即可
  ```

  查看生成的秘钥：

  ```
  cat ~/.ssh/id_rsa.pub
  // 或者直接在文件夹中打开，文件默认存在于 User/用户/.ssh
  ```

- 将公共的 SSH (**id_rsa.pub**) 放在远程仓库上；

- 验证 key 是否正常工作：

  ```
  // 使用 github 的话
  $ssh -T git@github.com
  ```

  之后会问你

  Are you sure you want to continue connecting (yes/no)?

  只要回答 yes ，回车就会看到下面的

  Hi Anonymous! You've successfully authenticated, but GITEE.COM does not provide shell access.

  就表示设置已经成功了。

- 拉取代码：

  复制你代码库的 ssh 后执行：

  ```shell
  $git clone ' 你复制 ssh '
  ```



### 配置多个 SSH key

- 创建新的 ssh-key

  ``` shell
  ssh-keygen -t rsa -f ~/.ssh/id_rsa_别名 -C “邮箱地址“
  ```

- 复制生成文件 id_rsa_别名.pub 的公钥至 git 进行配置

- 在 /.ssh 下创建 config 文件

  参数说明：

  Host: 随便起，用于区分用户

  HostName: 分别为公司自己的 git 托管的服务地址

  User: 随便

  IdentityFile: 公钥地址（**id_rsa**）。

  ``` 
  # first user (w654147175@gmail.com)
  Host draven
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_draven
  ```

- Clone 时将 SSH 链接对应的 HostName 改为 Host 即可。

  `git@github.com:HZRui654/notes.git` 改为以下：

  `draven:HZRui654/notes.git`



## Commit 规范

一般采用 Angular.js 规范：

详细参考：[https://feflowjs.com/zh/guide/rule-git-commit.html](https://feflowjs.com/zh/guide/rule-git-commit.html)

```javascript
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

其中，header 是必需的，body 和 footer 可以省略。
不管是哪一个部分，任何一行都不得超过72个字符（或100个字符）。这是为了避免自动换行影响美观。

可以使用 [commitlint](https://commitlint.js.org/guides/getting-started.html) + [@commitlint/cz-commitlint](https://www.npmjs.com/package/@commitlint/cz-commitlint) 。



### Header

Header 部分只有一行，包括三个字段：`type`（required）、`scope`（optional）、`subject`（optional）。

- type：用于说明 commit 的类别，只允许使用下面几个标识。

  | 类型     | 描述                                              |
  | -------- | ------------------------------------------------- |
  | build    | 发布版本                                          |
  | chore    | 改变构建流程、或者增加依赖库、工具等              |
  | ci       | 持续集成修改                                      |
  | docs     | 文档修改                                          |
  | feat     | 新功能（feature）                                 |
  | fix      | 修补 bug                                          |
  | perf     | 优化相关，比如提升性能、体验                      |
  | refactor | 重构（既不是新增功能，也不是修改 bug 的代码改动） |
  | revert   | 回滚到上一个版本                                  |
  | style    | 格式（不影响代码运行的变动）                      |
  | test     | 测试用例修改                                      |

- scope：用于说明 commit 影响的范围，每个项目对这个范围的定义可能不同。

  如果你的修改影响了不止一个 `scope` ，你可以使用 `*` 代替。

- subject：commit 目的的简短描述，不超过 50 个字符。

  - 以动词开头，使用第一人称现在时，比如 change ，而不是 changed 或 changes
  - 第一个字母小写
  - 结尾不加句号（.）



### Body

Body 部分是对本次 commit 的详细描述，可以分成多行。下面是一个范例。

```bash
More detailed explanatory text, if necessary.  Wrap it to 
about 72 characters or so. 

Further paragraphs come after blank lines.

 - Bullet points are okay, too
 - Use a hanging indent
```

> Tips：
>
> 1. 使用第一人称现在时，比如 change ，而不是 changed 或 changes ；
> 2. 与 Header 和 Footer 之间有
> 3. 应该说明代码变动的动机，以及与以前行为的对比。



### Footer

Footer 部分只用于两种情况：

1. 不兼容变动

  如果当前代码与上一个版本不兼容，则 Footer 部分以 `BREAKING CHANGE` 开头，后面是对变动的描述、以及变动理由和迁移方法。

  ```bash
  BREAKING CHANGE: isolate scope bindings definition has changed.
    
    To migrate the code follow the example below:
  
    Before:
    
    scope: {
      myAttr: 'attribute',
    }
    
    After:
    
    scope: {
      myAttr: '@',
    }
    
    The removed `inject` wasn't generaly useful for directives so there should be no code using it.
  ```

2. 关闭 Issue

  如果当前 commit 针对某个 issue，那么可以在 Footer 部分关闭这个 issue。

  ``` bash
  Closes #234
  ```

  也可以一次性关闭多个 issue 。

  ``` bash
  Close #123, #245, #992
  ```



### Revert

还有一种特殊情况，如果当前 commit 用于撤销以前的 commit ，则必须以 `revert:` 开头，后面跟着被撤销 commit 的 Header。

``` bash
revert: feat(pencil): add 'graphiteWidth' option
 
This reverts commit 667ecc1654a317a13331b17617d973392f415f02.
```

Body 部分格式是固定的，必须写成 `This reverts commit <hash>` ，其中 `hash` 是被撤销 commit 的 SHA 标识符。



### validate-commit-msg

[validate-commit-msg](https://github.com/conventional-changelog-archived-repos/validate-commit-msg) 用于检查 Node 项目的 Commit message 是否符合格式。



## Work flow

[Git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)

- **Base**

  基础需要两条分支，都是需要保护不可删除的。

  - `main` ：生产分支。`release` / `hotfix` 分支合并到该分支后打 `tag` 。
  - `develop` ：开发分支，从 `main` 分支检出，包含了生产的所有版本以及未来要发布的新版本。

- **Feature**

  添加新功能。

  `feature_branch` ：从 `develop` 检出，开发完新功能以后合并（ 合并前先 `Rebase` ）回 `develop` 分支。

- **Release**

  发布生产需要单独检出一条预生产分支。

  `release/<tagName>` ：`develop` 合并完 `feature_branch` 新功能后从 `develop` 分支检出，检出后在该分支上只能进行 bug 或者 doc 的修改，不能再添加新功能，测试没有问题就合并到 `main` 发布生产，并且再合并回 `develop` 分支。

- **Hotfix**

  热修复当天生产出现的问题。

  `hotfix_branch` ：从生产分支 `main` 检出，修改完 bug 后合并回 `main` ，并且需要同步合并到 `develop` 。
