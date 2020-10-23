## 2020-09-11 周五

1. 熟悉  `open_english_latest` 项目的业务逻辑

2. 完善 `detail.js` 在有缓存的情况下跳转去 `lyrics.vue` 的逻辑

   字幕按钮的 `@click` 方法 `clickCaptionBtn()` 需要增加判断逻辑

   ```js
   //  如果当前缓存中的播放 url => this.currentAudio.url
   //  与当前页面中的字幕 url 不同 => this.lessonInfp.lesson_mp3
   //  则更新缓存中的 bigAudio 信息
   if(this.currentAudio.url && 
      this.currentAudio.url !== this.lessonInfo.lesson_mp3){
     this.lessonPlayAct()	//	调用播放按钮 @click 方法 lessonPlayAct()
   }
   ```

3. 听大佬们开周会，每周五都会有一个周会，然后今年安排以前端为主，明年可能会调整去服务端

## 2020-09-14 周一

1. 在 `weex_layout_server` 的历史 `commit` 中，在 `message` 为  `init` 里找到了跑通 `weex_layout_server` 的 `sql` 文件
2. 学习 `mac` 怎么安装、卸载 `mysql`
   - 安装：https://juejin.im/post/6844903831298375693
   - 卸载：https://chengchaos.github.io/2019/06/24/uninstall-mysql-osx.html
3. 熟悉 `weex_layout` 项目的业务逻辑

## 2020-09-15 周二

1. 在本地成功跑通了 `weex_layout_server`，其需要先连接远程数据库，然后再确保 `app/public` 中有相应的组件 （在 `app/service/web.js` 中有相应的初始化方法），因为开始其中组件被历史 `commit` 中给 `ignore` 掉了，所以今天在 `app/service/web.js` 新增了复制方法 `copyTemplate()` 方便后续自动从  `weex/src/components/HelloLanguage` 或 `weex/src/components/LiveClass` 中复制组件模版去 `app/public` 中

   ```js
   /**
     * 
     * @param {目标文件夹路径} templatePath: string 
     */
   async copyTemplateFile(templatePath){
   
     /**
       * 遍历目标文件夹复制目标文件夹下所有路径去 app/public 中
      	* @param {目标文件夹中的所有文件名} filenames：array
       */
     const copyFile = (filenames) => {
       filenames.forEach(filename =>{
         try{
           const filePath = path.join(templatePath, filename)
           fs.copyFile(filePath, path.join(this.app.baseDir,'app/public',filename))
         }catch(e){
           this.app.logger.error(`复制 ${filePath} 组件时出现异常`)
         }
       })
     }
   
     let templateFilenames = '';
     try{
       templateFilenames = fs.readdirSync(templatePath)
     }catch(e){
       return this.app.logger.error(`${templatePath} -> 该路径不存在`)
     }
   
     copyFile(templateFilenames)
   }
   ```

2. 修改 `weex_layout_server` 的“千变万化”失败，因为相应逻辑以被定好，还要兼容 `weex` 所以不能直接使用富文本组件

3. 修改 `weex_layout_server` 的“星星点灯”，只需要修改“星星点灯”的组件，还有 `web/src/user-component/UserComponent.js` 中相应的“星星点灯”的配置

   ```json
   "data": {
     "style": {
       "$name": "样式属性",
       "marginTop": {
         "val": "24",
         "info": "",
         "name": "顶部距离",
         "type": "string"
       },
       "paddingLR": {
         "val": "15",
         "info": "",
         "name": "左右距离",
         "type": "string"
       },
       "color": { "val": "#484848", "name": "行字体色", "type": "color", "predefineColors": ["#000", "#333333", "#405BD3"] },
       "fontSize": { "val": "16", "name": "大小", "type": "string" },
       "imgWidth":{ "name":"图标大小","val":"24", "type":"string"}	//	新增
     },
     "content": {
       "$name": "内容属性",
       "icon": {
         "name": "图标",
         "val": "https://www.flaticon.com/svg/static/icons/svg/1828/1828884.svg",	//	修改为图标路径
         "type": "upload-url",	//	从 string 修改为 upload-url
         "info": ""
       },
       "text": {
         "name": "文本",
         "val": "星星点灯",
         "type": "string",
         "info": ""
       }
     }
   }
   ```

   但是未能打包到 `weex` 上成功，初步判断为未修改打包修改服务器上的 `weex` 组件，因为需要调用服务器上的 `weex` 组件再根据相应的样式 `json` 进行生成

## 2020-09-16 周三

1. 修改 `weex_layout_server` 的”星星点灯“，发现  `weex` 的 `image` 组件无法加载 `svg` 类型的图片！昨晚判断的错误原因不全对，这个项目的流程是需要从远程数据库中读取每个组件字段为 `data` 中的配置文件，然后生成 `web/src/user-component/UserComponent.js`，储存每个组件的信息，再根据这个 `UserComponent.js` 然后遍历动态生成组件注入项目中，当然前提是昨天的将组建模版复制到相应的位置，详情看 **`2020-09-15`** 的 `1.`
2. 开始着手修改 `weex_layout_server` 的“内容保存功能“，需要使用 `vuex` 储存一个 `focusItemId`，然后在 `Furnace.vue` 页面中通过 `focusItemId` 去做系列操作，并设置了一个 3 秒的 `setInterval` 进行自动保存功能

## 2020-09-17 周四

1. 学习了一下用 `CSS` 写评分组件，新增了些奇怪的 `CSS` 知识，但是 `CSS` 选择器这块明天有空需要再总结归纳下
2. 完善了 `weex_layout_server` 的“星星点灯”，现在其可以选择基础图标，也可以自己上传图标并改变大小
3. 查询了一下关于 `JS` 货币转换的库，**`currency.js`、`accounting.js`、`Dinero.js` 有空可以看下**，`open_english_latest` 需要用到，重新写一个货币转换的算法用于展示不同地区的货币折扣
4. 尝试修改 `weex_layout_server` 的“千变万化”，想用富文本编辑器，但是发现 `weex` 不能随便使用 `<span>` 与 `v-html`，尝试失败

## 2020-09-18 周五

1. 学习了一下 `JS` 的 `forEach()` 相关的操作，新增了些奇怪的 `JS` 的知识，但是下周有空需要再总结归纳下

2. `open_english_latest` 之前的学习记录的页面一直 `Loading` 的 `Bug` 又复现了，重新修复了这个 `Bug`，原因是后台返回的一个字段应该是 `[]`，而他返回了 `null`，导致一系列对数组的操作都出现了异常，并且更新了控制 `Loading` 的逻辑

   ```js
   //	isResponse: []
   isResponse: [false, false], // 刚进入页面，还没有数据，暂时先不展示 no data 提示
   
   //	this.isResponse.splice(i, 1, true)	
   this.$set(this.isResponse, i, true)
   ```

## 2020-09-21 周一

1. 研究了下 `oh-my-zsh`
2. 学习了下 `CSS` 选择器和  `JS` 的 `forEach()` 这块相关的知识
3. 学习了下使用  `promise.then()` 第二个参数和 `promise.catch()`捕获错误有什么区别

## 2020-09-22 周二

1. 学习了下 `JS` 的类型转换这块相关知识
2. 学习了下数据结构
3. 做了几道力扣的题

## 2020-09-23 周三

1. 为 `gc_web` 项目做准备，拿到 `UI` 图与需求文档后对项目组件划分、交互问题进行了讨论
2. 开发 `gc_web` 的 `GcButton` 组件
3. 开发 `gc_web` 的 `GcCard` 组件
4. 开发 `gc_web` 的 `GcAvatar` 组件

## 2020-09-24 周四

1. 开发 `gc_web` 的 `GcUpload` 组件
2. 开发 `gc_web` 的 `GcLoading` 组件

## 2020-09-25 周五

1. 开发 `gc_web` 的 `ForGot` 页面
2. 开发 `gc_web` 的 `SignIn` 页面
3. 开发 `gc_web` 的 `SignUp` 页面

## 2020-09-27 周日

1. 开发 `gc_web` 的 `DiffText` 组件
2. 开发 `gc_web` 的 `DiffTextGroup` 组件
3. 开发 `gc_web` 的 `Pagination` 组件
4. 开发 `gc_web` 的 `Tag` 组件

## 2020-09-28 周一

1. 想把 `gc_web` 里的图片都转为 `base64`，但其实不一定能优化网页性能，`base64` 编码的机制**有空需要看下**，包括它的优点和缺点，不要滥用
2. 学习了下根据屏幕自适应那块 `CSS` 的单位知识，本来想用 `rem` 做自适应的，但还是要根据后续自适应的设计稿重新选择
3. 开发 `gc_web` 的 `LayoutHeader` 组件
4. 开发 `gc_web` 的 `default2` 布局组件

## 2020-09-29 周二

1. 块级元素没有办法使 `transform` 生效，`rotate` 要改变旋转中心可以用 `transform-origin` 设置，这块的机制**有空需要看下**
2. 开发 `gc_web` 的 `ToolBar` 组件
3. 修改基于 `default2` 布局组件的页面样式与 `LayoutHeader` 组件等系列组件

## 2020-09-30 周三

1. `flex` 布局会引起的子元素设置 `height: 100%` 失效，这块的机制**有空需要看下**
2. 开发 `gc_web` 的 `Check` 页面
3. 开发 `gc_web` 的 `Setting` 页面

## 2020-10-09 周五

1. 将 `weex_layout` 的“星星点灯”分离为新旧两版，并更新部署教程
2. 前后端联调 `gc_web` 的 `SignIn` 页面

## 2020-10-10 周六

1. 开发 `gc_web` 的 `UpdatePass` 页面
2. 修改 `gc_web` 的 `Setting` 页面样式

## 2020-10-12 周一

1. 修改 `toast` 样式
2. 研究了下在 `nuxt`使用 `vuex` 的操作，在 `nuxt` 的 `vuex` 中，可以通过 `this._vm` 拿到 `vue` 实例，再调用 `vue` 实例里面的方法进行相应的操作，比如 `this._vm.$toast()` 这种
3. 将 `user` 相关的接口都使用 `vuex` 整理了，明天对不同页面进行联调
4. `localStorage` 或者 `sessionStorage` 无法设置过期时间，可以选择在存储时将过期时间戳一起存进去，取的时候再校验时间，直接点可以选择用 `web-storage-cache` 这个库

## 2020-10-13 周二

1. 想用 `v-model` 绑定从 `vuex` 中异步获得值，可以使用 `mapGetters` 结合 `watch`，再在 `data` 定义字段，最后在 `watch` 中进行改变，同时要设置 `watch` 的 `immediate` 为 `true`（表示该回调将会在侦听开始之后被立即调用）

    ```vue
    data() {
      return {
        formData: {},
        head_img: '',
      }
    },
      watch: {
        userInfo: {
          immediate: true,
            handler(newVal, oldVal) {
            if (newVal) {
              this.formData = newVal
              this.head_img = newVal.head_img
            }
          },
        },
      },
    ```

2. `nuxt` 里使用路由拦截如果直接使用 `router.beforeEach` 或者使用组件独享守卫，都会有刷新白屏的问题，正确的使用路由守卫是使用**中间件**的方法，并且中间件支持异步操作

    在 `middleware` 中创建 `auth.js`

    ```js
    import { localCache, sessionCache } from '@/utils/cache'
    
    export default function ({ store, redirect }) {
      const isClient = process.client
    
      const { getters, dispatch } = store
      const userInfo = getters['user/userInfo']
    
      let userId
    
      // 在客户端
      if (isClient) {
        userId = localCache.get('_user') || sessionCache.get('_user') || null
      }
    
      // 未获取到，重定向到登陆页面
      if (userInfo === null) {
        if (userId === null) {
          redirect('/signin')
        } else {
          dispatch('user/getUserInfo', userId)
        }
      }
    }
    ```

    在要使用路由中间件的组件中配置：

    ```js
    export default {
      middleware: 'auth',
    }
    ```

    详情可以参考：

    - https://zh.nuxtjs.org/api/pages-middleware/
    - https://codesandbox.io/s/github/nuxt/nuxt.js/tree/dev/examples/auth-routes?from-embed=&file=/middleware/auth.js
    - https://blog.csdn.net/Py1807A/article/details/105380224

3. `nuxt` 路由守卫的前置和后置的实现方式：

    - 前置路由守卫：
        - 全局守卫：
            - `nuxt.config` 中配置 `middleware`
            - `layouts` 中配置 `middleware`
            - `plugins` 中使用 `beforeEach()`（不推荐）
        - 局部守卫：
            - 组件中配置 `middleware`
    - 后置路由守卫：
        - 全局守卫：
            - `plugins` 中使用 `afterEach()`
            - 使用 `vue` 的 `beforeRouteLeave()` 钩子

## 2020-10-14 周三

1. 将 `localStorage` 或 `sessionStorage` 里存储的 `userId` 使用 `CryptoJS` 加密了一下（简单做个伪安全处理）

    相关链接可以参考：

    - https://www.jianshu.com/p/a47477e8126a
    - https://juejin.im/post/6844903742370742279
    - https://juejin.im/post/6844904159875956750

2. `axios` 也可以监听上传进度和下载进度，只要在他的.`config` 传入一个 `onUploadProgress` 或 `onDownloadProgress` 回调即可

    ```js
    axios.post('/more/post',{
      onUploadProgress(progressEvent) {
        // 监听上传进度
      }
    })
    
    axios.get('/more/get',{
      onDownloadProgress(progressEvent) {
        // 监听下载进度
      }
    })
    
    
    ```

3. 开发 `Progress` 组件，但是还没有完善，目前只有 `line` 的样式，其他样式后续看需求或者时间再处理

## 2020-10-15 周四

1. 考虑了一下要不要做按钮的防抖，但还是选择做按钮的状态改变，后续如果有时间可以跟 UI 讨论下是否加入按钮的 loading 样式
2. 整理了一下之前遗留的学习问题
3. `gc_web` 自适应的问题后续需要处理
4. `gc_web` 感觉可以在上传图片的时候提供给用户图片裁剪的界面，然后确定再上传

## 2020-10-16 周五

1. 上传图片裁剪方案取消，因为太麻烦了，没有正确使 `object-fit` 这个属性生效，因为没有为 `img` 这个替换元素设置高度，这个问题已记录到 `CSS` 笔记中
2. 修复 `gc_web` 的部分 `Bug`
3. 对 `gc_web` 做了简单的自适应处理

## 2020-10-19 周一

1. 对 `gc_web` 进行多语言适配，但部分多语言文案产品并未给出，需要持续跟进
2. 构建 `mail_purchase` 项目，并学习 `Vue3.0`

## 2020-10-20 周二

1. 完善 `gc_web` 多语言适配，并修改部分表单样式
2. 初始化 `email_pruchase` 项目，并学习了一下 `tailwindcss` 这个 `CSS` 框架，体验之后决定不使用它进行本次项目

## 2020-10-21 周三

1. 开发 `email_purchase` 的 `Layout` 相关组件
2. 开发 `email_purchase` 的 `Course` 页面

## 2020-10-22 周四

1. 为 `email_purchase` 的 `Layout` 相关组件 与 `Course`页面进行响应式布局处理
2. 学习了一下关于 `classList` 相关操作，开始更多的考虑兼容性，`IE` 真是头疼。。

## 2020-10-23 周五

1. 填学校要求的那个什么毕业登记表和就业推荐表，填了一个早上。。
2. 对之前在项目中遇到的问题进行整理，发现越整理越多不懂东西出现，对 `classList` 这个属性有了更深的理解，还有就是 `in` 运算符和 `hasOwnProperty` 的作用与区别