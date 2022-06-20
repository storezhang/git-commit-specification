# 为什么需要规范？
无规矩不成方圆，编程也一样。

如果你有一个项目，从始至终都是自己写，那么你想怎么写都可以，没有人可以干预你。可是如果在团队协作中，大家都张扬个性，那么代码将会是一团糟，好好的项目就被糟践了。不管是开发还是日后维护，都将是灾难。

这时候，有人提出了何不统一标准，大家都按照这个标准来。于是 ESLint，JSHint 等代码工具如雨后春笋般涌现，成为了项目构建的必备良品。

Git Commit 规范可能并没有那么夸张，但如果你在版本回退的时候看到一大段糟心的 Commit，恐怕会懊恼不已吧。所以，严格遵守规范，利人利己。

# 具体规则
所有的`Commit Message`必须满足如下格式
```
<type>(<scope>): <subject>
```

## type
用于说明提交的类别，只允许使用下面标识
- feat：新功能（feature）
- fix：修补（bug）
- docs：文档（documentation）
- style： 格式（不影响代码运行的变动）
- refactor：重构（即不是新增功能，也不是修改bug的代码变动）
- test：增加测试
- chore：构建过程或辅助工具的变动

## scope
用于说明影响的范围，这个范围可以是每个项目自己定义的范围，比如
- 数据层
- 控制层
- 视图层
可以不是上面这几种范围，只要在项目内大家认同即可

## subject
  是 commit 目的的简短描述，不超过50个字符。
  ```
  1.以动词开头，使用第一人称现在时，比如change，而不是changed或changes
  2.第一个字母小写
  3.结尾不加句号（.）
  ```

###  三、如何修改之前的 commit 信息？
其实并不复杂，我们只需要这样做:

1、将当前分支无关的工作状态进行暂存
```shell
git stash
```

2、将 HEAD 移动到需要修改的 commit 上
```shell
git rebase 9633cf0919^ --interactive
```

3、找到需要修改的 commit ,将首行的 pick 改成 edit
4、开始着手解决你的 bug
5、 git add 将改动文件添加到暂存
6、 git commit –amend 追加改动到提交
7、git rebase –continue 移动 HEAD 回最新的 commit
8、恢复之前的工作状态`git stash pop`

大功告成，是不是想把整个 Commit 都修改一遍，逃～

此处参考自：修改 Commit 日志和内容

### 四、项目中使用
这时候问题又来了，为什么我提交的时候会有警告，这个又是如何做到的呢？

这时候，我们需要一款 Node 插件 validate-commit-msg 来检查项目中 Commit message 是否规范。

1. 首先，安装插件：

```shell
npm install --save-dev validate-commit-msg
```

2. 使用方式一，建立 .vcmrc 文件：

```xml

{
  "types": ["feat", "fix", "docs", "style", "refactor", "perf", "test", "build", "ci", "chore", "revert"],
  "scope": {
    "required": false,
    "allowed": ["*"],
    "validate": false,
    "multiple": false
  },
  "warnOnFail": false,
  "maxSubjectLength": 100,
  "subjectPattern": ".+",
  "subjectPatternErrorMsg": "subject does not match subject pattern!",
  "helpMessage": "",
  "autoFix": false
} 
3.使用方式二：写入 package.json

{
  "config": {
    "validate-commit-msg": {
      /* your config here */
    }
  }
} 
4.可是我们如果想自动使用 ghooks 钩子函数呢？

{
  …
  "config": {
    "ghooks": {
      "pre-commit": "gulp lint",
      "commit-msg": "validate-commit-msg",
      "pre-push": "make test",
      "post-merge": "npm install",
      "post-rewrite": "npm install",
      …
    }
  }
  …
}

```

在 ghooks 中我们可以做很多事情，当然不只是 validate-commit-msg 哦。
更多细节请参考：validate-commit-msg

### 五、Commit 规范的作用
1. 提供更多的信息，方便排查与回退；
2. 过滤关键字，迅速定位；
3. 方便生成文档；

### 六、生成 Change log
正如上文提到的生成文档，如果我们的提交都按照规范的话，那就很简单了。生成的文档包括以下三个部分：
```config
  New features
  Bug fixes
  Breaking changes.
```
每个部分都会罗列相关的 commit ，并且有指向这些 commit 的链接。当然，生成的文档允许手动修改，所以发布前，你还可以添加其他内容。

这里需要使用工具 Conventional Changelog 生成 Change log ：
```shell
npm install -g conventional-changelog
cd jartto-domo
conventional-changelog -p angular -i CHANGELOG.md -w
```

为了方便使用，可以将其写入 package.json 的 scripts 字段。

```javascripts
{
  "scripts": {
    "changelog": "conventional-changelog -p angular -i CHANGELOG.md -w -r 0"
  }
}
```
这样，使用起来就很简单了：

```shell
npm run changelog
```

到这里，我们所有的问题都搞明白了，🍻Cheers～

### 七、总结
看完文章，你还会如此放荡不羁吗？你还会随心所欲的编写 Commit 吗？你还会如此 `git commit -m "hello jartto"`提交吗？

答案是否定的，因为使用了钩子函数，你没有机会了，否则将是无穷无尽的恢复 Commit。这倒可以养成良好的提交习惯，🙈～