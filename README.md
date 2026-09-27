# doubao-brand-douyin-capacity

**品牌抖音内容产能一手抽样调研技能** —— 从调研到报告的全链路工作流，可被任意 AI / Agent（豆包、Claude、其他支持 Skill 目录的平台）加载使用。

## 这个技能做什么

针对单个品牌（如王老吉）在抖音的内容生产现状做**一手抽样调研**：账号层级结构、各账号发布量与频率、内容方向占比、KOL 生态、AI 生成视频的模块分布。结论输出为**比例区间**（下限=已确认数，上限=含疑似数），全部用截图佐证，最终产出**品牌可对外汇报的静态 HTML 报告**（严格 16:9 PPT 比例，可嵌入汇报 PPT）。

典型用途：向该品牌推售"批量混剪 / AI 爆款 / 素材管理 / 数据回流归因"类产品前的售前背景调研。

## 安装方法（其他 AI / Agent）

1. 下载本仓库（zip 解压或 git clone）。
2. 把整个目录放入目标平台的技能根目录（如豆包的 `.user_skills/`、Claude 的 skill 目录），保持目录名 `doubao-brand-douyin-capacity` 不变。
3. 触发词：`用 doubao-brand-douyin-capacity 调研 <品牌名>` 或直接说"调研 <品牌名> 的抖音内容产能"。

## 依赖（必须搭配使用）

报告产出依赖 **frontend-slides**（张Zara 的 HTML PPT 技能），二者必须配合：

- 仓库：`https://github.com/zarazhangrui/frontend-slides.git`
- 安装：clone 到同一技能根目录（`frontend-slides/`），生成报告前先 Read 其 `SKILL.md`、`viewport-base.css`（全文包含）、`html-template.md`。
- 备选（非必须）：`https://github.com/op7418/guizang-ppt-skill.git`（瑞士风版式）。

## 文件结构

```
doubao-brand-douyin-capacity/
├── SKILL.md                        # 主流程：七步（风控→官号采样→矩阵盘点→深采→KOL→AI三模块→报告）
└── references/
    ├── risk-control.md             # 防验证码风控基线（登录态+随机节奏、iesdouyin 分享页通道、红线）
    ├── sampling-protocol.md        # 抽样协议与比例区间口径
    ├── ai-module-judging.md        # AI 三模块判定标准（模块1全AIGC/模块2AIGC+混剪/模块3纯拍摄混剪）
    ├── report-spec.md              # 报告规范（品牌视角措辞、9页结构、截图使用、区间口径）
    └── report-production.md        # 报告制作规范（frontend-slides 调用步骤、排版坑位、渲染自检）
```

## 环境适配说明

技能内部分路径为当前运行环境的实测值，换环境时按实际情况替换：

- **frontend-slides 路径**：`report-production.md` 中 `/home/user/.doubao/agent_mode/workspace/.user_skills/frontend-slides/` 为示例，指向你实际安装的技能根目录。
- **Playwright / Chromium 路径**：`report-production.md` 第4节渲染自检的 node 与 chromium 路径为当前沙箱实测值，换环境用目标平台自带的浏览器自动化即可（核心是：逐页截图 + 检查 `scrollHeight===1080` + 格子几何）。
- **沙箱网络**：当前环境 CDN（Google Fonts / jsDelivr / lucide）不可达，字体用 `<link>` + 本地 fallback；在联网环境可直接加载。
- **抖音风控**：`risk-control.md` 的触发规律（固定节奏滚动 2–3 屏触发图片拖拽验证码、搜索 `type=video` 是唯一高风险点）为 2026-09 实测，平台策略变化后需重新校准；红线不变——**不做验证码自动识别/绕过，扫码登录可接受，图片拖拽验证码不频繁人工处理**。

## 开源许可

本技能为通用工作流方法沉淀（采集与排版规范、风控经验），不包含任何平台私有数据或受版权保护内容。允许自由使用、修改与再分发。
