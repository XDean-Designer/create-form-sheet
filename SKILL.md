---
name: create-form-sheet
description: >-
  Generates salon/ERP「创建类」表单页：390×844 device, bottom ~80% sheet, pick-strip
  UX, same-layer sheets, numeric chips ≤3, paired PRD. Use when creating or
  aligning 新建/创建/添加 forms (员工、项目、产品、卡模板等同构页), 80% sheet
  prototypes, create-form-sheet, or when user asks to generate a similar create
  page from established UI/UX rules.
---

# 创建类表单 Sheet（剑琅 / 价目表族）

用已沉淀的 **创建员工 · 新建项目 · 添加卡模板 · 新建产品** 规范，生成或改造**同类创建主路径**：高保真 `*-prototype.html` + 配对 `*-PRD.md`。

扩展材料（按需读，勿整份塞进上下文）：

- 完整控件与禁区：[reference.md](reference.md)
- 四页对照与抄/不抄矩阵：[examples.md](examples.md)

## 何时使用

- 用户要**新建 / 创建 / 添加**某业务实体的表单页（原型和/或 PRD）
- 要求对齐「创建员工 / 新建项目 / 卡模板 / 新建产品」壳与交互
- 点名 `create-form-sheet`、80% sheet、选择卡横滑、同层 sheet 加号等

## 硬约束（生成物必须满足）

1. **壳**：画布 **390×844**；创建 UI = 蒙层 + **底部约 80%** sheet（顶柄、标题栏返回关 sheet、内容纵滚）；**仅底栏主按钮保存**（无标题栏右侧保存）。
2. **无红色必填 `*`**；校验用 toast；通栏白底；块间发丝左右缩进 **16**；内容边距 **16**。
3. **选择卡**：未选 = 白底 + 轻阴影 + `#D7D7D5` 描边；选中 = **2px `#F32F41`** + `#FFF2F2`；**无勾、无 N% 角标**。
4. **枚举行**：`.pick-strip` 单行横滑；桌面 **鼠标拖滑**（超阈值不误点）；已选 **`scrollIntoView` 可视**。
5. **「更多」**（仅非数值）：选项数（含自定义、**不含**加号）**>6** → 滑动内容最右**下划线「更多」**（非卡片、可滚走）→ **同层**列表 sheet，底栏「完成」；蒙层取消。
6. **加号**：行末 **40×40** 方卡 → **同层** sheet 新增（输入 + 最多 **5** 精选胶囊、去重）；**同时最多 1 自定义**；有自定义**藏加号**；× 删除。
7. **数值档（价格 / 时长 / 金额 chip）**：对齐项目时长——主路径约 **3** 档；升序；已选不在前三用最近邻凑满；**禁止数值「更多」与扩量演示态**（见项目规则 `numeric-options-max3.mdc`）。
8. **禁止**整页模拟 iOS 键盘作主新增路径；禁止叠第二层 sheet 做「列表上再弹新增」。
9. **原型 ↔ PRD 同轮同步**（见 `prototype-prd-sync.mdc`）；改完回复一句「原型与 PRD 已同步」。
10. **执行门禁**：先向用户确认方案（IA + 域字段 + 路径）；用户「确认执行」后再落盘。

## 执行流程

### 1. 读规范（按需）

- 必读本文件硬约束。
- 生成前扫一眼 [reference.md](reference.md) 对应章节；对照 [examples.md](examples.md) 选最近参考页。

### 2. 收域信息

至少搞清：

| 项 | 说明 |
|----|------|
| 业务对象 | 如员工 / 项目 / 产品 / 卡… |
| 主路径字段顺序 | 首屏突出什么 |
| 更多设置 | 有无；顺序；默认折叠 |
| 数值字段 | chip≤3 / 热点图 / 标尺 / 步进器（域特有） |
| 隐式默认 | 如适用门店=全部、不关联库存等 |
| 输出目录 | 默认 `docs/{功能}改造/` |

不足则一次问清；可参考现网 Activity / 旧 PRD。

### 3. 确认大纲（必做）

短表回复用户，含：标题、壳口径、主路径 IA、更多设置、数值控件选型、demo-nav 分组、输出路径、明确**不抄**的特有控件。  
**等「确认执行」再写文件。**

### 4. 落盘

1. `{短名}-prototype.html`：自包含 HTML（Noto Sans SC、CSS 变量、demo-nav、device、scrim、stage、optSheet、toast）。
2. 同目录 `{功能}改造-PRD.md` 或配对名：章节对齐现有改造 PRD（背景→现状→目标→范围→核心规则→链路→覆盖→验收→原型说明）。
3. 若新目录：在项目 `prototype-prd-sync.mdc` 配对表补一行（若该规则存在）。

### 5. 实现要点（代理速查）

从最近参考原型**复制壳 CSS/JS**（`bindHScrollDrag`、`ensureSelectedVisible`、`optSheet` list/add、`syncDemoNav`），再换成域数据与区块。

**推荐骨架 IA**（可按域删改）：

1. 主枚举（分类 / 类型）— strip + 加号 + 条件「更多」  
2. 核心量（价 / 工资 / …）— 按域选控件；若金额 chip → ≤3  
3. 名称类（可弱标题）— strip；自定义可置顶  
4. 更多设置手风琴 — 次要字段 / 图 / 开关 / 域特有入口  
5. 底栏保存  

**demo-nav（仅原型）**：只切**影响显示规则**的状态（有无推荐、折叠、权限门控、有无分类…）；**不要**做「金额常规可出更多」。

### 6. 自检

对照 [reference.md](reference.md)「生成自检」；用 [examples.md](examples.md) 确认未误抄热点图 / 头像 / 套餐步进等。

## 与其它 skill / 规则

| 资源 | 关系 |
|------|------|
| `numeric-options-max3.mdc` | 数值档硬规则；本 skill 必须遵守 |
| `prototype-prd-sync.mdc` | 原型与 PRD 同轮同步 |
| `prd-from-prototype` | 已有交互原型出详尽开发 PRD 时用；本 skill 出的是改造向短/中 PRD + 原型 |
| `pm-rule-prd` | 纯业务口径短 PRD；无壳交互时用 |

## 反例（默认不要生成）

- 全页表单 + Toolbar 保存 + 红 `*`  
- `pick-grid` 换行替代横滑（除非域强制如套餐三列 item-card）  
- 名称加号 → 假键盘 + 顶缘胶囊（已废；用同层 sheet）  
- 金额 / 时长主路径 >3 档或金额「更多」  
