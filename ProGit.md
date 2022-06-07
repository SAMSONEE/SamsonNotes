# Pro Git

## 2.1 Git 基础 - 获取 Git 仓库

如果你只想通过阅读一章来学习 Git，那么本章将是你的不二选择。 本章涵盖了你在使用 Git 完成各种工作时将会用到的各种基本命令。 在学习完本章之后，你应该能够**配置并初始化**一个仓库（repository）、**开始或停止跟踪**（track）文件、**暂存（stage）或提交（commit）更改**。 本章也将向你演示了如何配置 Git 来**忽略指定的文件和文件模式**、如何**迅速而简单地撤销错误操作**、如何**浏览你的项目的历史版本以及不同提交（commits）之间的差异**、如何**向你的远程仓库推送（push）以及如何从你的远程仓库拉取（pull）文件**。

### 获取 Git 仓库

通常有两种获取 Git 项目仓库的方式：

1.  将尚未进行版本控制的本地目录转换为 Git 仓库；
1.  从其它服务器 **克隆** 一个已存在的 Git 仓库。

两种方式都会在你的本地机器上得到一个工作就绪的 Git 仓库。

#### 在已存在目录中初始化仓库

如果你有一个尚未进行版本控制的项目目录，想要用 Git 来控制它，那么首先需要进入该项目目录中。 如果你还没这样做过，那么不同系统上的做法有些不同：

在 Linux 上：

```
$ cd /home/user/my_project
```

在 macOS 上：

```
$ cd /Users/user/my_project
```

在 Windows 上：

```
$ cd /c/user/my_project
```

之后执行：

```
$ git init
```

该命令将创建一个名为 `.git` 的子目录，这个子目录含有你初始化的 Git 仓库中所有的必须文件，这些文件是 Git 仓库的骨干。 但是，在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪。 (参见 [Git 内部原理](https://git-scm.com/book/zh/v2/ch00/ch10-git-internals) 来了解更多关于到底 `.git` 文件夹中包含了哪些文件的信息。)

如果在一个已存在文件的文件夹（而非空文件夹）中进行版本控制，你应该开始追踪这些文件并进行初始提交。 可以通过 `git add` 命令来指定所需的文件来进行追踪，然后执行 `git commit` ：

```
$ git add *.c
$ git add LICENSE
$ git commit -m 'initial project version'
```

稍后我们再逐一解释这些指令的行为。 现在，你已经得到了一个存在被追踪文件与初始提交的 Git 仓库。

#### 克隆现有的仓库

如果你想获得一份已经存在了的 Git 仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到 `git clone` 命令。 如果你对其它的 VCS 系统（比如说 Subversion）很熟悉，请留心一下你所使用的命令是"clone"而不是"checkout"。 **这是 Git 区别于其它版本控制系统的一个重要特性，Git 克隆的是该 Git 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要文件**。 当你执行 `git clone` 命令的时候，默认配置下远程 Git 仓库中的每一个文件的每一个版本都将被拉取下来。 事实上，如果你的服务器的磁盘坏掉了，你通常可以使用任何一个克隆下来的用户端来重建服务器上的仓库 （虽然可能会丢失某些服务器端的钩子（hook）设置，但是所有版本的数据仍在，详见 [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/ch00/_getting_git_on_a_server) ）。

克隆仓库的命令是 `git clone <url>` 。 比如，要克隆 Git 的链接库 `libgit2`，可以用下面的命令：

```
$ git clone https://github.com/libgit2/libgit2
```

这会在当前目录下创建一个名为 “libgit2” 的目录，并在这个目录下初始化一个 `.git` 文件夹， 从远程仓库拉取下所有数据放入 `.git` 文件夹，然后从中读取最新版本的文件的拷贝。 如果你进入到这个新建的 `libgit2` 文件夹，你会发现所有的项目文件已经在里面了，准备就绪等待后续的开发和使用。

如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以通过额外的参数指定新的目录名：

```
$ git clone https://github.com/libgit2/libgit2 mylibgit
```

这会执行与上一条命令相同的操作，但目标目录名变为了 `mylibgit`。

Git 支持多种数据传输协议。 上面的例子使用的是 `https://` 协议，不过你也可以使用 `git://` 协议或者使用 SSH 传输协议，比如 `user@server:path/to/repo.git` 。 [在服务器上搭建 Git](https://git-scm.com/book/zh/v2/ch00/_getting_git_on_a_server) 将会介绍所有这些协议在服务器端如何配置使用，以及各种方式之间的利弊。

## 2.2 Git 基础 - 记录每次更新到仓库

### 记录每次更新到仓库

现在我们的机器上有了一个 **真实项目** 的 Git 仓库，并从这个仓库中检出了所有文件的 **工作副本**。 通常，你会对这些文件做些修改，每当完成了一个阶段的目标，想要将记录下它时，就将它提交到仓库。

请记住，你工作目录下的每一个文件都不外乎这两种状态：**已跟踪** 或 **未跟踪**。 已跟踪的文件是指那些被纳入了版本控制的文件，在上一次快照中有它们的记录，在工作一段时间后， 它们的状态可能是未修改，已修改或已放入暂存区。简而言之，已跟踪的文件就是 Git 已经知道的文件。

工作目录中除已跟踪文件外的其它所有文件都属于未跟踪文件，它们既不存在于上次快照的记录中，也没有被放入暂存区。 初次克隆某个仓库的时候，工作目录中的所有文件都属于已跟踪文件，并处于未修改状态，因为 Git 刚刚检出了它们， 而你尚未编辑过它们。

编辑过某些文件之后，由于自上次提交后你对它们做了修改，Git 将它们标记为已修改文件。 在工作时，你可以选择性地将这些修改过的文件放入暂存区，然后提交所有已暂存的修改，如此反复。

![Git 下文件生命周期图。](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9d197ff2ef43442e88b416aba0dff8ec~tplv-k3u1fbpfcp-zoom-1.image)

Figure 8. 文件的状态变化周期

### 检查当前文件状态

可以用 `git status` 命令查看哪些文件处于什么状态。 如果在克隆仓库后立即使用此命令，会看到类似这样的输出：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working directory clean
```

这说明你现在的工作目录相当干净。换句话说，所有已跟踪文件在上次提交后都未被更改过。 此外，上面的信息还表明，当前目录下没有出现任何处于未跟踪状态的新文件，否则 Git 会在这里列出来。 最后，该命令还显示了当前所在分支，**并告诉你这个分支同远程服务器上对应的分支没有偏离**。 现在，分支名是“master”，这是默认的分支名。 我们在 [Git 分支](https://git-scm.com/book/zh/v2/ch00/ch03-git-branching) 中会详细讨论分支和引用。

现在，让我们在项目下创建一个新的 `README` 文件。 如果之前并不存在这个文件，使用 `git status` 命令，你将看到一个新的未跟踪文件：

```
$ echo 'My Project' > README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Untracked files:
  (use "git add <file>..." to include in what will be committed)

    README

nothing added to commit but untracked files present (use "git add" to track)
```

在状态报告中可以看到新建的 `README` 文件出现在 `Untracked files` 下面。 未跟踪的文件意味着 Git 在之前的快照（提交）中没有这些文件；Git 不会自动将之纳入跟踪范围，除非你明明白白地告诉它“我需要跟踪该文件”。 这样的处理让你不必担心将生成的二进制文件或其它不想被跟踪的文件包含进来。 不过现在的例子中，我们确实想要跟踪管理 `README` 这个文件。

### 跟踪新文件

使用命令 `git add` 开始跟踪一个文件。 所以，要跟踪 `README` 文件，运行：

```
$ git add README
```

此时再运行 `git status` 命令，会看到 `README` 文件已被跟踪，并处于暂存状态：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)

    new file:   README
```

只要在 `Changes to be committed` 这行下面的，就说明是已暂存状态。 如果此时提交，那么该文件在你运行 `git add` 时的版本将被留存在后续的历史记录中。 你可能会想起之前我们使用 `git init` 后就运行了 `git add <files>` 命令，开始跟踪当前目录下的文件。 `git add` 命令使用文件或目录的路径作为参数；如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

### 暂存已修改的文件

现在我们来修改一个已被跟踪的文件。 如果你修改了一个名为 `CONTRIBUTING.md` 的已被跟踪的文件，然后运行 `git status` 命令，会看到下面内容：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

文件 `CONTRIBUTING.md` 出现在 `Changes not staged for commit` 这行下面，说明已跟踪文件的内容发生了变化，但还没有放到暂存区。 要暂存这次更新，需要运行 `git add` 命令。 这是个多功能命令：可以用它开始跟踪新文件，或者把已跟踪的文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。 将这个命令理解为“精确地将内容添加到下一次提交中”而不是“将一个文件添加到项目中”要更加合适。 现在让我们运行 `git add` 将“CONTRIBUTING.md”放到暂存区，然后再看看 `git status` 的输出：

```
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

现在两个文件都已暂存，下次提交时就会一并记录到仓库。 假设此时，你想要在 `CONTRIBUTING.md` 里再加条注释。 重新编辑存盘后，准备好提交。 不过且慢，再运行 `git status` 看看：

```
$ vim CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

怎么回事？ 现在 `CONTRIBUTING.md` 文件同时出现在暂存区和非暂存区。 这怎么可能呢？ 好吧，实际上 Git 只不过暂存了你运行 `git add` 命令时的版本。 如果你现在提交，`CONTRIBUTING.md` 的版本是你最后一次运行 `git add` 命令时的那个版本，而不是你运行 `git commit` 时，在工作目录中的当前版本。 所以，运行了 `git add` 之后又作了修订的文件，需要重新运行 `git add` 把最新版本重新暂存起来：

```
$ git add CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    new file:   README
    modified:   CONTRIBUTING.md
```

### 状态简览

`git status` 命令的输出十分详细，但其用语有些繁琐。 Git 有一个选项可以帮你缩短状态命令的输出，这样可以以简洁的方式查看更改。 如果你使用 `git status -s` 命令或 `git status --short` 命令，你将得到一种格式更为紧凑的输出。

```
$ git status -s
 M README
MM Rakefile
A  lib/git.rb
M  lib/simplegit.rb
?? LICENSE.txt
```

新添加的**未跟踪**文件前面有 `??` 标记，新添加到**暂存区**中的文件前面有 `A` 标记，**修改过**的文件前面有 `M` 标记。 输出中有两栏，**左栏指明了暂存区的状态，右栏指明了工作区的状态**。例如，上面的状态报告显示： `README` 文件在工作区已修改但尚未暂存，而 `lib/simplegit.rb` 文件已修改且已暂存。 `Rakefile` 文件已修改，暂存后又作了修改，因此该文件的修改中既有已暂存的部分，又有未暂存的部分。

### 忽略文件

一般我们总会有些文件无需纳入 Git 的管理，也不希望它们总出现在未跟踪文件列表。 通常都是些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件等。 在这种情况下，我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。 来看一个实际的 `.gitignore` 例子：

```
$ cat .gitignore
*.[oa]
*~
```

第一行告诉 Git 忽略所有以 `.o` 或 `.a` 结尾的文件。**一般这类对象文件和存档文件都是编译过程中出现的**。 第二行告诉 Git 忽略所有名字以波浪符（~）结尾的文件，**许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本**。 此外，你可能还需要忽略 **log，tmp 或者 pid 目录，以及自动生成的文档等等。 要养成一开始就为你的新仓库设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件**。

文件 `.gitignore` 的格式规范如下：

-   所有空行或者以 `#` 开头的行都会被 Git 忽略。
-   可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
-   匹配模式可以以（`/`）开头防止递归。
-   匹配模式可以以（`/`）结尾指定目录。
-   要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。

我们再看一个 `.gitignore` 文件的例子：

```
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

| Tip | GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表， 你可以在 <https://github.com/github/gitignore> 找到它。 |
| --- | --------------------------------------------------------------------------------------------- |

| Note | 在最简单的情况下，一个仓库可能只根目录下有一个 `.gitignore` 文件，它递归地应用到整个仓库中。 然而，子目录下也可以有额外的 `.gitignore` 文件。子目录中的 `.gitignore` 文件中的规则只作用于它所在的目录中。 （Linux 内核的源码库拥有 206 个 `.gitignore` 文件。）多个 `.gitignore` 文件的具体细节超出了本书的范围，更多详情见 `man gitignore` 。 |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### 查看已暂存和未暂存的修改

如果 `git status` 命令的输出对于你来说过于简略，而你想知道具体修改了什么地方，可以用 `git diff` 命令。 稍后我们会详细介绍 `git diff`，你通常可能会用它来回答这两个问题：**当前做的哪些更新尚未暂存？ 有哪些更新已暂存并准备好下次提交？** 虽然 `git status` 已经通过在相应栏下列出文件名的方式回答了这个问题，但 `git diff` 能通过文件补丁的格式更加具体地显示哪些行发生了改变。

假如再次修改 README 文件后暂存，然后编辑 `CONTRIBUTING.md` 文件后先不暂存， 运行 `status` 命令将会看到：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

要查看**尚未暂存的文件更新了哪些部分**，不加参数直接输入 `git diff`：

```
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

此命令比较的是工作目录中当前文件和暂存区域快照之间的差异。 也就是修改之后还没有暂存起来的变化内容。

若要查看**已暂存的将要添加到下次提交里的内容**，可以用 `git diff --staged` 命令。 这条命令将比对已暂存文件与最后一次提交的文件差异：

```
$ git diff --staged
diff --git a/README b/README
new file mode 100644
index 0000000..03902a1
--- /dev/null
+++ b/README
@@ -0,0 +1 @@
+My Project
```

请注意，git diff **本身只显示尚未暂存的改动，而不是自上次提交以来所做的所有改动。 所以有时候你一下子暂存了所有更新过的文件，运行 `git diff` 后却什么也没有，就是这个原因**。

像之前说的，暂存 `CONTRIBUTING.md` 后再编辑，可以使用 `git status` 查看已被暂存的修改或未被暂存的修改。 如果我们的环境（终端输出）看起来如下：

```
$ git add CONTRIBUTING.md
$ echo '# test line' >> CONTRIBUTING.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    modified:   CONTRIBUTING.md

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md
```

现在运行 `git diff` 看暂存前后的变化：

```
$ git diff
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 643e24f..87f08c8 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -119,3 +119,4 @@ at the
 ## Starter Projects

 See our [projects list](https://github.com/libgit2/libgit2/blob/development/PROJECTS.md).
+# test line
```

然后用 `git diff --cached` 查看已经暂存起来的变化（ `--staged` 和 `--cached` 是同义词）：

```
$ git diff --cached
diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
index 8ebb991..643e24f 100644
--- a/CONTRIBUTING.md
+++ b/CONTRIBUTING.md
@@ -65,7 +65,8 @@ branch directly, things can get messy.
 Please include a nice description of your changes when you submit your PR;
 if we have to read the whole diff to figure out why you're contributing
 in the first place, you're less likely to get feedback and have your change
-merged in.
+merged in. Also, split your changes into comprehensive chunks if your patch is
+longer than a dozen lines.

 If you are starting to work on a particular area, feel free to submit a PR
 that highlights your work in progress (and note in the PR title that it's
```

| Note | Git Diff 的插件版本在本书中，我们使用 `git diff` 来分析文件差异。 但是你也可以使用图形化的工具或外部 diff 工具来比较差异。 可以使用 `git difftool` 命令来调用 emerge 或 vimdiff 等软件（包括商业软件）输出 diff 的分析结果。 使用 `git difftool --tool-help` 命令来看你的系统支持哪些 Git Diff 插件。 |
| ---- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

### 提交更新

现在的暂存区已经准备就绪，可以提交了。 在此之前，请务必确认还有什么已修改或新建的文件还没有 `git add` 过， 否则提交的时候不会记录这些尚未暂存的变化。 这些已修改但未暂存的文件只会保留在本地磁盘。 所以，每次准备提交前，先用 `git status` 看下，你所需要的文件是不是都已暂存起来了， 然后再运行提交命令 `git commit`：

```
$ git commit
```

这样会启动你选择的文本编辑器来输入提交说明。

| Note | 启动的编辑器是通过 Shell 的环境变量 `EDITOR` 指定的，一般为 vim 或 emacs。 当然也可以按照 [起步](https://git-scm.com/book/zh/v2/ch00/ch01-getting-started) 介绍的方式， 使用 `git config --global core.editor` 命令设置你喜欢的编辑器。 |
| ---- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

编辑器会显示类似下面的文本信息（本例选用 Vim 的屏显方式展示）：

```
# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
# On branch master
# Your branch is up-to-date with 'origin/master'.
#
# Changes to be committed:
#	new file:   README
#	modified:   CONTRIBUTING.md
#
~
~
~
".git/COMMIT_EDITMSG" 9L, 283C
```

可以看到，默认的提交消息包含最后一次运行 `git status` 的输出，放在注释行里，另外开头还有一个空行，供你输入提交说明。 你完全可以去掉这些注释行，不过留着也没关系，多少能帮你回想起这次更新的内容有哪些。

| Note | 更详细的内容修改提示可以用 `-v` 选项查看，这会将你所作的更改的 diff 输出呈现在编辑器中，以便让你知道本次提交具体作出哪些修改。 |
| ---- | --------------------------------------------------------------------- |

退出编辑器时，Git 会丢弃注释行，用你输入的提交说明生成一次提交。

另外，你也可以在 `commit` 命令后添加 `-m` 选项，将提交信息与命令放在同一行，如下所示：

```
$ git commit -m "Story 182: Fix benchmarks for speed"
[master 463dc4f] Story 182: Fix benchmarks for speed
 2 files changed, 2 insertions(+)
 create mode 100644 README
```

好，现在你已经创建了第一个提交！ 可以看到，提交后它会告诉你，当前是在哪个分支（`master`）提交的，本次提交的完整 SHA-1 校验和是什么（`463dc4f`），以及在本次提交中，有多少文件修订过，多少行添加和删改过。

请记住，提交时记录的是放在暂存区域的快照。 任何还未暂存文件的仍然保持已修改状态，可以在下次提交时纳入版本管理。 每一次运行提交操作，都是对你项目作一次快照，以后可以回到这个状态，或者进行比较。

### 跳过使用暂存区域

尽管使用暂存区域的方式可以精心准备要提交的细节，但有时候这么做略显繁琐。 Git 提供了一个跳过使用暂存区域的方式， 只要在提交的时候，给 `git commit` **加上 `-a` 选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 `git add` 步骤**：

```
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

    modified:   CONTRIBUTING.md

no changes added to commit (use "git add" and/or "git commit -a")
$ git commit -a -m 'added new benchmarks'
[master 83e38c7] added new benchmarks
 1 file changed, 5 insertions(+), 0 deletions(-)
```

看到了吗？提交之前不再需要 `git add` 文件“CONTRIBUTING.md”了。 这是因为 `-a` 选项使本次提交包含了所有修改过的文件。 这很方便，**但是要小心，有时这个选项会将不需要的文件添加到提交中**。

### 移除文件

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是**从暂存区域移除**），然后提交。 可以用 `git rm` 命令完成此项工作，**并连带从工作目录中删除指定的文件，这样以后就不会出现在未跟踪文件清单中了**。

如果只是简单地从工作目录中手工删除文件，运行 `git status` 时就会在 “Changes not staged for commit” 部分（也就是 *未暂存清单*）看到：

```
$ rm PROJECTS.md
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        deleted:    PROJECTS.md

no changes added to commit (use "git add" and/or "git commit -a")
```

然后再运行 `git rm` 记录此次移除文件的操作：

```
$ git rm PROJECTS.md
rm 'PROJECTS.md'
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    deleted:    PROJECTS.md
```

下一次提交时，该文件就不再纳入版本管理了。 **如果要删除之前修改过或已经放到暂存区的文件，则必须使用强制删除选项 `-f`**（译注：即 force 的首字母）。 这是一种安全特性，用于**防止误删尚未添加到快照的数据，这样的数据不能被 Git 恢复**。

另外一种情况是，我们想把文件**从 Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中**。 换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。 **当你忘记添加 `.gitignore` 文件，不小心把一个很大的日志文件或一堆 `.a` 这样的编译生成文件添加到暂存区时，这一做法尤其有用**。 为达到这一目的，**使用 `--cached` 选项**：

```
$ git rm --cached README
```

`git rm` 命令后面可以列出文件或者目录的名字，也可以使用 `glob` 模式。比如：

```
$ git rm log/*.log
```

注意到星号 `*` 之前的反斜杠 ``， 因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。 此命令删除 `log/` 目录下扩展名为 `.log` 的所有文件。 类似的比如：

```
$ git rm *~
```

该命令会删除所有名字以 `~` 结尾的文件。

### 移动文件

不像其它的 VCS 系统，Git 并不显式跟踪文件移动操作。 如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。 不过 Git 非常聪明，它会推断出究竟发生了什么，至于具体是如何做到的，我们稍后再谈。

既然如此，当你看到 Git 的 `mv` 命令时一定会困惑不已。 要在 Git 中对文件改名，可以这么做：

```
$ git mv file_from file_to
```

它会恰如预期般正常工作。 实际上，即便此时查看状态信息，也会明白无误地看到关于重命名操作的说明：

```
$ git mv README.md README
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

    renamed:    README.md -> README
```

其实，运行 `git mv` 就相当于运行了下面三条命令：

```
$ mv README.md README
$ git rm README.md
$ git add README
```

如此分开操作，Git 也会意识到这是一次重命名，所以不管何种方式结果都一样。 两者唯一的区别在于，`git mv` 是一条命令而非三条命令，直接使用 `git mv` 方便得多。 **不过在使用其他工具重命名文件时，记得在提交前 `git rm` 删除旧文件名，再 `git add` 添加新文件名**。

## [2.3 查看提交历史](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.3-%E6%9F%A5%E7%9C%8B%E6%8F%90%E4%BA%A4%E5%8E%86%E5%8F%B2)

在提交了若干更新之后，又或者克隆了某个项目，想回顾下提交历史，可以使用 `git log` 命令查看。

接下来的例子会用我专门用于演示的 simplegit 项目，运行下面的命令获取该项目源代码：

```
git clone git://github.com/schacon/simplegit-progit.git
```

然后在此项目中运行 `git log`，应该会看到下面的输出：

```
$ git log
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Mar 17 21:52:11 2008 -0700

    changed the version number

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test code

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sat Mar 15 10:31:28 2008 -0700

    first commit
```

默认不用任何参数的话，`git log` 会按提交时间列出所有的更新，最近的更新排在最上面。看到了吗，每次更新都有一个 SHA-1 校验和、作者的名字和电子邮件地址、提交时间，最后缩进一个段落显示提交说明。

`git log` 有许多选项可以帮助你搜寻感兴趣的提交，接下来我们介绍些最常用的。

我们常用 `-p` 选项展开显示每次提交的内容差异，用 `-2` 则仅显示最近的两次更新：

```
$ git log -p -2
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Mar 17 21:52:11 2008 -0700

    changed the version number

    diff --git a/Rakefile b/Rakefile
    index a874b73..8f94139 100644
    --- a/Rakefile
    +++ b/Rakefile
    @@ -5,7 +5,7 @@ require 'rake/gempackagetask'
    spec = Gem::Specification.new do |s|
    - s.version = "0.1.0"
    + s.version = "0.1.1"
    s.author = "Scott Chacon"

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test code

    diff --git a/lib/simplegit.rb b/lib/simplegit.rb
    index a0a60ae..47c6340 100644
    --- a/lib/simplegit.rb
    +++ b/lib/simplegit.rb
    @@ -18,8 +18,3 @@ class SimpleGit
    end

    end
    -
    -if $0 == __FILE__
    - git = SimpleGit.new
    - puts git.show
    -end
    \ No newline at end of file
```

在做代码审查，或者要快速浏览其他协作者提交的更新都作了哪些改动时，就可以用这个选项。此外，还有许多摘要选项可以用，比如 `--stat`，仅显示简要的增改行数统计：

```
$ git log --stat
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Mar 17 21:52:11 2008 -0700

    changed the version number

    Rakefile | 2 +-
    1 files changed, 1 insertions(+), 1 deletions(-)

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sat Mar 15 16:40:33 2008 -0700

    removed unnecessary test code

    lib/simplegit.rb | 5 -----
    1 files changed, 0 insertions(+), 5 deletions(-)

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sat Mar 15 10:31:28 2008 -0700

    first commit

    README | 6 ++++++
    Rakefile | 23 +++++++++++++++++++++++
    lib/simplegit.rb | 25 +++++++++++++++++++++++++
    3 files changed, 54 insertions(+), 0 deletions(-)
```

每个提交都列出了修改过的文件，以及其中添加和移除的行数，并在最后列出所有增减行数小计。还有个常用的 `--pretty` 选项，可以指定使用完全不同于默认格式的方式展示提交历史。比如用 `oneline` 将每个提交放在一行显示，这在提交数很大时非常有用。另外还有 `short`，`full` 和 `fuller` 可以用，展示的信息或多或少有些不同，请自己动手实践一下看看效果如何。

```
$ git log --pretty=oneline
    ca82a6dff817ec66f44342007202690a93763949 changed the version number
    085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 removed unnecessary test code
    a11bef06a3f659402fe7563abf99ad00de2209e6 first commit
```

但最有意思的是 `format`，可以定制要显示的记录格式，这样的输出便于后期编程提取分析，像这样：

```
$ git log --pretty=format:"%h - %an, %ar : %s"
    ca82a6d - Scott Chacon, 11 months ago : changed the version number
    085bb3b - Scott Chacon, 11 months ago : removed unnecessary test code
    a11bef0 - Scott Chacon, 11 months ago : first commit
```

表 2-1 列出了常用的格式占位符写法及其代表的意义。

```
选项 说明
    %H 提交对象（commit）的完整哈希字串
    %h 提交对象的简短哈希字串
    %T 树对象（tree）的完整哈希字串
    %t 树对象的简短哈希字串
    %P 父对象（parent）的完整哈希字串
    %p 父对象的简短哈希字串
    %an 作者（author）的名字
    %ae 作者的电子邮件地址
    %ad 作者修订日期（可以用 -date= 选项定制格式）
    %ar 作者修订日期，按多久以前的方式显示
    %cn 提交者(committer)的名字
    %ce 提交者的电子邮件地址
    %cd 提交日期
    %cr 提交日期，按多久以前的方式显示
    %s 提交说明
```

你一定奇怪*作者（author）* 和*提交者（committer）* 之间究竟有何差别，其实作者指的是实际作出修改的人，提交者指的是最后将此工作成果提交到仓库的人。所以，当你为某个项目发布补丁，然后某个核心成员将你的补丁并入项目时，你就是作者，而那个核心成员就是提交者。我们会在第五章再详细介绍两者之间的细微差别。

用 oneline 或 format 时结合 `--graph` 选项，可以看到开头多出一些 ASCII 字符串表示的简单图形，**形象地展示了每个提交所在的分支及其分化衍合情况**。在我们之前提到的 Grit 项目仓库中可以看到：

```
$ git log --pretty=format:"%h %s" --graph
    * 2d3acf9 ignore errors from SIGCHLD on trap
    * 5e3ee11 Merge branch 'master' of git://github.com/dustin/grit
    |\
    | * 420eac9 Added a method for getting the current branch.
    * | 30e367c timeout code and tests
    * | 5a09431 add timeout protection to grit
    * | e1193f8 support for heads with slashes in them
    |/
    * d6016bc require time for xmlschema
    * 11d191e Merge branch 'defunkt' into local
```

以上只是简单介绍了一些 `git log` 命令支持的选项。表 2-2 还列出了一些其他常用的选项及其释义。

```
选项 说明
    -p 按补丁格式显示每个更新之间的差异。
    --stat 显示每次更新的文件修改统计信息。
    --shortstat 只显示 --stat 中最后的行数修改添加移除统计。
    --name-only 仅在提交信息后显示已修改的文件清单。
    --name-status 显示新增、修改、删除的文件清单。
    --abbrev-commit 仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。
    --relative-date 使用较短的相对时间显示（比如，“2 weeks ago”）。
    --graph 显示 ASCII 图形表示的分支合并历史。
    --pretty 使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。
```

### 限制输出长度

除了定制输出格式的选项之外，`git log` 还有许多非常实用的限制输出长度的选项，也就是只输出部分提交信息。之前我们已经看到过 `-2` 了，它只显示最近的两条提交，实际上，这是 `-<n>` 选项的写法，其中的 `n` 可以是任何自然数，表示仅显示最近的若干条提交。不过实践中我们是不太用这个选项的，Git 在输出所有提交时会自动调用分页程序（less），要看更早的更新只需翻到下页即可。

另外还有按照时间作限制的选项，比如 `--since` 和 `--until`。下面的命令列出所有最近两周内的提交：

```
$ git log --since=2.weeks
```

你可以给出各种时间格式，比如说具体的某一天（“2008-01-15”），或者是多久以前（“2 years 1 day 3 minutes ago”）。

还可以给出若干搜索条件，列出符合的提交。用 `--author` 选项显示指定作者的提交，用 `--grep` 选项搜索提交说明中的关键字。（请注意，如果要得到同时满足这两个选项搜索条件的提交，就必须用 `--all-match` 选项。否则，满足任意一个条件的提交都会被匹配出来）

另一个真正实用的`git log`选项是路径(path)，如果只关心某些文件或者目录的历史提交，可以在 `git log` 选项的最后指定它们的路径。因为是放在最后位置上的选项，所以用两个短划线（`--`）隔开之前的选项和后面限定的路径名。

表 2-3 还列出了其他常用的类似选项。

```
选项 说明
    -(n) 仅显示最近的 n 条提交
    --since, --after 仅显示指定时间之后的提交。
    --until, --before 仅显示指定时间之前的提交。
    --author 仅显示指定作者相关的提交。
    --committer 仅显示指定提交者相关的提交。
```

来看一个实际的例子，如果要查看 Git 仓库中，2008 年 10 月期间，Junio Hamano 提交的但未合并的测试脚本（位于项目的 t/ 目录下的文件），可以用下面的查询命令：

```
$ git log --pretty="%h - %s" --author=gitster --since="2008-10-01" \
    --before="2008-11-01" --no-merges -- t/
    5610e3b - Fix testcase failure when extended attribute
    acd3b9e - Enhance hold_lock_file_for_{update,append}()
    f563754 - demonstrate breakage of detached checkout wi
    d1a43f2 - reset --hard/read-tree --reset -u: remove un
    51a94af - Fix "checkout --track -b newbranch" on detac
    b0ad11e - pull: allow "git pull origin $something:$cur
```

Git 项目有 20,000 多条提交，但我们给出搜索选项后，仅列出了其中满足条件的 6 条。

### 使用图形化工具查阅提交历史

有时候图形化工具更容易展示历史提交的变化，随 Git 一同发布的 gitk 就是这样一种工具。它是用 Tcl/Tk 写成的，基本上相当于 `git log` 命令的可视化版本，凡是 `git log` 可以用的选项也都能用在 gitk 上。在项目工作目录中输入 gitk 命令后，就会启动图 2-2 所示的界面。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8a935aa6966049b18e78b78b60012ef8~tplv-k3u1fbpfcp-zoom-1.image)\
图 2-2. gitk 的图形界面

上半个窗口显示的是历次提交的分支祖先图谱，下半个窗口显示当前点选的提交对应的具体差异。

## [2.4 撤消操作](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.4-%E6%92%A4%E6%B6%88%E6%93%8D%E4%BD%9C)

任何时候，你都有可能需要撤消刚才所做的某些操作。接下来，我们会介绍一些基本的撤消操作相关的命令。请注意，有些撤销操作是不可逆的，所以请务必谨慎小心，一旦失误，就有可能丢失部分工作成果。

### 修改最后一次提交

有时候我们提交完了才发现漏掉了几个文件没有加，或者提交信息写错了。想要**撤消刚才的提交操作，可以使用 `--amend` 选项重新提交**：

```
$ git commit --amend
```

此命令将使用当前的暂存区域快照提交。如果刚才提交完没有作任何改动，**直接运行此命令的话，相当于有机会重新编辑提交说明，但将要提交的文件快照和之前的一样**。

启动文本编辑器后，会看到上次提交时的说明，编辑它确认没问题后保存退出，就会使用新的提交说明覆盖刚才失误的提交。

**如果刚才提交时忘了暂存某些修改，可以先补上暂存操作，然后再运行 `--amend` 提交**：

```
$ git commit -m 'initial commit'
    $ git add forgotten_file
    $ git commit --amend
```

上面的三条命令**最终只是产生一个提交，第二个提交命令修正了第一个的提交内容**。

### 取消已经暂存的文件

接下来的两个小节将演示如何取消暂存区域中的文件，以及如何取消工作目录中已修改的文件。不用担心，查看文件状态的时候就提示了该如何撤消，所以不需要死记硬背。来看下面的例子，有两个修改过的文件，我们想要分开提交，但不小心用 `git add .` 全加到了暂存区域。该如何撤消暂存其中的一个文件呢？其实，`git status` 的命令输出已经告诉了我们该怎么做：

```
$ git add .
    $ git status
    # On branch master
    # Changes to be committed:
    # (use "git reset HEAD <file>..." to unstage)
    #
    # modified: README.txt
    # modified: benchmarks.rb
    #
```

就在 “Changes to be committed” 下面，括号中有提示，可以使用 `git reset HEAD <file>...` 的方式取消暂存。好吧，我们来试试取消暂存 benchmarks.rb 文件：

```
$ git reset HEAD benchmarks.rb
    benchmarks.rb: locally modified
    $ git status
    # On branch master
    # Changes to be committed:
    # (use "git reset HEAD <file>..." to unstage)
    #
    # modified: README.txt
    #
    # Changes not staged for commit:
    # (use "git add <file>..." to update what will be committed)
    # (use "git checkout -- <file>..." to discard changes in working directory)
    #
    # modified: benchmarks.rb
    #
```

这条命令看起来有些古怪，**先别管，能用就行。现在 benchmarks.rb 文件又回到了之前已修改未暂存的状态**。

### 取消对文件的修改

如果觉得刚才对 benchmarks.rb 的修改完全没有必要，该如何取消修改，回到之前的状态（也就是修改之前的版本）呢？`git status` 同样提示了具体的撤消方法，接着上面的例子，现在未暂存区域看起来像这样：

```
# Changes not staged for commit:
    # (use "git add <file>..." to update what will be committed)
    # (use "git checkout -- <file>..." to discard changes in working directory)
    #
    # modified: benchmarks.rb
    #
```

在第二个括号中，我们看到了抛弃文件修改的命令（至少在 Git 1.6.1 以及更高版本中会这样提示，如果你还在用老版本，我们强烈建议你升级，以获取最佳的用户体验），让我们试试看：

```
$ git checkout -- benchmarks.rb
    $ git status
    # On branch master
    # Changes to be committed:
    # (use "git reset HEAD <file>..." to unstage)
    #
    # modified: README.txt
    #
```

可以看到，该文件已经恢复到修改前的版本。你可能已经意识到了，这条命令有些危险，所有对文件的修改都没有了，因为我们刚刚把之前版本的文件复制过来重写了此文件。**所以在用这条命令前，请务必确定真的不再需要保留刚才的修改。如果只是想回退版本，同时保留刚才的修改以便将来继续工作，可以用下章介绍的 stashing 和分支来处理，应该会更好些**。

记住，任何已经提交到 Git 的都可以被恢复。即便在已经删除的分支中的提交，或者用 `--amend` 重新改写的提交，都可以被恢复（关于数据恢复的内容见第九章）。所以，**你可能失去的数据，仅限于没有提交过的，对 Git 来说它们就像从未存在过一样**。

## [2.5 远程仓库的使用](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.5-%E8%BF%9C%E7%A8%8B%E4%BB%93%E5%BA%93%E7%9A%84%E4%BD%BF%E7%94%A8)

要参与任何一个 Git 项目的协作，必须要了解该如何管理远程仓库。远程仓库是指托管在网络上的项目仓库，可能会有好多个，其中有些你只能读，另外有些可以写。同他人协作开发某个项目时，需要管理这些远程仓库，以便推送或拉取数据，分享各自的工作进展。管理远程仓库的工作，包括添加远程库，移除废弃的远程库，管理各式远程库分支，定义是否跟踪这些分支，等等。本节我们将详细讨论远程库的管理和使用。

### 查看当前的远程库

要查看当前配置有哪些远程仓库，可以用 `git remote` 命令，它会列出每个远程库的简短名字。在克隆完某个项目后，**至少可以看到一个名为 origin 的远程库，Git 默认使用这个名字来标识你所克隆的原始仓库**：

```
$ git clone git://github.com/schacon/ticgit.git
    Initialized empty Git repository in /private/tmp/ticgit/.git/
    remote: Counting objects: 595, done.
    remote: Compressing objects: 100% (269/269), done.
    remote: Total 595 (delta 255), reused 589 (delta 253)
    Receiving objects: 100% (595/595), 73.31 KiB | 1 KiB/s, done.
    Resolving deltas: 100% (255/255), done.
    $ cd ticgit
    $ git remote
    origin
```

也可以加上 `-v` 选项（译注：此为 `--verbose` 的简写，取首字母），显示对应的克隆地址：

```
$ git remote -v
    origin git://github.com/schacon/ticgit.git
```

如果有多个远程仓库，此命令将全部列出。比如在我的 Grit 项目中，可以看到：

```
$ cd grit
    $ git remote -v
    bakkdoor git://github.com/bakkdoor/grit.git
    cho45 git://github.com/cho45/grit.git
    defunkt git://github.com/defunkt/grit.git
    koke git://github.com/koke/grit.git
    origin git@github.com:mojombo/grit.git
```

这样一来，我就可以非常轻松地从这些用户的仓库中，拉取他们的提交到本地。请注意，上面列出的地址**只有 origin 用的是 SSH URL 链接，所以也只有这个仓库我能推送数据上去（我们会在第四章解释原因）**。

### 添加远程仓库

要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用，运行 `git remote add [shortname] [url]`：

```
$ git remote
    origin
    $ git remote add pb git://github.com/paulboone/ticgit.git
    $ git remote -v
    origin git://github.com/schacon/ticgit.git
    pb git://github.com/paulboone/ticgit.git
```

现在可以用字符串 `pb` 指代对应的仓库地址了。比如说，**要抓取所有 Paul 有的，但本地仓库没有的信息，可以运行 `git fetch pb`**：

```
$ git fetch pb
    remote: Counting objects: 58, done.
    remote: Compressing objects: 100% (41/41), done.
    remote: Total 44 (delta 24), reused 1 (delta 0)
    Unpacking objects: 100% (44/44), done.
    From git://github.com/paulboone/ticgit
    * [new branch] master -> pb/master
    * [new branch] ticgit -> pb/ticgit
```

现在，Paul 的主干分支（master）已经完全可以在本地访问了，对应的名字是 `pb/master`，**你可以将它合并到自己的某个分支，或者切换到这个分支，看看有些什么有趣的更新**。

### 从远程仓库抓取数据

正如之前所看到的，可以用下面的命令从远程仓库抓取数据到本地：

```
$ git fetch [remote-name]
```

此命令会到远程仓库中拉取所有你本地仓库中还没有的数据。运行完成后，你就可以在本地访问该远程仓库中的所有分支，将其中某个分支合并到本地，或者只是取出某个分支，一探究竟。（我们会在第三章详细讨论关于分支的概念和操作。）

如果是克隆了一个仓库，此命令会自动将远程仓库归于 origin 名下。所以，`git fetch origin` **会抓取从你上次克隆以来别人上传到此远程仓库中的所有更新（或是上次 fetch 以来别人提交的更新）。有一点很重要，需要记住，fetch 命令只是将远端的数据拉到本地仓库，并不自动合并到当前工作分支，只有当你确实准备好了，才能手工合并**。

如果设置了**某个分支用于跟踪某个远端仓库的分支（参见下节及第三章的内容**），可以使用 `git pull` **命令自动抓取数据下来，然后将远端分支自动合并到本地仓库中当前分支**。在日常工作中我们经常这么用，既快且好。实际上，默认情况下 `git clone` **命令本质上就是自动创建了本地的 master 分支用于跟踪远程仓库中的 master 分支（假设远程仓库确实有 master 分支）。所以一般我们运行 `git pull`，目的都是要从原始克隆的远端仓库中抓取数据后，合并到工作目录中的当前分支**。

### 推送数据到远程仓库

项目进行到一个阶段，要同别人分享目前的成果，可以将本地仓库中的数据推送到远程仓库。实现这个任务的命令很简单： `git push [remote-name] [branch-name]`。如果要把本地的 master 分支推送到 `origin` 服务器上（再次说明下，克隆操作会自动使用默认的 master 和 origin 名字），可以运行下面的命令：

```
$ git push origin master
```

**只有在所克隆的服务器上有写权限，或者同一时刻没有其他人在推数据，这条命令才会如期完成任务。如果在你推数据前，已经有其他人推送了若干更新，那你的推送操作就会被驳回。你必须先把他们的更新抓取到本地，合并到自己的项目中，然后才可以再次推送**。有关推送数据到远程仓库的详细内容见第三章。

### 查看远程仓库信息

我们可以通过命令 `git remote show [remote-name]` 查看某个远程仓库的详细信息，比如要看所克隆的 `origin` 仓库，可以运行：

```
$ git remote show origin
    * remote origin
    URL: git://github.com/schacon/ticgit.git
    Remote branch merged with 'git pull' while on branch master
    master
    Tracked remote branches
    master
    ticgit
```

除了对应的克隆地址外，它还给出了许多额外的信息。它友善地告诉你如果是在 master 分支，就可以用 `git pull` 命令抓取数据合并到本地。另外还列出了所有处于跟踪状态中的远端分支。

上面的例子非常简单，而随着使用 Git 的深入，`git remote show` 给出的信息可能会像这样：

```
$ git remote show origin
    * remote origin
    URL: git@github.com:defunkt/github.git
    Remote branch merged with 'git pull' while on branch issues
    issues
    Remote branch merged with 'git pull' while on branch master
    master
    New remote branches (next fetch will store in remotes/origin)
    caching
    Stale tracking branches (use 'git remote prune')
    libwalker
    walker2
    Tracked remote branches
    acl
    apiv2
    dashboard2
    issues
    master
    postgres
    Local branch pushed with 'git push'
    master:master
```

它告诉我们，运行 `git push` 时缺省推送的分支是什么（译注：最后两行）。它还显示了有哪些远端分支还没有同步到本地（译注：第六行的 `caching` 分支），哪些已同步到本地的远端分支在远端服务器上已被删除（译注：`Stale tracking branches` 下面的两个分支），以及运行 `git pull` 时将自动合并哪些分支（译注：前四行中列出的 `issues` 和 `master` 分支）。

### 远程仓库的删除和重命名

在新版 Git 中可以用 `git remote rename` 命令修改某个远程仓库在本地的简称，比如想把 `pb` 改成 `paul`，可以这么运行：

```
$ git remote rename pb paul
    $ git remote
    origin
    paul
```

注意，对远程仓库的重命名，也会使对应的分支名称发生变化，原来的 `pb/master` 分支现在成了 `paul/master`。

碰到远端仓库服务器迁移，或者原来的克隆镜像不再使用，又或者某个参与者不再贡献代码，那么需要移除对应的远端仓库，可以运行 `git remote rm` 命令：

```
$ git remote rm paul
    $ git remote
    origin
```

## [2.6 打标签](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.6-%E6%89%93%E6%A0%87%E7%AD%BE)

同大多数 VCS 一样，Git 也可以对某一时间点上的版本打上标签。人们在发布某个软件版本（比如 v1.0 等等）的时候，经常这么做。本节我们一起来学习如何列出所有可用的标签，如何新建标签，以及各种不同类型标签之间的差别。

### 列显已有的标签

列出现有标签的命令非常简单，直接运行 `git tag` 即可：

```
$ git tag
    v0.1
    v1.3
```

显示的标签按字母顺序排列，所以标签的先后并不表示重要程度的轻重。

我们可以用特定的搜索模式列出符合条件的标签。在 Git 自身项目仓库中，有着超过 240 个标签，如果你只对 1.4.2 系列的版本感兴趣，可以运行下面的命令：

```
$ git tag -l 'v1.4.2.*'
    v1.4.2.1
    v1.4.2.2
    v1.4.2.3
    v1.4.2.4
```

### 新建标签

Git 使用的标签有两种类型：**轻量级的（lightweight）和含附注的（annotated）**。轻量级标签就像是个不会变化的分支，实际上它就是个指向特定提交对象的引用。而含附注标签，实际上是存储在仓库中的一个独立对象，它有自身的校验和信息，包含着标签的名字，电子邮件地址和日期，以及标签说明，标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。**一般我们都建议使用含附注型的标签，以便保留相关信息；当然，如果只是临时性加注标签，或者不需要旁注额外信息，用轻量级标签也没问题**。

### 含附注的标签

创建一个含附注类型的标签非常简单，用 `-a` （译注：取 `annotated` 的首字母）指定标签名字即可：

```
$ git tag -a v1.4 -m 'my version 1.4'
    $ git tag
    v0.1
    v1.3
    v1.4
```

而 `-m` 选项则指定了对应的标签说明，Git 会将此说明一同保存在标签对象中。如果没有给出该选项，Git 会启动文本编辑软件供你输入标签说明。

可以使用 `git show` 命令查看相应标签的版本信息，并连同显示打标签时的提交对象。

```
$ git show v1.4
    tag v1.4
    Tagger: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Feb 9 14:45:11 2009 -0800

    my version 1.4
    commit 15027957951b64cf874c3557a0f3547bd83b3ff6
    Merge: 4a447f7... a6b4c97...
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sun Feb 8 19:02:46 2009 -0800

    Merge branch 'experiment'
```

我们可以看到在提交对象信息上面，列出了此标签的提交者和提交时间，以及相应的标签说明。

### 签署标签

如果你有自己的私钥，还可以用 GPG 来签署标签，只需要把之前的 `-a` 改为 `-s` （译注： 取 `signed` 的首字母）即可：

```
$ git tag -s v1.5 -m 'my signed 1.5 tag'
    You need a passphrase to unlock the secret key for
    user: "Scott Chacon <schacon@gee-mail.com>"
    1024-bit DSA key, ID F721C45A, created 2009-02-09
```

现在再运行 `git show` 会看到对应的 GPG 签名也附在其内：

```
$ git show v1.5
    tag v1.5
    Tagger: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Feb 9 15:22:20 2009 -0800

    my signed 1.5 tag
    -----BEGIN PGP SIGNATURE-----
    Version: GnuPG v1.4.8 (Darwin)

    iEYEABECAAYFAkmQurIACgkQON3DxfchxFr5cACeIMN+ZxLKggJQf0QYiQBwgySN
    Ki0An2JeAVUCAiJ7Ox6ZEtK+NvZAj82/
    =WryJ
    -----END PGP SIGNATURE-----
    commit 15027957951b64cf874c3557a0f3547bd83b3ff6
    Merge: 4a447f7... a6b4c97...
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sun Feb 8 19:02:46 2009 -0800

    Merge branch 'experiment'
```

稍后我们再学习如何验证已经签署的标签。

### 轻量级标签

轻量级标签实际上就是一个保存着对应提交对象的校验和信息的文件。要创建这样的标签，一个 `-a`，`-s` 或 `-m` 选项都不用，直接给出标签名字即可：

```
$ git tag v1.4-lw
    $ git tag
    v0.1
    v1.3
    v1.4
    v1.4-lw
    v1.5
```

现在运行 `git show` 查看此标签信息，就只有相应的提交对象摘要：

```
$ git show v1.4-lw
    commit 15027957951b64cf874c3557a0f3547bd83b3ff6
    Merge: 4a447f7... a6b4c97...
    Author: Scott Chacon <schacon@gee-mail.com>
    Date: Sun Feb 8 19:02:46 2009 -0800

    Merge branch 'experiment'
```

### 验证标签

可以使用 `git tag -v [tag-name]` （译注：取 `verify` 的首字母）的方式验证已经签署的标签。此命令会调用 GPG 来验证签名，所以你需要有签署者的公钥，存放在 keyring 中，才能验证：

```
$ git tag -v v1.4.2.1
    object 883653babd8ee7ea23e6a5c392bb739348b1eb61
    type commit
    tag v1.4.2.1
    tagger Junio C Hamano <junkio@cox.net> 1158138501 -0700

    GIT 1.4.2.1

    Minor fixes since 1.4.2, including git-mv and git-http with alternates.
    gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
    gpg: Good signature from "Junio C Hamano <junkio@cox.net>"
    gpg: aka "[jpeg image of size 1513]"
    Primary key fingerprint: 3565 2A26 2040 E066 C9A7 4A7D C0C6 D9A4 F311 9B9A
```

若是没有签署者的公钥，会报告类似下面这样的错误：

```
gpg: Signature made Wed Sep 13 02:08:25 2006 PDT using DSA key ID F3119B9A
    gpg: Can't check signature: public key not found
    error: could not verify the tag 'v1.4.2.1'
```

### 后期加注标签

你甚至可以在后期对早先的某次提交加注标签。比如在下面展示的提交历史中：

```
$ git log --pretty=oneline
    15027957951b64cf874c3557a0f3547bd83b3ff6 Merge branch 'experiment'
    a6b4c97498bd301d84096da251c98a07c7723e65 beginning write support
    0d52aaab4479697da7686c15f77a3d64d9165190 one more thing
    6d52a271eda8725415634dd79daabbc4d9b6008e Merge branch 'experiment'
    0b7434d86859cc7b8c3d5e1dddfed66ff742fcbc added a commit function
    4682c3261057305bdd616e23b64b0857d832627b added a todo file
    166ae0c4d3f420721acbb115cc33848dfcc2121a started write support
    9fceb02d0ae598e95dc970b74767f19372d61af8 updated rakefile
    964f16d36dfccde844893cac5b347e7b3d44abbc commit the todo
    8a5cbc430f1a9c3d00faaeffd07798508422908a updated readme
```

我们忘了在提交 “updated rakefile” 后为此项目打上版本号 v1.2，没关系，现在也能做。只要在打标签的时候跟上对应提交对象的校验和（或前几位字符）即可：

```
$ git tag -a v1.2 9fceb02
```

可以看到我们已经补上了标签：

```
$ git tag
    v0.1
    v1.2
    v1.3
    v1.4
    v1.4-lw
    v1.5

    $ git show v1.2
    tag v1.2
    Tagger: Scott Chacon <schacon@gee-mail.com>
    Date: Mon Feb 9 15:32:16 2009 -0800

    version 1.2
    commit 9fceb02d0ae598e95dc970b74767f19372d61af8
    Author: Magnus Chacon <mchacon@gee-mail.com>
    Date: Sun Apr 27 20:43:35 2008 -0700

    updated rakefile
    ...
```

### 分享标签

默认情况下，`git push` **并不会把标签传送到远端服务器上，只有通过显式命令才能分享标签到远端仓库。其命令格式如同推送分支，运行** `git push origin [tagname]` 即可：

```
$ git push origin v1.5
    Counting objects: 50, done.
    Compressing objects: 100% (38/38), done.
    Writing objects: 100% (44/44), 4.56 KiB, done.
    Total 44 (delta 18), reused 8 (delta 1)
    To git@github.com:schacon/simplegit.git
    * [new tag] v1.5 -> v1.5
```

如果要**一次推送所有本地新增的标签上去**，可以使用 `--tags` 选项：

```
$ git push origin --tags
    Counting objects: 50, done.
    Compressing objects: 100% (38/38), done.
    Writing objects: 100% (44/44), 4.56 KiB, done.
    Total 44 (delta 18), reused 8 (delta 1)
    To git@github.com:schacon/simplegit.git
    * [new tag] v0.1 -> v0.1
    * [new tag] v1.2 -> v1.2
    * [new tag] v1.4 -> v1.4
    * [new tag] v1.4-lw -> v1.4-lw
    * [new tag] v1.5 -> v1.5
```

现在，其他人克隆共享仓库或拉取数据同步后，也会看到这些标签。

## [2.7 技巧和窍门](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.7-%E6%8A%80%E5%B7%A7%E5%92%8C%E7%AA%8D%E9%97%A8)

在结束本章之前，我还想和大家分享一些 Git 使用的技巧和窍门。很多使用 Git 的开发者可能根本就没用过这些技巧，我们也不是说在读过本书后非得用这些技巧不可，但至少应该有所了解吧。说实话，有了这些小窍门，我们的工作可以变得更简单，更轻松，更高效。

### 自动补全

如果你用的是 Bash shell，可以试试看 Git 提供的自动补全脚本。下载 Git 的源代码，进入 `contrib/completion` 目录，会看到一个 `git-completion.bash` 文件。将此文件复制到你自己的用户主目录中（译注：按照下面的示例，还应改名加上点：`cp git-completion.bash ~/.git-completion.bash`），并把下面一行内容添加到你的 `.bashrc` 文件中：

```
source ~/.git-completion.bash
```

也可以为系统上所有用户都设置默认使用此脚本。Mac 上将此脚本复制到 `/opt/local/etc/bash_completion.d` 目录中，Linux 上则复制到 `/etc/bash_completion.d/` 目录中。这两处目录中的脚本，都会在 Bash 启动时自动加载。

如果在 Windows 上安装了 msysGit，默认使用的 Git Bash 就已经配好了这个自动补全脚本，可以直接使用。

在输入 Git 命令的时候可以敲两次跳格键（Tab），就会看到列出所有匹配的可用命令建议：

```
$ git co<tab><tab>
    commit config
```

此例中，键入 git co 然后连按两次 Tab 键，会看到两个相关的建议（命令） commit 和 config。继而输入 `m<tab>` 会自动完成 `git commit` 命令的输入。

命令的选项也可以用这种方式自动完成，其实这种情况更实用些。比如运行 `git log` 的时候忘了相关选项的名字，可以输入开头的几个字母，然后敲 Tab 键看看有哪些匹配的：

```
$ git log --s<tab>
    --shortstat --since= --src-prefix= --stat --summary
```

这个技巧不错吧，可以节省很多输入和查阅文档的时间。

### Git 命令别名

Git 并不会推断你输入的几个字符将会是哪条命令，不过如果想偷懒，少敲几个命令的字符，可以用 `git config` 为命令设置别名。来看看下面的例子：

```
$ git config --global alias.co checkout
    $ git config --global alias.br branch
    $ git config --global alias.ci commit
    $ git config --global alias.st status
```

现在，如果要输入 `git commit` 只需键入 `git ci` 即可。而随着 Git 使用的深入，会有很多经常要用到的命令，遇到这种情况，不妨建个别名提高效率。

使用这种技术还可以创造出新的命令，比方说取消暂存文件时的输入比较繁琐，可以自己设置一下：

```
$ git config --global alias.unstage 'reset HEAD --'
```

这样一来，下面的两条命令完全等同：

```
$ git unstage fileA
    $ git reset HEAD fileA
```

显然，使用别名的方式看起来更清楚。另外，我们还经常设置 `last` 命令：

```
$ git config --global alias.last 'log -1 HEAD'
```

然后要看最后一次的提交信息，就变得简单多了：

```
$ git last
    commit 66938dae3329c7aebe598c2246a8e6af90d04646
    Author: Josh Goebel <dreamer3@example.com>
    Date: Tue Aug 26 19:48:51 2008 +0800

    test for current head

    Signed-off-by: Scott Chacon <schacon@example.com>
```

可以看出，实际上 Git 只是简单地在命令中替换了你设置的别名。不过有时候我们希望运行某个外部命令，而非 Git 的子命令，这个好办，只需要在命令前加上 `!` 就行。如果你自己写了些处理 Git 仓库信息的脚本的话，就可以用这种技术包装起来。作为演示，我们可以设置用 `git visual` 启动 `gitk`：

```
$ git config --global alias.visual '!gitk'
```

## [2.8 小结](https://git.oschina.net/progit/2-Git-%E5%9F%BA%E7%A1%80.html#2.8-%E5%B0%8F%E7%BB%93)

到目前为止，你已经学会了最基本的 Git 本地操作：创建和克隆仓库，做出修改，暂存并提交这些修改，以及查看所有历史修改记录。接下来，我们将学习 Git 的必杀技特性：分支模型。

# 3 Git 分支

1.  [3.1 何谓分支](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.1-%E4%BD%95%E8%B0%93%E5%88%86%E6%94%AF)
1.  [3.2 分支的新建与合并](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.2-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)
1.  [3.3 分支的管理](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.3-%E5%88%86%E6%94%AF%E7%9A%84%E7%AE%A1%E7%90%86)
1.  [3.4 利用分支进行开发的工作流程](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.4-%E5%88%A9%E7%94%A8%E5%88%86%E6%94%AF%E8%BF%9B%E8%A1%8C%E5%BC%80%E5%8F%91%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)
1.  [3.5 远程分支](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.5-%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF)
1.  [3.6 分支的衍合](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.6-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)
1.  [3.7 小结](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.7-%E5%B0%8F%E7%BB%93)

几乎每一种版本控制系统都以某种形式支持分支。使**用分支意味着你可以从开发主线上分离开来，然后在不影响主线的同时继续工作**。在很多版本控制系统中，这是个昂贵的过程，常常需要创建一个源代码目录的完整副本，对大型项目来说会花费很长时间。

有人把 Git 的分支模型称为“必杀技特性”，而正是因为它，将 Git 从版本控制系统家族里区分出来。Git 有何特别之处呢？Git 的分支可谓是难以置信的轻量级，它的新建操作几乎可以在瞬间完成，并且在不同分支间切换起来也差不多一样快。和许多其他版本控制系统不同，**Git 鼓励在工作流程中频繁使用分支与合并，哪怕一天之内进行许多次都没有关系。理解分支的概念并熟练运用后，你才会意识到为什么 Git 是一个如此强大而独特的工具，并从此真正改变你的开发方式**。

## [3.1 何谓分支](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.1-%E4%BD%95%E8%B0%93%E5%88%86%E6%94%AF)

为了理解 Git 分支的实现方式，我们需要回顾一下 Git 是如何储存数据的。或许你还记得第一章的内容，Git 保存的不是文件差异或者变化量，而只是一系列文件快照。

在 Git 中提交时，会保存一个提交（commit）对象，该对象包含一个指向暂存内容快照的指针，包含本次提交的作者等相关附属信息，包含零个或多个指向该提交对象的父对象指针：首次提交是没有直接祖先的，普通提交有一个祖先，由两个或多个分支合并产生的提交则有多个祖先。

为直观起见，我们假设在工作目录中有三个文件，准备将它们暂存后提交。暂存操作会对每一个文件计算校验和（即第一章中提到的 SHA-1 哈希字串），然后把当前版本的文件快照保存到 Git 仓库中（Git 使用 blob 类型的对象存储这些快照），并将校验和加入暂存区域：

```
$ git add README test.rb LICENSE
    $ git commit -m 'initial commit of my project'
```

当使用 `git commit` 新建一个提交对象前，Git 会先计算每一个子目录（本例中就是项目根目录）的校验和，然后在 Git 仓库中将这些目录保存为树（tree）对象。之后 Git 创建的提交对象，除了包含相关提交信息以外，还包含着指向这个树对象（项目根目录）的指针，如此它就可以在将来需要的时候，重现此次快照的内容了。

现在，Git 仓库中有五个对象：三个表示文件快照内容的 blob 对象；一个记录着目录树内容及其中各个文件对应 blob 对象索引的 tree 对象；以及一个包含指向 tree 对象（根目录）的索引和其他提交信息元数据的 commit 对象。概念上来说，仓库中的各个对象保存的数据和相互关系看起来如图 3-1 所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d9e0571b94c94a62ad47b489c76d5add~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-1. 单个提交对象在仓库中的数据结构

作些修改后再次提交，那么这次的提交对象会包含一个指向上次提交对象的指针（译注：即下图中的 parent 对象）。两次提交后，仓库历史会变成图 3-2 的样子：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/608fe5fb30ab428996e1f0dfb1d0f844~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-2. 多个提交对象之间的链接关系

现在来谈分支。Git 中的分支，其实本质上仅仅是个指向 commit 对象的可变指针。Git 会使用 master 作为分支的默认名字。在若干次提交后，你其实已经有了一个指向最后一次提交对象的 master 分支，它在每次提交的时候都会自动向前移动。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9a59bdee4abd47d2872ee0761205e39a~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-3. 分支其实就是从某个提交对象往回看的历史

那么，Git 又是如何创建一个新的分支的呢？答案很简单，创建一个新的分支指针。比如新建一个 testing 分支，可以使用 `git branch` 命令：

```
$ git branch testing
```

这会在当前 commit 对象上新建一个分支指针（见图 3-4）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1d7064906ab644a8bc61669ff8b915c2~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-4. 多个分支指向提交数据的历史

那么，Git 是如何知道你当前在哪个分支上工作的呢？其实答案也很简单，它保存着一个名为 HEAD 的特别指针。请注意它和你熟知的许多其他版本控制系统（比如 Subversion 或 CVS）里的 HEAD 概念大不相同。在 Git 中，它是一个指向你正在工作中的本地分支的指针（译注：将 HEAD 想象为当前分支的别名。）。运行 `git branch` 命令，仅仅是建立了一个新的分支，**但不会自动切换到这个分支中去，所以在这个例子中，我们依然还在 master 分支里工作**（参考图 3-5）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4492bd6e66684ca3a42b86be4602fb08~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-5. HEAD 指向当前所在的分支

要切换到其他分支，可以执行 `git checkout` 命令。我们现在转换到新建的 testing 分支：

```
$ git checkout testing
```

这样 HEAD 就指向了 testing 分支（见图3-6）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3723918da59546549a6ff0ab8983f82c~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-6. HEAD 在你转换分支时指向新的分支

这样的实现方式会给我们带来什么好处呢？好吧，现在不妨再提交一次：

```
$ vim test.rb
    $ git commit -a -m 'made a change'
```

图 3-7 展示了提交后的结果。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3e8697ae007a44238526fb6f2803bd6b~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-7. 每次提交后 HEAD 随着分支一起向前移动

非常有趣，现在 testing 分支向前移动了一格，而 master 分支仍然指向原先 `git checkout` 时所在的 commit 对象。现在我们回到 master 分支看看：

```
$ git checkout master
```

图 3-8 显示了结果。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1d91bd12cd145aaa69ee84ec5c24346~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-8. HEAD 在一次 checkout 之后移动到了另一个分支

这条命令做了两件事。它把 HEAD 指针移回到 master 分支，并把工作目录中的文件换成了 master 分支所指向的快照内容。也就是说，现在开始所做的改动，将始于本项目中一个较老的版本。它的主要作用是将 testing 分支里作出的修改暂时取消，这样你就可以向另一个方向进行开发。

我们作些修改后再次提交：

```
$ vim test.rb
    $ git commit -a -m 'made other changes'
```

现在我们的项目提交历史产生了分叉（如图 3-9 所示），因为刚才我们创建了一个分支，转换到其中进行了一些工作，然后又回到原来的主分支进行了另外一些工作。这些改变分别孤立在不同的分支里：我们可以在不同分支里反复切换，并在时机成熟时把它们合并到一起。而所有这些工作，仅仅需要 `branch` 和 `checkout` 这两条命令就可以完成。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a1ce3d10dfa34dcd8563f0695d8a8af6~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-9. 不同流向的分支历史

由于 Git 中的分支实际上仅是一个包含所指对象校验和（40 个字符长度 SHA-1 字串）的文件，所以创建和销毁一个分支就变得非常廉价。说白了，新建一个分支就是向一个文件写入 41 个字节（外加一个换行符）那么简单，当然也就很快了。

这和大多数版本控制系统形成了鲜明对比，它们管理分支大多采取备份所有项目文件到特定目录的方式，所以根据项目文件数量和大小不同，可能花费的时间也会有相当大的差别，快则几秒，慢则数分钟。而 Git 的实现与项目复杂度无关，它永远可以在几毫秒的时间内完成分支的创建和切换。同时，因为每次提交时都记录了祖先信息（译注：即 `parent` 对象），将来要合并分支时，寻找恰当的合并基础（译注：即共同祖先）的工作其实已经自然而然地摆在那里了，所以实现起来非常容易。Git 鼓励开发者频繁使用分支，正是因为有着这些特性作保障。

接下来看看，我们为什么应该频繁使用分支。

## [3.2 分支的新建与合并](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.2-%E5%88%86%E6%94%AF%E7%9A%84%E6%96%B0%E5%BB%BA%E4%B8%8E%E5%90%88%E5%B9%B6)

现在让我们来看一个简单的分支与合并的例子，实际工作中大体也会用到这样的工作流程：

1.  开发某个网站。
1.  为实现某个新的需求，创建一个分支。
1.  在这个分支上开展工作。

假设此时，你突然接到一个电话说有个很严重的问题需要紧急修补，那么可以按照下面的方式处理：

1.  返回到原先已经发布到生产服务器上的分支。
1.  为这次紧急修补建立一个新分支，并在其中修复问题。
1.  通过测试后，回到生产服务器所在的分支，将修补分支合并进来，然后再推送到生产服务器上。
1.  切换到之前实现新需求的分支，继续工作。

### 分支的新建与切换

首先，我们假设你正在项目中愉快地工作，并且已经提交了几次更新（见图 3-10）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/cf029aee72aa41dea6f5e55e05f1c19d~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-10. 一个简短的提交历史

现在，你决定要修补问题追踪系统上的 #53 问题。顺带说明下，Git 并不同任何特定的问题追踪系统打交道。这里为了说明要解决的问题，才把新建的分支取名为 iss53。要新建并切换到该分支，运行 `git checkout` 并加上 `-b` 参数：

```
$ git checkout -b iss53
    Switched to a new branch "iss53"
```

这相当于执行下面这两条命令：

```
$ git branch iss53
    $ git checkout iss53
```

图 3-11 示意该命令的执行结果。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84030a8bde994fd5bc78dbdcdd29316f~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-11. 创建了一个新分支的指针

接着你开始尝试修复问题，在提交了若干次更新后，`iss53` 分支的指针也会随着向前推进，因为它就是当前分支（换句话说，当前的 `HEAD` 指针正指向 `iss53`，见图 3-12）：

```
$ vim index.html
    $ git commit -a -m 'added a new footer [issue 53]'
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ee4f30e2f72472aad242e94960bcdba~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-12. iss53 分支随工作进展向前推进

现在你就接到了那个网站问题的紧急电话，需要马上修补。有了 Git ，我们就不需要同时发布这个补丁和 `iss53` 里作出的修改，也不需要在创建和发布该补丁到服务器之前花费大力气来复原这些修改。唯一需要的仅仅是切换回 `master` 分支。

不过在此之前，留心你的暂存区或者工作目录里，那些还没有提交的修改，它会和你即将检出的分支产生冲突从而阻止 Git 为你切换分支。切换分支的时候最好保持一个清洁的工作区域。稍后会介绍几个绕过这种问题的办法（分别叫做 stashing 和 commit amending）。目前已经提交了所有的修改，所以接下来可以正常转换到 `master` 分支：

```
$ git checkout master
    Switched to branch "master"
```

此时工作目录中的内容和你在解决问题 #53 之前一模一样，你可以集中精力进行紧急修补。这一点值得牢记：Git 会把工作目录的内容恢复为检出某分支时它所指向的那个提交对象的快照。它会自动添加、删除和修改文件以确保目录的内容和你当时提交时完全一样。

接下来，你得进行紧急修补。我们创建一个紧急修补分支 `hotfix` 来开展工作，直到搞定（见图 3-13）：

```
$ git checkout -b 'hotfix'
    Switched to a new branch "hotfix"
    $ vim index.html
    $ git commit -a -m 'fixed the broken email address'
    [hotfix]: created 3a0874c: "fixed the broken email address"
    1 files changed, 0 insertions(+), 1 deletions(-)
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f951675afce84848b8044cd030f90cde~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-13. hotfix 分支是从 master 分支所在点分化出来的

有必要作些测试，确保修补是成功的，然后回到 `master` 分支并把它合并进来，然后发布到生产服务器。用 `git merge` 命令来进行合并：

```
$ git checkout master
    $ git merge hotfix
    Updating f42c576..3a0874c
    Fast forward
    README | 1 -
    1 files changed, 0 insertions(+), 1 deletions(-)
```

请注意，合并时出现了“Fast forward”的提示。由于当前 `master` 分支所在的提交对象是要并入的 `hotfix` 分支的直接上游，Git 只需把 `master` 分支指针直接右移。换句话说，如果顺着一个分支走下去可以到达另一个分支的话，那么 Git 在合并两者时，只会简单地把指针右移，因为这种单线的历史分支不存在任何需要解决的分歧，所以这种合并过程可以称为快进（Fast forward）。

现在最新的修改已经在当前 `master` 分支所指向的提交对象中了，可以部署到生产服务器上去了（见图 3-14）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e661208c01e5437e8f9d756cc8eaa47b~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-14. 合并之后，master 分支和 hotfix 分支指向同一位置。

在那个超级重要的修补发布以后，你想要回到被打扰之前的工作。由于当前 `hotfix` 分支和 `master` 都指向相同的提交对象，所以 `hotfix` 已经完成了历史使命，可以删掉了。使用 `git branch` 的 `-d` 选项执行删除操作：

```
$ git branch -d hotfix
    Deleted branch hotfix (3a0874c).
```

现在回到之前未完成的 #53 问题修复分支上继续工作（图 3-15）：

```
$ git checkout iss53
    Switched to branch "iss53"
    $ vim index.html
    $ git commit -a -m 'finished the new footer [issue 53]'
    [iss53]: created ad82d7a: "finished the new footer [issue 53]"
    1 files changed, 1 insertions(+), 0 deletions(-)
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e229dc77bc6d4125afe4c4c770ac4437~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-15. iss53 分支可以不受影响继续推进。

不用担心之前 `hotfix` 分支的修改内容尚未包含到 `iss53` 中来。**如果确实需要纳入此次修补，可以用 `git merge master` 把 master 分支合并到 `iss53`；或者等 `iss53` 完成之后，再将 `iss53` 分支中的更新并入** `master`。

### 分支的合并

在问题 #53 相关的工作完成之后，可以合并回 `master` 分支。实际操作同前面合并 `hotfix` 分支差不多，只需回到 `master` 分支，运行 `git merge` 命令指定要合并进来的分支：

```
$ git checkout master
    $ git merge iss53
    Merge made by recursive.
    README | 1 +
    1 files changed, 1 insertions(+), 0 deletions(-)
```

请注意，这次合并操作的底层实现，并不同于之前 `hotfix` 的并入方式。因为这次你的开发历史是从更早的地方开始分叉的。由于当前 `master` 分支所指向的提交对象（C4）并不是 `iss53` 分支的直接祖先，Git 不得不进行一些额外处理。就此例而言，Git 会用两个分支的末端（C4 和 C5）以及它们的共同祖先（C2）进行一次简单的三方合并计算。图 3-16 用红框标出了 Git 用于合并的三个提交对象：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bcd452fb24a14100a9d9ce515dddd1b8~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-16. Git 为分支合并自动识别出最佳的同源合并点。

这次，Git 没有简单地把分支指针右移，而是对三方合并后的结果重新做一个新的快照，并自动创建一个指向它的提交对象（C6）（见图 3-17）。这个提交对象比较特殊，它有两个祖先（C4 和 C5）。

值得一提的是 Git 可以自己裁决哪个共同祖先才是最佳合并基础；这和 CVS 或 Subversion（1.5 以后的版本）不同，它们需要开发者手工指定合并基础。所以此特性让 Git 的合并操作比其他系统都要简单不少。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8b728003731f49c0905953d0a68683be~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-17. Git 自动创建了一个包含了合并结果的提交对象。

既然之前的工作成果已经合并到 `master` 了，那么 `iss53` 也就没用了。**你可以就此删除它，并在问题追踪系统里关闭该问题**。

```
$ git branch -d iss53
```

### 遇到冲突时的分支合并

有时候合并操作并不会如此顺利。如果在不同的分支中都修改了同一个文件的同一部分，Git 就无法干净地把两者合到一起（译注：逻辑上说，这种问题只能由人来裁决。）。如果你在解决问题 #53 的过程中修改了 `hotfix` 中修改的部分，将得到类似下面的结果：

```
$ git merge iss53
    Auto-merging index.html
    CONFLICT (content): Merge conflict in index.html
    Automatic merge failed; fix conflicts and then commit the result.
```

**Git 作了合并，但没有提交，它会停下来等你解决冲突。要看看哪些文件在合并时发生冲突，可以用 `git status` 查阅**：

```
[master*]$ git status
    index.html: needs merge
    # On branch master
    # Changes not staged for commit:
    # (use "git add <file>..." to update what will be committed)
    # (use "git checkout -- <file>..." to discard changes in working directory)
    #
    # unmerged: index.html
    #
```

任何包含未解决冲突的文件都会以未合并（unmerged）的状态列出。Git 会在有冲突的文件里加入标准的冲突解决标记，可以通过它们来手工定位并解决这些冲突。可以看到此文件包含类似下面这样的部分：

```
<<<<<<< HEAD:index.html
    <div id="footer">contact : email.support@github.com</div>
    =======
    <div id="footer">
    please contact us at support@github.com
    </div>
    >>>>>>> iss53:index.html
```

可以看到 `=======` 隔开的上半部分，是 `HEAD`（即 `master` 分支，在运行 `merge` 命令时所切换到的分支）中的内容，下半部分是在 `iss53` 分支中的内容。解决冲突的办法无非是二者选其一或者由你亲自整合到一起。比如你可以通过把这段内容替换为下面这样来解决：

```
<div id="footer">
    please contact us at email.support@github.com
    </div>
```

这个解决方案各采纳了两个分支中的一部分内容，而且我还删除了 `<<<<<<<`，`=======` 和 `>>>>>>>` 这些行。**在解决了所有文件里的所有冲突后，运行 `git add` 将把它们标记为已解决状态（译注：实际上就是来一次快照保存到暂存区域。）。因为一旦暂存，就表示冲突已经解决**。如果你想用一个有图形界面的工具来解决这些问题，不妨运行 `git mergetool`，它会调用一个可视化的合并工具并引导你解决所有冲突：

```
$ git mergetool
    merge tool candidates: kdiff3 tkdiff xxdiff meld gvimdiff opendiff emerge vimdiff
    Merging the files: index.html

    Normal merge conflict for 'index.html':
    {local}: modified
    {remote}: modified
    Hit return to start merge resolution tool (opendiff):
```

如果不想用默认的合并工具（Git 为我默认选择了 `opendiff`，因为我在 Mac 上运行了该命令），你可以在上方"merge tool candidates"里找到可用的合并工具列表，输入你想用的工具名。我们将在第七章讨论怎样改变环境中的默认值。

退出合并工具以后，Git 会询问你合并是否成功。如果回答是，它会为你把相关文件暂存起来，以表明状态为已解决。

再运行一次 `git status` 来确认所有冲突都已解决：

```
$ git status
    # On branch master
    # Changes to be committed:
    # (use "git reset HEAD <file>..." to unstage)
    #
    # modified: index.html
    #
```

如果觉得满意了，并且确认所有冲突都已解决，也就是进入了暂存区，就可以用 `git commit` 来完成这次合并提交。提交的记录差不多是这样：

```
Merge branch 'iss53'

    Conflicts:
    index.html
    #
    # It looks like you may be committing a MERGE.
    # If this is not correct, please remove the file
    # .git/MERGE_HEAD
    # and try again.
    #
```

如果想给将来看这次合并的人一些方便，可以修改该信息，提供更多合并细节。比如你都作了哪些改动，以及这么做的原因。**有时候裁决冲突的理由并不直接或明显，有必要略加注解**。

## [3.3 分支的管理](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.3-%E5%88%86%E6%94%AF%E7%9A%84%E7%AE%A1%E7%90%86)

到目前为止，你已经学会了如何创建、合并和删除分支。除此之外，我们还需要学习如何管理分支，在日后的常规工作中会经常用到下面介绍的管理命令。

`git branch` 命令不仅仅能创建和删除分支，如果不加任何参数，它会给出当前所有分支的清单：

```
$ git branch
    iss53
    * master
    testing
```

注意看 `master` 分支前的 `*` 字符：它表示当前所在的分支。也就是说，如果现在提交更新，`master` 分支将随着开发进度前移。若要查看各个分支最后一个提交对象的信息，运行 `git branch -v`：

```
$ git branch -v
    iss53 93b412c fix javascript issue
    * master 7a98805 Merge branch 'iss53'
    testing 782fd34 add scott to the author list in the readmes
```

要从该清单中筛选出你已经（或尚未）与当前分支合并的分支，可以用 `--merge` 和 `--no-merged` 选项（Git 1.5.6 以上版本）。比如用 `git branch --merge` 查看哪些分支已被并入当前分支（译注：也就是说哪些分支是当前分支的直接上游。）：

```
$ git branch --merged
    iss53
    * master
```

之前我们已经合并了 `iss53`，所以在这里会看到它。一般来说，列表中没有 `*` 的分支通常都可以用 `git branch -d` 来删掉。**原因很简单，既然已经把它们所包含的工作整合到了其他分支，删掉也不会损失什么**。

另外可以用 `git branch --no-merged` 查看尚未合并的工作：

```
$ git branch --no-merged
    testing
```

它会显示还未合并进来的分支。由于这些分支中还包含着尚未合并进来的工作成果，所以简单地用 `git branch -d` 删除该分支会提示错误，因为那样做会丢失数据：

```
$ git branch -d testing
    error: The branch 'testing' is not an ancestor of your current HEAD.
    If you are sure you want to delete it, run 'git branch -D testing'.
```

不过，如果你确实想要删除该分支上的改动，可以用大写的删除选项 `-D` 强制执行，就像上面提示信息中给出的那样。

## [3.4 利用分支进行开发的工作流程](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.4-%E5%88%A9%E7%94%A8%E5%88%86%E6%94%AF%E8%BF%9B%E8%A1%8C%E5%BC%80%E5%8F%91%E7%9A%84%E5%B7%A5%E4%BD%9C%E6%B5%81%E7%A8%8B)

现在我们已经学会了新建分支和合并分支，可以（或应该）用它来做点什么呢？在本节，我们会介绍一些利用分支进行开发的工作流程。而正是由于分支管理的便捷，才衍生出了这类典型的工作模式，你可以根据项目的实际情况选择一种用用看。

### 长期分支

由于 Git 使用简单的三方合并，所以就算在较长一段时间内，反复多次把某个分支合并到另一分支，也不是什么难事。也就是说，**你可以同时拥有多个开放的分支，每个分支用于完成特定的任务，随着开发的推进，你可以随时把某个特性分支的成果并到其他分支中**。

许多使用 Git 的开发者都喜欢用这种方式来开展工作，比如**仅在 `master` 分支中保留完全稳定的代码，即已经发布或即将发布的代码。与此同时，他们还有一个名为 `develop` 或 `next` 的平行分支，专门用于后续的开发，或仅用于稳定性测试 — 当然并不是说一定要绝对稳定，不过一旦进入某种稳定状态，便可以把它合并到 `master` 里**。这样，**在确保这些已完成的特性分支（短期分支，比如之前的 `iss53` 分支）能够通过所有测试，并且不会引入更多错误之后，就可以并到主干分支中，等待下一次的发布**。

本质上我们刚才谈论的，是随着提交对象不断右移的指针。稳定分支的指针总是在提交历史中落后一大截，而前沿分支总是比较靠前（见图 3-18）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/53eb539294a848c2b5cbcab99f2de6c1~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-18. 稳定分支总是比较老旧。

或者把它们想象成工作流水线，或许更好理解一些，经过测试的提交对象集合被遴选到更稳定的流水线（见图 3-19）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3239aad541824ae8b0339ca67c7ac73e~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-19. 想象成流水线可能会容易点。

你可以用这招维护不同层次的稳定性。某些大项目还会有个 `proposed`（建议）或 `pu`（proposed updates，建议更新）分支，它包含着那些可能还没有成熟到进入 `next` 或 `master` 的内容。**这么做的目的是拥有不同层次的稳定性：当这些分支进入到更稳定的水平时，再把它们合并到更高层分支中去。再次说明下，使用多个长期分支的做法并非必需，不过一般来说，对于特大型项目或特复杂的项目，这么做确实更容易管理**。

### 特性分支

在任何规模的项目中都可以使用特性（Topic）分支。一个特性分支是指一个短期的，用来实现单一特性或与其相关工作的分支。可能你在以前的版本控制系统里从未做过类似这样的事情，因为通常创建与合并分支消耗太大。然而在 Git 中，一天之内建立、使用、合并再删除多个分支是常见的事。

我们在上节的例子里已经见过这种用法了。我们创建了 `iss53` 和 `hotfix` 这两个特性分支，在提交了若干更新后，把它们合并到主干分支，然后删除。该技术允许你迅速且完全的进行语境切换 — 因为你的工作分散在不同的流水线里，每个分支里的改变都和它的目标特性相关，浏览代码之类的事情因而变得更简单了。你可以把作出的改变保持在特性分支中几分钟，几天甚至几个月，等它们成熟以后再合并，而不用在乎它们建立的顺序或者进度。

现在我们来看一个实际的例子。请看图 3-20，由下往上，起先我们在 `master` 工作到 C1，然后开始一个新分支 `iss91` 尝试修复 91 号缺陷，提交到 C6 的时候，又冒出一个解决该问题的新办法，于是从之前 C4 的地方又分出一个分支 `iss91v2`，干到 C8 的时候，又回到主干 `master` 中提交了 C9 和 C10，再回到 `iss91v2` 继续工作，提交 C11，接着，又冒出个不太确定的想法，从 `master` 的最新提交 C10 处开了个新的分支 `dumbidea` 做些试验。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2a511cde441e44fea762a1d7231e56c8~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-20. 拥有多个特性分支的提交历史。

现在，假定两件事情：我们最终决定使用第二个解决方案，即 `iss91v2` 中的办法；另外，我们把 `dumbidea` 分支拿给同事们看了以后，发现它竟然是个天才之作。所以接下来，我们准备抛弃原来的 `iss91` 分支（实际上会丢弃 C5 和 C6），直接在主干中并入另外两个分支。最终的提交历史将变成图 3-21 这样：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/add9bfe194d94a03ad90177290a0600e~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-21. 合并了 dumbidea 和 iss91v2 后的分支历史。

**请务必牢记这些分支全部都是本地分支，这一点很重要。当你在使用分支及合并的时候，一切都是在你自己的 Git 仓库中进行的 — 完全不涉及与服务器的交互**。

## [3.5 远程分支](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.5-%E8%BF%9C%E7%A8%8B%E5%88%86%E6%94%AF)

远程分支（remote branch）是对远程仓库中的分支的索引。它们是一些无法移动的本地分支；只有在 Git 进行网络交互时才会更新。**远程分支就像是书签，提醒着你上次连接远程仓库时上面各分支的位置**。

我们用 `(远程仓库名)/(分支名)` 这样的形式表示远程分支。比如我们想看看上次同 `origin` 仓库通讯时 `master` 分支的样子，就应该查看 `origin/master` 分支。如果你和同伴一起修复某个问题，但他们先推送了一个 `iss53` 分支到远程仓库，虽然你可能也有一个本地的 `iss53` 分支，但指向服务器上最新更新的却应该是 `origin/iss53` 分支。

可能有点乱，我们不妨举例说明。假设你们团队有个地址为 `git.ourcompany.com` 的 Git 服务器。如果你从这里克隆，Git 会自动为你将此远程仓库命名为 `origin`，并下载其中所有的数据，建立一个指向它的 `master` 分支的指针，在本地命名为 `origin/master`，但你无法在本地更改其数据。接着，Git 建立一个属于你自己的本地 `master` 分支，始于 `origin` 上 `master` 分支相同的位置，你可以就此开始工作（见图 3-22）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b9281101cd646aca8fe0e03f0197d64~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-22. 一次 Git 克隆会建立你自己的本地分支 master 和远程分支 origin/master，并且将它们都指向 `origin` 上的 `master` 分支。

如果你在本地 `master` 分支做了些改动，与此同时，其他人向 `git.ourcompany.com` 推送了他们的更新，那么服务器上的 `master` 分支就会向前推进，而于此同时，你在本地的提交历史正朝向不同方向发展。不过只要你不和服务器通讯，你的 `origin/master` 指针仍然保持原位不会移动（见图 3-23）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f046065d26dc410b8ce4f2332366d1e9~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-23. 在本地工作的同时有人向远程仓库推送内容会让提交历史开始分流。

可以运行 `git fetch origin` 来同步远程服务器上的数据到本地。该命令首先找到 `origin` 是哪个服务器（本例为 `git.ourcompany.com`），从上面获取你尚未拥有的数据，更新你本地的数据库，然后把 `origin/master` 的指针移到它最新的位置上（见图 3-24）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/59aecf5a20934ead8030f814eba9a308~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-24. git fetch 命令会更新 remote 索引。

为了演示拥有多个远程分支（在不同的远程服务器上）的项目是如何工作的，我们假设你还有另一个仅供你的敏捷开发小组使用的内部服务器 `git.team1.ourcompany.com`。可以用第二章中提到的 `git remote add` 命令把它加为当前项目的远程分支之一。我们把它命名为 `teamone`，以便代替完整的 Git URL 以方便使用（见图 3-25）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7c26d9771f454a348c1a609358b724e9~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-25. 把另一个服务器加为远程仓库

现在你可以用 `git fetch teamone` 来获取小组服务器上你还没有的数据了。由于当前该服务器上的内容是你 `origin` 服务器上的子集，Git 不会下载任何数据，而只是简单地创建一个名为 `teamone/master` 的远程分支，指向 `teamone` 服务器上 `master` 分支所在的提交对象 `31b8e`（见图 3-26）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/eed2c7f7fdeb4f86b92a7fed07b95c97~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-26. 你在本地有了一个指向 teamone 服务器上 master 分支的索引。

### 推送本地分支

要想和其他人分享某个本地分支，**你需要把它推送到一个你拥有写权限的远程仓库**。你创建的本地分支不会因为你的写入操作而被自动同步到你引入的远程服务器上，你需要明确地执行推送分支的操作。换句话说，对于无意分享的分支，你尽管保留为私人分支好了，而**只推送那些协同工作要用到的特性分支**。

如果你有个叫 `serverfix` 的分支需要和他人一起开发，可以运行 `git push (远程仓库名) (分支名)`：

```
$ git push origin serverfix
    Counting objects: 20, done.
    Compressing objects: 100% (14/14), done.
    Writing objects: 100% (15/15), 1.74 KiB, done.
    Total 15 (delta 5), reused 0 (delta 0)
    To git@github.com:schacon/simplegit.git
    * [new branch] serverfix -> serverfix
```

这里其实走了一点捷径。Git 自动把 `serverfix` 分支名扩展为 `refs/heads/serverfix:refs/heads/serverfix`，意为“取出我在本地的 serverfix 分支，推送到远程仓库的 serverfix 分支中去”。我们将在第九章进一步介绍 `refs/heads/` 部分的细节，不过一般使用的时候都可以省略它。也可以运行 `git push origin serverfix:serverfix` 来实现相同的效果，它的意思是“上传我本地的 serverfix 分支到远程仓库中去，仍旧称它为 serverfix 分支”。通过此语法，你可以把本地分支推送到某个命名不同的远程分支：若想把远程分支叫作 `awesomebranch`，可以用 `git push origin serverfix:awesomebranch` 来推送数据。

接下来，当你的协作者再次从服务器上获取数据时，他们将得到一个新的远程分支 `origin/serverfix`，并指向服务器上 `serverfix` 所指向的版本：

```
$ git fetch origin
    remote: Counting objects: 20, done.
    remote: Compressing objects: 100% (14/14), done.
    remote: Total 15 (delta 5), reused 0 (delta 0)
    Unpacking objects: 100% (15/15), done.
    From git@github.com:schacon/simplegit
    * [new branch] serverfix -> origin/serverfix
```

值得注意的是，在 `fetch` 操作下载好新的远程分支之后，你仍然无法在本地编辑该远程仓库中的分支。换句话说，在本例中，你不会有一个新的 `serverfix` 分支，有的只是一个你无法移动的 `origin/serverfix` 指针。

**如果要把该远程分支的内容合并到当前分支，可以运行** `git merge origin/serverfix`。如果想要一份自己的 `serverfix` 来开发，可以在远程分支的基础上分化出一个新的分支来：

```
$ git checkout -b serverfix origin/serverfix
    Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
    Switched to a new branch "serverfix"
```

这会切换到新建的 `serverfix` 本地分支，其内容同远程分支 `origin/serverfix` 一致，这样你就可以在里面继续开发了。

### 跟踪远程分支

从远程分支 `checkout` 出来的本地分支，称为 *跟踪分支* (tracking branch)。跟踪分支是一种和某个远程分支有直接联系的本地分支。**在跟踪分支里输入 `git push`，Git 会自行推断应该向哪个服务器的哪个分支推送数据。同样，在这些分支里运行 `git pull` 会获取所有远程索引，并把它们的数据都合并到本地分支中来**。

在克隆仓库时，Git 通常会自动创建一个名为 `master` 的分支来跟踪 `origin/master`。这正是 `git push` 和 `git pull` 一开始就能正常工作的原因。当然，你可以随心所欲地设定为其它跟踪分支，比如 `origin` 上除了 `master` 之外的其它分支。刚才我们已经看到了这样的一个例子：`git checkout -b [分支名] [远程名]/[分支名]`。如果你有 1.6.2 以上版本的 Git，还可以用 `--track` 选项简化：

```
$ git checkout --track origin/serverfix
    Branch serverfix set up to track remote branch refs/remotes/origin/serverfix.
    Switched to a new branch "serverfix"
```

要为本地分支设定不同于远程分支的名字，只需在第一个版本的命令里换个名字：

```
$ git checkout -b sf origin/serverfix
    Branch sf set up to track remote branch refs/remotes/origin/serverfix.
    Switched to a new branch "sf"
```

现在你的本地分支 `sf` 会自动将推送和抓取数据的位置定位到 `origin/serverfix` 了。

### 删除远程分支

如果不再需要某个远程分支了，比如搞定了某个特性并把它合并进了远程的 `master` 分支（或任何其他存放稳定代码的分支），可以用这个非常无厘头的语法来删除它：`git push [远程名] :[分支名]`。如果想在服务器上删除 `serverfix` 分支，运行下面的命令：

```
$ git push origin :serverfix
    To git@github.com:schacon/simplegit.git
    - [deleted] serverfix
```

咚！服务器上的分支没了。你最好特别留心这一页，因为你一定会用到那个命令，而且你很可能会忘掉它的语法。有种方便记忆这条命令的方法：记住我们不久前见过的 `git push [远程名] [本地分支]:[远程分支]` 语法，如果省略 `[本地分支]`，那就等于是在说“在这里提取空白然后把它变成`[远程分支]`”。

## [3.6 分支的衍合](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.6-%E5%88%86%E6%94%AF%E7%9A%84%E8%A1%8D%E5%90%88)

把一个分支中的修改整合到另一个分支的办法有两种：`merge` 和 `rebase`（译注：`rebase` 的翻译暂定为“衍合”，大家知道就可以了。）。在本章我们会学习什么是衍合，如何使用衍合，为什么衍合操作如此富有魅力，以及我们应该在什么情况下使用衍合。

### 基本的衍合操作

请回顾之前有关合并的一节（见图 3-27），你会看到开发进程分叉到两个不同分支，又各自提交了更新。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/defaadc9bb6e4f8ba162d46f9761fdaf~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-27. 最初分叉的提交历史。

之前介绍过，最容易的整合分支的方法是 `merge` 命令，它会把两个分支最新的快照（C3 和 C4）以及二者最新的共同祖先（C2）进行三方合并，合并的结果是产生一个新的提交对象（C5）。如图 3-28 所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/532292e8f48d413fb530736e355918bb~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-28. 通过合并一个分支来整合分叉了的历史。

其实，还有另外一个选择：你可以把在 C3 里产生的变化补丁在 C4 的基础上重新打一遍。在 Git 里，这种操作叫做*衍合（rebase）* 。有了 `rebase` 命令，就可以把在一个分支里提交的改变移到另一个分支里重放一遍。

在上面这个例子中，运行：

```
$ git checkout experiment
    $ git rebase master
    First, rewinding head to replay your work on top of it...
    Applying: added staged command
```

它的原理是回到两个分支最近的共同祖先，根据当前分支（也就是要进行衍合的分支 `experiment`）后续的历次提交对象（这里只有一个 C3），生成一系列文件补丁，然后以基底分支（也就是主干分支 `master`）最后一个提交对象（C4）为新的出发点，逐个应用之前准备好的补丁文件，最后会生成一个新的合并提交对象（C3'），从而改写 `experiment` 的提交历史，使它成为 `master` 分支的直接下游，如图 3-29 所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/74c948957526447095d6047ca57bd618~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-29. 把 C3 里产生的改变到 C4 上重演一遍。

现在回到 `master` 分支，进行一次快进合并（见图 3-30）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f29b9b3daabd4a4caf30f2c843c3bd4e~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-30. master 分支的快进。

现在的 C3' 对应的快照，其实和普通的三方合并，即上个例子中的 C5 对应的快照内容一模一样了。**虽然最后整合得到的结果没有任何区别，但衍合能产生一个更为整洁的提交历史。如果视察一个衍合过的分支的历史记录，看起来会更清楚：仿佛所有修改都是在一根线上先后进行的，尽管实际上它们原本是同时并行发生的**。

一般我们使用衍合的目的，是想要得到一个能在远程分支上干净应用的补丁 — **比如某些项目你不是维护者，但想帮点忙的话，最好用衍合：先在自己的一个分支里进行开发，当准备向主项目提交补丁的时候，根据最新的 `origin/master` 进行一次衍合操作然后再提交，这样维护者就不需要做任何整合工作（译注：实际上是把解决分支补丁同最新主干代码之间冲突的责任，化转为由提交补丁的人来解决**。），只需根据你提供的仓库地址作一次快进合并，或者直接采纳你提交的补丁。

请注意，合并结果中最后一次提交所指向的快照，无论是通过衍合，还是三方合并，都会得到相同的快照内容，**只不过提交历史不同罢了。衍合是按照每行的修改次序重演一遍修改，而合并是把最终结果合在一起**。

### 有趣的衍合

衍合也可以放到其他分支进行，并不一定非得根据分化之前的分支。以图 3-31 的历史为例，我们为了给服务器端代码添加一些功能而创建了特性分支 `server`，然后提交 C3 和 C4。然后又从 C3 的地方再增加一个 `client` 分支来对客户端代码进行一些相应修改，所以提交了 C8 和 C9。最后，又回到 `server` 分支提交了 C10。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a0e4766bb9b342fe88f6cc639b039300~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-31. 从一个特性分支里再分出一个特性分支的历史。

假设在接下来的一次软件发布中，我们决定先把客户端的修改并到主线中，而暂缓并入服务端软件的修改（因为还需要进一步测试）。这个时候，我们就可以把基于 `server` 分支而非 `master` 分支的改变（即 C8 和 C9），跳过 `server` 直接放到 `master` 分支中重演一遍，但这需要用 `git rebase` 的 `--onto` 选项指定新的基底分支 `master`：

```
$ git rebase --onto master server client
```

这好比在说：“取出 `client` 分支，找出 `client` 分支和 `server` 分支的共同祖先之后的变化，然后把它们在 `master` 上重演一遍”。是不是有点复杂？不过它的结果如图 3-32 所示，非常酷（译注：虽然 `client` 里的 C8, C9 在 C3 之后，但这仅表明时间上的先后，而非在 C3 修改的基础上进一步改动，因为 `server` 和 `client` 这两个分支对应的代码应该是两套文件，虽然这么说不是很严格，但应理解为在 C3 时间点之后，对另外的文件所做的 C8，C9 修改，放到主干重演。）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/77d57692234e4d33a794660523fda8f1~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-32. 将特性分支上的另一个特性分支衍合到其他分支。

现在可以快进 `master` 分支了（见图 3-33）：

```
$ git checkout master
    $ git merge client
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d1640641e8ec4b88a7a83ade5f6636de~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-33. 快进 master 分支，使之包含 client 分支的变化。

现在我们决定把 `server` 分支的变化也包含进来。我们可以直接把 `server` 分支衍合到 `master`，而不用手工切换到 `server` 分支后再执行衍合操作 — `git rebase [主分支] [特性分支]` 命令会先取出特性分支 `server`，然后在主分支 `master` 上重演：

```
$ git rebase master server
```

于是，`server` 的进度应用到 `master` 的基础上，如图 3-34 所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/0a0af595b6bc4569b5d8956264fe9884~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-34. 在 master 分支上衍合 server 分支。

然后就可以快进主干分支 `master` 了：

```
$ git checkout master
    $ git merge server
```

现在 `client` 和 `server` 分支的变化都已经集成到主干分支来了，可以删掉它们了。最终我们的提交历史会变成图 3-35 的样子：

```
$ git branch -d client
    $ git branch -d server
```

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/964affa70203425f90b3f327ef1d155d~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-35. 最终的提交历史

### 衍合的风险

呃，奇妙的衍合也并非完美无缺，要用它得遵守一条准则：

**一旦分支中的提交对象发布到公共仓库，就千万不要对该分支进行衍合操作。**

如果你遵循这条金科玉律，就不会出差错。否则，人民群众会仇恨你，你的朋友和家人也会嘲笑你，唾弃你。

在进行衍合的时候，实际上抛弃了一些现存的提交对象而创造了一些类似但不同的新的提交对象。如果你把原来分支中的提交对象发布出去，并且其他人更新下载后在其基础上开展工作，而稍后你又用 `git rebase` 抛弃这些提交对象，把新的重演后的提交对象发布出去的话，你的合作者就不得不重新合并他们的工作，这样当你再次从他们那里获取内容时，提交历史就会变得一团糟。

下面我们用一个实际例子来说明为什么公开的衍合会带来问题。假设你从一个中央服务器克隆然后在它的基础上搞了一些开发，提交历史类似图 3-36 所示：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/03f0a2cfbdc6482e89967bf5d46872a6~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-36. 克隆一个仓库，在其基础上工作一番。

现在，某人在 C1 的基础上做了些改变，并合并他自己的分支得到结果 C6，推送到中央服务器。当你抓取并合并这些数据到你本地的开发分支中后，会得到合并结果 C7，历史提交会变成图 3-37 这样：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d92be1c8bb264b919f8ec3d1826cba99~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-37. 抓取他人提交，并入自己主干。

接下来，那个推送 C6 上来的人决定用衍合取代之前的合并操作；继而又用 `git push --force` 覆盖了服务器上的历史，得到 C4'。而之后当你再从服务器上下载最新提交后，会得到：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/562bcf565ab54fbe8e703a081ef8dc65~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-38. 有人推送了衍合后得到的 C4'，丢弃了你作为开发基础的 C4 和 C6。

下载更新后需要合并，但此时衍合产生的提交对象 C4' 的 SHA-1 校验值和之前 C4 完全不同，所以 Git 会把它们当作新的提交对象处理，而实际上此刻你的提交历史 C7 中早已经包含了 C4 的修改内容，于是合并操作会把 C7 和 C4' 合并为 C8（见图 3-39）:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/059f6e43d8d7446f84423fac2c6a06a1~tplv-k3u1fbpfcp-zoom-1.image)\
图 3-39. 你把相同的内容又合并了一遍，生成一个新的提交 C8。

C8 这一步的合并是迟早会发生的，因为只有这样你才能和其他协作者提交的内容保持同步。而在 C8 之后，你的提交历史里就会同时包含 C4 和 C4'，两者有着不同的 SHA-1 校验值，如果用 `git log` 查看历史，会看到两个提交拥有相同的作者日期与说明，令人费解。而更糟的是，当你把这样的历史推送到服务器后，会再次把这些衍合后的提交引入到中央服务器，进一步困扰其他人（译注：这个例子中，出问题的责任方是那个发布了 C6 后又用衍合发布 C4' 的人，其他人会因此反馈双重历史到共享主干，从而混淆大家的视听。）。

如果把衍合当成一种在推送之前清理提交历史的手段，而且仅仅衍合那些尚未公开的提交对象，就没问题。如果衍合那些已经公开的提交对象，并且已经有人基于这些提交对象开展了后续开发工作的话，就会出现叫人沮丧的麻烦。

## [3.7 小结](https://git.oschina.net/progit/3-Git-%E5%88%86%E6%94%AF.html#3.7-%E5%B0%8F%E7%BB%93)

读到这里，你应该已经学会了如何创建分支并切换到新分支，在不同分支间转换，合并本地分支，把分支推送到共享服务器上，使用共享分支与他人协作，以及在分享之前进行衍合。

# 4 服务器上的 Git

1.  [4.1 协议](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.1-%E5%8D%8F%E8%AE%AE)
1.  [4.2 在服务器上部署 Git](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.2-%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2-Git)
1.  [4.3 生成 SSH 公钥](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.3-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)
1.  [4.4 架设服务器](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.4-%E6%9E%B6%E8%AE%BE%E6%9C%8D%E5%8A%A1%E5%99%A8)
1.  [4.5 公共访问](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.5-%E5%85%AC%E5%85%B1%E8%AE%BF%E9%97%AE)
1.  [4.6 GitWeb](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.6-GitWeb)
1.  [4.7 Gitosis](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.7-Gitosis)
1.  [4.8 Gitolite](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.8-Gitolite)
1.  [4.9 Git 守护进程](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.9-Git-%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)
1.  [4.10 Git 托管服务](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.10-Git-%E6%89%98%E7%AE%A1%E6%9C%8D%E5%8A%A1)
1.  [4.11 小结](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.11-%E5%B0%8F%E7%BB%93)

到目前为止，你应该已经学会了使用 Git 来完成日常工作。然而，如果想与他人合作，还需要一个远程的 Git 仓库。尽管技术上可以从个人的仓库里推送和拉取修改内容，但我们不鼓励这样做，因为一不留心就很容易弄混其他人的进度。另外，你也一定希望合作者们即使在自己不开机的时候也能从仓库获取数据 — 拥有一个更稳定的公共仓库十分有用。因此，更好的合作方式是建立一个大家都可以访问的共享仓库，从那里推送和拉取数据。我们将把这个仓库称为 "Git 服务器"；代理一个 Git 仓库只需要花费很少的资源，几乎从不需要整个服务器来支持它的运行。

架设一台 Git 服务器并不难。第一步是选择与服务器通讯的协议。本章第一节将介绍可用的协议以及各自优缺点。下面一节将介绍一些针对各个协议典型的设置以及如何在服务器上实施。最后，如果你不介意在他人服务器上保存你的代码，又想免去自己架设和维护服务器的麻烦，倒可以试试我们介绍的几个仓库托管服务。

如果你对架设自己的服务器没兴趣，可以跳到本章最后一节去看看如何申请一个代码托管服务的账户然后继续下一章，我们会在那里讨论分布式源码控制环境的林林总总。

远程仓库通常只是一个*裸仓库（bare repository）*  — 即一个没有当前工作目录的仓库。因为该仓库只是一个合作媒介，所以不需要从硬盘上取出最新版本的快照；仓库里存放的仅仅是 Git 的数据。简单地说，裸仓库就是你工作目录中 `.git` 子目录内的内容。

## [4.1 协议](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.1-%E5%8D%8F%E8%AE%AE)

Git 可以使用四种主要的协议来传输数据：本地传输，SSH 协议，Git 协议和 HTTP 协议。下面分别介绍一下哪些情形应该使用（或避免使用）这些协议。

值得注意的是，除了 HTTP 协议外，其他所有协议都要求在服务器端安装并运行 Git。

### 本地协议

最基本的就是*本地协议（Local protocol）* ，所谓的远程仓库在该协议中的表示，就是硬盘上的另一个目录。这常见于团队每一个成员都对一个共享的文件系统（例如 NFS）拥有访问权，或者比较少见的多人共用同一台电脑的情况。后面一种情况并不安全，因为所有代码仓库实例都储存在同一台电脑里，增加了灾难性数据损失的可能性。

如果你使用一个共享的文件系统，就可以在一个本地文件系统中克隆仓库，推送和获取。克隆的时候只需要将远程仓库的路径作为 URL 使用，比如下面这样：

```
$ git clone /opt/git/project.git
```

或者这样：

```
$ git clone file:///opt/git/project.git
```

如果在 URL 开头明确使用 `file://` ，那么 Git 会以一种略微不同的方式运行。如果你只给出路径，Git 会尝试使用硬链接或直接复制它所需要的文件。如果使用了 `file://` ，Git 会调用它平时通过网络来传输数据的工序，而这种方式的效率相对较低。使用 `file://` 前缀的主要原因是当你需要一个不包含无关引用或对象的干净仓库副本的时候 — 一般指从其他版本控制系统导入的，或类似情形（参见第 9 章的维护任务）。我们这里仅仅使用普通路径，这样更快。

要添加一个本地仓库作为现有 Git 项目的远程仓库，可以这样做：

```
$ git remote add local_proj /opt/git/project.git
```

然后就可以像在网络上一样向这个远程仓库推送和获取数据了。

#### 优点

基于文件仓库的优点在于它的简单，同时保留了现存文件的权限和网络访问权限。如果你的团队已经有一个全体共享的文件系统，建立仓库就十分容易了。你只需把一份裸仓库的副本放在大家都能访问的地方，然后像对其他共享目录一样设置读写权限就可以了。我们将在下一节“在服务器上部署 Git ”中讨论如何导出一个裸仓库的副本。

这也是从别人工作目录中获取工作成果的快捷方法。假如你和你的同事在一个项目中合作，他们想让你检出一些东西的时候，运行类似 `git pull /home/john/project` 通常会比他们推送到服务器，而你再从服务器获取简单得多。

#### 缺点

这种方法的缺点是，与基本的网络连接访问相比，难以控制从不同位置来的访问权限。如果你想从家里的笔记本电脑上推送，就要先挂载远程硬盘，这和基于网络连接的访问相比更加困难和缓慢。

另一个很重要的问题是该方法不一定就是最快的，尤其是对于共享挂载的文件系统。本地仓库只有在你对数据访问速度快的时候才快。在同一个服务器上，如果二者同时允许 Git 访问本地硬盘，通过 NFS 访问仓库通常会比 SSH 慢。

### SSH 协议

Git 使用的传输协议中最常见的可能就是 SSH 了。这是因为大多数环境已经支持通过 SSH 对服务器的访问 — 即便还没有，架设起来也很容易。SSH 也是唯一一个同时支持读写操作的网络协议。另外两个网络协议（HTTP 和 Git）通常都是只读的，所以虽然二者对大多数人都可用，但执行写操作时还是需要 SSH。SSH 同时也是一个验证授权的网络协议；而因为其普遍性，一般架设和使用都很容易。

通过 SSH 克隆一个 Git 仓库，你可以像下面这样给出 ssh:// 的 URL：

```
$ git clone ssh://user@server/project.git
```

或者不指明某个协议 — 这时 Git 会默认使用 SSH ：

```
$ git clone user@server:project.git
```

如果不指明用户，Git 会默认使用当前登录的用户名连接服务器。

#### 优点

使用 SSH 的好处有很多。首先，如果你想拥有对网络仓库的写权限，基本上不可能不使用 SSH。其次，SSH 架设相对比较简单 — SSH 守护进程很常见，很多网络管理员都有一些使用经验，而且很多操作系统都自带了它或者相关的管理工具。再次，通过 SSH 进行访问是安全的 — 所有数据传输都是加密和授权的。最后，和 Git 及本地协议一样，SSH 也很高效，会在传输之前尽可能压缩数据。

#### 缺点

SSH 的限制在于你不能通过它实现仓库的匿名访问。即使仅为读取数据，人们也必须在能通过 SSH 访问主机的前提下才能访问仓库，这使得 SSH 不利于开源的项目。如果你仅仅在公司网络里使用，SSH 可能是你唯一需要使用的协议。如果想允许对项目的匿名只读访问，那么除了为自己推送而架设 SSH 协议之外，还需要支持其他协议以便他人访问读取。

### Git 协议

接下来是 Git 协议。这是一个包含在 Git 软件包中的特殊守护进程； 它会监听一个提供类似于 SSH 服务的特定端口（9418），而无需任何授权。打算支持 Git 协议的仓库，需要先创建 `git-export-daemon-ok` 文件 — 它是协议进程提供仓库服务的必要条件 — 但除此之外该服务没有什么安全措施。要么所有人都能克隆 Git 仓库，要么谁也不能。这也意味着该协议通常不能用来进行推送。你可以允许推送操作；然而由于没有授权机制，一旦允许该操作，网络上任何一个知道项目 URL 的人将都有推送权限。不用说，这是十分罕见的情况。

#### 优点

Git 协议是现存最快的传输协议。如果你在提供一个有很大访问量的公共项目，或者一个不需要对读操作进行授权的庞大项目，架设一个 Git 守护进程来供应仓库是个不错的选择。它使用与 SSH 协议相同的数据传输机制，但省去了加密和授权的开销。

#### 缺点

Git 协议消极的一面是缺少授权机制。用 Git 协议作为访问项目的唯一方法通常是不可取的。一般的做法是，同时提供 SSH 接口，让几个开发者拥有推送（写）权限，其他人通过 `git://` 拥有只读权限。Git 协议可能也是最难架设的协议。它要求有单独的守护进程，需要定制 — 我们将在本章的 “Gitosis” 一节详细介绍它的架设 — 需要设定 `xinetd` 或类似的程序，而这些工作就没那么轻松了。该协议还要求防火墙开放 9418 端口，而企业级防火墙一般不允许对这个非标准端口的访问。大型企业级防火墙通常会封锁这个少见的端口。

### HTTP/S 协议

最后还有 HTTP 协议。HTTP 或 HTTPS 协议的优美之处在于架设的简便性。基本上，只需要把 Git 的裸仓库文件放在 HTTP 的根目录下，配置一个特定的 `post-update` 挂钩（hook）就可以搞定（Git 挂钩的细节见第 7 章）。此后，每个能访问 Git 仓库所在服务器上 web 服务的人都可以进行克隆操作。下面的操作可以允许通过 HTTP 对仓库进行读取：

```
$ cd /var/www/htdocs/
    $ git clone --bare /path/to/git_project gitproject.git
    $ cd gitproject.git
    $ mv hooks/post-update.sample hooks/post-update
    $ chmod a+x hooks/post-update
```

这样就可以了。Git 附带的 `post-update` 挂钩会默认运行合适的命令（`git update-server-info`）来确保通过 HTTP 的获取和克隆正常工作。这条命令在你用 SSH 向仓库推送内容时运行；之后，其他人就可以用下面的命令来克隆仓库：

```
$ git clone http://example.com/gitproject.git
```

在本例中，我们使用了 Apache 设定中常用的 `/var/www/htdocs` 路径，不过你可以使用任何静态 web 服务 — 把裸仓库放在它的目录里就行。 Git 的数据是以最基本的静态文件的形式提供的（关于如何提供文件的详情见第 9 章）。

通过 HTTP 进行推送操作也是可能的，不过这种做法不太常见，并且牵扯到复杂的 WebDAV 设定。由于很少用到，本书将略过对该内容的讨论。如果对 HTTP 推送协议感兴趣，不妨打开这个地址看一下操作方法：`http://www.kernel.org/pub/software/scm/git/docs/howto/setup-git-server-over-http.txt` 。通过 HTTP 推送的好处之一是你可以使用任何 WebDAV 服务器，不需要为 Git 设定特殊环境；所以如果主机提供商支持通过 WebDAV 更新网站内容，你也可以使用这项功能。

#### 优点

使用 HTTP 协议的好处是易于架设。几条必要的命令就可以让全世界读取到仓库的内容。花费不过几分钟。HTTP 协议不会占用过多服务器资源。因为它一般只用到静态的 HTTP 服务提供所有数据，普通的 Apache 服务器平均每秒能支撑数千个文件的并发访问 — 哪怕让一个小型服务器超载都很难。

你也可以通过 HTTPS 提供只读的仓库，这意味着你可以加密传输内容；你甚至可以要求客户端使用特定签名的 SSL 证书。一般情况下，如果到了这一步，使用 SSH 公共密钥可能是更简单的方案；不过也存在一些特殊情况，这时通过 HTTPS 使用带签名的 SSL 证书或者其他基于 HTTP 的只读连接授权方式是更好的解决方案。

HTTP 还有个额外的好处：HTTP 是一个如此常见的协议，以至于企业级防火墙通常都允许其端口的通信。

#### 缺点

HTTP 协议的消极面在于，相对来说客户端效率更低。克隆或者下载仓库内容可能会花费更多时间，而且 HTTP 传输的体积和网络开销比其他任何一个协议都大。因为它没有按需供应的能力 — 传输过程中没有服务端的动态计算 — 因而 HTTP 协议经常会被称为*傻瓜（dumb）* 协议。更多 HTTP 协议和其他协议效率上的差异见第 9 章。

## [4.2 在服务器上部署 Git](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.2-%E5%9C%A8%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2-Git)

开始架设 Git 服务器前，需要先把现有仓库导出为裸仓库 — 即一个不包含当前工作目录的仓库。做法直截了当，克隆时用 `--bare` 选项即可。裸仓库的目录名一般以 `.git` 结尾，像这样：

```
$ git clone --bare my_project my_project.git
    Initialized empty Git repository in /opt/projects/my_project.git/
```

该命令的输出或许会让人有些不解。其实 `clone` 操作基本上相当于 `git init` 加 `git fetch`，所以这里出现的其实是 `git init` 的输出，先由它建立一个空目录，而之后传输数据对象的操作并无任何输出，只是悄悄在幕后执行。现在 `my_project.git` 目录中已经有了一份 Git 目录数据的副本。

整体上的效果大致相当于：

```
$ cp -Rf my_project/.git my_project.git
```

但在配置文件中有若干小改动，不过对用户来讲，使用方式都一样，不会有什么影响。它仅取出 Git 仓库的必要原始数据，存放在该目录中，而不会另外创建工作目录。

### 把裸仓库移到服务器上

有了裸仓库的副本后，剩下的就是把它放到服务器上并设定相关协议。假设一个域名为 `git.example.com` 的服务器已经架设好，并可以通过 SSH 访问，我们打算把所有 Git 仓库储存在 `/opt/git` 目录下。只要把裸仓库复制过去：

```
$ scp -r my_project.git user@git.example.com:/opt/git
```

现在，所有对该服务器有 SSH 访问权限，并可读取 `/opt/git` 目录的用户都可以用下面的命令克隆该项目：

```
$ git clone user@git.example.com:/opt/git/my_project.git
```

如果某个 SSH 用户对 `/opt/git/my_project.git` 目录有写权限，那他就有推送权限。如果到该项目目录中运行 `git init` 命令，并加上 `--shared` 选项，那么 Git 会自动修改该仓库目录的组权限为可写（译注：实际上 `--shared` 可以指定其他行为，只是默认为将组权限改为可写并执行 `g+sx`，所以最后会得到 `rws`。）。

```
$ ssh user@git.example.com
    $ cd /opt/git/my_project.git
    $ git init --bare --shared
```

由此可见，根据现有的 Git 仓库创建一个裸仓库，然后把它放上你和同事都有 SSH 访问权的服务器是多么容易。现在已经可以开始在同一项目上密切合作了。

值得注意的是，这的的确确是架设一个少数人具有连接权的 Git 服务的全部 — 只要在服务器上加入可以用 SSH 登录的帐号，然后把裸仓库放在大家都有读写权限的地方。一切都准备停当，无需更多。

下面的几节中，你会了解如何扩展到更复杂的设定。这些内容包含如何避免为每一个用户建立一个账户，给仓库添加公共读取权限，架设网页界面，使用 Gitosis 工具等等。然而，只是和几个人在一个不公开的项目上合作的话，仅仅是一个 SSH 服务器和裸仓库就足够了，记住这点就可以了。

### 小型安装

如果设备较少或者你只想在小型开发团队里尝试 Git ，那么一切都很简单。架设 Git 服务最复杂的地方在于账户管理。如果需要仓库对特定的用户可读，而给另一部分用户读写权限，那么访问和许可的安排就比较困难。

#### SSH 连接

如果已经有了一个所有开发成员都可以用 SSH 访问的服务器，架设第一个服务器将变得异常简单，几乎什么都不用做（正如上节中介绍的那样）。如果需要对仓库进行更复杂的访问控制，只要使用服务器操作系统的本地文件访问许可机制就行了。

如果需要团队里的每个人都对仓库有写权限，又不能给每个人在服务器上建立账户，那么提供 SSH 连接就是唯一的选择了。我们假设用来共享仓库的服务器已经安装了 SSH 服务，而且你通过它访问服务器。

有好几个办法可以让团队的每个人都有访问权。第一个办法是给每个人建立一个账户，直截了当但略过繁琐。反复运行 `adduser` 并给所有人设定临时密码可不是好玩的。

第二个办法是在主机上建立一个 `git` 账户，让每个需要写权限的人发送一个 SSH 公钥，然后将其加入 `git` 账户的 `~/.ssh/authorized_keys` 文件。这样一来，所有人都将通过 `git` 账户访问主机。这丝毫不会影响提交的数据 — 访问主机用的身份不会影响提交对象的提交者信息。

另一个办法是让 SSH 服务器通过某个 LDAP 服务，或者其他已经设定好的集中授权机制，来进行授权。只要每个人都能获得主机的 shell 访问权，任何可用的 SSH 授权机制都能达到相同效果。

## [4.3 生成 SSH 公钥](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.3-%E7%94%9F%E6%88%90-SSH-%E5%85%AC%E9%92%A5)

大多数 Git 服务器都会选择使用 SSH 公钥来进行授权。系统中的每个用户都必须提供一个公钥用于授权，没有的话就要生成一个。生成公钥的过程在所有操作系统上都差不多。首先先确认一下是否已经有一个公钥了。SSH 公钥默认储存在账户的主目录下的 `~/.ssh` 目录。进去看看：

```
$ cd ~/.ssh
    $ ls
    authorized_keys2 id_dsa known_hosts
    config id_dsa.pub
```

关键是看有没有用 `something` 和 `something.pub` 来命名的一对文件，这个 `something` 通常就是 `id_dsa` 或 `id_rsa`。有 `.pub` 后缀的文件就是公钥，另一个文件则是密钥。假如没有这些文件，或者干脆连 `.ssh` 目录都没有，可以用 `ssh-keygen` 来创建。该程序在 Linux/Mac 系统上由 SSH 包提供，而在 Windows 上则包含在 MSysGit 包里：

```
$ ssh-keygen
    Generating public/private rsa key pair.
    Enter file in which to save the key (/Users/schacon/.ssh/id_rsa):
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /Users/schacon/.ssh/id_rsa.
    Your public key has been saved in /Users/schacon/.ssh/id_rsa.pub.
    The key fingerprint is:
    43:c5:5b:5f:b1:f1:50:43:ad:20:a6:92:6a:1f:9a:3a schacon@agadorlaptop.local
```

它先要求你确认保存公钥的位置（`.ssh/id_rsa`），然后它会让你重复一个密码两次，如果不想在使用公钥的时候输入密码，可以留空。

现在，所有做过这一步的用户都得把它们的公钥给你或者 Git 服务器的管理员（假设 SSH 服务被设定为使用公钥机制）。他们只需要复制 `.pub` 文件的内容然后发邮件给管理员。公钥的样子大致如下：

```
$ cat ~/.ssh/id_rsa.pub
    ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEAklOUpkDHrfHY17SbrmTIpNLTGK9Tjom/BWDSU
    GPl+nafzlHDTYW7hdI4yZ5ew18JH4JW9jbhUFrviQzM7xlELEVf4h9lFX5QVkbPppSwg0cda3
    Pbv7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8V6RjsNAQwdsdMFvSlVK/7XA
    t3FaoJoAsncM1Q9x5+3V0Ww68/eIFmb1zuUFljQJKprrX88XypNDvjYNby6vw/Pb0rwert/En
    mZ+AW4OZPnTPI89ZPmVMLuayrD2cE86Z/il8b+gw3r3+1nKatmIkjn2so1d01QraTlMqVSsbx
    NrRFi9wrf+M7Q== schacon@agadorlaptop.local
```

关于在多个操作系统上设立相同 SSH 公钥的教程，可以查阅 GitHub 上有关 SSH 公钥的向导：`http://github.com/guides/providing-your-ssh-key`。

## [4.4 架设服务器](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.4-%E6%9E%B6%E8%AE%BE%E6%9C%8D%E5%8A%A1%E5%99%A8)

现在我们过一边服务器端架设 SSH 访问的流程。本例将使用 `authorized_keys` 方法来给用户授权。我们还将假定使用类似 Ubuntu 这样的标准 Linux 发行版。首先，创建一个名为 'git' 的用户，并为其创建一个 `.ssh` 目录。

```
$ sudo adduser git
    $ su git
    $ cd
    $ mkdir .ssh
```

接下来，把开发者的 SSH 公钥添加到这个用户的 `authorized_keys` 文件中。假设你通过电邮收到了几个公钥并存到了临时文件里。重复一下，公钥大致看起来是这个样子：

```
$ cat /tmp/id_rsa.john.pub
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCB007n/ww+ouN4gSLKssMxXnBOvf9LGt4L
    ojG6rs6hPB09j9R/T17/x4lhJA0F3FR1rP6kYBRsWj2aThGw6HXLm9/5zytK6Ztg3RPKK+4k
    Yjh6541NYsnEAZuXz0jTTyAUfrtU3Z5E003C4oxOj6H0rfIF1kKI9MAQLMdpGW1GYEIgS9Ez
    Sdfd8AcCIicTDWbqLAcU4UpkaX8KyGlLwsNuuGztobF8m72ALC/nLF6JLtPofwFBlgc+myiv
    O7TCUSBdLQlgMVOFq1I2uPWQOkOWQAHukEOmfjy2jctxSDBQ220ymjaNsHT4kgtZg2AYYgPq
    dAv8JggJICUvax2T9va5 gsg-keypair
```

只要把它们逐个追加到 `authorized_keys` 文件尾部即可：

```
$ cat /tmp/id_rsa.john.pub >> ~/.ssh/authorized_keys
    $ cat /tmp/id_rsa.josie.pub >> ~/.ssh/authorized_keys
    $ cat /tmp/id_rsa.jessica.pub >> ~/.ssh/authorized_keys
```

现在可以用 `--bare` 选项运行 `git init` 来建立一个裸仓库，这会初始化一个不包含工作目录的仓库。

```
$ cd /opt/git
    $ mkdir project.git
    $ cd project.git
    $ git --bare init
```

这时，Join，Josie 或者 Jessica 就可以把它加为远程仓库，推送一个分支，从而把第一个版本的项目文件上传到仓库里了。值得注意的是，每次添加一个新项目都需要通过 shell 登入主机并创建一个裸仓库目录。我们不妨以 `gitserver` 作为 `git` 用户及项目仓库所在的主机名。如果在网络内部运行该主机，并在 DNS 中设定 `gitserver` 指向该主机，那么以下这些命令都是可用的：

```
# 在 John 的电脑上
    $ cd myproject
    $ git init
    $ git add .
    $ git commit -m 'initial commit'
    $ git remote add origin git@gitserver:/opt/git/project.git
    $ git push origin master
```

这样，其他人的克隆和推送也一样变得很简单：

```
$ git clone git@gitserver:/opt/git/project.git
    $ vim README
    $ git commit -am 'fix for the README file'
    $ git push origin master
```

用这个方法可以很快捷地为少数几个开发者架设一个可读写的 Git 服务。

作为一个额外的防范措施，你可以用 Git 自带的 `git-shell` 工具限制 `git` 用户的活动范围。只要把它设为 `git` 用户登入的 shell，那么该用户就无法使用普通的 bash 或者 csh 什么的 shell 程序。编辑 `/etc/passwd` 文件：

```
$ sudo vim /etc/passwd
```

在文件末尾，你应该能找到类似这样的行：

```
git:x:1000:1000::/home/git:/bin/sh
```

把 `bin/sh` 改为 `/usr/bin/git-shell` （或者用 `which git-shell` 查看它的实际安装路径）。该行修改后的样子如下：

```
git:x:1000:1000::/home/git:/usr/bin/git-shell
```

现在 `git` 用户只能用 SSH 连接来推送和获取 Git 仓库，而不能直接使用主机 shell。尝试普通 SSH 登录的话，会看到下面这样的拒绝信息：

```
$ ssh git@gitserver
    fatal: What do you think I am? A shell?
    Connection to gitserver closed.
```

## [4.5 公共访问](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.5-%E5%85%AC%E5%85%B1%E8%AE%BF%E9%97%AE)

匿名的读取权限该怎么实现呢？也许除了内部私有的项目之外，你还需要托管一些开源项目。或者因为要用一些自动化的服务器来进行编译，或者有一些经常变化的服务器群组，而又不想整天生成新的 SSH 密钥 — 总之，你需要简单的匿名读取权限。

或许对小型的配置来说最简单的办法就是运行一个静态 web 服务，把它的根目录设定为 Git 仓库所在的位置，然后开启本章第一节提到的 `post-update` 挂钩。这里继续使用之前的例子。假设仓库处于 `/opt/git` 目录，主机上运行着 Apache 服务。重申一下，任何 web 服务程序都可以达到相同效果；作为范例，我们将用一些基本的 Apache 设定来展示大体需要的步骤。

首先，开启挂钩：

```
$ cd project.git
    $ mv hooks/post-update.sample hooks/post-update
    $ chmod a+x hooks/post-update
```

如果用的是 Git 1.6 之前的版本，则可以省略 `mv` 命令 — Git 是从较晚的版本才开始在挂钩实例的结尾添加 .sample 后缀名的。

`post-update` 挂钩是做什么的呢？其内容大致如下：

```
$ cat .git/hooks/post-update
    #!/bin/sh
    exec git-update-server-info
```

意思是当通过 SSH 向服务器推送时，Git 将运行这个 `git-update-server-info` 命令来更新匿名 HTTP 访问获取数据时所需要的文件。

接下来，在 Apache 配置文件中添加一个 VirtualHost 条目，把文档根目录设为 Git 项目所在的根目录。这里我们假定 DNS 服务已经配置好，会把对 `.gitserver` 的请求发送到这台主机：

```
<VirtualHost *:80>
    ServerName git.gitserver
    DocumentRoot /opt/git
    <Directory /opt/git/>
    Order allow, deny
    allow from all
    </Directory>
    </VirtualHost>
```

另外，需要把 `/opt/git` 目录的 Unix 用户组设定为 `www-data` ，这样 web 服务才可以读取仓库内容，因为运行 CGI 脚本的 Apache 实例进程默认就是以该用户的身份起来的：

```
$ chgrp -R www-data /opt/git
```

重启 Apache 之后，就可以通过项目的 URL 来克隆该目录下的仓库了。

```
$ git clone http://git.gitserver/project.git
```

这一招可以让你在几分钟内为相当数量的用户架设好基于 HTTP 的读取权限。另一个提供非授权访问的简单方法是开启一个 Git 守护进程，不过这将要求该进程作为后台进程常驻 — 接下来的这一节就要讨论这方面的细节。

## [4.6 GitWeb](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.6-GitWeb)

现在我们的项目已经有了可读可写和只读的连接方式，不过如果能有一个简单的 web 界面访问就更好了。Git 自带一个叫做 GitWeb 的 CGI 脚本，运行效果可以到 `http://git.kernel.org` 这样的站点体验下（见图 4-1）。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b3d7daea185b4ad5962a9dbd46506ad3~tplv-k3u1fbpfcp-zoom-1.image)\
Figure 4-1. 基于网页的 GitWeb 用户界面

如果想看看自己项目的效果，不妨用 Git 自带的一个命令，可以使用类似 `lighttpd` 或 `webrick` 这样轻量级的服务器启动一个临时进程。如果是在 Linux 主机上，通常都预装了 `lighttpd` ，可以到项目目录中键入 `git instaweb` 来启动。如果用的是 Mac ，Leopard 预装了 Ruby，所以 `webrick` 应该是最好的选择。如果要用 lighttpd 以外的程序来启动 `git instaweb`，可以通过 `--httpd` 选项指定：

```
$ git instaweb --httpd=webrick
    [2009-02-21 10:02:21] INFO WEBrick 1.3.1
    [2009-02-21 10:02:21] INFO ruby 1.8.6 (2008-03-03) [universal-darwin9.0]
```

这会在 1234 端口开启一个 HTTPD 服务，随之在浏览器中显示该页，十分简单。关闭服务时，只需在原来的命令后面加上 `--stop` 选项就可以了：

```
$ git instaweb --httpd=webrick --stop
```

如果需要为团队或者某个开源项目长期运行 GitWeb，那么 CGI 脚本就要由正常的网页服务来运行。一些 Linux 发行版可以通过 `apt` 或 `yum` 安装一个叫做 `gitweb` 的软件包，不妨首先尝试一下。我们将快速介绍一下手动安装 GitWeb 的流程。首先，你需要 Git 的源码，其中带有 GitWeb，并能生成定制的 CGI 脚本：

```
$ git clone git://git.kernel.org/pub/scm/git/git.git
    $ cd git/
    $ make GITWEB_PROJECTROOT="/opt/git" \
    prefix=/usr gitweb/gitweb.cgi
    $ sudo cp -Rf gitweb /var/www/
```

注意，通过指定 `GITWEB_PROJECTROOT` 变量告诉编译命令 Git 仓库的位置。然后，设置 Apache 以 CGI 方式运行该脚本，添加一个 VirtualHost 配置：

```
<VirtualHost *:80>
    ServerName gitserver
    DocumentRoot /var/www/gitweb
    <Directory /var/www/gitweb>
    Options ExecCGI +FollowSymLinks +SymLinksIfOwnerMatch
    AllowOverride All
    order allow,deny
    Allow from all
    AddHandler cgi-script cgi
    DirectoryIndex gitweb.cgi
    </Directory>
    </VirtualHost>
```

不难想象，GitWeb 可以使用任何兼容 CGI 的网页服务来运行；如果偏向使用其他 web 服务器，配置也不会很麻烦。现在，通过 `http://gitserver` 就可以在线访问仓库了，在 `http://git.server` 上还可以通过 HTTP 克隆和获取仓库的内容。

## [4.7 Gitosis](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.7-Gitosis)

把所有用户的公钥保存在 `authorized_keys` 文件的做法，只能凑和一阵子，当用户数量达到几百人的规模时，管理起来就会十分痛苦。每次改删用户都必须登录服务器不去说，这种做法还缺少必要的权限管理 — 每个人都对所有项目拥有完整的读写权限。

幸好我们还可以选择应用广泛的 Gitosis 项目。简单地说，Gitosis 就是一套用来管理 `authorized_keys` 文件和实现简单连接限制的脚本。有趣的是，用来添加用户和设定权限的并非通过网页程序，而只是管理一个特殊的 Git 仓库。你只需要在这个特殊仓库内做好相应的设定，然后推送到服务器上，Gitosis 就会随之改变运行策略，听起来就很酷，对吧？

Gitosis 的安装算不上傻瓜化，但也不算太难。用 Linux 服务器架设起来最简单 — 以下例子中，我们使用装有 Ubuntu 8.10 系统的服务器。

Gitosis 的工作依赖于某些 Python 工具，所以首先要安装 Python 的 setuptools 包，在 Ubuntu 上称为 python-setuptools：

```
$ apt-get install python-setuptools
```

接下来，从 Gitosis 项目主页克隆并安装：

```
$ git clone git://eagain.net/gitosis.git
    $ cd gitosis
    $ sudo python setup.py install
```

这会安装几个供 Gitosis 使用的工具。默认 Gitosis 会把 `/home/git` 作为存储所有 Git 仓库的根目录，这没什么不好，不过我们之前已经把项目仓库都放在 `/opt/git` 里面了，所以为方便起见，我们可以做一个符号连接，直接划转过去，而不必重新配置：

```
$ ln -s /opt/git /home/git/repositories
```

Gitosis 将会帮我们管理用户公钥，所以先把当前控制文件改名备份，以便稍后重新添加，准备好让 Gitosis 自动管理 `authorized_keys` 文件：

```
$ mv /home/git/.ssh/authorized_keys /home/git/.ssh/ak.bak
```

接下来，如果之前把 `git` 用户的登录 shell 改为 `git-shell` 命令的话，先恢复 'git' 用户的登录 shell。改过之后，大家仍然无法通过该帐号登录（译注：因为 `authorized_keys` 文件已经没有了。），不过不用担心，这会交给 Gitosis 来实现。所以现在先打开 `/etc/passwd` 文件，把这行：

```
git:x:1000:1000::/home/git:/usr/bin/git-shell
```

改回:

```
git:x:1000:1000::/home/git:/bin/sh
```

好了，现在可以初始化 Gitosis 了。你可以用自己的公钥执行 `gitosis-init` 命令，要是公钥不在服务器上，先临时复制一份：

```
$ sudo -H -u git gitosis-init < /tmp/id_dsa.pub
    Initialized empty Git repository in /opt/git/gitosis-admin.git/
    Reinitialized existing Git repository in /opt/git/gitosis-admin.git/
```

这样该公钥的拥有者就能修改用于配置 Gitosis 的那个特殊 Git 仓库了。接下来，需要手工对该仓库中的 `post-update` 脚本加上可执行权限：

```
$ sudo chmod 755 /opt/git/gitosis-admin.git/hooks/post-update
```

基本上就算是好了。如果设定过程没出什么差错，现在可以试一下用初始化 Gitosis 的公钥的拥有者身份 SSH 登录服务器，应该会看到类似下面这样：

```
$ ssh git@gitserver
    PTY allocation request failed on channel 0
    fatal: unrecognized command 'gitosis-serve schacon@quaternion'
    Connection to gitserver closed.
```

说明 Gitosis 认出了该用户的身份，但由于没有运行任何 Git 命令，所以它切断了连接。那么，现在运行一个实际的 Git 命令 — 克隆 Gitosis 的控制仓库：

```
# 在你本地计算机上
    $ git clone git@gitserver:gitosis-admin.git
```

这会得到一个名为 `gitosis-admin` 的工作目录，主要由两部分组成：

```
$ cd gitosis-admin
    $ find .
    ./gitosis.conf
    ./keydir
    ./keydir/scott.pub
```

`gitosis.conf` 文件是用来设置用户、仓库和权限的控制文件。`keydir` 目录则是保存所有具有访问权限用户公钥的地方— 每人一个。在 `keydir` 里的文件名（比如上面的 `scott.pub`）应该跟你的不一样 — Gitosis 会自动从使用 `gitosis-init` 脚本导入的公钥尾部的描述中获取该名字。

看一下 `gitosis.conf` 文件的内容，它应该只包含与刚刚克隆的 `gitosis-admin` 相关的信息：

```
$ cat gitosis.conf
    [gitosis]

    [group gitosis-admin]
    writable = gitosis-admin
    members = scott
```

它显示用户 `scott` — 初始化 Gitosis 公钥的拥有者 — 是唯一能管理 `gitosis-admin` 项目的人。

现在我们来添加一个新项目。为此我们要建立一个名为 `mobile` 的新段落，在其中罗列手机开发团队的开发者，以及他们拥有写权限的项目。由于 'scott' 是系统中的唯一用户，我们把他设为唯一用户，并允许他读写名为 `iphone_project` 的新项目：

```
[group mobile]
    writable = iphone_project
    members = scott
```

修改完之后，提交 `gitosis-admin` 里的改动，并推送到服务器使其生效：

```
$ git commit -am 'add iphone_project and mobile group'
    [master]: created 8962da8: "changed name"
    1 files changed, 4 insertions(+), 0 deletions(-)
    $ git push
    Counting objects: 5, done.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 272 bytes, done.
    Total 3 (delta 1), reused 0 (delta 0)
    To git@gitserver:/opt/git/gitosis-admin.git
    fb27aec..8962da8 master -> master
```

在新工程 `iphone_project` 里首次推送数据到服务器前，得先设定该服务器地址为远程仓库。但你不用事先到服务器上手工创建该项目的裸仓库— Gitosis 会在第一次遇到推送时自动创建：

```
$ git remote add origin git@gitserver:iphone_project.git
    $ git push origin master
    Initialized empty Git repository in /opt/git/iphone_project.git/
    Counting objects: 3, done.
    Writing objects: 100% (3/3), 230 bytes, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To git@gitserver:iphone_project.git
    * [new branch] master -> master
```

请注意，这里不用指明完整路径（实际上，如果加上反而没用），只需要一个冒号加项目名字即可 — Gitosis 会自动帮你映射到实际位置。

要和朋友们在一个项目上协同工作，就得重新添加他们的公钥。不过这次不用在服务器上一个一个手工添加到 `~/.ssh/authorized_keys` 文件末端，而只需管理 `keydir` 目录中的公钥文件。文件的命名将决定在 `gitosis.conf` 中对用户的标识。现在我们为 John，Josie 和 Jessica 添加公钥：

```
$ cp /tmp/id_rsa.john.pub keydir/john.pub
    $ cp /tmp/id_rsa.josie.pub keydir/josie.pub
    $ cp /tmp/id_rsa.jessica.pub keydir/jessica.pub
```

然后把他们都加进 'mobile' 团队，让他们对 `iphone_project` 具有读写权限：

```
[group mobile]
    writable = iphone_project
    members = scott john josie jessica
```

如果你提交并推送这个修改，四个用户将同时具有该项目的读写权限。

Gitosis 也具有简单的访问控制功能。如果想让 John 只有读权限，可以这样做：

```
[group mobile]
    writable = iphone_project
    members = scott josie jessica

    [group mobile_ro]
    readonly = iphone_project
    members = john
```

现在 John 可以克隆和获取更新，但 Gitosis 不会允许他向项目推送任何内容。像这样的组可以随意创建，多少不限，每个都可以包含若干不同的用户和项目。甚至还可以指定某个组为成员之一（在组名前加上 `@` 前缀），自动继承该组的成员：

```
[group mobile_committers]
    members = scott josie jessica

    [group mobile]
    writable = iphone_project
    members = @mobile_committers

    [group mobile_2]
    writable = another_iphone_project
    members = @mobile_committers john
```

如果遇到意外问题，试试看把 `loglevel=DEBUG` 加到 `[gitosis]` 的段落（译注：把日志设置为调试级别，记录更详细的运行信息。）。如果一不小心搞错了配置，失去了推送权限，也可以手工修改服务器上的 `/home/git/.gitosis.conf` 文件 — Gitosis 实际是从该文件读取信息的。它在得到推送数据时，会把新的 `gitosis.conf` 存到该路径上。所以如果你手工编辑该文件的话，它会一直保持到下次向 `gitosis-admin` 推送新版本的配置内容为止。

## [4.8 Gitolite](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.8-Gitolite)

Note: the latest copy of this section of the ProGit book is always available within the [gitolite documentation](http://sitaramc.github.com/gitolite/progit.html). The author would also like to humbly state that, while this section is accurate, and *can* (and often *has*) been used to install gitolite without reading any other documentation, it is of necessity not complete, and cannot completely replace the enormous amount of documentation that gitolite comes with.

Git has started to become very popular in corporate environments, which tend to have some additional requirements in terms of access control. Gitolite was originally created to help with those requirements, but it turns out that it's equally useful in the open source world: the Fedora Project controls access to their package management repositories (over 10,000 of them!) using gitolite, and this is probably the largest gitolite installation anywhere too.

Gitolite allows you to specify permissions not just by repository, but also by branch or tag names within each repository. That is, you can specify that certain people (or groups of people) can only push certain "refs" (branches or tags) but not others.

### Installing

Installing Gitolite is very easy, even if you don't read the extensive documentation that comes with it. You need an account on a Unix server of some kind; various Linux flavours, and Solaris 10, have been tested. You do not need root access, assuming git, perl, and an openssh compatible ssh server are already installed. In the examples below, we will use the `gitolite` account on a host called `gitserver`.

Gitolite is somewhat unusual as far as "server" software goes -- access is via ssh, and so every userid on the server is a potential "gitolite host". As a result, there is a notion of "installing" the software itself, and then "setting up" a user as a "gitolite host".

Gitolite has 4 methods of installation. People using Fedora or Debian systems can obtain an RPM or a DEB and install that. People with root access can install it manually. In these two methods, any user on the system can then become a "gitolite host".

People without root access can install it within their own userids. And finally, gitolite can be installed by running a script *on the workstation*, from a bash shell. (Even the bash that comes with msysgit will do, in case you're wondering.)

We will describe this last method in this article; for the other methods please see the documentation.

You start by obtaining public key based access to your server, so that you can log in from your workstation to the server without getting a password prompt. The following method works on Linux; for other workstation OSs you may have to do this manually. We assume you already had a key pair generated using `ssh-keygen`.

```
$ ssh-copy-id -i ~/.ssh/id_rsa gitolite@gitserver
```

This will ask you for the password to the gitolite account, and then set up public key access. This is **essential** for the install script, so check to make sure you can run a command without getting a password prompt:

```
$ ssh gitolite@gitserver pwd
    /home/gitolite
```

Next, you clone Gitolite from the project's main site and run the "easy install" script (the third argument is your name as you would like it to appear in the resulting gitolite-admin repository):

```
$ git clone git://github.com/sitaramc/gitolite
    $ cd gitolite/src
    $ ./gl-easy-install -q gitolite gitserver sitaram
```

And you're done! Gitolite has now been installed on the server, and you now have a brand new repository called `gitolite-admin` in the home directory of your workstation. You administer your gitolite setup by making changes to this repository and pushing.

That last command does produce a fair amount of output, which might be interesting to read. Also, the first time you run this, a new keypair is created; you will have to choose a passphrase or hit enter for none. Why a second keypair is needed, and how it is used, is explained in the "ssh troubleshooting" document that comes with Gitolite. (Hey the documentation has to be good for *something*!)

Repos named `gitolite-admin` and `testing` are created on the server by default. If you wish to clone either of these locally (from an account that has SSH console access to the gitolite account via *authorized_keys*), type:

```
$ git clone gitolite:gitolite-admin
    $ git clone gitolite:testing
```

To clone these same repos from any other account:

```
$ git clone gitolite@servername:gitolite-admin
    $ git clone gitolite@servername:testing
```

### Customising the Install

While the default, quick, install works for most people, there are some ways to customise the install if you need to. If you omit the `-q` argument, you get a "verbose" mode install -- detailed information on what the install is doing at each step. The verbose mode also allows you to change certain server-side parameters, such as the location of the actual repositories, by editing an "rc" file that the server uses. This "rc" file is liberally commented so you should be able to make any changes you need quite easily, save it, and continue. This file also contains various settings that you can change to enable or disable some of gitolite's advanced features.

### Config File and Access Control Rules

Once the install is done, you switch to the `gitolite-admin` repository (placed in your HOME directory) and poke around to see what you got:

```
$ cd ~/gitolite-admin/
    $ ls
    conf/ keydir/
    $ find conf keydir -type f
    conf/gitolite.conf
    keydir/sitaram.pub
    $ cat conf/gitolite.conf
    #gitolite conf
    # please see conf/example.conf for details on syntax and features

    repo gitolite-admin
    RW+ = sitaram

    repo testing
    RW+ = @all
```

Notice that "sitaram" (the last argument in the `gl-easy-install` command you gave earlier) has read-write permissions on the `gitolite-admin` repository as well as a public key file of the same name.

The config file syntax for gitolite is liberally documented in `conf/example.conf`, so we'll only mention some highlights here.

You can group users or repos for convenience. The group names are just like macros; when defining them, it doesn't even matter whether they are projects or users; that distinction is only made when you *use* the "macro".

```
@oss_repos = linux perl rakudo git gitolite
    @secret_repos = fenestra pear

    @admins = scott # Adams, not Chacon, sorry :)
    @interns = ashok # get the spelling right, Scott!
    @engineers = sitaram dilbert wally alice
    @staff = @admins @engineers @interns
```

You can control permissions at the "ref" level. In the following example, interns can only push the "int" branch. Engineers can push any branch whose name starts with "eng-", and tags that start with "rc" followed by a digit. And the admins can do anything (including rewind) to any ref.

```
repo @oss_repos
    RW int$ = @interns
    RW eng- = @engineers
    RW refs/tags/rc[0-9] = @engineers
    RW+ = @admins
```

The expression after the `RW` or `RW+` is a regular expression (regex) that the refname (ref) being pushed is matched against. So we call it a "refex"! Of course, a refex can be far more powerful than shown here, so don't overdo it if you're not comfortable with perl regexes.

Also, as you probably guessed, Gitolite prefixes `refs/heads/` as a syntactic convenience if the refex does not begin with `refs/`.

An important feature of the config file's syntax is that all the rules for a repository need not be in one place. You can keep all the common stuff together, like the rules for all `oss_repos` shown above, then add specific rules for specific cases later on, like so:

```
repo gitolite
    RW+ = sitaram
```

That rule will just get added to the ruleset for the `gitolite` repository.

At this point you might be wondering how the access control rules are actually applied, so let's go over that briefly.

There are two levels of access control in gitolite. The first is at the repository level; if you have read (or write) access to *any* ref in the repository, then you have read (or write) access to the repository.

The second level, applicable only to "write" access, is by branch or tag within a repository. The username, the access being attempted (`W` or `+`), and the refname being updated are known. The access rules are checked in order of appearance in the config file, looking for a match for this combination (but remember that the refname is regex-matched, not merely string-matched). If a match is found, the push succeeds. A fallthrough results in access being denied.

### Advanced Access Control with "deny" rules

So far, we've only seen permissions to be one or `R`, `RW`, or `RW+`. However, gitolite allows another permission: `-`, standing for "deny". This gives you a lot more power, at the expense of some complexity, because now fallthrough is not the *only* way for access to be denied, so the *order of the rules now matters*!

Let us say, in the situation above, we want engineers to be able to rewind any branch *except* master and integ. Here's how to do that:

```
 RW master integ = @engineers
    - master integ = @engineers
    RW+ = @engineers
```

Again, you simply follow the rules top down until you hit a match for your access mode, or a deny. Non-rewind push to master or integ is allowed by the first rule. A rewind push to those refs does not match the first rule, drops down to the second, and is therefore denied. Any push (rewind or non-rewind) to refs other than master or integ won't match the first two rules anyway, and the third rule allows it.

### Restricting pushes by files changed

In addition to restricting what branches a user can push changes to, you can also restrict what files they are allowed to touch. For example, perhaps the Makefile (or some other program) is really not supposed to be changed by just anyone, because a lot of things depend on it or would break if the changes are not done *just right*. You can tell gitolite:

```
repo foo
    RW = @junior_devs @senior_devs

    RW NAME/ = @senior_devs
    - NAME/Makefile = @junior_devs
    RW NAME/ = @junior_devs
```

This powerful feature is documented in `conf/example.conf`.

### Personal Branches

Gitolite also has a feature called "personal branches" (or rather, "personal branch namespace") that can be very useful in a corporate environment.

A lot of code exchange in the git world happens by "please pull" requests. In a corporate environment, however, unauthenticated access is a no-no, and a developer workstation cannot do authentication, so you have to push to the central server and ask someone to pull from there.

This would normally cause the same branch name clutter as in a centralised VCS, plus setting up permissions for this becomes a chore for the admin.

Gitolite lets you define a "personal" or "scratch" namespace prefix for each developer (for example, `refs/personal/<devname>/*`); see the "personal branches" section in `doc/3-faq-tips-etc.mkd` for details.

### "Wildcard" repositories

Gitolite allows you to specify repositories with wildcards (actually perl regexes), like, for example `assignments/s[0-9][0-9]/a[0-9][0-9]`, to pick a random example. This is a *very* powerful feature, which has to be enabled by setting `$GL_WILDREPOS = 1;` in the rc file. It allows you to assign a new permission mode ("C") which allows users to create repositories based on such wild cards, automatically assigns ownership to the specific user who created it, allows him/her to hand out R and RW permissions to other users to collaborate, etc. This feature is documented in `doc/4-wildcard-repositories.mkd`.

### Other Features

We'll round off this discussion with a sampling of other features, all of which, and many more, are described in great detail in the "faqs, tips, etc" and other documents.

**Logging**: Gitolite logs all successful accesses. If you were somewhat relaxed about giving people rewind permissions (`RW+`) and some kid blew away "master", the log file is a life saver, in terms of easily and quickly finding the SHA that got hosed.

**Git outside normal PATH**: One extremely useful convenience feature in gitolite is support for git installed outside the normal `$PATH` (this is more common than you think; some corporate environments or even some hosting providers refuse to install things system-wide and you end up putting them in your own directories). Normally, you are forced to make the *client-side* git aware of this non-standard location of the git binaries in some way. With gitolite, just choose a verbose install and set `$GIT_PATH` in the "rc" files. No client-side changes are required after that :-)

**Access rights reporting**: Another convenient feature is what happens when you try and just ssh to the server. Gitolite shows you what repos you have access to, and what that access may be. Here's an example:

```
 hello sitaram, the gitolite version here is v1.5.4-19-ga3397d4
    the gitolite config gives you the following access:
    R anu-wsd
    R entrans
    R W git-notes
    R W gitolite
    R W gitolite-admin
    R indic_web_input
    R shreelipi_converter
```

**Delegation**: For really large installations, you can delegate responsibility for groups of repositories to various people and have them manage those pieces independently. This reduces the load on the main admin, and makes him less of a bottleneck. This feature has its own documentation file in the `doc/` directory.

**Gitweb support**: Gitolite supports gitweb in several ways. You can specify which repos are visible via gitweb. You can set the "owner" and "description" for gitweb from the gitolite config file. Gitweb has a mechanism for you to implement access control based on HTTP authentication, so you can make it use the "compiled" config file that gitolite produces, which means the same access control rules (for read access) apply for gitweb and gitolite.

**Mirroring**: Gitolite can help you maintain multiple mirrors, and switch between them easily if the primary server goes down.

## [4.9 Git 守护进程](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.9-Git-%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B)

对于提供公共的，非授权的只读访问，我们可以抛弃 HTTP 协议，改用 Git 自己的协议，这主要是出于性能和速度的考虑。Git 协议远比 HTTP 协议高效，因而访问速度也快，所以它能节省很多用户的时间。

重申一下，这一点只适用于非授权的只读访问。如果建在防火墙之外的服务器上，那么它所提供的服务应该只是那些公开的只读项目。如果是在防火墙之内的服务器上，可用于支撑大量参与人员或自动系统（用于持续集成或编译的主机）只读访问的项目，这样可以省去逐一配置 SSH 公钥的麻烦。

但不管哪种情形，Git 协议的配置设定都很简单。基本上，只要以守护进程的形式运行该命令即可：

```
git daemon --reuseaddr --base-path=/opt/git/ /opt/git/
```

这里的 `--reuseaddr` 选项表示在重启服务前，不等之前的连接超时就立即重启。而 `--base-path` 选项则允许克隆项目时不必给出完整路径。最后面的路径告诉 Git 守护进程允许开放给用户访问的仓库目录。假如有防火墙，则需要为该主机的 9418 端口设置为允许通信。

以守护进程的形式运行该进程的方法有很多，但主要还得看用的是什么操作系统。在 Ubuntu 主机上，可以用 Upstart 脚本达成。编辑该文件：

```
/etc/event.d/local-git-daemon
```

加入以下内容：

```
start on startup
    stop on shutdown
    exec /usr/bin/git daemon \
    --user=git --group=git \
    --reuseaddr \
    --base-path=/opt/git/ \
    /opt/git/
    respawn
```

出于安全考虑，强烈建议用一个对仓库只有读取权限的用户身份来运行该进程 — 只需要简单地新建一个名为 `git-ro` 的用户（译注：新建用户默认对仓库文件不具备写权限，但这取决于仓库目录的权限设定。务必确认 `git-ro` 对仓库只能读不能写。），并用它的身份来启动进程。这里为了简化，后面我们还是用之前运行 Gitosis 的用户 'git'。

这样一来，当你重启计算机时，Git 进程也会自动启动。要是进程意外退出或者被杀掉，也会自行重启。在设置完成后，不重启计算机就启动该守护进程，可以运行：

```
initctl start local-git-daemon
```

而在其他操作系统上，可以用 `xinetd`，或者 `sysvinit` 系统的脚本，或者其他类似的脚本 — 只要能让那个命令变为守护进程并可监控。

接下来，我们必须告诉 Gitosis 哪些仓库允许通过 Git 协议进行匿名只读访问。如果每个仓库都设有各自的段落，可以分别指定是否允许 Git 进程开放给用户匿名读取。比如允许通过 Git 协议访问 iphone_project，可以把下面两行加到 `gitosis.conf` 文件的末尾：

```
[repo iphone_project]
    daemon = yes
```

在提交和推送完成后，运行中的 Git 守护进程就会响应来自 9418 端口对该项目的访问请求。

如果不考虑 Gitosis，单单起了 Git 守护进程的话，就必须到每一个允许匿名只读访问的仓库目录内，创建一个特殊名称的空文件作为标志：

```
$ cd /path/to/project.git
    $ touch git-daemon-export-ok
```

该文件的存在，表明允许 Git 守护进程开放对该项目的匿名只读访问。

Gitosis 还能设定哪些项目允许放在 GitWeb 上显示。先打开 GitWeb 的配置文件 `/etc/gitweb.conf`，添加以下四行：

```
$projects_list = "/home/git/gitosis/projects.list";
    $projectroot = "/home/git/repositories";
    $export_ok = "git-daemon-export-ok";
    @git_base_url_list = ('git://gitserver');
```

接下来，只要配置各个项目在 Gitosis 中的 `gitweb` 参数，便能达成是否允许 GitWeb 用户浏览该项目。比如，要让 iphone_project 项目在 GitWeb 里出现，把 `repo` 的设定改成下面的样子：

```
[repo iphone_project]
    daemon = yes
    gitweb = yes
```

在提交并推送过之后，GitWeb 就会自动开始显示 iphone_project 项目的细节和历史。

## [4.10 Git 托管服务](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.10-Git-%E6%89%98%E7%AE%A1%E6%9C%8D%E5%8A%A1)

如果不想经历自己架设 Git 服务器的麻烦，网络上有几个专业的仓库托管服务可供选择。这样做有几大优点：托管账户的建立通常比较省时，方便项目的启动，而且不涉及服务器的维护和监控。即使内部创建并运行着自己的服务器，同时为开源项目提供一个公共托管站点还是有好处的 — 让开源社区更方便地找到该项目，并给予帮助。

目前，可供选择的托管服务数量繁多，各有利弊。在 Git 官方 wiki 上的 Githosting 页面有一个最新的托管服务列表：

```
http://git.or.cz/gitwiki/GitHosting
```

由于本书无法全部一一介绍，而本人（译注：指本书作者 Scott Chacon。）刚好在其中一家公司工作，所以接下来我们将会介绍如何在 GitHub 上建立新账户并启动项目。至于其他托管服务大体也是这么一个过程，基本的想法都是差不多的。

GitHub 是目前为止最大的开源 Git 托管服务，并且还是少数同时提供公共代码和私有代码托管服务的站点之一，所以你可以在上面同时保存开源和商业代码。事实上，本书就是放在 GitHub 上合作编著的。（译注：本书的翻译也是放在 GitHub 上广泛协作的。）

### GitHub

GitHub 和大多数的代码托管站点在处理项目命名空间的方式上略有不同。GitHub 的设计更侧重于用户，而不是完全基于项目。也就是说，如果我在 GitHub 上托管一个名为 `grit` 的项目的话，它的地址不会是 `github.com/grit`，而是按在用户底下 `github.com/shacon/grit` （译注：本书作者 Scott Chacon 在 GitHub 上的用户名是 `shacon`。）。不存在所谓某个项目的官方版本，所以假如第一作者放弃了某个项目，它可以无缝转移到其它用户的名下。

GitHub 同时也是一个向使用私有仓库的用户收取费用的商业公司，但任何人都可以方便快捷地申请到一个免费账户，并在上面托管数量不限的开源项目。接下来我们快速介绍一下 GitHub 的基本使用。

### 建立新账户

首先注册一个免费账户。访问 Pricing and Signup 页面 `http://github.com/plans` 并点击 Free acount 里的 Sign Up 按钮（见图 4-2），进入注册页面。

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/6e4762b63b404f67a5d9c92e771181b3~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-2. GitHub 服务简介页面

选择一个系统中尚未使用的用户名，提供一个与之相关联的电邮地址，并输入密码（见图 4-3）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/085ba6a3c0444a1e99017d2045a75a4f~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-3. GitHub 用户注册表单

如果方便，现在就可以提供你的 SSH 公钥。我们在前文的"小型安装" 一节介绍过生成新公钥的方法。把新生成的公钥复制粘贴到 SSH Public Key 文本框中即可。要是对生成公钥的步骤不太清楚，也可以点击 "explain ssh keys" 链接，会显示各个主流操作系统上完成该步骤的介绍。点击 "I agree，sign me up" 按钮完成用户注册，并转到该用户的 dashboard 页面（见图 4-4）:

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8913a64de3b840439050b910bd3dcc2a~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-4. GitHub 的用户面板

接下来就可以建立新仓库了。

### 建立新仓库

点击用户面板上仓库旁边的 "create a new one" 链接，显示 Create a New Repository 的表单（见图 4-5）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a9d47c23ef02483b95988fa87b7bdec7~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-5. 在 GitHub 上建立新仓库

当然，项目名称是必不可少的，此外也可以适当描述一下项目的情况或者给出官方站点的地址。然后点击 "Create Repository" 按钮，新仓库就建立起来了（见图 4-6）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3b1e18e3f17c408586a89f891bb437e9~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-6. GitHub 上各个项目的概要信息

由于尚未提交代码，点击项目地址后 GitHub 会显示一个简要的指南，告诉你如何新建一个项目并推送上来，如何从现有项目推送，以及如何从一个公共的 Subversion 仓库导入项目（见图 4-7）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4c58db4407504e6098f299768b1966a8~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-7. 新仓库指南

该指南和本书前文介绍的类似，对于新的项目，需要先在本地初始化为 Git 项目，添加要管理的文件并作首次提交：

```
$ git init
    $ git add .
    $ git commit -m 'initial commit'
```

然后在这个本地仓库内把 GitHub 添加为远程仓库，并推送 master 分支上来：

```
$ git remote add origin git@github.com:testinguser/iphone_project.git
    $ git push origin master
```

现在该项目就托管在 GitHub 上了。你可以把它的 URL 分享给每位对此项目感兴趣的人。本例的 URL 是 `http://github.com/testinguser/iphone_project`。而在项目页面的摘要部分，你会发现有两个 Git URL 地址（见图 4-8）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ec69a86b218146eb86a39d947a08bed1~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-8. 项目摘要中的公共 URL 和私有 URL

Public Clone URL 是一个公开的，只读的 Git URL，任何人都可以通过它克隆该项目。可以随意散播这个 URL，比如发布到个人网站之类的地方等等。

Your Clone URL 是一个基于 SSH 协议的可读可写 URL，只有使用与上传的 SSH 公钥对应的密钥来连接时，才能通过它进行读写操作。其他用户访问该项目页面时只能看到之前那个公共的 URL，看不到这个私有的 URL。

### 从 Subversion 导入项目

如果想把某个公共 Subversion 项目导入 Git，GitHub 可以帮忙。在指南的最后有一个指向导入 Subversion 页面的链接。点击它会看到一个表单，包含有关导入流程的信息以及一个用来粘贴公共 Subversion 项目连接的文本框（见图 4-9）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f299a7f441484e2bb3096e62d98f7672~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-9. Subversion 导入界面

如果项目很大，采用非标准结构，或者是私有的，那就无法借助该工具实现导入。到第 7 章，我们会介绍如何手工导入复杂工程的具体方法。

### 添加协作开发者

现在把团队里的其他人也加进来。如果 John，Josie 和 Jessica 都在 GitHub 注册了账户，要赋予他们对该仓库的推送权限，可以把他们加为项目协作者。这样他们就可以通过各自的公钥访问我的这个仓库了。

点击项目页面上方的 "edit" 按钮或者顶部的 Admin 标签，进入该项目的管理页面（见图 4-10）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/151b24055b544b31be76ef30fc294b5b~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-10. GitHub 的项目管理页面

为了给另一个用户添加项目的写权限，点击 "Add another collaborator" 链接，出现一个用于输入用户名的表单。在输入的同时，它会自动跳出一个符合条件的候选名单。找到正确用户名之后，点 Add 按钮，把该用户设为项目协作者（见图 4-11）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/a51989eb727d464891990c1215fb6c69~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-11. 为项目添加协作者

添加完协作者之后，就可以在 Repository Collaborators 区域看到他们的名单（见图 4-12）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1ceed05985b242959281f40eae51f1de~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-12. 项目协作者名单

如果要取消某人的访问权，点击 "revoke" 即可取消他的推送权限。对于将来的项目，你可以从现有项目复制协作者名单，或者直接借用协作者群组。

### 项目页面

在推送或从 Subversion 导入项目之后，你会看到一个类似图 4-13 的项目主页：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/42a9f87316814166929b6dec8068a0c9~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-13. GitHub 上的项目主页

别人访问你的项目时看到的就是这个页面。它有若干导航标签，提交标签用于显示提交历史，最新的提交位于最上方，这和 `git log` 命令的输出类似。Network 标签展示所有派生了该项目并做出贡献的用户的关系图谱。Downloads 标签允许你上传项目的二进制文件，提供下载该项目各个版本的 tar/zip 包。Wiki 标签提供了一个用于撰写文档或其他项目相关信息的 wiki 站点。Graphs 标签包含了一些可视化的项目信息与数据。默认打开的 Source 标签页面，则列出了该项目的目录结构和概要信息，并在下方自动展示 README 文件的内容（如果该文件存在的话），此外还会显示最近一次提交的相关信息。

### 派生项目

如果要为一个自己没有推送权限的项目贡献代码，GitHub 鼓励使用派生（fork）。到那个感兴趣的项目主页上，点击页面上方的 "fork" 按钮，GitHub 就会为你复制一份该项目的副本到你的仓库中，这样你就可以向自己的这个副本推送数据了。

采取这种办法的好处是，项目拥有者不必忙于应付赋予他人推送权限的工作。随便谁都可以通过派生得到一个项目副本并在其中展开工作，事后只需要项目维护者将这些副本仓库加为远程仓库，然后提取更新合并即可。

要派生一个项目，到原始项目的页面（本例中是 mojombo/chronic）点击 "fork" 按钮（见图 4-14）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/329a11be16a948fabe224ed2e13c39a5~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-14. 点击 "fork" 按钮获得任意项目的可写副本

几秒钟之后，你将进入新建的项目页面，会显示该项目派生自哪一个项目（见图 4-15）：

![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b9618a2a051f42e9b3d33877fd6081ba~tplv-k3u1fbpfcp-zoom-1.image)\
图 4-15. 派生后得到的项目副本

### GitHub 小结

关于 GitHub 就先介绍这么多，能够快速达成这些事情非常重要（译注：门槛的降低和完成基本任务的简单高效，对于推动开源项目的协作发展有着举足轻重的意义。）。短短几分钟内，你就能创建一个新账户，添加一个项目并开始推送。如果项目是开源的，整个庞大的开发者社区都可以立即访问它，提供各式各样的帮助和贡献。最起码，这也是一种 Git 新手立即体验尝试 Git 的捷径。

## [4.11 小结](https://git.oschina.net/progit/4-%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E7%9A%84-Git.html#4.11-%E5%B0%8F%E7%BB%93)

我们讨论并介绍了一些建立远程 Git 仓库的方法，接下来你可以通过这些仓库同他人分享或合作。

运行自己的服务器意味着更多的控制权以及在防火墙内部操作的可能性，当然这样的服务器通常需要投入一定的时间精力来架设维护。如果直接托管，虽然能免去这部分工作，但有时出于安全或版权的考虑，有些公司禁止将商业代码托管到第三方服务商。

所以究竟采取哪种方案，并不是个难以取舍的问题，或者其一，或者相互配合，哪种合适就用哪种。
