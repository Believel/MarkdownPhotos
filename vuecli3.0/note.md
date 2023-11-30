# 资料
1. [Vue3官网](https://cn.vuejs.org/)  
2. [Vue CLI脚手架](https://cli.vuejs.org/zh/) Vue脚手架
3. [pinia](https://pinia.vuejs.org/zh/) 状态管理库
4. [vue-router](https://router.vuejs.org/) 路由库

5. [better-scroll](https://better-scroll.github.io/docs/zh-CN/) 滚动库
6. [vue3-lazy](https://github.com/ustbhuangyi/vue3-lazy) 图片懒加载
7. [create-keyframe-animation](https://github.com/HenrikJoreteg/create-keyframe-animation) 用js创建动画

# Vue3知识点
1. 自定义指令
  * loading
  * no-result
2. 组合式函数封装
  * use-scroll
  * use-slider
  * use-fixed
  * use-shortcut
  * use-animate
  * use-cd
  * use-lyric
  * use-middle-interactive
  * use-mini-slider
  * use-mode
3. 插槽
  * `<slot></slot>`
4. 父子级页面传参
  * 父 -> 子：`prop`
  * 子 -> 父：`emit`
5. 组合式使用如何获取自定义组件的属性和方法
  * 需要在封装自定义组件中通过`defineExpose`把需要的方法和属性暴露出去，才能在外部调用
6. transition 过渡动画使用
  * 方式1：内置组件：
  ```js
  <transition name="move"></transition>
  ```
  * 方式2：js钩子使用：
  ```js
  <Transition
  @enter="onEnter"
  @after-enter="onAfterEnter"
  @leave="onLeave"
  @after-leave="onAfterLeave"
>
  <!-- ... -->
</Transition>
  ```
7. 生命周期
```js
onMounted(() => {
  <!-- 组件挂载完成之后执行 -->
})
onUnmounted(() => {
  <!-- 组件卸载完成之后执行 -->
})
```
8. Teleport
  * `<teleport></teleport>` 是一个内置组件，它可以将一个组件内部的一部分模版”传送“到该组件的DOM结构外层的位置去
9. 组件 `v-model`
  * 组件定义
  ```js
  // search-input.vue
  <script setup>
  import { computed, defineProps, defineEmits } from 'vue'

    const props = defineProps({
      modelValue: String
    })

    const emit = defineEmits(['update:modelValue'])

    const query = computed({
      get () {
        return props.modelValue
      },
      set (value) {
        emit('update:modelValue', value)
      }

    })

    </script>

    <template>
      <div class="search-input">
        <input
          class="input-inner"
          v-model="query"
        />
      </div>
    </template>
  ```
  * 组件使用
  ```js
  <script setup>
  import { ref } from 'vue'
  import SearchInput from '@/components/search/search-input'

  const query = ref('')
  </script>
  <template>
    <div class="search">
      <div class="search-input-wrapper">
        <SearchInput v-model="query"></SearchInput>
      </div>
    </div>
  </template>

  ```

# 服务器部署总结
> 自己购买的阿里云服务器，操作系统是`CentOS 8.5`

1. 安装服务器上需要的软件：`node`、`git`、`pm2`、`nginx`
2. 服务器管理控制台开放需要的端口：我这里开放的是`9000`端口
3. 用`nginx`来设置后端代理
  * 进入主配置文件`/etc/nginx/nginx.conf`
  ```js
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  _;
        root         /usr/share/nginx/html;

        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        location / {
        }
        <!-- ++++++++++++  start +++++++++++++++ -->
        location /music/  {
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_set_header Host $http_host;
                proxy_set_header X-Nginx-Proxy true;
                proxy_pass http://127.0.0.1:9000/;
        }
      <!-- ++++++++++++++ end ++++++++++++++++++++++ -->
  }
  ```
  这里的意思是在主配置文件中，当我们访问`域名:80/music/`时会代理到`域名:9000/`上，这里的`域名:9000/`地址就是我们项目中要部署的地址

4. 项目中需要修改的地方
  * 4.1 前端 Vue3 项目
  ```js
  // vue.config.js
    publicPath: process.env.NODE_ENV === 'production' ? '/music/' : '/'
  ```
  * 4.2 前端访问接口地址修改
  ```js
  // service/base.js
  const baseURL = process.env.NODE_ENV === 'production' ? 'http://47.109.78.127/music/' : '/'
  ```
  * 4.3 增加打包及发布命令
  ```js
  // package.json
  "deploy": "yarn run build && pm2 start backend/prod.server.js"
  ```
5. 用法
  5.1 在服务器上下载了项目`/usr/xxx`之后，进入项目文件夹然后运行
  ```js
  yarn
  npm run deploy
  ```


# 总结

![组件](./组件.png)
![技术](./技术.png)
