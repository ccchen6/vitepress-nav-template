---
layout: home
layoutClass: 'm-home-layout'

hero:
  name: U-POE
  text: Your Personal AI assistant !
  tagline: 您的专有私人大模型 ！
  image:
    src: /logo233.png
    alt: miao
  actions:
    - text: 服务介绍
      link: /nav/
    - text: 使用介绍
      link: /nav/ #TODO
      theme: alt
    
features:
  - icon: 🌟
    title: 个人用户
    details: 使用说明<br>套餐说明
    link: https://upoe.cc # TODO
    linkText: 点击使用
  - icon: 🚀
    title: 开发者用户
    details: 开发者概述<br>API说明
    link: #TODO
    linkText: 点击使用
  - icon: 💡
    title: 社区分享
    details: 开放中，敬请期待
    link: #TODO
    linkText: 点击使用

---

<style>
/*爱的魔力转圈圈*/
.m-home-layout .image-src:hover {
  transform: translate(-50%, -50%) rotate(666turn);
  transition: transform 59s 1s cubic-bezier(0.3, 0, 0.8, 1);
}

.m-home-layout .details small {
  opacity: 0.8;
}

.m-home-layout .bottom-small {
  display: block;
  margin-top: 2em;
  text-align: right;
}
</style>
