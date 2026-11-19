# 测试数据

[results.json](results.json) 收录了 Lumina 的浏览器实测、页序更新后的六条分流请求，以及一组上游单测的结果。

## Lumina 浏览器实测

同一份公开页面用 Microsoft Edge 和 Playwright 测了两次。第一次打开原页面，第二次打开局部修正的副本。两次都在离线环境里用回退字体渲染。视口模拟只调整宽高。[公开页面](https://github.com/pbakaus/impeccable/tree/main/demos/landing-demo) · [公开测试任务](https://github.com/pbakaus/impeccable/blob/main/docs/openai-plugin-submission.md)

| 视口 | 横向溢出 | 首屏主按钮底边 | 最短导航点击高度 |
| --- | ---: | ---: | ---: |
| 1440 × 900 | 0 → 0 | 712 → 716 | 22 → 44 |
| 768 × 900 | 0 → 0 | 576 → 576 | 22 → 44 |
| 375 × 812 | 330 → 0 | 781 → 512 | 22 → 44 |
| 320 × 700 | 385 → 0 | 882 → 592 | 22 → 44 |

单位为 CSS 像素。按钮底边以视口顶部为原点。原样例的 10 个链接使用占位地址。本次也量了主内容地标数量和首次 Tab 是否匹配焦点可见状态。

复测时从公开样例下载 `index.html`，用 JSON 里记录的 SHA-256 核对版本。在临时副本上使用页序执行「保留品牌和内容，修复窄屏导航溢出、主按钮位置、点击区域和焦点」。本次副本把窄屏导航拆行、压缩首屏纵向留白、增大导航点击区域，并补了主内容地标和焦点样式。仓库收录测量值。复测者自行生成修正版布局时，具体坐标可能不同。

两次都使用相同 Edge 版本、离线设置和视口。在浏览器控制台用下面的方式读取布局数据。

```js
({
  overflow: document.documentElement.scrollWidth - innerWidth,
  ctaBottom: Math.round(document.querySelector('.ctas .btn-primary').getBoundingClientRect().bottom),
  navHeights: Array.from(document.querySelectorAll('.nav a'),
    link => Math.round(link.getBoundingClientRect().height)),
  placeholderLinks: document.querySelectorAll('a[href="#"]').length,
  mainLandmarks: document.querySelectorAll('main, [role="main"]').length
})
```

## 分流试跑

六条请求分别覆盖只读审查、局部打磨、新页面、整页重做、已选稿落地和范围不明。第 1 条做了只读浏览器审查，其余五条只检查页序能否判断接下来该做什么。浏览器修正数据来自前一轮 Lumina 实测。

Serenity Spa 的四项通过来自 UI/UX Pro Max 的现有单测。页序在该样例只走到需求追问和策划分支。[单测源码](https://github.com/nextlevelbuilder/ui-ux-pro-max-skill/blob/main/src/ui-ux-pro-max/scripts/tests/test_design_system_mode.py)

这批数据的范围是单个页面的局部修改、流程判断和上游对照。没有真实用户选稿后的新页面验收、实体触屏或屏幕阅读器实测，也没有做多套 Skill 成品盲评。
