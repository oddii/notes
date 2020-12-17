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

## 2020-10-26 周一

1. 搜索了一下关于 `React Native` 的教学视频，找到一个在 B 站很少人看的白嫖版 `Meng To` 大牛的教学，跟着他一步步搭建环境学习 `React Native`
2. `React Native` 的中文文档版本太旧了，跟着他搭的东西虽然能跑起来，但是效率太慢，还是得多看英文文档，英文文档也推荐使用 `expo` 脚手架创建 `React Native` 项目

## 2020-10-27 周二

1. 肚子依旧不是很舒服，上了好几次厕所，晚上吃饭都蒸蛋加生菜了
2. 跟着 `Meng To` 的教学视频学了一天的 `React Native`，感叹 `React Native` 在很多方面的便捷性，就是这个用 `styled-components` 写 `CSS` 的方式要多习惯熟悉

## 2020-10-28 周三

1. 继续学习 `React Native`，将第一阶段都学完了，开始接触 `Animated` 的效果
2. 使用 `Contentful` 和 `graphql` 进行模拟数据并获得数据进行填充

## 2020-10-29 周四

1. 学习了很多的 `Animated` 效果，基本上要有动画效果的话就要使用 `Animated.createAnimatedCompoennt` 对需要动画效果的组件进行包裹，然后再配合自定义的动画
2. `React` 的 `useState` 和 `useSelector` 同步异步问题明天要研究一下，因为这两个 `hooks` ，`Projects` 有两个 `Bug` 要修改，还要将 `Project` 的关闭按钮处理的更好，现在只使用 `opacity` 变为 0，这个元素还说会存在于 `Project` 上，还是会触发点击事件，这个交互体验会变差

## 2020-10-30 周五

1. 对 `useState` 无法即时修改值的这个问题进行了搜索，找到一种解决方案就是

    ```js
    useState(state => setState(newState))
    ```

    这样可以即时反馈 `state`  的值，但是具体是什么原理还需要有空整理一下

2. 昨天发现的那个 `useSelector` 获取 `action` 的问题应该不是 `useSelector` 的问题，应该是 `React Native` 自身的 `PanResponder` 在 `create` 之后就只能获取一次外部的值，在  `componentWillMount` 这个生命周期使用 `PanResponder.create()` 好像能解决这个问题，但是函数组件中这个生命周期已被废弃，在 `stackoverflow` 找到解决方案！永远滴神！

## 2020-11-2 周一

1. 开始对后台公告管理项目 `backstate_notice` 进行开发评估和了解接口，选择使用 `Antd Pro` 进行一把梭！
2. 本来想用 `ts` 进行开发，但是看了一下 `ts` 对快速熟悉 `Antd Pro` 有点费时间，还是选择先用 `js` 弄个 `demo` 出来，再在 `review` 的时候转回 `ts`

## 2020-11-3 周二

1. 学习了一下 `ts` 的基本操作
2. 今天把公告列表的查询和 `Antd Pro` 的 `ProTable` 与 `ProForm` 的 `api` 熟悉了一下，由于 `Antd Pro` 之前没有用过，还是有不少新的坑要踩
3. 学习了下 `umi hooks` 这个库，决定使用 `useRequest` 进行网络请求访问

## 2020-11-4 周三

1. 基本将公告列表目前除搜索外的功能给做完了，明天优化下细节的问题就准备重构一个 `ts` 项目了

## 2020-11-5 周四

1. 将公告列表使用 `ts` 重构了，目前没有什么大问题，但是还有那个**时间戳和选择语言需无法重复的问题需要处理一下**
2. 上 `figma` 和 `drri~ ` 搜索了一下毕设的 UI 设计，找到了几个还不错的，明天建个项目试试

## 2020-11-6 周五

1. 开始写毕设的前端，学习怎么自定义 `zipper` 底部的 `tabbar`，但是发现图标组件 `@expo/vector-icons` 遇到了玄学的找不到字体文件问题，重新建几个项目都会有几率找不到字体文件。。差不多给搞了一整天
2. 将后台公告项目丢上了 `GitLab`，**后期需要学习一下 `Git Flow` 这个东西**

## 2020-11-9 周一

1. 后台公告项目新增删除公告功能
2. 将编辑公告使用多语言的时候，已选语言不会再出现的需求进行优化，现在调整和旧后台的表现一致
3. 请教惠威将后台公告项目部署上测试环境，**后面要整理一下部署流程**

## 2020-11-10 周二

1. 请假回学校毕业证照片采集

## 2020-11-11 周三

1. 请假回学校毕业证照片采集

## 2020-11-12 周四

1. 开发 `zipper` 的 `TabBar` 组件
2. 开发 `zipper` 的 `HomeScreen`

## 2020-11-13 周五

1. 对了一下邮件购买项目的获取商品数据接口，太麻烦了，接口文档也没有，下周继续联调好了
2. 开发 `zipper` 的 `StudioScreen`
3. 开发 `zipper` 的 `ProfileScreen`

## 2020-11-16 周一

1. 开始对接邮件购买项目的获取商品信息接口
2. 开发 `zipper` 的 `ActivityScreen`、`AttentionScreen`、`LikedScreen`、`MessageScreen`，感觉写了一天这个- -，本来想看一下文章看看别人怎么实现 `TabView` 滑动吸顶效果，结果发现已经有人写好了，但是还需要作者 `Review`，赶紧的呀

## 2020-11-17 周二

1. 基本明白邮件购买项目获取商品怎么玩，基本信息已对接好，等笃志把 `vuex` 配置好就能基本完成对接
2. 开发 `zipper` 的 `NotificationScreen`，`LoginScreen`、`QuickLoginScreen`、`RegisterScreen`、`ForgotPasswordScreen`
3. 将 `open_english_latest` 的分词解释中的收藏按钮点击无反应的 `Bug` 修复了，原来是很久之前没有导入 `showProgress` 却用了它，尴尬这么久才被发现，究竟有没有人用这个收藏的。。

## 2020-11-18 周三

1. 将 `LoginScreen` 系列需要用到的 `InputText` 封装成了一个带 `Icon` 的 `AnimatedIconText` 组件
2. 了解了一下正则中的捕获组和非捕获组的区别，**后面要自己整理一下**
3. 将邮件购买项目的遍历推荐列表后，根据 `hp_userId` 顺序请求的商品详情的方法改成并发请求，确实并发网络请求要比代码里的两个嵌套循环要好点
4. 学习了一下 React Native 的 `Bottom Modal` 怎么写（做了一篇阅读理解。。）

## 2020-11-19 周四

1. 将邮件购买项目获取推荐列表即商品信息的方法统一使用 `vuex` 进行管理
2. 学习了一下 `React Native` 的下拉刷新动画处理
3. 学习了一下 `React Native` 的进度条动画效果
4. 了解了一下 `Github` 自己的 `readme.md` 的效果，**以后有空可以自己写一下好玩的 `readme`**

## 2020-11-20 周五

1. 学习了一下关于  `React` 的 `useRef` 这个 `hook` 的操作，理解更深刻了一下，其在`function` 组件内可以保存值而不会被 `re render` 的时候重新初始化，不但可以对 `dom` 进行引用 `reference`，还可以保存一些需要使用的实例对象，还使用其模拟 `class` 组件的 `componentDidUpdate`，如下：

    ```react
    const hasMounted = useRef(false)
    
    useEffect(() => {
      if(hasMounted.current){
        // do some thing
      } else {
    		hasMounted.current = true    
      }
    })
    ```

    **以后有空可以整理一下这篇文章**：https://medium.com/javascript-in-plain-english/react-useref-can-be-used-for-more-than-you-think-d4cfe7d90797

2. 邮件购买项目根据公众号 id 获取商品详情信息的方法里，原本使用的
   
   ```js
   Object.assign(
     this.recommend.find((el) => el.hp_userid === item.hp_userid),
     data[0]	//	需要数据
   )
   ```
   
   这样改变数组某个原先对象的值，即如下：
   
   ```
   this.arr[index] = newValue
   ```
   
   在 `vue2` 中不会被监听到，导致不会重新渲染页面的数据，要用 `$set` 或者 `vue2` 封装的数组 `splice` 方法对原数组进行处理，如下：
   
   ```js
   this.$set(this.recommend, index, {
     ...this.recommend[index],
     ...data[0], //	需要数据
   })
   
   // or
   
   this.recommend.splice(index, 1, {
     ...this.recommend[index],
     ...data[0], //	需要数据
   })
   console.log(this.recommend)
   ```
   
3. 将邮件购买项目的多语言进行了配置

## 2020-11-21 周一

1. 邮件购买项目购买后商品信息展示逻辑调整

2. 研究了一下 `zipper` 的 `bottomBar` 能否重写样式，但是要用到一个第三方库，较为麻烦，还是算了

3. 研究了一下关于 `React Native` 的 `FlatList` 的上拉加载数据大概需要什么操作，并用 `react-persist` 这个库结合 `redux`、`react-redux` 对 `FlatList` 的数据进行持久化，写了一个 `demo`，并整理了一下，原来之前一直以为自己没用到定义 `action`，但是直接用 `useDispatch` 这样

    ```js
    dispatch({
       type: "UPDATE_LIST_RESULTS",
       items: list,
    });
    ```

    ```js
    ({
      type: "UPDATE_LIST_RESULTS",
       items: list,
    })	
    ```

    这个东西就是一个 action，可以将它封装到 `action` 中统一管理，如：

    ```js
    import { UPDATE_LIST_RESULTS } from "../../constants/actions";
    
    export const updateListResults = (items: any) => ({
      type: UPDATE_LIST_RESULTS,
      items,
    });
    ```

    这样外面就只要调用这个方法返回 `action` 对象即可

    ```js
    dispatch(updateListResults(list));
    ```

## 2020-11-22 周二

1. 研究了一下 `braintree` 填银行卡后校验错误的逻辑，`braintree` 的错误校验需要在提交后用一堆回调函数进行捕获，也可以使用 `await`配合 `try/catch` 对错误进行捕获
2. 调整了下后台公告项目的代码，将一些看的懂的，脚手架生成的不用的代码给删掉了，然后在公告字段新增了一个客户端状态字段
3. 深入学习了一下 `memo`、`useCallback`、`useMemo` 的使用方式，**有空要自己整理一下那个博主的文章**
4. 把邮件购买项目的一些小 `Bug` 和笃志调了一下

## 2020-11-23 周三

1. 将邮件购买项目的默认多语言逻辑调整了一下，将除了配置的多语言外其他全部默认为 `en`，并调整了一下折叠的代码
2. 邮件购买项目在本地做了一个对商品购买后的缓存，以便马上区分是否已购买过
3. 在判断 `{} !== {}` 的时候，忘记这两个是对象比较，比较的是他们的地址，返回的肯定是 `true`，如果需要判断一个对象是不是 `{}` 时，可以用 `Object.key(obj).length === 0` 去判断，因为 `Object.key(obj)` 会将 `{}` 变为 `[]`，此时再用数组的 `.length` 去判断即可
4. 将后台公告项目的添加、修改公告时的默认语言调整为英文
5. 将邮件购买项目的缓存使用 `web-storage-cache` 设置了一个三分钟的过期时间，并且添加了一个字段判断用户是否登陆后再拿商品信息
6. 将邮件购买项目的展示图片高度样式调整了一下，并用公司的 `cdn` 把样式文件直接引入项目，后来又改了回去，因为查出是台湾 `tailwind` 自动加了一个清洗 `CSS` 的插件把其他样式文件干掉了

## 2020-11-24 周四

1. 将邮件购买项目的价格配合后端进行了一下转换处理

2. 回顾了一下之前浏览的网页，发现使用 `React Hooks` 去模拟生命周期的时候，会将可以用

    ```react
    const useComponentWillMount = (func) => {
      const willMount = useRef(true)
      
      if(willMount.current) func()
      
      willMount.current = false
    }
    
    //	or
    
    const useComponentWillMount = (func) => {
      useMemo(func, [])
    }
    ```

    引起了一个思考，然后自己又去写了个 `demo` 发现，`useMemo` 和 `useCallback` 的执行都会比 `useEffect` 要快，所以 `useEffect` 是用来模拟 `componentDidMount` 等，**这个是为什么以后还要研究一下**

    然后还对 `useCallback` 和 `useMemo` 的执行顺序研究了一下，发现是根据 `JS` 从上到下执行的，而且

    ![image-20201126174647755](/Users/hellotalk/Library/Application Support/typora-user-images/image-20201126174647755.png)

    这里可以看到

    - `useCallback` 默认会返回一个函数，所以是用来**缓存函数的引用**
    - `useMemo` 不会默认返回，可以指定返回一个值，所以是用来**缓存计算量较大的结果值**

## 2020-11-25 周五

1. 电脑重装系统丢失这部分记录

## 2020-11-26 周六

1. 电脑重装系统丢失这部分记录

## 2020-11-30 周一

1. 电脑重装系统，需要重新安装开发环境
2. 将开发环境与常用软件进行配置

## 2020-12-1 周二

1. 将开发环境与需要用的软件用一个文档进行记录，只要拿到新系统后直接打开文档按步骤操作即可，存储到了自己 `Github` 的 `dotfiles` 仓库中
2. 大概了解了一下 `weex` 的编写方式、注意事项与写了一下 `HL` 首页改版项目的一部分样式

## 2020-12-2 周三

1. 将 `HL` 首页改版项目的基本样式写完了，但是设计师跟我说这版还要改？？
2. `HL` 首页改版项目有个需求需要将 `<list>` 组件 的 `<header>` 置顶时，改变 `<header>` 中的 `navbar` 的背景颜色，可以在 `navbar` 上添加一条 1 `px` 的透明的 `<cell>` 并监听它的 `onappear` 和 `ondisappear` 事件，然后改变 `navbar` 的背景颜色
3. 归纳了一下之前 `JS` 获取 `div` 高度的方法

## 2020-12-3 周四

1. 研究 `HL` 首页改版的样式实现，`Android` 还是无法修改顶部状态栏的样式，但是 `iOS` 可以用 `position: relative` 结合 `top: -44px` 覆盖状态栏，明天结合 `<list>` 一起试试样式
2. 和笃志一起整理了一下 `HL` 首页原来的代码

## 2020-12-4 周五

1. 开发 `HL` 首页改版项目样式
2. 跟进了一下午的 `HL` 下架黑五出现的 `Bug`，其中笃志把中国用户的 `isFreeTrail` 条件在下架黑五的时候去掉了，然后就是后台配置出现了问题，无法从 `config_data` 中拿到 `month` 这个字段，导致计算月份价格的时候无法显示，展示错误

## 2020-12-7 周一

1. 对 `HL`  首页改版项目进行样式的调整，基本样式已经开发完了，但是后端还没有开发完接口
2. 封装 `zipper` 的 `BottomModal` 和 `ResultModal`
3. 将 `zipper` 的 `LoginScreen` 系列页面样式进行微调
4. 将 `zipper` 的 `redux`、`react-redux`、`redux-persist` 使用 `typescript` 进行整合并整理

## 2020-12-8 周二

1. 将 `HL` 首页改版项目进行样式的调整，并根据后端返回数据结构 `mock` 了基本数据
2. `HL` 首页改版项目测试评审，对交互逻辑进行探讨，现在首页不做播放操作，需要点击播放按钮然后跳转到详情页再播放
3. 浏览了一下文章，但是感觉没有什么太有用的信息获取

## 2020-12-9 周三

1. 归纳了一下 `JS` 判断空对象的方法
2. 基本整了一天的 `HL` 首页改版项目新引导页蒙层，本来想用鑫大佬的 `border` 镂空方案去做蒙层，但是发现 `weex` 的 `box-sizing` 全是 `border-box`，无法使用这个方案，最后没办法，只能复制多一份 `DOM` 元素进蒙层中。。
3. 配置 `HL` 首页改版项目的多语言，但是韩语和日语没有给出，还有些文案没有多语言提供

## 2020-12-10 周四

1. 配置 `HL` 首页改版项目的多语言，第一次见识到阿拉伯语和波斯语的语法，太恐怖了全是右边读起来
2. 将 `HL` 首页改版项目的部分组件封装了一下，将 `home.vue` 的代码简洁了一些，并小调了一下 `UI` 样式
3. 配置 8 周年海报活动项目的多语言

## 2020-12-11 周五

1. 终于开始 `HL` 首页改版的联调，但是接口返回还是有问题，需要调整一下
2. 新建一个 `h5_single_page` 的分支，专门用来存放简单的 `H5` 页面，并写了 `GC` `H5` 的分享页，手动撸了一个多语言适配方案
3. 记录一下猫哥提供的 `H5` 的尺寸适配方案，太好用了完美适配手机页面

## 2020-12-14 周一

1. 将 `HL` 首页改版项目除了学习时长以外的改动都放到测试环境去了
2. `HL` 首页改版学习时长写了两个方法尝试了一下，但是还需要整理一下在哪里开始统计和结束统计
3. 重新整理了一下 `zipper` 的代码，准备过阵子不忙的时候开始重新写一下

## 2020-12-15 周二

1. 封装了一下  `zipper`  的 `Intro` 组件，并将其与 `CollapsibleTabView` 组件整合了一下，现在已经可以搞定了
2. 昨天写的统计 `HL` 首页改版学习时长的方法好像有点问题。。将代码又还原了没有改动的时候
3. 看了一下 `Medium`，并没有什么收获什么营养的东西
4. 8 周年海报活动项目那个姓名栏带加号的问题，想用 `URLSearchParams` 去解决的，发现 `weex` 没有这个 `api`。。只能用简单粗暴的正则匹配加号然后去掉的方法了

## 2020-12-16 周三

1. 上午都在处理 `HL` 首页改版的列表背景的小瑕疵，但是无法解决，下午产品说每个等级至少有五个课程包，所以这个问题应该不大
2. 和谢为开了个会，反应了近况，并将 `HL` 首页改版后能测的东西都用测试熟悉的 `xmind` 文件提交了
3. 开始改 `HL` 首页改版的 `Bug`，原来引导页是分两种显示而不是分步走

## 2020-12-17 周四

1. 继续修 `HL` 首页改版的 `Bug`，把大部分 `Bug` 都干掉了
2. 浏览了下邮件与 `Medium` 的文章，了解到 `PDF` 原来是一种矢量图，收藏了几个图标的网站，其他没有什么好记录的
3. 修 `HL` 首页改版的时候遇到一个从详情页播放返回首页，暂停时不会上报学习时长的问题，和笃志调了一个下午，原因是 **明天过来写**