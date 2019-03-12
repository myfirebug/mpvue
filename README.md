# mpvue全局引入sass文件

## 1、安装依赖：

``` bash
npm install --save-dev node-sass sass-loader
```

## 2、在.vue文件中的style节点加上lang=”scss”，这样就可以愉快地使用sass进行开发了，无需再webpack.base.config.js中配置loader，webpack会自动识别.scss文件以及.vue中的scss代码。