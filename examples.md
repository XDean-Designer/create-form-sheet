# 创建类表单 — 参考页与抄/不抄

仓库根：`rtaandroid/docs/`（路径随工程可能为 `docs/`）。

## 1. 四页配对

| 页 | 原型 | PRD | 最适合复用 |
|----|------|-----|------------|
| 创建员工 | `创建员工改造/创建员工-prototype.html` | `创建员工改造-PRD.md` | 门控、提成 strip、「更多」、同层头衔 sheet、demo-nav |
| 新建项目 | `价目表新建项目改造/新建项目-prototype.html` | `价目表新建项目改造-PRD.md` | **默认壳母版**；时长≤3；名称置顶；热点图（仅服务项目） |
| 添加卡模板 | `添加卡模板改造/添加卡模板-prototype.html` | `添加卡模板改造-PRD.md` | 多类型显隐、金额≤3、折扣面板、套餐 item-card |
| 新建产品 | `价目表新建产品改造/新建产品-prototype.html` | `价目表新建产品改造-PRD.md` | 产品分类+金额 chip≤3、更多内关联库存、无热点图 |

生成新页时：**优先复制「新建项目」或「新建产品」壳**，再按域换区块。

## 2. 抄 / 不抄矩阵

| 模式 | 来源 | 默认同类页 |
|------|------|------------|
| 80% sheet 壳 + demo-nav | 新建项目 | **抄** |
| pick-strip / 拖滑 / 已选可视 | 三页共有 | **抄** |
| 加号 → 同层 list/add sheet | 新建项目 / 卡模板名 | **抄** |
| 数值档 ≤3、无「更多」 | 项目时长；卡/产品金额 | **抄** |
| 更多设置手风琴 | 项目 / 卡 / 产品 | **抄**（若有次要字段） |
| 价格热点图 + 占比文案 | 仅新建项目 | **仅服务项目价**；产品/卡用 chip+¥ |
| 自定义名称置顶 | 项目 / 产品 | 名称推荐行 **抄**；卡名防重默认卡 **不套用置顶** |
| 身份头像 + 姓名手机 kb 条 | 仅创建员工 | **不抄** |
| 工资横向标尺 | 仅创建员工 | **不抄**（除非工资域） |
| 提成方案卡 + 右侧提成子页 | 仅创建员工 | **不抄**；折扣用页内完成面板（卡） |
| 角色黄星网格 | 仅创建员工 | **不抄** |
| 套餐三列 item-card + 卡内步进 | 仅卡模板 | **不抄**到普通创建页 |
| 模拟 iOS 键盘 | 卡模板旧版已废 | **禁止** |
| 适用门店主路径 | 项目/产品隐式 | **默认隐藏**；写隐式默认 |
| 关联库存 | 项目隐藏不关联；**产品**更多内保留 | 按域 |

## 3. 生成示例（口述级）

**用户**：「按规范做价目表新建优惠券模板页，执行前确认。」

**代理**：

1. 读本 skill + 扫 `reference.md` §1–7。  
2. 大纲：壳抄新建产品；主路径 类型→面额 chip≤3→名称；更多：有效期、说明；demo-nav：类型/折叠/有无推荐。  
3. 用户确认执行 → 写 `docs/优惠券模板改造/新建优惠券模板-prototype.html` + PRD → 更新 sync 配对表 →「原型与 PRD 已同步」。

## 4. 最小 HTML 结构备忘

```html
<aside class="demo-nav">…</aside>
<div class="device">
  <div class="device-bg">…</div>
  <div class="page-scrim">
    <div class="stage">
      <div class="sheet-handle-top"></div>
      <header class="title-bar">…</header>
      <div class="scroll">…blocks… more-settings…</div>
      <div class="bottom-bar"><button class="btn-primary">保存…</button></div>
      <div class="sheet-mask" id="optSheet">…list/add panes…</div>
      <div class="toast" id="toast"></div>
    </div>
  </div>
</div>
```

JS 必备函数名可沿用参考页：`bindHScrollDrag`、`ensureSelectedVisible`、`openListSheet`、`openAddSheet`、`setDemo`、`syncDemoNav`、`visibleYenAmounts` / `visibleDurs`（数值）、`save`。
