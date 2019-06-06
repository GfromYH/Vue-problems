### 关于回车

今天在做搜索的时候想添加一个回车搜索，结果直接绑定键盘事件发现不好使，在下边是错误代码



> ```javascript
> <el-button  icon="el-icon-search" type="danger" style="width: 100%;text-align: center" @keyup.enter="search" @click="search">搜索</el-button>
> ```

发现只有在点击该按钮获取焦点之后才回车才能触发键盘回车事件然后网上咨询了一下，正确写法应该先把他绑定在document上，然后就好啦，下边是正确写法

```javascript
        created(){

         this.docKeydown()
        },
            methods:{
              docKeydown() {
                document.onkeydown = (e) => {
                    let key = window.event.keyCode
                    if (key === 13) {
                        //search是自己定义的一个搜索功能就不写上去了
                        this.search()
                    }
                }
            },
          }
```



### 关于token

> cnpm install jsonwebtoken 签发token
>
> 项目中用到的验证token是passport passport-jwt
>
> cnpm install passport passport-jwt



- 在用户等录判断成功的地方加上

~~~javascript
                jwt.sign(rule,"secret",{expiresIn:20},(err,token)=>{
                    res.json({
                        success:true,
                        token:"Bearer " + token //这里Bearer 后面是有个空格的这是格式
                    })

                })
~~~



- 在node入口文件app.js中引入passport来验证token

~~~javascript
const passport=require('passport')

//passport初始化
app.use(passport.initialize());

require("./config/passport")(passport)
~~~



~~~javascript
const passport=require('passport')
//passport初始化
app.use(passport.initialize());
//将passport抽离出来
require("./config/passport")(passport)
~~~

- +

./config.passport中

~~~javascript
//npmjs 搜索passport
const JwtStrategy = require('passport-jwt').Strategy,
    ExtractJwt = require('passport-jwt').ExtractJwt;
const mongoose=require('mongoose')

const User=mongoose.model('User')
const opts = {}
opts.jwtFromRequest = ExtractJwt.fromAuthHeaderAsBearerToken();
opts.secretOrKey = 'secret';


module.exports=passport=>{
    passport.use(new JwtStrategy(opts, (jwt_payload, done)=> {
            User.findById(jwt_payload.id)
                .then(user=>{
                    if (user) {
                        return done(null,user)
                    }else{
                        return done(null,false)
                    }
                })

    }));
}

~~~

+ 然后再后台中需要请求获得数据的地方都加上如下代码

~~~javascript
// 根据当前用户获取信息
router.get('/getUser',passport.authenticate("jwt",{session:false}),(req,res)=>{
    res.json({
        id:req.user.id,
        username:req.user.username
    })
})
~~~

~~~javascript
passport.authenticate("jwt",{session:false}) //加上这条代码，就达到只有附带token才能请求的效果
~~~

