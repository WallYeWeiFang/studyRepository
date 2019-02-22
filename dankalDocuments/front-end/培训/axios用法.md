# Axios用法

## 1.传输query-string参数(GET请求)

```javascript 

//bad 
axios.get('xxx?id=1&name=ivan')


// good
let data={
    id:1,
    name:'ivan'
}
axios({
    url:'xxx',
    params:data    
})

```


## 2.传输JSON数据(常见于POST、PUT请求)

> Axios默认POST请求,请求头**Content-Type为:application/json**

```javascript 
Axios({
    url:'xxx',
    data
})


```


## 3.传输普通的表单数据(一般为POST请求)

>普通的表单上传常见于POST请求,且**Content-Type为:x-www-form-urlencoded**

```javascript

 Axios({
     url:'xxx',
     data:'id=1&name=ivan' //实际上传输的格式就是query-string,如果参数多的话建议可以用一些序列化函数将JS对象转换成query-string格式
 })

```

## 4.传输特殊的表单数据，也就是支持二进制文件的表单数据(一般为POST请求)

>二进制文件一般包括图片，音频，excel表格等等,使用Axios上传二进制文件时必须显式得去声明请求头**Content-Type:multipart/form-data**

```javascript
// create a FormData object
let formData=new FormData()

//append parameters into this object
formData.append('token',token)
formData.append('file',file)

Axios({
    url:'xxx',
    data:formData //data的类型必须是FormData,否则无法携带二进制文件
})
```


## 5.Axios拦截器

```javascript

// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });

// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });

```