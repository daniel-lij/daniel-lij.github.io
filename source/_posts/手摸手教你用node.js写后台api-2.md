---
title: 手摸手教你用node.js写后台api(2)
date: 2018-07-26 11:33:26
tags:
- node, express
categories: [javascript, node.js, express]
---

## 文中代码github地址 &nbsp;&nbsp;[[文中代码github地址]](https://github.com/daniel-lij/write-the-api-interface-with-node/tree/master/node-rest-shop)

#### 1.全局错误处理
###### 在我们访问页面时有可能参数传递错误, 或者未知页面都有可能出现后端服务错误, 需要错误处理机制来处理这些问题。

```javascript
    const express = require('express')
    const app = express()

    const productRoutes = require('./api/routes/products')
    const ordersRoutes = require('./api/routes/orders')

    app.use('/products', productRoutes)
    app.use('/orders', ordersRoutes)

    // 添加错误处理机制(express中use写的顺序很重要,如果错误处理写道api逻辑代码前面,错误处理就会提前执行)
    app.use((req, res, next) =>{
        const error = new Error('Not Found')
        error.status = 404
        next(error) 
    })

    app.use((error, req, res, next) =>{
        res.status(error.status || 500)
        res.json({
            error:{
                message: error.message
            }
        })
    })

    module.exports = app
```

#### 2.安装日志记录工具npm install --save morgan

```javascript   
    const express = require('express')
    const app = express()
    // 记录日志中间件
    const morgan = require('morgan')

    const productRoutes = require('./api/routes/products')
    const ordersRoutes = require('./api/routes/orders')

    // 日志启动的名字dev
    app.use(morgan('dev'))

    app.use('/products', productRoutes)
    app.use('/orders', ordersRoutes)

    // 处理错误
    app.use((req, res, next) =>{
        const error = new Error('Not Found')
        error.status = 404
        next(error)
    })

    app.use((error, req, res, next) =>{
        res.status(error.status || 500)
        res.json({
            error:{
                message: error.message
            }
        })
    })

    module.exports = app
```

#### 3.安装body-parser处理post请求 npm install --save body-parser

> * 在app.js中引入body-parser

```javascript   
    const express = require('express')
    const app = express()
    const morgan = require('morgan')

    // 引入解析post请求中间件
    const bodyParser = require('body-parser')

    const productRoutes = require('./api/routes/products')
    const ordersRoutes = require('./api/routes/orders')

    app.use(morgan('dev'))

    // 对post请求进行解析
    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(bodyParser.json());

    app.use('/products', productRoutes)
    app.use('/orders', ordersRoutes)

    app.use((req, res, next) =>{
        const error = new Error('Not Found')
        error.status = 404
        next(error)
    })

    app.use((error, req, res, next) =>{
        res.status(error.status || 500)
        res.json({
            error:{
                message: error.message
            }
        })
    })

    module.exports = app
```

> * 修改products.js文件的post做测试
```javascript   
    // 修改post文件
    router.post('/',(req, res, next)=>{
        var product = {
            name: req.body.name,
            weight: req.body.weight
        }
        res.status(201).json({
            message:'this is products post req',
            content: product
        })
    })
```

> * postman测试图片

![](/images/node/body-parser.png)

#### 4.设置一些res相关参数,支持跨域

```javascript 
    const express = require('express')
    const app = express()
    const morgan = require('morgan')
    const bodyParser = require('body-parser')

    const productRoutes = require('./api/routes/products')
    const ordersRoutes = require('./api/routes/orders')

    app.use(morgan('dev'))

    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(bodyParser.json());

    // 设置res.header相关的设置(然后做具体的业务处理)
    app.use((req, res, next) =>{
        res.header('Access-Control-Allow-Origin', '*')
        res.header('Access-Control-Allow-Headers',
        'Origin, x-Requested-With, Content-Type, Accept, Authorization'
        )
        if( req.method === 'OPTIONS') {
            res.header('Access-Control-Allow-Methods', 'PUT, POST, PATCH, DELETE, GET')
            return res.status(200).json({})
        }
        next()
    })

    app.use('/products', productRoutes)
    app.use('/orders', ordersRoutes)

    app.use((req, res, next) =>{
        const error = new Error('Not Found')
        error.status = 404
        next(error)
    })

    app.use((error, req, res, next) =>{
        res.status(error.status || 500)
        res.json({
            error:{
                message: error.message
            }
        })
    })


    module.exports = app
```

> * postman测试图片

![](/images/node/body-parser.png)

> * postman测试响应参数

![](/images/node/res-header.png)

