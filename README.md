# BNTestdemo
Layabox2.x如何使用 bignumber.js 的例子

关于大数字使用，前面已经系统的介绍过这个，[大数字BigNumber.js使用](http://blog.asroads.com/post/5aa84949.html)  在Laya1.x的时候也使用这个类库，只是当时项目比较忙，加上我疏于记录，后面就不了了之，昨天下班后，群里一个群友问我Layabox2.x关于([bignumber.js](https://mikemcl.github.io/bignumber.js))使用报错，才有了今天这个文章。
<!--more-->
下面就说说这个类库如何在Layabox2.x里面使用，以及报错 Error: /Users/xxxx/my/test/laya/demo/BNTestdemo/src/script/GameUI.ts(27,22): semantic error TS2304: Cannot find name 'BigNumber'. 后如果修改

首先我们还是按照Layabox 正常的添加类库

### 步骤

#### 新建一个项目

这里新建了一个示例项目

![image-20200930120915849](README/image-20200930120915849.png)

#### 下载bignumber.js

去官方地址下载 ([bignumber.js](https://mikemcl.github.io/bignumber.js))到本地

![image-20200930121113814](README/image-20200930121113814.png)

由于项目选择的是Typescript项目所以我们接下来要先把声明文件放到 项目的 libs 下面

![image-20200930121252022](README/image-20200930121252022.png)

然后把 `bignumber.js`文件放到 bin下面的libs 并且去 LayaIDE里面设置 勾选类库

![image-20200930121438183](README/image-20200930121438183.png)

勾选类库

![image-20200930121555007](README/image-20200930121555007.png)

#### 项目内写测试代码

这里我们选择 GameUI.ts 文件的 onEnable 函数下写测试代码

```typescript
import { ui } from "./../ui/layaMaxUI";
import GameControl from "./GameControl"
import BigNumber from "../../libs/bignumber";
    
    ......
    ......
    
    onEnable(): void {
        this._control = this.getComponent(GameControl);
        //点击提示文字，开始游戏
        this.tipLbll.on(Laya.Event.CLICK, this, this.onTipClick);
        let bn = new BigNumber("10000")
        console.log(bn.valueOf())
    }
```

这个时候我们编译代码 发现报错！！！

![image-20200930121832803](README/image-20200930121832803.png)

```
开始编译: Wed Sep 30 2020 12:34:02 GMT+0800 (CST)
--------------------------------------
[12:34:03] Working directory changed to /Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app
[12:34:03] Using gulpfile ~/my/test/laya/demo/BNTestdemo/.laya/compile.js
[12:34:03] Starting 'compile'...
{ Error: /Users/smile/my/test/laya/demo/BNTestdemo/src/script/GameUI.ts(27,22): semantic error TS2304: Cannot find name 'BigNumber'.
at error (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup/dist/rollup.js:9402:30)
at Object.error (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup/dist/rollup.js:15621:24)
at Object.error (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup/dist/rollup.js:16065:38)
at RollupContext.error (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:17187:30)
at lodash_3 (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:24954:23)
at arrayEach (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:532:11)
at forEach (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:9360:14)
at printDiagnostics (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:24927:5)
at Object.transform (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup-plugin-typescript2/dist/rollup-plugin-typescript2.cjs.js:26760:17)
at Promise.resolve.then (/Applications/LayaAirIDE_2_7_0_beta.app/Contents/Resources/app/node_modules/rollup/dist/rollup.js:15768:25)
id: '/Users/smile/my/test/laya/demo/BNTestdemo/src/script/GameUI.ts',
hook: 'transform',
code: 'PLUGIN_ERROR',
plugin: 'rpt2' }
[12:34:06] Finished 'compile' after 2.82 s
--------------------------------------
编译结束，gulp编译耗时: 4.102s
编译结束，总耗时: 4.191s
```

这个 怎么回事？难道是路径错了？还是这样加载不对，经过两个小时的搜索和本地测试，发现了问题！

![image-20200930122052641](README/image-20200930122052641.png)

#### 解决方案

原来是声明文件 需要修改才行

原来的声明长这样：代码收缩后

![image-20200930122151362](README/image-20200930122151362.png)

应该修改为 代码收缩后

![image-20200930122242165](README/image-20200930122242165.png)

##### 主要操作 

1. 注释掉 第一行 // export default BigNumber;

2. 把 第二行的 export 修改为 declare

3. 删掉 export

然后去修改  GameUI.ts  因为类直接导出了，所以不需要 import BigNumber from "../../libs/bignumber";

##### 删除引用导入

```typescript
import { ui } from "./../ui/layaMaxUI";
import GameControl from "./GameControl"
......
......
    onEnable(): void {
        this._control = this.getComponent(GameControl);
        //点击提示文字，开始游戏
        this.tipLbll.on(Laya.Event.CLICK, this, this.onTipClick);
        let bn = new BigNumber("10000")
        console.log(bn.valueOf())
    }
```

我们再次编译 正常通过 

![image-20200930122616556](README/image-20200930122616556.png)

最后运行项目 查看输出

![image-20200930122702863](README/image-20200930122702863.png)

以上 bignumber.js 正常被项目引入 项目地址：https://github.com/jsroads/BNTestdemo.git

[点击进入](https://github.com/jsroads/BNTestdemo.git)

### 总结

问题不是什么大问题，但对第三方库不是很了解的，特别容易采坑，一不小心就很难解决。



### 参考链接

> 
>
> ```
> import YQBCommon = require("../../libs/pb2");
> ```
>
> 引用这个没必要，而且路径也是错的。删除这行，pb2.d.ts没问题后，即可正常使用。。
>
> pb2.d.ts和protobufjs.d.ts也有问题，别用export as namespace 用 declare module
>
> 代码已改。下载改后的项目你试试。
>
> - [myLaya2D.zip](https://ask.layabox.com/file/download/file_name-bXlMYXlhMkQuemlw__url-aHR0cHM6Ly9pbWcubGF5YWJveC5jb20vYW5zd2VyLzIwMTkwMjIwLzg4ZDQ3NzdkODQ1MGRjNDJiODNjNDliNzY1M2ZkYmNm)

[laya2.0引入第三方库失败](https://ask.layabox.com/question/41139?token=37_xuvr6CbtPl4GOvU9q4mU2A6kG4ZHstwtxm8nUwj-hRXN8pR_AymqEzjVplLLUsZazfveY_TbLzTD9wNJScU11fW7oIiWnuNSQJr2npebL5k)

