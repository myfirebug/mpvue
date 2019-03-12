# mpvue全局引入sass文件

## 1、安装依赖：

``` bash
npm install --save-dev node-sass sass-loader
```

## 2、在.vue文件中的style节点加上lang=”scss”，这样就可以愉快地使用sass进行开发了，无需再webpack.base.config.js中配置loader，webpack会自动识别.scss文件以及.vue中的scss代码。

在使用sass开发的时候，我们经常需要在各个页面使用统一的变量，还有一些方法，这种公共的资源如果不进行特殊处理的话，我们在每个页面都需要@import进来才能使用，当页面多起来的话，后期维护起来可能会有点吃力，这里介绍在mpvue下如何处理sass全局引入sass资源文件的步骤：

1. 首先需要安装sass-resource-loader

``` bash
npm install --save-dev sass-resources-loader
```

2. 然后是找到工程根目录下的build/utils.js文件，我们新增一个loader对象,resources数组中，放的是需要引入的sass资源文件的路径

``` javascript
var sassResourceLoader = {
    loader: 'sass-resources-loader',
    options: {
      resources: [
        path.resolve(__dirname, '../src/assets/scss/settings.scss'),
      ]
    }
  }
```
3. 然后改写一下generateLoaders函数

```javascript
// generate loader string to be used with extract text plugin
  function generateLoaders (loader, loaderOptions, anotherLoader) {
    var loaders = [cssLoader, px2rpxLoader, postcssLoader]
    if (loader) {
      loaders.push({
        loader: loader + '-loader',
        options: Object.assign({}, loaderOptions, {
          sourceMap: options.sourceMap
        })
      })
    }

    if (!!anotherLoader) loaders.push(anotherLoader)

    // Extract CSS when that option is specified
    // (which is the case during production build)
    if (options.extract) {
      return ExtractTextPlugin.extract({
        use: loaders,
        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
  }
```

4. 最后我们把自定义加上的loader加上去

```javascript
return {
    css: generateLoaders(),
    wxss: generateLoaders(),
    postcss: generateLoaders(),
    less: generateLoaders('less'),
    sass: generateLoaders('sass', { indentedSyntax: true }, sassResourceLoader),
    scss: generateLoaders('sass', {}, sassResourceLoader),
    stylus: generateLoaders('stylus'),
    styl: generateLoaders('stylus')
  }
```

# 如何修改mpvue小程序 动态修改小程序每个页面的导航栏背景颜色、导航栏标题颜色、导航栏标题文字内容、窗口的背景色

```javascript
<template>
  <div>
    这里是html
  </div>
</template>

<script>
export default {
    created(){

    },
    onLoad(){
        wx.setNavigationBarTitle({
          title:'修改后的导航',
        })
        wx.setNavigationBarColor({
          frontColor:'#ffffff', // 前景颜色值，包括按钮、标题、状态栏的颜色，仅支持 #ffffff 和 #000000 (微信小程序官方规定)
          backgroundColor:'27ADFF' // 背景颜色值，有效值为十六进制颜色
        })
    }
}
</script>
```