[TOC]

# 1.项目debugger调试

[egg官网教程](https://eggjs.org/zh-cn/core/development.html#%E6%97%A5%E5%BF%97%E8%BE%93%E5%87%BA)

[vscode的另外一种模式](https://segmentfault.com/a/1190000021259583)---暂未调试成功

- 最近做egg和vue的项目,发现在egg代码中写无法进行debugger调试，很不方便。

## 1.方案1

1. 在egg项目的package.json文件中

   ```js
       "debug": "egg-bin debug",
   
   ```

   

2. 命令行运行npm run debug

   ![image-20210109163903805](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20210109163903805.png)

3. 在谷歌浏览器访问 `chrome://inspect`配置端口号

   ![image-20210109164119080](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20210109164119080.png)

4. 点击Open dedicated DevTools for Node
5. 会打开控制台跟html5调试的控制台是一样的
6. 在egg代码中需要断电的地方写debugger,此时就可以正常使用debugger了

## 2.方案2

- 直接在vscode中进行调试

- 点击调试按钮配置launch.json文件

```js
{
  "version": "0.2.0",
  "configurations": [{
    "name": "Launch Egg",
    "type": "node",
    "request": "launch",
    "cwd": "${workspaceRoot}",
    "runtimeExecutable": "npm",
    "windows": {
      "runtimeExecutable": "npm.cmd"
    },
    "runtimeArgs": [
      "run",
      "debug"
    ],
    "console": "integratedTerminal",
    "protocol": "auto",
    "restart": true,
    "port": 9999,
    "autoAttachChildProcesses": true
  }]
}
```

- 这里有个坑，官方给的端口号是9229，但是运行调试不成功，不知道原因，这里改成9999端口号就可以调试成功了
- 可以在代码中写debugger或者是打小红点，然后访问下使用到的接口，vscode就可以正常进入到debugger中

![image-20210109204153864](/Users/zhangxiaoning/Library/Application Support/typora-user-images/image-20210109204153864.png)

# 2.信息输出





