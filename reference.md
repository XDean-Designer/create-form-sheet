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
| 底栏 | 仅主按钮；主色 `#F32F41`；安全底可选 |
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

## 5. 数值档（价格 / 时长 / 金额）

对齐「新建项目 · 项目时长」与 `numeric-options-max3.mdc`：

1. 常用档（如 30/60/90 或金额三档）+ 建议值 + 自定义并集。
2. 按数值 **升序**。
3. 主路径 **最多约 3** 张；若已选不在前三 →「选中 + 最近邻」凑满 3 再升序。
4. 自定义：行末「+」或「¥输入」；确认后并入。
5. **禁止**数值「更多」；demo-nav **禁止**「金额常规可出更多」。

**例外**：服务项目**价格热点图**属连续调价控件，不是 chip 档；若改成金额 chip 则遵守本条。

大号金额手输：可点大号 / ¥输入；小数 ≤2 位；非负；默认可不设上下限（域另定）。

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

## 8. 必填、默认、隐式

- 界面不标 `*`；保存前 toast。
- **零操作可保存**为默认目标：进页给齐默认分类/名/价/开关。
- 不展示但写入的字段在 PRD 写「隐式默认」（例：适用门店=全部；项目不关联库存）。**产品关联库存**可展示在更多设置（域差异）。

## 9. demo-nav（仅原型）

- 画布外左栏约 160px；非正式产品 UI。
- 只切**显示规则 / 门控**：有无样本、有无推荐、选项数量（**仅分类·名称**）、更多折叠、权限、有无分类、防重名占用等。
- `setDemo` + `syncDemoNav` 高亮当前态。

## 10. 同层业务 sheet（域）

赠送 / 会员价 / 到期日 / 关联库存等：可保留 **取消 | 标题 | 确认** 头；蒙层=取消。视觉可加顶柄，与 optSheet 词汇统一。

套餐 **item-card 三列 + 卡内步进**：属卡模板域，**不要**默认用在其它创建页。

## 11. 字长（常见默认；以域为准）

| 用途 | 常见上限 |
|------|----------|
| 名称（项目/产品/卡名） | 16 |
| 分类自定义 | 12 |
| 时长分钟 | 3 位数字 |
| 卡项说明 | 100 |
| 员工姓名 / 头衔 | 见创建员工 PRD |

## 12. PRD 建议章节

背景 → 1 现状 → 2 目标 → 3 范围 → 4 核心规则（壳 / 横滑 / 更多 / 字段）→ 5 链路 → 6 覆盖 → 7 验收 → 8 原型说明（含 demo-nav）。

## 13. 生成自检

- [ ] sheet 内容自适应 + max 80% + 无下限；`fitSheetHeight` + `.22s` 高度动画  
- [ ] 无红 `*` + 底栏保存  
- [ ] 选择卡无勾；strip + 拖滑 + 已选可视  
- [ ] 非数值 >6 才有「更多」；数值 ≤3 且无金额更多  
- [ ] 加号 → 同层 add sheet + ≤5 胶囊；有自定义藏加号  
- [ ] 更多设置默认折叠（若有）；折叠/展开后高度动画正确  
- [ ] demo-nav 无金额扩量开关  
- [ ] 原型与 PRD 同轮；配对表已更新（若新目录）  
- [ ] 未误抄：假键盘、热点图、头像工资尺、套餐步进（除非域需要）  
