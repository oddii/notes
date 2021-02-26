## Odd 刘宇樑（实习）工作交接

### 语伴天天练

- 该项目位于 `single_page` [^1] 中的 `/src/page` 目录下的 `exclusive_lang_partner.vue`

- 里面使用的组件都放在了 `/src/components/ExclusiveLang` 目录下

- 其中的 `/src/components/ExclusiveLang/Calendar.vue` 日历组件，用到的算法放在 `/src/formatDate.js` 中，这个组件是从飞猪 `WeexUI` 的日历组件改造过来的，所以有算法有什么问题可以谷歌看看

- 项目中有一个放视频的组件，是使用 `weex` 自带的  `web` 组件，加载一段自己写的 `html` 代码[^2]（里面只有一个 `video` 组件），所以其实是用 `H5` 的 `video` 组件播放的视频（为了抹平 `Android` 与 `iOS` 的表现差异），每次要修改视频或者其他需求就要先修改上述中的 `html` 代码再重新上传公司 `cdn`，再修改代码中 `web` 组件的 `src`，当然您有什么更好的方案可以将这个实现方式替换掉～

- 语伴天天练项目目前只有 2 个页面，但其实是用 `v-if` 判断从后端获得的 `isPay` 字段进行控制显示页面

- 该项目展示页面都用的前端写死的数据

- 该项目支付模块都是调用的客户端原生提供的方法[^3]，其中 `iOS` 判断是否购买是否支付成功采用了曲线救国的方式，因为谢为同学还未有空重写有关的接口，这段代码需要好好看下，可能会有点绕，可以直接在代码中搜 `支付成功`，查看 `iOS` 与 `Android` 不同的判断支付成功逻辑

- 该项目上线时，需要在终端输入 `yarn build:prod` 进行打包，`single_page` 已配置了阿里云上传插件，在打包时将每个 `/src/page` 里的页面分别上传到公司 `cdn`，需要在打包后终端输出的信息中找到相应的 `js` 链接：

  ![image-20210225120011719](/Users/odd/Library/Application Support/typora-user-images/image-20210225120011719.png)
  
  然后打开[公司新后台](https://station3.hellotalk.org/htmall/fastadmin/public/index.php/admin/index/index)，菜单栏的**Weex活动管理**中找到**模版配置**：
  
  <img src="/Users/odd/Library/Application Support/typora-user-images/image-20210225120309777.png" alt="image-20210225120309777" style="zoom:53%;" />
  
  点击**操作栏右侧**的搜索按钮：
  
  ![image-20210225120434163](/Users/odd/Library/Application Support/typora-user-images/image-20210225120434163.png)
  
  在活动名称中输入**ht_language_partner**，然后点击**Submit**，即可找到语伴天天练的模版配置记录：
  
  ![image-20210225120557020](/Users/odd/Library/Application Support/typora-user-images/image-20210225120557020.png)
  
  点击操作中的**笔形符号**，可打开**详细操作面板**：
  
  ![image-20210225120755121](/Users/odd/Library/Application Support/typora-user-images/image-20210225120755121.png)
  
  在**详细操作面板**中，将从终端复制的语伴天天练打包后的 `js` 复制替换**活动链接**，再点击**OK**，即可更新线上文件
  
  <img src="/Users/odd/Library/Application Support/typora-user-images/image-20210225120942547.png" alt="image-20210225120942547" style="zoom: 33%;" />
  
  你以为这就上线完了？
  
  接着需要在公司新后台的**左侧搜索框**中输入**caidan**，找到**公众号菜单列表**并点击：
  
  <img src="/Users/odd/Library/Application Support/typora-user-images/image-20210225121535534.png" alt="image-20210225121535534" style="zoom: 50%;" />
  
  同样点击**操作栏**中的**搜索按钮**，然后在**用户Userid**中输入**15087**[^ 4]，并点击**Submit**，找到相应记录，然后再点击记录中的**笔形符号**，打开操作面板，将**weex_res**中的内容先复制一份出来，再代码中将每一个 `"name"` 为 `"home"` 的对象中的 `"url"` 的 `js` 链接替换为刚刚在终端打包后新的语伴天天练页面的 `js` 链接：
  
  ![image-20210225122314493](/Users/odd/Library/Application Support/typora-user-images/image-20210225122314493.png)
  
  再点击**OK**，即可更新线上文件，到这就真的上线完了
  
- **注意：上线到不同环境需要在正式/测试后台进行更新线上文件**

### 邮件直达购买

- 该项目位于 `mail_purchase` [^1] ，是一个纯 `web` 项目
- 该项目某些地方尝试使用了 `vue3` 的语法与 `tailwind.css`，并接入了 `braintree` 的 `cdk` 作为支付模块，支付模块相关的请咨询谭笃志同学与谢为同学
- 该项目的 `/src/views/Login` 页面在新的需求[^ 5]中已废弃，改为使用 `/src/views/SignIn` 页面登陆，但在我离职时因为后端接口未能提供完成，所以还需要根据需求文档继续开发
- 该项目的多语言映射文件在 `/src/locales` 目录中，使用 `vue-i18n` 进行多语言开发，您应该懂得
- 该项目已配置了阿里云上传插件，在终端使用 `yarn build` 后，将打包出来的 `html` 文件发给罗玉/谢为同学，由服务端同学部署上正式/测试环境即可

### WebGC

- 该项目位于 `gc_web` [^1] ，是一个纯 `web` 项目
- 该项目某些地方尝试使用了 `tailwind.css` 与 `nuxt.js`
- 该项目的多语言映射文件在 `/src/locales` 目录中，使用 `vue-i18n` 进行多语言开发，您应该懂得
- 该项目已配置了阿里云上传插件，在终端使用 `yarn build` 后，将打包出来的 `html` 文件发给李娜同学，由服务端同学部署上正式/测试环境即可

### 社区防骗指南

- 该项目位于 `h5_single_page` [^1] 中的 `anti_fraud_reminder` 目录下的 `anti_fraud_reminder.html`
- 该项目部署上线的方式需要在文件进行修改后，然后发给黄惠威同学，由服务端同学部署上正式/测试环境即可

### 动态发布技巧

- 该项目位于 `h5_single_page` [^1] 中的 `moments_posting_skills` 目录下的 `moments_posting_skills.html`
- 该项目部署上线的方式需要将这个 `html` 文件上传到公司 `cdn`，然后将链接发给付雨婕同学，由运营同学进行配置

### HEP 首页改版

- 该项目需要谭笃志同学直接进行对接，因为HL项目涉及的分支有点多



### 脚注解释：

[^1]: `single_page`、`mail_purchase`、`gc_web` 、`h5_single_page`都是 [HelloTalk 前端仓库](https://github.com/BLHT/frontend_manager) 中的一个分支
[^2]: 这段 `html` 代码上传在公司 [cdn](https://ali-hk-cdn.hellotalk8.com//upload/exc_web_video_1.html) 中，可以下载下来修改
[^3]: [Weex客户端接口(客户端注入到Weex的函数)](https://wiki.hellotalk8.com/confluence/pages/viewpage.action?pageId=509837636) 这个页面中搜 `payUtils`
[^ 4]: 语伴天天练的公众号id为**15087**
[^ 5]: [HL_邮件直达购买需求V1.1.0](https://wiki.hellotalk8.com/confluence/pages/viewpage.action?pageId=549991607)

