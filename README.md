# 路西法小铺 — 静态站点

源站 `shop.daoxiangclub.cc` 的照抄克隆（黑 / 白 / 荧光绿配色，价格以美元显示）。
纯 HTML + CSS + JS，没有框架、没有构建步骤、不发起任何外部请求（公告里两条第三方链接除外）。

文字与图片按源站逐字逐像素搬：36 个商品详情页的「宝贝详情」就是源站那段富文本原文
（含 emoji、`<font>` 标题、换行位置），详情配图是源站原图的原尺寸无损副本，
封面 / 分类图标 / 背景 / favicon 同样是源站原图的原始像素尺寸。只有价格、客服联系方式、
店铺名换成了本站自己的。

## 怎么部署

**Cloudflare Pages（推荐）**：Dashboard → Workers & Pages → Create → Pages → Upload assets
→ 把这个文件夹整个拖进去 → Deploy。不需要构建设置。

**接 GitHub 仓库**：仓库根目录放这些文件，Cloudflare Pages 里 Framework preset 选 `None`、
Build command 留空、Build output directory 填 `/`。

**本地预览**（目录形式的 URL 需要 HTTP 服务，直接双击只能看首页）：

```bash
python3 -m http.server 8000
# 打开 http://localhost:8000/
```

## 目录

```
index.html                     /                    首页：默认分类（combolist）19 件 + ?q= 搜索
cat/<id>/index.html            /cat/<id>/           分类页（只渲染该分类商品）
item/<id>/index.html           /item/<id>/          商品详情页
user/index/query/index.html    /user/index/query/   订单查询
404.html                                            找不到页面时显示
assets/style.css                                    全部样式
assets/app.js                                       语言切换 / 搜索 / 数量步进 / 表单
assets/img/                                         背景、favicon、商品封面、分类图标
```

## 怎么改

- **商品名 / 价格 / 分类 / 库存**：改 `index.html`、`cat/*/index.html`、`item/*/index.html` 里对应的
  `<a class="ls-col" data-name="…">`、`.ls-price`、`.ls-card__title` 等节点；或用生成脚本
  `scripts/build-static-site.py`（在上层模板仓库里）一次重建全部 44 页。
- **公告文案**：每个页面里的 `<div class="ls-panel__body ls-notice">` 结构一致，改首页后逐页同步，
  或改生成脚本里的 `notice_panel()`。
- **商品说明**：`item/<id>/index.html` 的 `.ls-rich` 区块；内容取自源站
  `docs/research/daoxiangshop/detail-rich/<id>.html`（源站 `.acg-rich` 原文），
  重建时只做两处替换：图片指向本地文件、正文里的客服号换成可点击的 `@imluxifa`
  （`https://t.me/imluxifa`，点击唤起 Telegram）。
- **详情页配图**：`assets/img/detail/<商品id>-<序号>.webp`，原站那一张图的原尺寸、
  原顺序（20 个商品 2 张、5 个商品 1 张、11 个商品 0 张，共 45 张），无损 WebP。
  原始 PNG 字节缓存在 `docs/research/daoxiangshop/detail-imgs/`，重建时按
  `detail-structure.json` 的源码顺序直接取用；文件名不变直接覆盖即可换图
  （尺寸任意，页面按 max-width 100% 显示）。要增减图片就直接在
  `item/<id>/index.html` 里增删 `<img src="../../assets/img/detail/…">`。
- **封面 / 图标 / 背景**：`assets/img/covers/*.webp`、`assets/img/icons/*.webp`、
  `assets/img/bg.webp`、`assets/img/favicon.webp`，都是源站原图的原始像素尺寸
  （封面最宽 1920，背景 2560×1440，favicon 640×640），无损 WebP 直接覆盖即可换图。
- **配色**：`assets/style.css` 顶部的 `:root` 变量（`--ls-neon` 是荧光绿）。

## 说明

购买与订单查询都是前端演示态：购买按钮提示联系客服，订单查询返回"未找到相关订单"。
没有后端、没有支付、不落单。页头的「登录 / 创建账号」只占位，点击不跳转。

和源站有意不同的只有三件事：价格（源站人民币，本站美元，2 号商品 $7000）、
客服联系方式（源站公告里的客服号、详情正文里的 @ 号，全部换成 @imluxifa 并可点击跳 Telegram）、
店铺名（源站稻香数据小店）。
