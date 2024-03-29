### 关于回车

今天在做搜索的时候想添加一个回车搜索，结果直接绑定键盘事件发现不好使，在下边是错误代码



> ```javascript
> <el-button  icon="el-icon-search" type="danger" style="width: 100%;text-align: center" @keyup.enter="search" @click="search">搜索</el-button>
> ```

+ 第一种
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
+ 第二种
加上native 注意！！！如果用了封装组件的话，比如element，这个时候使用按键修饰符需要加上.native
> ```javascript
> <el-button  icon="el-icon-search" type="danger" style="width: 100%;text-align: center" @keyup.native.enter="search">搜索</el-button>
> ```

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

### 关于Vuecli3的axios请求本地json数据与v-bind绑定本地资源的一些问题

访问静态资源只能在public文件夹下才可以，并且目前只能用get请求，post不会。

用v:bing或axios请求的时候，除非跨域，都会直接指定到localhost:8080/public下，所以不用在请求里面重复加 /public/

如果你请求本地数据通过其他路径，那么浏览器就会一直报404的错误，这就是我早上在做背景切换功能时卡住我的问题==

同样v-bind也一样
例如
~~~
<img :src="showImg" />

简写一下
export default({
     data(){
        return{
            showImg:"static/bg/0.jpg"         
        }
     }
})
~~~
如果是上述代码，在寻找showImg的地址就会自动区public文件下找static文件，如果有，则显示图片，没有则你会得不到图片
### 关于express
express框架中并没有解析post数据，所以你必须安装一个body-parser

### 关于vue-router
hash模式与history模式
hash模式是完整的url一般最后会跟这个#
history模式是将丑的url美化了，省略后面的# 但是如果没有配置404页面，或者是没跟后端交互，当用户搜索不存在的页面，就会返回404状态吗，这样交互就不太好



### 关于Vue中的keep-alive

往往在项目中，我们前往一个页面时，返回后仍希望之前页面数据还在

这个是后我们就需要用到**keep-alive**

> keep-alive属性
>
> include:字符串或正则表达式，只有名称匹配的组件会被缓存
>
> exclude字符串或正则表达式，任何名称匹配的组件都不会被缓存
>
> max:数字，最多可以缓存多少组件实例
>
> 以及新的2个生命周期的钩子函数
>
> actived:页面第一次进入的时候，钩子触发的顺序是created->mounted->activated
>
> deactived:页面退出的时候会触发deactivated，当再次前进或者后退的时候只触发activated

使用vue2.0中的keep-alive来缓存页面，导致每次编辑页面保存后返回到list页的数据不会实时更新

使用keep-alive后，**mounted生命周期函数不会每次都触发**，而vue新增了activated和deactivated函数，所以我们可以在activated函数里执行一次ajax请求，list页面就会实时更新了

