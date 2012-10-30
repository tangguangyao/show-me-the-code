# 无规则，不成方圆

## Gitlab

统一使用内部的 [gitlab](http://gitlab.alibaba-inc.com) 进行代码托管。

如没有 gitlab 账号的同学，请联系 @渐本 或 @唐容 开通权限。

### 基于git的分支命名原则与分支开发步骤

git 通常是走分支开发，一般来说，master 的写权限只有很少人拥有，
开发者通常都是针对自己需要实现的功能，创建对应的分支，然后开发，然后提交合并请求。
进行 code review 之后，决定是否合并到 master 。

分支开发一般步骤：

1. 新功能或者bug fixed提交到issues；
2. 创建 `issue$ID-featureName` 或者 `issue$ID-somebugHotfix` 分支，进行新功能开发或者修复bug，$ID就是对应的issue生成的id；
3. coding，unit test 通过后，提交 Merge request，指定 code review 人；
4. code review 人进行review，如果觉得没问题，可以合并，就添加一个 `+1` 的评论；项目组其他人员也可以参与review，同样以 `+1` 代表review通过；如果review不通过，请对有问题的代码进行评论说明，有理有据。
5. review通过，最终通过web的auto merge 功能进行自动合并，到处，这次快速迭代开发完毕。
6. 继续按 1. 循环进行下一次迭代。

### 设定你的git身份

为了不污染全局的git身份标示，建议对每个git项目单独设置内部的身份标示，如

```bash
$ git config user.name 苏千
$ git config user.email suqian.yf@taobao.com
```

## 代码规范

必要严格参照 [Node代码规范](https://github.com/windyrobin/iFrame/blob/master/style.md)

## 单元测试

所有代码必须包含相对于的单元测试，代码文件与测试文件一一对应。

如 `lib/utils.js` => `test/lib/utils.test.js`

无特殊情况，努力做到代码覆盖率 **100%** 

### Makefile

使用 `Makefile` , `make test` 及 `make test-cov` 启动测试

一个web应用的 `Makefile` 示例:

```bash
NAME = nodeblog
TESTS = $(shell ls -S `find test -type f -name "*.test.js" -print`)
TIMEOUT = 5000
REPORTER = tap
JSCOVERAGE = ./node_modules/visionmedia-jscoverage/jscoverage
PROJECT_DIR = $(shell pwd)
NPM_INSTALL_PRODUCTION = PYTHON=`which python2.6` NODE_ENV=production \
  npm --registry=http://registry.npm.tbdata.org --production install
NPM_INSTALL_TEST = PYTHON=`which python2.6` NODE_ENV=test \
  npm --registry=http://registry.npm.tbdata.org install 

install:
  @$(NPM_INSTALL_PRODUCTION)

test:
  @$(MAKE) install
  @$(NPM_INSTALL_TEST)
  @NODE_ENV=test node_modules/mocha/bin/mocha \
    --reporter $(REPORTER) --timeout $(TIMEOUT) $(TESTS)

cov:
  @rm -rf ../$(NAME)-cov
  @$(JSCOVERAGE) --encoding=utf-8 --exclude=node_modules --exclude=test --exclude=public \
    --exclude=bin --exclude=client --exclude=benchmarks --exclude=conf \
    ./ ../$(NAME)-cov
  @cp -rf ./node_modules ./bin ./test ./public ./conf ./dispatch.js ./hsf.js ../$(NAME)-cov

test-cov: cov
  @$(MAKE) -C $(PROJECT_DIR)/../$(NAME)-cov test REPORTER=dot
  @$(MAKE) -C $(PROJECT_DIR)/../$(NAME)-cov test REPORTER=html-cov > $(PROJECT_DIR)/coverage.html

.PHONY: install test test-cov cov
```

### 单元测试参考 

[实战Nodejs单元测试](http://fengmk2.github.com/ppt/unittest-and-bdd-in-nodejs-with-mocha.html)
