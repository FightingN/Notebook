# 知识总结

[翻译](https://github.com/CavsZhouyou/Front-End-Interview-Notebook)

vscode配置自己

{

  "files.associations": {

​    "*.cjson": "jsonc",

​    "*.wxss": "css",

​    "*.wxs": "javascript"

  },

  "emmet.includeLanguages": {

​    "wxml": "html"

  },

  "minapp-vscode.disableAutoConfig": true,

  "[javascript]": {

​    "editor.defaultFormatter": "vscode.typescript-language-features"

  },

  "editor.fontSize": 16,

  "eslint.codeAction.disableRuleComment": {},

  "[html]": {

​    "editor.defaultFormatter": "vscode.html-language-features"

  },

  "dart.openDevTools": "flutter",

  "files.autoSave": "afterDelay",

  "javascript.updateImportsOnFileMove.enabled": "always",

  // 对EasyLess的配置，此段配置去掉则默认生成一个css文件

  "less.compile": {

​    "compress": false, //是否压缩

​    "sourceMap": false, //是否生成map文件

​    "out": true, // 是否输出文件，false为不输出

​    "outExt": ".wxss" // 输出文件的后缀,小程序可以写'wxss'

  },

  "cssrem.rootFontSize": 112,

  "editor.formatOnSave": true

}

贺年的

{

  "files.associations": {
    "*.cjson": "jsonc",
    "*.wxss": "css",
    "*.wxs": "javascript"
  }, 

  "emmet.includeLanguages": {
    "wxml": "html"
  },

  "minapp-vscode.disableAutoConfig": true,

 // vscode默认启用了根据文件类型自动设置tabsize的选项
  "editor.detectIndentation": false,
  // 重新设定tabsize
  "editor.tabSize": 2,
  // #每次保存的时候自动格式化 
  "editor.formatOnSave": true,
  //  #让函数(名)和后面的括号之间加个空格
  "javascript.format.insertSpaceBeforeFunctionParenthesis": true,
  // // #这个按用户自身习惯选择 
  "vetur.format.defaultFormatter.html": "js-beautify-html",
  // #让vue中的js按编辑器自带的ts格式进行格式化 
  "vetur.format.defaultFormatter.js": "vscode-typescript",
  "vetur.format.defaultFormatterOptions": {
    "js-beautify-html": {
      "wrap_attributes": "force-aligned",
      // #vue组件中html代码格式化样式
    },
  },
  "editor.codeActionsOnSave": {
    // #每次保存的时候将代码按eslint格式进行修复
    "source.fixAll.eslint": true,
    "eslint.autoFixOnSave": true,
  },


  "[html]": {
    "editor.defaultFormatter": "HookyQR.beautify"
  },
  "eslint.codeAction.showDocumentation": {
    "enable": true
  }
}

