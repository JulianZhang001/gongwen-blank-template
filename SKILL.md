---
name: gongwen-blank-template
description: 生成空白的中文公文印制格式 Word 模板（.docx），按本地《文件印制格式基本要求》（A4、上35/下29/左右25.5mm、固定30磅、方正小标宋_GBK二号标题、方正仿宋_GBK三号正文、黑体一级/楷体二级标题、右侧落款、外侧页码首页无码）。当用户说「公文格式模板」「空公文模板」「要个通知模板/报告模板/请示模板」「导出公文格式」时使用。
agent_created: true
---

# 公文空白格式模板生成

产出**空白可填写**的公文格式模板，排版一律调用
`~/.workbuddy/skills/gongwen-writing-skill/docx-official-format/scripts/format_docx.py`
（用户自有技能仓库，格式标准的唯一来源）。本技能只负责「造一份占位文档 → 套标准 → 补附注」。

## 用法

```bash
<WORKBUDDY_PYTHON> \
  ~/.workbuddy/skills/gongwen-blank-template/scripts/make_blank_template.py \
  --doc-type 通知 --out "/path/公文格式模板.docx"
```

参数：
- `--doc-type`：`通知` / `报告` / `请示` / `方案` / `纪要` / `通用`，默认 `通用`
- `--title`：自定义标题占位（覆盖 doc-type 生成的标题）
- `--no-contact`：不生成末尾「（联系人：……）」附注行
- `--out`：输出 .docx 路径

## 必须知道的三个坑

1. **附注（联系人）行必须后置插入。** `format_docx.py` 只在「最后一段是日期、倒数第二段是单位名/人名」时才识别落款。
   源文档里如果带了联系人行，落款识别即失效。脚本已按此逻辑处理，改动时别破坏。
2. **占位符用全角 `×` 时是不可断行的整段。** 正文占位控制在 20 个 `×` 以内；标题 ≤19 字（二号字一行约 20 字）。
   否则会出现整行跳到下一行、或被两端对齐拉出巨大字距。
3. **单页优先。** 样例段落总行数控制在 19 行内（每行 30pt），否则落款/日期被挤到第 2 页，模板看起来不像一份完整公文。

## 验证（每次都做）

```bash
/Applications/LibreOffice.app/Contents/MacOS/soffice -env:UserInstallation=file:///tmp/lo_gw \
  --headless --convert-to pdf --outdir <dir> <out.docx>
sips -s format png -Z 1400 <out.pdf> --out <预览.png>   # 然后肉眼复核
```

读回校验：页面 210×297mm、页边距 35/29/25.5/25.5mm、`Normal`=方正仿宋_GBK 16pt、
`sectPr` 含 `w:titlePg`（首页无码）、落款与日期右对齐且 `right_indent=64pt`。

## 字体

本机已装：方正小标宋_GBK、方正仿宋_GBK、方正楷体_GBK（**方正黑体_GBK 未装**，Word 里可能回退）。
脚本写入的字体名以技能仓库默认值为准（`方正小标宋体_GBK` 等），即使本机没装也照样写入，Word 端可用即可。
