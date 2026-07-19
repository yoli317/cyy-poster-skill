# posterskill

一个 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 技能，可从你的论文自动生成可打印的学术会议海报。指向你的 Overleaf 源文件和项目网站，它会提取内容、下载图片、获取 Logo，并生成可在浏览器中编辑的交互式海报。单个 HTML 文件，无需构建步骤。

核心理念：海报是一个**实时编辑器**。拖动分隔线调整列宽和行高，点击卡片进行替换或移动，调整字体大小——然后将你的布局反馈给 Claude 进一步优化。在浏览器和 Claude 之间反复迭代，直到满意为止。

## 快速开始

```bash
git clone https://github.com/yoli317/cyy-poster-skill.git poster && cd poster
git clone https://git.overleaf.com/YOUR_PROJECT_ID overleaf   # 你的论文
```

可选：添加参考海报用于风格匹配：

```bash
cp ~/some_poster.pdf references/
```

启动 Claude Code 并运行技能：

```bash
claude
```

```
/make-poster
```

它会读取你的论文、抓取项目网站、匹配参考风格，并生成 `poster/` 目录。在浏览器中打开 `poster/index.html` 预览和编辑。

## 你将获得什么

海报是一个**自包含的 HTML 文件**，内置可视化编辑器：

- **拖动列分隔线** 调整列宽
- **拖动行分隔线** 调整卡片高度
- **点击交换** 卡片（点击一个菱形手柄，再点击另一个）
- **移动/插入** 卡片到任意位置（点击手柄，再点击放置区）
- **A- / A+** 按钮全局调整字体大小
- **预览** 模式查看打印效果
- **保存 / 复制配置** 将布局导出为 JSON

无需 npm、无需构建步骤、无需服务器，直接在 Chrome 中打开 `index.html`。

## 输入项

| 输入 | 来源 | 是否必须 |
|------|------|---------|
| 论文 | `overleaf/` 目录 | 是 |
| 项目网站 | URL（运行时询问） | 是 |
| 参考海报 | `references/` 目录 | 否 |
| 作者网站 | URL，用于品牌/风格匹配 | 否 |
| 格式规范 | 文字说明或会议要求 URL | 缺失时询问 |
| Logo | 从你的网站自动下载到 `poster/logos/` | 自动 |
| Git 仓库 | 用于推送海报的 URL | 可选 |

## 编辑工作流

1. Claude 生成初稿并在浏览器中打开
2. 在浏览器中拖动分隔线、交换卡片、调整字体
3. 点击工具栏中的 **复制配置**
4. 将 JSON 粘贴回 Claude——它会更新默认值
5. 重复以上步骤直到满意
6. 点击 **预览** 确认效果，然后打印为 PDF（页边距：无，背景图形：开启）

## 工作原理

海报使用通过 CDN 加载的 React 应用，包含：

- **`CARD_REGISTRY`** — 定义每张卡片的内容（标题、颜色、JSX 主体）
- **`DEFAULT_LAYOUT`** — 定义列结构和卡片排列顺序
- **`DEFAULT_LOGOS`** — 头部的机构 Logo
- **`window.posterAPI`** — 用于自动化的编程接口

Claude 使用 [Playwright](https://playwright.dev/) 来：
- 测量图片宽高比并分配到匹配的列
- 自动优化列宽以减少空白
- 截图并可视化验证布局
- 以完整打印分辨率生成 PDF

## 编程接口

可在浏览器控制台或通过 Playwright 调用：

```js
posterAPI.swapCards('method', 'results')      // 交换两张卡片
posterAPI.moveCard('quant', 'col1', 2)        // 移动卡片到指定位置
posterAPI.setColumnWidth('col1', 280)          // 调整列宽（mm）
posterAPI.setCardHeight('method', 150)         // 设置卡片高度（mm）
posterAPI.setFontScale(1.5)                    // 调整文字大小
posterAPI.getWaste()                           // 测量空白量
posterAPI.getLayout()                          // 获取当前布局
posterAPI.getConfig()                          // 获取完整配置 JSON
posterAPI.resetLayout()                        // 恢复默认设置
```

## 示例

参见 [Fillerbuster 海报](http://ethanweber.me/fillerbuster-poster)（[仓库](https://github.com/ethanweber/fillerbuster-poster)），这是使用本技能构建的示例。

## 待改进

- 架构可以自己提出修改
- 接入 Nature 风格
