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
请求转发在 vue 项目的 config/index.js 文件中配置如下

proxyTable:{
	"/":{
		target: "需要请求的域",
		changeOrigin: true,
		pathRewrite:{
			"^/":""
		}
	}
}

```



