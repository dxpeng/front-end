# front-end
前端知识点随记

#### Axios封装
```
import axios from 'axios'
import {Message} from 'element-ui'

//请求拦截，可添加token等统一请求头信息
axios.interceptors.request.use(config => {
	//Do something before request is sent
    return config;
}, err => {
	// 全局提示
    Message.error({message: '请求超时!'});
})

//响应拦截
axios.interceptors.response.use(data => {
		if (data.status && data.status == 200 && data.data.status == 500) {
			Message.error({message: data.data.msg});
			return;
		}
		if (data.data.msg) {
			Message.success({message: data.data.msg});
		}
		return data.data;
	}, err => {
		if (err.response.status == 504 || err.response.status == 404) {
			Message.error({message: '服务器被吃了⊙﹏⊙∥'});
		} else if (err.response.status == 403) {
			Message.error({message: '权限不足,请联系管理员!'});
		} else if (err.response.status == 401) {
			Message.error({message: err.response.data.msg});
		} else {
			if (err.response.data.msg) {
				Message.error({message: err.response.data.msg});
			}else{
				Message.error({message: '未知错误!'});
			}
		}
})
```

#### 方法封装
```
let base = '';

export const postRequest = (url, params) => {
  return axios({
    method: 'post',
    url: `${base}${url}`,
    data: params,
    headers: {
      'Content-Type': 'application/json'
    }
  });
}

export const putRequest = (url, params) => {
  return axios({
    method: 'put',
    url: `${base}${url}`,
    data: params,
    headers: {
      'Content-Type': 'application/json'
    }
  });
}

export const deleteRequest = (url) => {
  return axios({
    method: 'delete',
    url: `${base}${url}`
  });
}

export const getRequest = (url) => {
  return axios({
    method: 'get',
    url: `${base}${url}`
  });
}
```

#### main.js入库文件中注册到Vue全局属性
```
import {postRequest} from "./utils/api";
import {putRequest} from "./utils/api";
import {deleteRequest} from "./utils/api";
import {getRequest} from "./utils/api";
Vue.prototype.getRequest = getRequest;
Vue.prototype.deleteRequest = deleteRequest;
Vue.prototype.putRequest = putRequest;
Vue.prototype.postRequest = postRequest;
```

#### 组件中使用
```
this.postRequest("/doLogin", this.user).then(msg=>{
  if (msg) {
    //登录成功，页面跳转
  }
})
```

#### 跨域请求转发
```
vue-cli2配置

请求转发在 vue 项目的 config/index.js 文件中配置如下

proxyTable:{
	"/":{
		target: "http://localhost:8088",
		changeOrigin: true,
		pathRewrite:{
			"^/":""
		}
	},
	'/ws/*': {
        target: 'ws://127.0.0.1:8088',
        ws: true
      }
}



vue-lic3配置：

let proxyObj = {};
proxyObj['/ws'] = {
    ws: true,
    target: "ws://localhost:8081"
};
proxyObj['/'] = {
    ws: false,
    target: "http://localhost:8081",
    changeOrigin: true,
    pathRewrite: {
        '^/': ''
    }
};
module.exports = {
    devServer: {
        host: 'localhost',
        port: 8080,
        proxy: proxyObj
    }
}

```

#### 发布生产请求跨域问题
```
1. 方案一
前端编译打包后放到spring-boot项目的resources/static/目录下，
再由后台打包一并部署。

2. nginx代理转发

```



