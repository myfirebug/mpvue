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

在相应的页面里增加main.json
```
{
  "navigationBarTitleText": "查询车牌号"
}
```

# 如何才能使用上拉加载下拉刷新
在相应的页面里增加main.json
```
"enablePullUpRefresh": true,  // 上拉加载
"enablePullDownRefresh": true // 下拉刷新
```

# 如何才能使用地图
在app.json里增加
```
"permission": {
    "scope.userLocation": {
      "desc": "你的位置信息将用于小程序位置接口的效果展示"
    }
  }
```

# 有没有遇到过同样的问题，在使用地图时，动态获取数据，不知为何第一进入地图界面，地图的数据没有，获取成功显示数据，但第二次进入时，数据已经存在，
导致，地图上的markers显示不出来，这里有一个hack,即在onUnload里把所有的数据置为初始值
```
onUnload () {
    this.list = []
    this.selectDetail = []
    this.markers = []
  }
```
