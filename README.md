# create-form-sheet

Cursor Agent Skill：按「创建员工 / 新建项目 / 添加卡模板 / 新建产品」沉淀的 UI/UX，生成或对齐同类**创建类表单**（底部约 80% sheet 原型 + 配对 PRD）。

## 文件

| 文件 | 说明 |
|------|------|
| [SKILL.md](SKILL.md) | 主说明：硬约束、生成流程、自检 |
| [reference.md](reference.md) | 壳 / 选择卡 / 横滑 / 更多 / 数值≤3 等详表 |
| [examples.md](examples.md) | 四页路径与抄/不抄矩阵 |

## 安装（Cursor Personal Skill）

1. 将本仓库内容放到：
   - macOS/Linux: `~/.cursor/skills/create-form-sheet/`
   - Windows: `%USERPROFILE%\.cursor\skills\create-form-sheet\`
2. 确保目录内含 `SKILL.md`（可含 `reference.md` / `examples.md`）。
3. 在对话中点名 `create-form-sheet`，或描述「按创建表单规范生成某某新建页」。

亦可：

```bash
git clone https://github.com/XDean-Designer/create-form-sheet.git ~/.cursor/skills/create-form-sheet
```

（Windows 将目标路径改为 `%USERPROFILE%\.cursor\skills\create-form-sheet`。）

## 配套项目规则（可选）

若在具体业务仓库中使用，建议同时保留：

- `numeric-options-max3`：价格/时长/金额主路径约 3 档
- `prototype-prd-sync`：改原型必须同步 PRD

## License

未另行声明前，仅供协作与安装参考。
