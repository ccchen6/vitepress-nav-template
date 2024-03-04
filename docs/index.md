---
layout: home
layoutClass: 'm-home-layout'

hero:
  name: U-POE
  text: Your Personal AI assistant !
  tagline: 您的专属私人大模型 ！
  image:
    src: /logo233.png
    alt: miao
  actions:
    - text: 大语言模型
      link: /fe/upoe/about

    - text: 爆款应用
      link: /nav

    - text: 工作助手
      link: /nav

    - text: 开发者
      link: /fe/upoe/dev
      theme: alt

features:
  - icon: 🔥
    title: 体验大语言模型
    details: ChatGPT<br>文心一言<br>智谱等二十多种国内外顶尖大模型
    link: https://upoe.cc # TODO
    linkText: Go
    theme: alt

  - icon: 🚀
    title: 爆款应用
    details: MidJourney文生图<br>AI宇宙<br><del>Sora文生视频</del>(支持中)<br>
    link: /nav # TODO
    linkText: Go
  - icon: 💡
    title: 工作助手
    details: PDF/Doc文档问答<br>党建助手<br>编程助手等工作效率助手一有尽有
    link: /nav
    linkText: Go
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
