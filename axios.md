# 安装
npm install axios  --save

# 引用
import axios from 'axios'

# get请求
```js
// 不带参数的
axios.get(url).then(function (response) {
  console.log(response)
}).catch(function (error) {
  console.log(error);
});

// 带参数的，参数使用对象定义，{ params: { ID: 123, name: test } }
axios.get(url, {
  params: {
    ID：12345
  }
}).then(function (response) {
  console.log(response)
}).catch(function (error) {
  console.log(error);
});
```

# post请求
```js
axios.post(url,{
  name: 'Lee',
  pwd: '123456'
}).then( function (res) {
  console.log(res);
}).catch( fucntion (err) {
  console.log(err);
})
```

# 执行多个并行请求
```js
function getUserAccount(){
  return axios.get('/user/12345');
}
function getUserPermissions(){
  return axios.get('/user/12345/permissions');
}
axios.all([getUserAccount(),getUserPermissions()]).then(axios.spread(function (acct,perms)))
```

# axios详情说明

## 使用then时，将得到以下响应
```js
axios.get(url).then( function (res) {
  console.log(res.data,
              res.status,
              res.statusText,
              res.headers,
              res.config)
});
```


# 配置
## 配置默认值
### 可配置全局axios默认值
```js
// 全局配置
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```

### 自定义实例配置
```js
//在创建实例时设置配置默认值
var instance = axios.create（{
   baseURL：'https://api.example.com'
}）;
 
//在实例创建后改变默认值
instance.defaults.headers.common ['Authorization'] = AUTH_TOKEN;
```

### 配置的优先级
请求config参数 > 自定义实例的defaults > lib/defaults.js库默认值
```js
//使用库提供的配置默认值创建实例
//此时，超时配置值为`0`，这是库的默认值
var instance = axios.create（）;
 
//覆盖库的超时默认值
//现在所有请求将在超时前等待2.5秒
instance.defaults.timeout = 2500;
 
//覆盖此请求的超时，因为它知道需要很长时间
instance.get（'/ longRequest'，{
   timeout：5000
}）;
```

## 配置拦截器
截取请求或响应在被 then 或者 catch 处理之前
```js
// 添加请求拦截器,
axios.interceptors.request.use（function（config）{
     // 在发送请求之前做某事
     return config;
   }，function（error）{
     // 请求错误时做些事
     return Promise.reject（error）;
   }）;
 
// 添加响应拦截器
axios.interceptors.response.use（function（response）{
     // 对响应数据做些事
     return response;
   }，function（error）{
     // 请求错误时做些事
     return Promise.reject（error）;
   }）;

// 在实例上使用拦截器
var instance = axios.create();
// 增加拦截器
var myInterceptor = instance.interceptors.request.use( function () { /*...*/ })
// 删除拦截器
axios.interceptors.request.eject(myInterceptor);
```

# axios API
可以通过相关配置传递给axios来进行请求

## axios(config)
```js
// 默认是get请求
axios(url);

// post请求
axios({
  method: 'post',
  url: 'xxx',
  data: {
    firstName: 'Lee',
    lastName: 'Dee'
  }
})
```
