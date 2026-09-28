# 报告制作规范（frontend-slides 链路 · 本次放量跑沉淀）

本文件记录"从调研数据到可交付 HTML 报告"的完整制作链路。基于王老吉调研（2026-09）跑通验证：用户对美观要求极高、已多次否定（16:9 显示异常 → 大面积留白 → 换技能重做 → 单页塌陷），下列规则均为**硬约束**。

## 0. 前置依赖与调用步骤（必须搭配 frontend-slides 使用）

本技能的报告产出**依赖 frontend-slides（张Zara HTML PPT 技能）**，二者必须配合使用。制作前按以下顺序准备，细节不可省：

1. **确认技能已安装**：`/home/user/.doubao/agent_mode/workspace/.user_skills/frontend-slides/`（含 SKILL.md、viewport-base.css、html-template.md、bold-template-pack/）。
2. **Read `frontend-slides/SKILL.md`**：按其风格发现流程（Phase 2：先 3 预览再全量）、密度模式（本场景默认 high density / reading-first）、固定舞台规则执行。
3. **Read `frontend-slides/viewport-base.css`**：生成时**全文包含**进 `<style>` 块，不可删减。
4. **Read `frontend-slides/html-template.md`**：按其 HTML 架构与 JS 功能（SlidePresentation 控制器：方向键/空格/PgUp/PgDn 翻页、touch 滑动、stage 等比缩放、页码控件）实现。
5. **静态要求**：用户明确"不要动态"时，跳过 `animation-patterns.md`，全 deck 无动画；用户未表态时默认静态（本技能场景均为汇报用途，静态更稳妥）。
6. **沙箱 CDN 不可达**（jsDelivr/fonts.googleapis.com/lucide 在本环境无网）：字体用 `<link>` 引用 + 本地 fallback（发布到用户浏览器可加载）；图标不依赖外链库。生成前如果 frontend-slides 文件被清空/缺失，先 `git clone https://github.com/zarazhangrui/frontend-slides.git` 重装。

备选：guizang-ppt-skill（归藏，`/home/user/.doubao/agent_mode/workspace/.user_skills/guizang-ppt-skill/`）可出瑞士风版，但默认走 frontend-slides。

## 1. 风格发现流程（先预览，再全量）

1. 先做 **3 个自包含单页封面预览**（1920×1080 固定舞台）供用户选，**禁止直接全量生成**。
2. 用户明确给过方向时（如"紫/红系强调、素色浅底、静态"）按方向生成 3 个差异化预览；**强调色取"品牌色"**——即品牌自身主色（王老吉=红 #C8102E，其他品牌按其 VI 取色），或用户指定的色系。王老吉案例三预览：素白紫罗兰（#6A3AB2）/ 品牌色红（#C8102E）/ 酒红+紫双色（#7C1D3A）。
3. 预览内**不得出现** preview / 模板名 / 内部标注字样。
4. 用户选定后，把该预览的 CSS 系统（`:root` 变量、字体栈、边线/徽章/装饰语言）**原样扩展**到全部页面，不切换风格。
5. 预览生成后先自行渲染核对配色像素（如强调色边 RGB(200,16,46)），再交付。

## 2. 页面设计要点（避免"丑/留白/撑开"三大投诉）

- **静态**：用户明确"不要动态的"，全 deck 无动画、无 reveal。若用户未要求动画，默认静态。
- **配色**：暖白/素白浅底（#FAF8F5 / #FAFAFA 类）+ **单一强调色 = 品牌色**（品牌主色，如王老吉红 #C8102E；用户说"紫或红系"时可选紫罗兰 #6A3AB2 / 品牌色红 #C8102E / 酒红 #7C1D3A），灰阶用 #F2EFEA / #DDD8D0 / #8B867D。一份 deck 只用一套强调色，不混搭。
- **字体**：中文大标题衬线（Noto Serif SC 900），数字 Space Grotesk 700，正文 Noto Sans SC 300。
- **每页骨架**（防止留白堆积）：顶部 meta 行（左调研名·右页码）+ 3px 强调色顶线 + kicker（红/紫小字）+ 大标题（≤12 字）+ 内容区（占页面 55–70%）+ 底部 foot（口径/结论说明，贴底）。
- **内容区饱满**：数据页用矩阵/条形图/柱塔/ledger 等图表占满内容区，不要让内容挤在上半屏、下半屏空白。
- **严格 16:9**：stage 等比缩放（`min(innerWidth/1920, innerHeight/1080)` 变换），任何元素不得把页面撑开；交付前每页 `scrollHeight === 1080` 核对。

## 3. 排版坑位清单（已踩过，必须遵守）

1. **截图/图片格高度塌陷（P5 事故）**：在 grid/flex 容器内嵌 `<img height:100%>` 且容器未定高时，img 会按原始比例把行撑高、其余格子被挤出页面。修复模板：
   - 容器定高：网格给 `flex:1; min-height:0;`，行用 `repeat(2,1fr)`；
   - 图格改 `position:relative`，img 用 `position:absolute; inset:0; width:100%; height:100%; object-fit:cover;`，caption 用 `position:absolute; bottom:0` 压底。
   - 同类检查：任何 `height:100%` 的 img 都必须有确定高度的父链。
2. **整页被截图自然高度撑开（双维版 P4/P5/P6 事故，页面被撑到 1311~1728px）**：`<img width:100%>`（无定高、无 object-fit）在窄栏里按原始比例渲染出几百 px 高，把内容区、gaprow、foot 全挤出页面。**所有内嵌截图必须用"定高槽"**，统一模板：
   ```css
   .ximg { border:2px solid var(--accent); position:relative; height:250px; overflow:hidden; background:#fff; }
   .ximg img { position:absolute; inset:0; width:100%; height:100%; object-fit:cover; display:block; }
   ```
   - 槽位高度按栏宽估算（栏宽 430px、原始截图近方形 → 槽 250px 起；内容流/列表截图用 `object-fit:cover` 裁边；主页头部信息密集的截图加 `object-position:top center` 保留顶部数据）。
   - 竖排多图页（交叉发现页：左栏截图+文字、右栏图表+结论）每栏总高控制在内容区可用高度内：截图槽 180–250px，塔/条图最高值按可用高度反推（如 4 塔 190/136/68/40px），gaprow + foot 必须留在页面内。
   - 布局后余量：foot 底边距页面底 ≥30px，否则继续压缩槽位或图表高度。
3. **元素自动撑开页面**：大字号一律限高（`min(Xvw,Yvh)`），卡片内容超限时拆页或换版式，不缩小字号硬塞。
4. **图片重复**：每张截图只出现一次；表达多个点时用一张图描述不同内容，或截新图。
5. **截图内嵌**：交付为单 HTML 链接时，截图用 base64 内嵌（每张 JPEG 压到 1400px、quality 84，4 张约 0.9MB，全文件 ≤1MB），不引用外部相对路径。
6. **scrollHeight 检查陷阱（本次已踩）**：`.page` 通常有 `overflow:hidden`，内部元素溢出会被**裁剪而不撑高容器**——此时 `.slide.active` 的 `scrollHeight` 仍等于 1080，自检"通过"但内容实际已被裁掉（gaprow/foot 消失）。**必须做元素级越界审计**：遍历 `.page` 内所有元素 `getBoundingClientRect()`，任何 `rect.right>1920 / rect.bottom>1080 / rect.left<0 / rect.top<0` 即报错；并检查关键槽位（`.foot` / `.gaprow` / `.totalrow`）的 rect 是否在页面内。只看 scrollHeight 不算验证。
7. **base64 内嵌后编辑技巧**：内嵌截图的 HTML 单行可达几十万 token，`Read`/`Edit` 会失败（超行长度限制）。改这类文件用脚本精确替换（python 读全文 → 按唯一锚点字符串 replace → 写回），不要逐行读取编辑；锚点避免选图片 src（已被 base64 占据），用图片的 `alt`、相邻 class 或文本内容定位。

## 4. 渲染自检（交付前必做）

用 Playwright 脚本（node + `/opt/vm/preinstall/npm-global/lib/node_modules/playwright`，chromium 可执行 `/opt/vm/preinstall/ms-playwright/chromium-1169/chrome-linux/chrome`）：

1. 逐页 `keyboard.press('ArrowRight')` 截图（1600×900），收集 `pageerror`。
2. **元素级越界审计（代替只看 scrollHeight）**：逐页遍历 `.page` 内所有元素 `getBoundingClientRect()`，任何元素越出 1920×1080 即报错；并输出关键槽位（`.foot` / `.gaprow` / `.totalrow`）的 top/bottom，确认都在页面内且 foot 底边距 ≥30px。
3. 对含图片格的页面检查格子 rect：行数、高度均分、截图槽为定高（防塌陷与自然高度撑页复发）。
4. 肉眼过一遍拼接图：无大面积留白、无重叠、数据与截图清晰。
5. 全部通过才 `present_files` 交付；交付后用户再反馈问题 → 修改 HTML 后必须重新渲染该页验证（布局几何 + 无溢出 + 无 JS 错误）再重新交付，旧链接作废不发。

## 5. 报告内容（默认双维结构，见 brand-strategy-guide.md Step C）

默认 = 内容产能 × 品牌策略双维交叉报告：封面 → 品牌策略现状 → 新品与市场 → 内容产能现状 → 交叉发现（2–3 页）→ 机会与可探讨方向 → 收束。单维快速版（用户明确只要内容维度时）：封面 → 账号量级(9宫格) → 内容方向(条形图) → 产出节奏(柱塔+店铺截图) → 账号矩阵(六格+搜索TAB截图) → 经销商/职人(说明+KPI+作品列表截图) → KOL/达人(四类) → AI三模块(ledger+主号内容流截图) → 收束(左强调色栏核心主张+右3条takeaways)。
措辞（含黑话白话对照）、截图使用、比例区间口径见 `report-spec.md`。
