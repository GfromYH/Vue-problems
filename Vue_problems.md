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

