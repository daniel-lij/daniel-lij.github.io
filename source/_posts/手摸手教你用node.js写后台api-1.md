---
title: 手摸手教你用node.js写后台api(1)
date: 2018-07-25 15:40:16
tags:
- node
categories: [javascript, node.js]
---

## 文中代码github地址 &nbsp;&nbsp;[[文中代码github地址]](https://github.com/daniel-lij/write-the-api-interface-with-node/tree/master/node-rest-shop)

## restful风格编码简单流程图
![](/images/node/流程图.png)


#### 1.在文件夹中用cmd中初始化一个项目(推荐使用Conemu)

```javascript
    <!-- 产生一个package.json文件 -->
    npm init
```

#### 2.安装express,在根目录下创建一个server.js文件

```javascript
    npm install express
```

```javascript
    <!-- 引入node内置http中间件 -->
    const http = require('http')
    <!-- 导入app.js文件 -->
    const app = require('./app')

    <!-- 设置端口号, 环境变量中端口号 或者 自己设置的端口号 -->
    const port = process.env.PORT || 3000

    <!-- 用内置http中间件创造一个服务器,将app文件跑在node服务器上 -->
    const server = http.createServer(app)

    <!-- 监听端口 -->
    server.listen(port, ()=>{
        console.log(`数据库连接成功, 端口: ${port}`)
    })
```
#### 2.在根目录下创建一个app.js文件

```javascript
    // 引入express后端框架
    const express = require('express')
    const app = express()

    // 引入后端构造的商品products文件 
    const productRoutes = require('./api/routes/products')
    // 引入后端构造的订单orders文件
    const ordersRoutes = require('./api/routes/orders')

    // app中使用products和orders,并指定路径
    app.use('/products', productRoutes)
    app.use('/orders', ordersRoutes)

    module.exports = app
```

#### 2.在根目录下创建一个api文件夹,api文件夹下创建routes文件夹(这是后端路由),创建products.js,orders.js文件

> * 创建products.js文件,进行一些常规操作

```javascript
    // 引入express文件
    const express = require('express')
    // 使用express的路由方法
    const router = express.Router()

    // 比如获得商品
    router.get('/',(req, res, next)=>{
        res.status(200).json({
            message:'this is products get req'
        })
    })

    // 比如创建商品
    router.post('/',(req, res, next)=>{
        res.status(201).json({
            message:'this is products post req'
        })
    })

    // 比如获取商品的id
    router.post('/:productId', (req, res, next)=>{
        const id = req.params.productId
        if(id === "special"){
            res.status(200).json({
                message: 'this is products true id',
                id: id
            })
        }else{
            res.status(200).json({
                message: 'this is products error id',
                id: -1
            })
        }
    })

    // 比如修改商品id
    router.patch('/:productId', (req, res, next)=>{
        res.status(200).json({
            message: 'this is products patch'
        })
    })

    // 比如删除商品
    router.delete('/:productId', (req, res, next)=>{
        res.status(200).json({
            message: 'this is products delete'
        })
    })

    // 导出router
    module.exports = router
```

> * 创建orders.js文件,进行一些常规操作,意图同上

```javascript
const express = require('express')
const router = express.Router()

router.get('/',(req, res, next)=>{
    res.status(200).json({
        message:'this is orders get req'
    })
})

router.post('/',(req, res, next)=>{
    res.status(201).json({
        message:'this is orders post req'
    })
})

router.post('/:orderId', (req, res, next)=>{
    const id = req.params.orderId
    console.log(id)
    if(id === "123"){
        res.status(200).json({
            message: 'this is orders true id',
            id: id
        })
    }else{
        res.status(200).json({
            message: 'this is orders error id',
            id: -1
        })
    }
})

router.delete('/:orderId', (req, res, next)=>{
    res.status(200).json({
        message: 'this is orders delete'
    })
})

module.exports = router
```

#### 3.在cmd中node server.js运行后台服务进行测试
> * 在浏览器中进行测试 
##### http://localhost:3000/products

```javascript
    // 20180725165116
    // http://localhost:3000/products
    {
    "message": "this is get req"
    }
```
##### 但是很不幸的是浏览器只能测试get方法,不能测试post以及其他的方法所以测试后台api写的正确与否,一般使用postman进行测试
##### postman下载地址 &nbsp;&nbsp;[[postman下载地址]](https://www.getpostman.com/apps)

> * 在postman中测试
##### GET http://localhost:3000/products

![](/images/node/get.png)

##### POST http://localhost:3000/products

![](/images/node/post.png)

##### 带正确id POST http://localhost:3000/products/special

![](/images/node/带正确id.png)

##### 修改 PATCH http://localhost:3000/products/special

![](/images/node/patch.png)

##### 删除 DELETE http://localhost:3000/products/special

![](/images/node/delete.png)

#### 3.后端服务器进行热跟新,安装nodemon npm install --save-dev nodemon（--save-dev安装开发时依赖文件）

##### 修改package.json文件修改启动方式
```javascript
    {
    "name": "rest-shop",
    "version": "1.0.0",
    "description": "a describe",
    "main": "index.js",
    "scripts": {
        "test": "echo \"Error: no test specified\" && exit 1",
        // 增加这一句启动方式就可以了
        "start": "nodemon server.js"
    },
    "author": "daniel-lij",
    "license": "MIT",
    "dependencies": {
        "express": "^4.16.3"
    },
    "devDependencies": {
        "nodemon": "^1.18.3"
    }
    }
```
##### 在cmd中运行npm start后修改代码会从新启动服务
