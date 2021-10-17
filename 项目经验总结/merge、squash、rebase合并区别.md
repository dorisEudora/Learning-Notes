# merge、squash、rebase合并区别

在团队合作中都要使用git开发，使用分支合并，分支合并有三种，merge、squash、rebase

### 一个简单的模型：

假设一开始的master分支上有好久个提交

![image-20210314152759335](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314152759335.png)

我们切出一条开发的分支，进行了一些 Feature 的开发，然后我们的分支可能就是这种情况：

![image-20210314152908068](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314152908068.png)

这种情况还好，也比较常遇到，但是，现在问题来了，如果在这个时候 master 有了一些新提交（可能是其他分支合并进来的），那么这个时候情形就成了这样：

![image-20210314153132737](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314153132737.png)

### 1、普通的Merge

说到合并分支，我们最熟悉的操作是这样的：

- 先切换到目标分支master
- 执行命令：git merge devel（分支名）
- 删除旧分支：git branch -d devel
- 提交到远程分支：git push origin master

好像这样没啥问题的样子，但是这样操作之后，你知道结果是怎么样吗？假设合并之前的这样的：

![image-20210314153413119](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314153413119.png)

我们操作之后，最后我们的分支历史将会是：

![image-20210314153448956](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314153448956.png)

是的，看上去很不错，也是一条直直的 commit line，我们在 devel 分支中的 commit 也是一个不差得保留在了 master 中。但是，很多时候，我们并不需要那么多的 commit，假设你给一个开源项目提交一个 Bug Fixes，然后一个简单的修改因为你的粗心大意 pr 了十几个 commit 过去，如果作者给你 merge 了，这就在这个项目的历史长河中增加了十几个 commit 啊，以后的人看 commit history 估计都崩溃了吧；同时，对于你自己管理的项目来说，当你 merge 之后发现有问题，想回滚都蛋疼！

### 2、squash 合并

在使用git 的过程中，可能你会遇到想要合并多个commit为一个，然后很多人告诉你使用git commit --amend，然后你发现里面有你的多个commit历史，你可以通过pick选择，squash合并等，同样的，merge的时候也可以这样干，你只需要这么简单的两步：

- 切换到目标分支：git checkout master
- 以squash的形式merge：**git merge --squash devel**

你会发现在master分支上居然有未提交的修改，然后你需要在master上主动提交修改，注意，这里是你的commit，也就是改变了commit的author，结果是：

![image-20210314154203245](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314154203245.png)

这里好了，比前面普通的 merge 来说，我们只有一个 commit 了，不管在分支中 commit 了多少，这里都只有**一个**！

### 3、rebase合并

但是，作为处女座的程序员肯定是不能忍受目前的情况的，因为我们既想合并 commits，又想保留作者的信息，那么有没有什么好办法呢？肯定是有的啦，这个时候我们可以尝试一下 rebase，操作步骤是这样的：

- **先切换到devel分支**（不一样咯）：git checkout devel
- 变基：git rebase -i master
- 切换回目标分支：git checkout master
- 合并：git merge devel

这里完成了第二步之后我想你应该大概知道发生了什么事了，我们在 devel 里面对照 master 进行了变基，所谓的**变基**其实就是找到两个分支共同的祖先，然后在当前分支上合并从共同祖先到现在的所有 commit，所以我们在第二步的时候会选择怎么处理这些 commit，然后我们就得到了一个从公共commit 到现在的单个 commit，这个时候别人讲我们这个 commit 合并到 master 也只会在 master 上留下一个 commit 记录，就像这样：

![image-20210314154615979](C:\Users\l\AppData\Roaming\Typora\typora-user-images\image-20210314154615979.png)

虽然这个 commit history 线看上去很不错，而且也比较符合实际情况，但是我们需要注意到的有点就是分支上的开发者需要自己执行变基操作，从而导致他的原始 commit history 变化了（可以理解成被合并了）。

### 4、总结

1. rebase可以尽可能保持master分支干净整洁，并且易于识别author
2. squash也可以保持master分支干净，但是master中的author都是maintainer，而不是原owner
3. merge不能保持master分支干净，但是保持了所有的commit history，大多数情况下都是不好的，个别情况挺好的
4. 相比之下，使用rebase较好，但是使用要求很高

