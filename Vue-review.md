### axios

![1561638318771](C:\Users\ZX50V\AppData\Roaming\Typora\typora-user-images\1561638318771.png)

~~~javascript
import axios from 'axios'
import { Message, Loading } from 'element-ui';
import router from './router'

let loading
function startLoading(){
  loading= Loading.service({
      lock: true,
      text: '拼命加载中...',
      background: 'rgba(0, 0, 0, 0.7)'
  });
}
function endoLoading(){
    loading.close()
}
//拦截器
axios.interceptors.request.use(config=>{
    startLoading()
    if (localStorage.eleToken) {
        //设置统一的请求头
        config.headers.Authorization=localStorage.eleToken
    }
    return config;
},function (error) {
    return Promise.reject(error)
})

// 响应拦截器

axios.interceptors.response.use(res=>{
    endoLoading()
    return res
},function (error) {
    endoLoading()
    Message.error(error.response.data)
    //获取错误状态吗
    const {status} =error.response
    if (status === 401) {
        Message.error("token已失效，请重新登录")
        //清除token
        localStorage.removeItem("eleToken")
        //跳转到登陆页面
        router.push('/login')
    }
    return Promise.reject(error)
})


export default axios;

~~~

### Vue 路由之间的传值

$router为路由的实例对象，可以调用一些函数， $route为路由的信息对象

+ query传值

~~~javascript
query传值特点通过path来实现传值
this.$router.push({
    path:'/index'
    query:{
    	username:res.data.username //假设之前进行了axios请求
	}
})
~~~



+ params传值

~~~javascript
params传值特点通过name来实现传值
this.$router.push({
    name:'index'
    params:{
    	username:res.data.username //假设之前进行了axios请求
	}
})
~~~

两者区别：

首先传值方式就不同，上述已经列出

其次query向请求里的get会将参数显示在url上

而params则向post则不会将参数显示在url上，但是在刷新页面的时候params容易丢参

所以具体情况具体使用

