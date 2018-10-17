---
title: 手摸手教你用node.js写后台api(3)
date: 2018-07-27 15:54:47
tags:
- node, express
categories: [javascript, node.js, express]
---

## 文中代码github地址 &nbsp;&nbsp;[[文中代码github地址]](https://github.com/daniel-lij/write-the-api-interface-with-node/tree/master/node-rest-shop)

## mongDB简单使用方法 &nbsp;&nbsp;[[mongDB简单使用方法]](https://coding.net/u/daniel-lij/p/daniel-lij-xmind/git/blob/master/mongodb.xmind)

> * 当然mongDB的使用还有很多,随便上百度查查都可以查出不少于几万篇文章。

#### 1.引入在app.js中引入mongoose

```javascript   
    const express = require('express')
    const app = express()
    const morgan = require('morgan')
    const bodyParser = require('body-parser')
    // 获得mongoose做数据库
    const mongoose = require('mongoose')

    const productRoutes = require('./api/routes/products')
    const ordersRoutes = require('./api/routes/orders')

    // 只需要自己启动mongdb之后创建一个shop的数据库就能连接上
    mongoose.connect('mongodb://localhost/shop')

    mongoose.Promise = global.Promise

    // 日志启动的名字dev
    app.use(morgan('dev'))

    // 对post请求进行解析
    app.use(bodyParser.urlencoded({ extended: false }));
    app.use(bodyParser.json());

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

#### 2.创建数据库之后,开始做数据模型api文件夹下models文件夹创建product数据模型

```javascript   
    const mongoose = require('mongoose')
    const productSchema = mongoose.Schema({
        // 创建唯一的id
        _id: mongoose.Schema.Types.ObjectId,
        // 创建name属性为必需属性
        name: {type: String, require: true},
        // 创建price属性为必需属性
        price: {type: Number, require: true}
    })
    module.exports = mongoose.model('Product', productSchema)
```

#### 3.修改路由文件routes/products.js

```javascript   
    const express = require('express')
    const router = express.Router()

    const mongoose = require('mongoose')
    const Product = require('../models/product') 

    router.get('/',(req, res, next)=>{
        Product.find().select('name , price , _id').exec().then(result =>{
            if(result.length > 0){
                const response ={
                    count : result.length,
                    products: result.map(doc =>{
                        return {
                            name: doc.name,
                            price: doc.price,
                            _id: doc._id,
                            request:{
                                type:'GET',
                                url: "https://localhost:3000/" + doc._id
                            }
                        }
                    })
                }
                res.status(201).json(response)
            }else{
                res.status(201).json({
                    message:'没有请求到数据'
                })
            }
        }).catch(err =>{
            res.status(500).json({
                error: err
            })
        })
    })

    router.post('/',(req, res, next)=>{
        // 存储请求的数据
        const product = new Product({
            _id: new mongoose.Types.ObjectId(),
            name: req.body.name,
            price: req.body.price
        })

        // 将请求的数据存储到数据库
        product.save().then(result =>{
            console.log(result)
            res.status(201).json({
                message:'this is products post req',
                content: {
                    name: result.name,
                    price: result.price,
                    _id: result._id,
                    request:{
                        type:'POST',
                        url: "https://localhost:3000/" + doc._id
                    }
                }
            })
        }).catch(err => {
            console.log(err)
            res.status(500).json({
                error: err
            })
        })
    })


    router.get('/:productId', (req, res, next)=>{
        const id = req.params.productId
        Product.findById(id).exec().then(doc =>{
            if(doc){
                res.status(200).json(doc)
            }else{
                res.status(404).json({
                    message: '没有找到有效Id'
                })
            }
        }).catch(err =>{
            res.status(500).json({
                error: err
            })
        })
    })


    router.patch('/:productId', (req, res, next)=>{
        const id = req.params.productId
        const updateOps = {}
        for(const ops of req.body){
            updateOps[ops.propName] = ops.value
        }
        console.log(updateOps)
        Product.update({_id: id}, {$set: updateOps}).exec().then(result =>{
            res.status(200).json(result)        
        }).catch(err =>{
            res.status(500).json({
                error: err
            })
        })
    })

    router.delete('/:productId', (req, res, next)=>{
        const id = req.params.productId
        Product.remove({ _id: id}).exec().then(result =>{
            res.status(200).json({
                message: '删除成功'
            })
        }).catch(err =>{
            res.status(500).json({
                error: err
            })
        })
    })

    module.exports = router
```