---
layout: post
title: 记录那些年编程时踩过的坑
date: 2018-02-24 17:55:09.000000000 +09:00
tags: 编程||坑||DOM||移动端适配||小程序||登录态
---
1、Mobile端阻止DOM默认长按选中时，结果input框无法输入

解决办法：

```
*:not(input,textarea) {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -webkit-touch-callout: none;
}
```
2、第三方平台文档不详细，比如腾讯云的华北与上海地区选择

3、上传图片时本地展示缩略图：base64、FIleReader实例
```
let files = event.target.files;
let reader = new FileReader();
reader.readAsDataURL(files[0]);
reader.onload = (e) => {
    console.log(e);
    this.imgURL_raw = e.target.result
};
```
4、rem为单位解决移动端适配问题

5、box-sizing解决元素宽度问题

6、flex解决流媒体布局问题

7、小程序群ID解决同一群组用户识别的问题

8、小程序app.js和page.js状态不同步，用setInterval监听（vuex的作用）

9、为了与客户端作区分，需要在HTTP Header中加入自定义字段，以供后端识别为本公司H5访问的请求，用md5加密保证安全性

10、在后端不校验登录态时，前端自己研发了一套登录态及过期的办法（非安全不推荐）

登录时回调对本地记录：
```
//            设置过期时间
let expiryTime = new Date();
expiryTime.setHours(expiryTime.getHours() + 12);
console.log('过期时间', expiryTime);
localStorage.expiryTime = expiryTime;
```
跳转时校验：
```
router.beforeEach((to, from, next) => {
    // console.log('现在时间', new Date());
    // console.log('过期时间', localStorage.expiryTime);
    // 验证登陆状态
    if (localStorage.loginStatus === 'login' && new Date() < new Date(localStorage.expiryTime)) {
        if (to.path === '/login') {
            return next({ path: '/dataStatistics' });
        }
        next();
    } else {
        if (to.path !== '/login') {
            let app = new Vue();
            app.$message({
                type: 'info',
                message: '登录已过期，请重新登录'
            });
            return next({ path: '/login' });
        } else {
            next();
        }
    }
});
```