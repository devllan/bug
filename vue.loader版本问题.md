# 在做webpack+vue的demo时 #
# 遇到的一个vue-loader版本问题 #
在demo的时候遇到报错：

![](https://i.imgur.com/nQGoO6H.png)

`vue-loader was used without the corresponding plugin. Make sure to include VueLoaderPlugin in your webpack config.`
*没有相应的插件就使用了vue-loader。 确保在webpack配置中包含VueLoaderPlugin。*


因为刚开始开发的时候用的是vue-loader15+的版本，所以在项目中加了以下代码：

    const {VueLoaderPlugin} = require('vue-loader')


    plugins: [
    new VueLoaderPlugin()
    ],


按照在文档给出的解决方案来说，加入上面的代码就可以解决15+版本的问题，可是在实际demo中，还是会报最开始的错误，

所以决定降低版本为14+：

    npm i vue-loader@^14.2.2

安装成功后，npm run dev， 成功运行
![](https://i.imgur.com/tEB1huV.png)

