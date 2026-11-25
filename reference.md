# 创建类表单 — 规范详表

供 `create-form-sheet` 生成时查阅。权威行为以仓库内最新原型 + PRD 为准；本表为跨页公约。

## 1. 壳与布局

| 项 | 规则 |
|----|------|
| 画布 | `.device` **390×844**；底层 `.device-bg` 列表示意 |
| 蒙层 | `.page-scrim` `rgba(0,0,0,0.45)`；点蒙层关 sheet（toast 示意） |
| Sheet | `.stage`：`height: auto` + **`max-height: 80%`**（相对 `.device`）；**无最小高度下限**；顶圆角 16；入场上滑可选 |
| 高度动画 | `transition: height .22s ease`；用 `fitSheetHeight()` 量自然高后写成 px 再过渡（`height: auto` 不可动画） |
| 顶柄 | `.sheet-handle-top` 36×4 `#E0E0E0` |
| 标题栏 | 高 44；左关闭；中标题；**无右侧保存** |
| 滚动 | `.scroll` 常态 `flex:1; overflow-y:auto; min-height:0`；**量高时**临时 `flex:0 0 auto; overflow:visible` 再恢复 |
| 底栏 | 默认单主按钮（文案域定）；主色 `#F32F41`；安全底可选 |
| 底栏阴影 | **仅向上**：`0 -4px 8px -6px rgba(0,0,0,0.12)`；禁止向下溢 |
| 通栏 | `.block` padding 16；`+ .block::before` 发丝 inset 16 `#EDEDED` |
| 字体 | Noto Sans SC / Source Han Sans；品牌色 `--brand: #F32F41` |

## 2. 选择卡

| 态 | 样式 |
|----|------|
| 未选 | 白底；`box-shadow` 轻 + `inset 0 0 0 1px #D7D7D5`；圆角 12 |
| 选中 | `#FFF2F2`；字色品牌；`inset 0 0 0 2px #F32F41` |
| 禁止 | 右上角勾、热度角标、「N%」徽章 |
| 加号卡 | 40×40；灰 +；与选择卡同壳 |

`:active` 可轻微 `scale(0.98)`。

## 3. 横滑与拖滑

- 容器：`.pick-strip`；`overflow-x:auto`；隐藏滚动条；`cursor: grab`。
- `bindHScrollDrag`：mousedown 拖动；超过阈值才算拖；拖后吞 click，防误选。
- `ensureSelectedVisible`：渲染/回填后对 `.sel-card.active` `scrollIntoView({ inline: "center" })`。

## 4. 「更多」（非数值）

- 条件：该行选项数 **>6**（含自定义标签；**不含**加号卡）。
- UI：内容最右 **下划线文字「更多」**（`.strip-more`），随内容滚动，不钉死视口右缘。
- 行为：同层 `sheet-mask` → 列表 pane；点选草稿；底栏「完成」写回；点蒙层取消不写回。
- **不适用**：价格 / 时长 / 金额数值 chip（见 §5）。

## 5. 数值档与售价展示

对齐「新建项目 · 项目时长」与 `numeric-options-max3.mdc`：

1. 常用档（如 30/60/90 或金额三档）+ 建议值 + 自定义并集。
2. 按数值 **升序**。
3. 主路径 **最多约 3** 张；若已选不在前三 →「选中 + 最近邻」凑满 3 再升序。
4. 自定义：行末「+」或「¥输入」；确认后并入。
5. **禁止**数值「更多」；demo-nav **禁止**「金额常规可出更多」。

**售价分级（人工收敛后）**：

| 域 | 控件 | 禁止 |
|----|------|------|
| 服务项目价 | 热点图；峰值 `约 X% 定价 ¥Y` 浅灰**右对齐** | 勿把热点图抄到产品/卡 |
| 产品 / 卡金额 / 券面额 | chip≤3 +「¥输入」；小数 ≤2；非负 | **`price-big` 大号金额区** |
| 产品多规格已关联 | 普通行文案区间（约 15px / medium）只读 | 「已关联多规格·只读」等副提示 |

## 6. 加号 → 同层 sheet

- **同一** `optSheet`：`list` | `add` 两 pane 切换，不叠第二层。
- 新增：`input` + `field-count` + `.suggest-scroll` 胶囊（最多 5，克制文案，与场上去重）。
- 取消 / 蒙层：不提交；完成：校验字长后落自定义卡并选中。
- 有自定义：藏加号；× 删除后回默认/热度第一。
- **名称自定义置顶**（新建项目 / 产品）：自定义卡最左，推荐后移；卡模板「默认防重名卡 + 自定义」勿改成置顶推荐逻辑 unless 域要求。

## 7. 更多设置

- 页内手风琴（`.more-head` / `.more-body`）；**新建默认折叠**。
- 放入次要 / 低频：图、开关、关联库存、期限、赠送入口等。
- 顺序在 PRD 写死；demo-nav 可切折叠/展开。
- **折叠/展开必须触发 `fitSheetHeight`**：折叠后 sheet 收至纯内容高（可远低于 80%）；展开后增高，封顶 80%。
- **发丝**：`.more-head` 下**不强行**加下划线；条件首行（如归属店铺可见）再控制分隔（见新增顾客）。

### `fitSheetHeight` 约定（全创建页共用）

```js
function fitSheetHeight() {
  const stage = document.querySelector(".stage");
  const device = document.querySelector(".device");
  if (!stage || !device) return;
  const maxH = Math.round(device.clientHeight * 0.8);
  const scrolls = [...stage.querySelectorAll(".scroll")].filter(el => !el.classList.contains("hidden"));
  const prevH = stage.getBoundingClientRect().height || maxH;
  stage.style.transition = "none";
  stage.style.height = "auto";
  scrolls.forEach(el => {
    el.style.flex = "0 0 auto";
    el.style.overflow = "visible";
    el.style.maxHeight = "none";
  });
  const natural = Math.ceil(stage.getBoundingClientRect().height);
  scrolls.forEach(el => {
    el.style.flex = "";
    el.style.overflow = "";
    el.style.maxHeight = "";
  });
  const target = Math.min(Math.max(natural, 1), maxH); // 无业务下限，仅防 0
  stage.style.height = prevH + "px";
  void stage.offsetHeight;
  stage.style.transition = "height .22s ease";
  stage.style.height = target + "px";
}
```

挂载：`syncMoreUI` / `toggleMore` / `applyMore` / boot / `resize` → `requestAnimationFrame(() => fitSheetHeight())`。

## 8. 正式 UI 文案卫生

- **禁止**（画布内）：路径说明、上限提示、「示意」「可不填」「只读」教学句、「随分类推荐」、组数上限旁注、「可多选·示意/必填」等。
- **允许**：字段标签、placeholder（如「选填」「请输入…」）、备注/说明 **计数器**、toast 校验文案、画布外 demo-nav。

## 9. 现网能力对齐（禁止无声缺口）

- 生成前对照现网入口（Activity / ViewModel / Fragment / 旧 PRD）：凡现网已支持的**字段编辑、校验、显隐、联动、提交分支**，改造页须具备同等能力（控件形态可变，能力不可无声缺失）。
- **禁止**：套壳简化、零操作预填、美观收敛时悄悄砍能力。
- **允许省略的唯一条件**：用户主动提出，且确认大纲 + PRD 标明 **有意 GAP**（及原因）。
- PRD 建议含对照表列：`现网能力 | 改造口径 | MATCH / CHANGE / 有意 GAP`。
- 零操作只改默认值与空态，**不删**现网已有编辑入口与校验分支。

## 10. 零操作可保存与例外

- **默认目标**：进页给齐默认分类/名/价 chip/开关/渠道/性别等，使保存可通过。
- **隐式默认**：不展示但写入的字段在 PRD 写明（例：适用门店=全部；项目不关联库存）。**产品关联库存**可展示在更多设置。
- **例外登记（必须写 PRD）**：

| 域 | 例外 |
|----|------|
| 新增顾客 · 姓名 | **不预填**；空 → toast「请输入顾客姓名」；不能宣称完整零操作 |
| 新增顾客 · 手机 | 可空；空 → 唯一 `900` + 8 位（对已有号 ∪ 本会话占位 Set 去重） |
| 新增预约 · 姓名 | 可预填「预约客户」；聚焦/点击 **全选** 便于覆盖 |
| 新增预约 · 项目 | 默认选中 **Top3 第一项**；结束按时长重算 |
| 新建产品 | 有分类时分类/推荐名/建议价 chip 均预选 |

## 11. 主路径行输入（guest-grid）

- 适用：手输姓名 / 手机等（非名称推荐 strip）。
- 布局：左标签（约 15px semibold）+ 右定宽输入、**右对齐**；多行字段 **左缘对齐**。
- 尺寸参考：预约姓名约 **120×36**、手机高 36；顾客姓名/手机约 **200×40**。
- 透气：区块加大上下 padding / 行距（顾客 `guest-block` 约 22–24 / 行距 20）。
- **禁止**通栏 `width:100%` / `flex:1` 拉满主路径输入。

## 12. 底栏 CTA

- 默认：单主按钮实心品牌色。
- **双 CTA（顾客新建）**：左副「保存并开卡」= 白底 + 红字 + `box-shadow: inset 0 0 0 1.5px var(--brand)`；右「保存」实心；编辑态可仅保存。
- 预约等可用「确定」；冲突确认若现网有弹窗，改造可约定 **仅 toast**（写 PRD GAP）。

## 13. 域控件（顾客 / 预约等）

### 13.1 性别透气大卡

- **非** `pick-strip`：双列均分、高约 96、圆角 16；上图标下文案。
- 可选域色：男浅蓝 / 女粉（选中描边与浅底）；默认跟现网（常为女）。

### 13.2 顾问类可选 strip

- 首项 **「不选」** 且新建默认选中；>6 出「更多」同层列表。
- 勿做成行入口 toast 检索（除非域强制）。

### 13.3 同层日历

- 选具体日；范围写 PRD（生日常见 **1940-01-01～今天**）；支持年月切换。
- 到店日：主路径也可 **7 日 chip + 加号** 进日历。

### 13.4 预计时间（iOS 风）

- 字段名「预计时间」；浅底居中 `开始 — 结束`；点左右数值分别改（同层时段 sheet）。
- 结束 = 开始 + Σ时长；无项目默认 60；开始/项目变则重算结束。
- **废弃**「开始三档 chip + 加号」除非用户明确回归。
- 临时占用等 Tab **同构**此控件。

### 13.5 预约内容组速记

- 手艺人 strip（首项「到店分配」）+ **散客关 / 点客开**（`assign` 0/1，**不反转**、不阻断）。
- 项目：**门店预约量 Top3** + 加号；默认第一项；加号 sheet = 分类横条 + 列表 + 行右红圆 +；主路径胶囊名+时长可 ×。
- 「新增手艺人预约」在**内容卡下方**（非更多设置）；左侧图标；满 N 隐藏、无上限 hint。
- 更多设置：**仅备注**（预约域）。

### 13.6 链式入口图标

- 「选择顾客」等链接按钮：文案**左侧** 16px 品牌色 SVG（可 Figma 导出本地 `assets/`）。

## 14. demo-nav（仅原型）

- 画布外左栏约 160px；非正式产品 UI。
- 只切**显示规则 / 门控**：有无样本、有无推荐、选项数量（**仅分类·名称**）、更多折叠、权限、有无分类、防重名占用、新建/编辑、开卡 CTA 等。
- 售价 / 时长 / 面额 **不参与**「选项过多」演示。
- `setDemo` + `syncDemoNav` 高亮当前态。

## 15. 同层业务 sheet（域）

赠送 / 会员价 / 到期日 / 关联库存 / 日历 / 时段等：可保留 **取消 | 标题 | 确认** 头；蒙层=取消。视觉可加顶柄，与 optSheet 词汇统一。

套餐 **item-card 三列 + 卡内步进**：属卡模板域，**不要**默认用在其它创建页。

## 16. 字长（常见默认；以域为准）

| 用途 | 常见上限 |
|------|----------|
| 名称（项目/产品/卡名） | 16 |
| 分类自定义 | 12 |
| 时长分钟 | 3 位数字 |
| 卡项说明 / 预约备注 | 100 |
| 顾客备注 | 800 |
| 员工姓名 / 头衔 | 见创建员工 PRD |
| 预约人姓名 | 16 |

## 17. PRD 建议章节

背景 → 1 现状 → 2 目标 → 3 范围 → 4 核心规则（壳 / 横滑 / 更多 / 字段 / 零操作例外 / **现网对照表**）→ 5 链路 → 6 覆盖 → 7 验收 → 8 原型说明（含 demo-nav）。

## 18. 生成自检

- [ ] sheet 内容自适应 + max 80% + 无下限；`fitSheetHeight` + `.22s` 高度动画  
- [ ] 无红 `*`；底栏向上阴影；正式 UI 无教学 hint  
- [ ] **现网字段编辑能力已对照**；无未点头的能力缺口；有意 GAP 已写入 PRD  
- [ ] 零操作默认已设；例外（姓名/900/预约客户等）已写 PRD  
- [ ] 手输行用 guest-grid 短框；产品价无 `price-big`  
- [ ] 选择卡无勾；strip + 拖滑 + 已选可视  
- [ ] 非数值 >6 才有「更多」；数值 ≤3 且无金额更多  
- [ ] 加号 → 同层 add sheet + ≤5 胶囊；有自定义藏加号  
- [ ] 更多设置默认折叠（若有）；发丝不过度；折叠后高度动画正确  
- [ ] demo-nav 无金额扩量开关  
- [ ] 原型与 PRD 同轮；配对表已更新（若新目录）  
- [ ] 未误抄：假键盘、产品热点图、大号金额、头像工资尺、套餐步进、预约硬套价目表 IA（除非域需要）  
