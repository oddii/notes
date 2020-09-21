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

1. 学习了一下用 `CSS` 写评分组件，新增了些奇怪的 `CSS` 知识，但是 `CSS` 选择器这块**明天有空需要再总结归纳下**
2. 完善了 `weex_layout_server` 的“星星点灯”，现在其可以选择基础图标，也可以自己上传图标并改变大小
3. 查询了一下关于 `JS` 货币转换的库，**`currency.js`、`accounting.js`、`Dinero.js` 有空可以看下**，`open_english_latest` 需要用到，重新写一个货币转换的算法用于展示不同地区的货币折扣
4. 尝试修改 `weex_layout_server` 的“千变万化”，想用富文本编辑器，但是发现 `weex` 不能随便使用 `<span>` 与 `v-html`，尝试失败

## 2020-09-18 周五

1. 学习了一下 `JS` 的 `forEach()` 相关的操作，新增了些奇怪的 `JS` 的知识，但是**下周有空需要再总结归纳下**

2. `open_english_latest` 之前的学习记录的页面一直 `Loading` 的 `Bug` 又复现了，重新修复了这个 `Bug`，原因是后台返回的一个字段应该是 `[]`，而他返回了 `null`，导致一系列对数组的操作都出现了异常，并且更新了控制 `Loading` 的逻辑

   ```js
   //	isResponse: []
   isResponse: [false, false], // 刚进入页面，还没有数据，暂时先不展示 no data 提示
   
   //	this.isResponse.splice(i, 1, true)	
   this.$set(this.isResponse, i, true)
   ```

