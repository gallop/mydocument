# vue 项目 在nginx子目录部署

## 1、vue 配置
>说明：因为要部署到nginx的子目录下，所以vue项目base 为子目录的名字，这里名字为 ：bigears  

配置路径，使得部署vue项目能正确获取到静态资源（js，css等），在项目工程的webpack.prod.conf.js文件中，在output 添加publicPath参数，如下：
```
output: {
    publicPath: '/bigears/',
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/[name].[chunkhash:8].js'),
    chunkFilename: utils.assetsPath('js/[name].[chunkhash:8].js')
  },

```
>注：网上说要改config目录下的index.js中build项的 assetsPublicPath为 bigears 但我测试了没效果，仍然无法正确获取静态资源，所以此处默认为：

```
build: {
    // Template for index.html
    index: path.resolve(__dirname, '../dist/index.html'),

    // Paths
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',

    /**
     * You can set by youself according to actual condition
     * You will need to set this if you plan to deploy your site under a sub path,
     * for example GitHub pages. If you plan to deploy your site to https://foo.github.io/bar/,
     * then assetsPublicPath should be set to "/bar/".
     * In most cases please use '/' !!!
     * assetsPublicPath: './',
     */
    assetsPublicPath: './',

    /**
     * Source Maps
     */
    productionSourceMap: false,
    // https://webpack.js.org/configuration/devtool/#production
    devtool: 'source-map',

    // Gzip off by default as many popular static hosts such as
    // Surge or Netlify already gzip all static assets for you.
    // Before setting to `true`, make sure to:
    // npm install --save-dev compression-webpack-plugin
    productionGzip: false,
    productionGzipExtensions: ['js', 'css'],

    // Run the build command with an extra argument to
    // View the bundle analyzer report after build finishes:
    // `npm run build:prod --report`
    // Set to `true` or `false` to always turn it on or off
    bundleAnalyzerReport: process.env.npm_config_report || false,

    // `npm run build:prod --generate_report`
    generateAnalyzerReport: process.env.npm_config_generate_report || false
  }

```

修改router目录下的index.js，配置如下：

```
export default new Router({
  mode: 'history', // require service support
  scrollBehavior: () => ({ y: 0 }),
  base: '/bigears/',
  routes: constantRouterMap
})

```

>说明：主要修改mode 和 base两个参数。

## 2、修改nginx配置
在nginx/conf.d/下新建配置文件 www.conf,内容如下：
```
server {
    listen       80;
    server_name  www.mygallop.cn;

    #charset koi8-r;
    #access_log  /var/log/nginx/host.access.log  main;
    root /opt/app;
    location / {
        add_header Access-Control-Allow-Origin *;
        add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
        add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
        index index.html index.htm;
    }
    location /bigears {
        #proxy_pass http://192.168.2.125:8080;
        #alias  /opt/app/bigears;
        index index.html index.htm;
        try_files $uri $uri/ /bigears/index.html last;
    }
    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   /usr/share/nginx/html;
    }

}

```

>说明：配置主要有两点，  
第一、配置vue的跨域访问权限
第二、将所有以bigears开头的连接都重定向到 /bigears/index.html页面，因为vue项目工程的入口为 /bigears/index.html ,如果不配置  
try_files $uri $uri/ /bigears/index.html last;  
则打开vue页面后刷新会出现404错误。

## 3、解决线上部署vue HTML5 History 模式，刷新页面出现空白的问题

>问题描述：当vue项目以History模式线上部署是，直接按F5刷新，会出现  
Uncaught SyntaxError: Unexpected token <错误，  
因为浏览器是无法识别 vue-router 的路径的，解决的办法是配置historyApiFallback参数

具体如下：
在vue项目中的webpack.prod.conf.js文件，添加historyApiFallback参数，详细如下：
```
output: {
    publicPath: '/bigears/',
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/[name].[chunkhash:8].js'),
    chunkFilename: utils.assetsPath('js/[name].[chunkhash:8].js')
  },
  //HTML5 History 模式,如下配置，当浏览器刷新才能正常返回数据
  // 注意：index的前缀需要和output下的publicPath一致，publicPath为/bigears/,
  //则index: '/bigears/index.html'
  devServer: {
    historyApiFallback: {
      index: '/bigears/index.html'
    }
  },

```

>说明：output 的publicPath必须配置项目部署的子路径，比如部署在nginx 的 bigears目录下，访问地址为http://www.mygallop.cn/bigears.则配置为/bigears/
另外historyApiFallback中的index的前缀需要和output下的publicPath一致。  
另外devServer 要和output同级。
