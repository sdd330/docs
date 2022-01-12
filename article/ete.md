说到端对端测试，市面上的测试框架很多，比如：Selenium、Sikuli、puppeteer、rize、playwright 等，但说到`0代码`测试工具，可能又是少之又少，Selenium 虽然也支持自动好录制脚本，却不支持断言，对于需要断言操作，还是避免不了 coding,这就大大提高了自动化测试的入门门槛，为什么[eTest](https://onepiece-smile.github.io/docs/)能做到 `0代码`测试呢？

## 设计思路

我们发现 ete 测试，本质就是重复性操作之前的动作，如果我们把操作用例记录下来，下次自动回放上一次的动作，岂不是就可以实现自动化测试，那么怎么实现对项目操作的信息收集呢？把收集操作信息的脚本写进项目里？是个思路但不是最优解决方案，这样代码会入侵了项目，对项目造成不必要的污染，而且迁移性很差， 还有其他的解决方案吗？

我们根据这个思路，设想怎么在不入侵项目的基础上也能做到操作信息的收集，即能保证项目不受影响，顺着这个思路我们打起了浏览器的注意，测试的项目需要在浏览器里运作，那我们总不能写个浏览器吧！这个工程量太大了，而且也不一定能达到我们的需求；有没有一种介于浏览器与项目之间方式呢？我们想到了浏览器扩展，对于浏览器扩展，使用的时候打开，不使用的时候关闭，而且也不会对项目有任何影响，在确定了通过浏览器扩展收集操作信息后，我们开启了一些列的验证，做了很多测试用例分析，最终得到了满意的结果。

### chrome 插件信息收集流程图

![chrome.png](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/28b8e4d9a31b44d780b25240b240cd9c~tplv-k3u1fbpfcp-watermark.image?)

为了保证录制回放的准确性，在开始录制脚本信息前，请尽量使用无痕模式录入数据，或者清空历史数据，以免回放的时候受到历史缓存数据的影响导致回放过程操作数据不准确，导致测试结果异常。

我们通过开启扩展注入脚本到项目，监听项目的操作事件，收集操作信息，对于'点击'、‘文本框的改变’、’鼠标滚动‘等事件系统会默认收集信息，操作人员不需要再通过右键菜单添加事件，脚本会将每一步的操作发送消息给扩展，扩展将收到的信息保存在的内存中，同时扩展也会监听浏览器的前进、后退、刷新、以及 tap 页的切换。等待操作结束后，导出 json 脚本文件。

Chrome 插件下载方式 [eTest 插件商店下载](https://chrome.google.com/webstore/detail/etest/nkjmdclbdiljcaeepkclamgboojhdnhi?hl=zh-CN) 或者 [eTest github 下载](https://github.com/onepiece-smile/docs/releases)

### ete 测试流程

![etetest.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/594c322e80cc4375bc915ed7f511f7f6~tplv-k3u1fbpfcp-watermark.image?)

将刚收集好的操作信息，导入到测试工具中，ete 内置解析引擎，会自动解析每一条数据，最后生成测试结果，操作回放的测试用例都能实现，对于一些自动化测试需要对脚本信息需要二次处理，这就需要高级设置了，系统刚开始内置了一部分系统变量，也可以通过自定义生成所需的变量，对断言值或者输入数据进行处理，满足测试需求，如果变量也满足不了； 我们还提供扩展功能，对测试的每一步都可以控制，真正满足您的测试需求。

### 缺陷

由于 ete 测试的设计思想是先通过插件录制测试脚本，然后根据脚本回放测试，从而达到 0 代码自动化测试的目的，这就局限了它本身就无法支持移动端或者桌面端的测试能力。由于 ete 测试对产品验收、线上运行环境监控等，都是最佳运行场景，所以我们打算与工程化部署结合，扩大自动化测试的能力。

### 总结

ete 功能是我们开发时间最长的一个，也是功能最为复杂的，但是我们依旧不能覆盖所有侧测试流程，后续我们计划加大测试脚本力度，提高 ete 测试的场景覆盖率，提升测试质量，优化操作交互，对 etec 测试后续我们会继续功能迭代，真的打造一款 `0代码`的 ete 测试框架，请大家持续关注[eTest 的测试工具](https://onepiece-smile.github.io/docs/#/)。