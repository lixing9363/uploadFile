## iView的使用

- 创建vue-cli项目

  ```shell
  vue create my-app
  ```

  ```shell
  npm install
  ```

  ```shell
  npm run serve
  ```

- 在vue项目下安装iview

  ```shell
  npm install  iview  --save
  ```

- 在main.js下引入

  ```javascript
  import Vue from 'vue';
  import VueRouter from 'vue-router';
  import App from 'components/app.vue';    // 路由挂载
  import Routers from './router.js';       // 路由列表
  import iView from 'iview';
  import 'iview/dist/styles/iview.css';    // 使用 CSS
  
  Vue.use(VueRouter);
  Vue.use(iView);
  
  // 路由配置
  const RouterConfig = {
      routes: Routers
  };
  const router = new VueRouter(RouterConfig);
  
  new Vue({
      el: '#app',
      router: router,
      render: h => h(App)
  });
  ```

  
