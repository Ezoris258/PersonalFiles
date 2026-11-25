---
name: oi-lecture-h5
description: "当用户给出或指向一份学习材料（PDF、讲义、题解、笔记、PDF 转出的文本）并要求\"生成讲解网页 / 做成 H5 / 做个页面讲给我听\"时使用：产出单文件 HTML，逐概念讲透、C++14 可编译代码逐行注释、LaTeX 公式渲染，全站统一字号、不做移动端与大字适配。"
---

# 学习材料 → 单文件 H5 讲解页

把一份材料变成网页讲义。下面的规则是硬性的：读者情况、字号样式、代码环境、公式渲染都不能按自己的判断"优化"。

## 何时用

用户提供/指向一份学习材料，并要求生成讲解网页、H5、单页讲义。材料里通常有 `content` 字段或直接就是正文。仅要求口头讲解、生成 Markdown 或 Word 时不用本技能。

## 流程

1. **读全材料**：先完整读取源文件，不凭文件名猜内容。PDF 先转文本（用自带的skill）；扫描版 PDF 没有文字层，先渲染成图片再看。
2. **定范围**：材料很长（多章、多题、多种算法）时，一个页面只讲一个主题；先问用户要讲哪一部分，或按章节拆成多个文件。不要把无关内容塞进同一页。
3. **建覆盖清单**：列出材料中出现的全部概念、代码段、公式、图表、术语。最后逐项对账，这是"详细"的唯一验收标准。
4. **排顺序**：按依赖关系排，前置知识在前；先给整体图景，再进细节。
5. **写页面**：一次写完整的 HTML 文件（用文件写入工具），不要分几次拼。
6. **校验**（见末尾清单），修正后再交付，并告诉用户文件路径。

## 读者与讲法（硬性）

- 读者是正在学信息学竞赛（OI）的学生，有轻度智力障碍。讲法要清楚、直白、分步骤：每个概念都按 **是什么 → 为什么需要它 → 怎么用 → 常见错误 → 例子** 讲透；缺前置知识就先补（比如讲并查集前先讲"合并集合"这一需求为什么朴素做法会超时）。
- **不要**为了"显得简单"把长句拆成短句。句子保持完整、有主语和因果，条理靠标题层级、列表、编号步骤、由浅入深的顺序来体现。
- **不做任何视觉上的"适应性调整"**：全站统一字号、字体、行距，不放大正文、不做大字模式、不做高对比主题、不做响应式字号、不加 `<meta name="viewport">`、不做移动端适配。用户明确要求过这一点。
- 但**正常的讲解强调不算"适应性调整"**：关键词可以加粗，可以用一两种克制的强调色标出结论或易错点，层级靠标题与列表区分。不要为了"照应读者情况"而额外加粗、放大、换色。
- 页面正文**不要**强调读者的身体/认知情况，也不提这些要求本身：不写"轻度智力障碍""因为你是初学者""为了让你更容易理解""考虑到你的情况"之类的话，也不解释排版为什么这样设计，直接呈现讲解内容。

## 内容要求

1. **逐点讲透**：清单里每个概念、每段代码、每个公式都单独成节讲，不能只概述；推导要写完整思路与每一步，算法要给步骤、正确性理由和复杂度。
2. **公式写全**：定义、递推式、边界条件都要摆出来，不要只写结论式。
3. **例题至少一道完整走通**：题意 → 暴力思路及其问题 → 正解思路 → 实现 → 用样例手动推演一遍（表格或逐步列表） → 复杂度。样例推演要与代码实际行为一致。
4. **术语首次出现给中文 + 英文/符号**，后文统一用同一种叫法。

## 代码要求

- 环境固定为 NOIP 标准环境：**g++ 14，`-std=c++14`**。所有代码必须能在该标准下编译运行。
- **统一用万能头**：每段代码都以 `#include <bits/stdc++.h>` 开头，紧跟 `using namespace std;`；不要改成罗列 `#include <cstdio>` 之类的具体头文件（NOIP 的 g++ 支持万能头；若确实要在 MSVC 等不支持的环境演示，才展开头文件并说明原因）。
- 材料里的代码若不合规（C++17/20 特性如结构化绑定、`if constexpr`、`std::optional`、`string_view`；非标准库；平台相关函数如 `windows.h`、`conio.h`；变长数组 VLA；依赖特定编译器扩展），改成等价且正确的 C++14 写法，并在旁边用一小段说明改了什么、为什么改。
- **逐行注释**：每条语句对应一行注释，注释与代码严格对应；注释写"这一行在做什么、为什么这样做"，不是复述语法。
- 每段代码都要给：样例输入、样例输出（**真跑出来的**，不要凭空写）、时间复杂度及其针对的量（如"对边数 $m$ 与点数 $n$"）。
- 能编译就真编译：把代码抽到临时文件，`g++ -std=c++14 -O2` 编译并跑样例比对；环境里没有编译器时，在页面里明确标注"未实测编译"，不要装作验证过。

注释写法示例（这种密度，不要更稀）：

```cpp
#include <bits/stdc++.h>          // 万能头：一次引入常用标准库（输入输出、容器、算法等）
using namespace std;              // 展开 std 名字空间，后面写 cin/cout 不必加 std::
const int MAXN = 100005;          // 数组上限，按题目数据范围定，放在函数外避免爆栈
int a[MAXN];                      // 存储待处理序列，下标从 1 开始用更贴题
int main() {                      // 程序入口，返回值类型为 int
    int n = 0;                    // n 表示元素个数，初值 0
    cin >> n;                     // 从标准输入读入 n
    for (int i = 1; i <= n; ++i)  // i 从 1 到 n 依次遍历
        cin >> a[i];              // 把第 i 个数读进 a[i]
    return 0;                     // 正常结束，返回 0
}
```

## 公式渲染

- 一律 LaTeX：行内 `$...$`，独立公式 `$$...$$`。
- **复杂数学符号必须写成 LaTeX 代码，禁止用 Unicode 字符或图片代替**。求和 `\sum_{i=1}^{n}`、连乘 `\prod`、积分 `\int`、根号 `\sqrt{x}`、上下标 `a_i^2`、分数 `\frac{a}{b}`、希腊字母 `\alpha \beta \theta`、关系符 `\le \ge \ne \approx \equiv`、箭头 `\to \Rightarrow \leftrightarrow`、集合与逻辑 `\in \notin \subseteq \cup \cap \emptyset \forall \exists \land \lor \lnot`、省略号 `\cdots \ldots \ddots`、取模 `\bmod`、组合数 `\binom{n}{m}` 等，一律写 LaTeX，不用 `∑ ∏ ∫ √ ≥ ≤ ≠ → α` 这类字符，也不贴公式截图。单字母变量、普通数字、简单四则运算可以留在正文里，其余进公式环境。
- 用 MathJax 3 的 CDN，并在 `<head>` 中显式配置 `inlineMath`（默认不含 `$`），否则行内公式不渲染：

```html
<script>
  window.MathJax = {
    tex: { inlineMath: [['$', '$']], displayMath: [['$$', '$$']] },
    svg: { fontCache: 'global' }
  };
</script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js"></script>
```

- 渲染前必须检查：`$` 成对；正文里的钱数等普通 `$` 转义成 `\$`；`\\`、`\{`、`\&` 转义正确；公式内的 `<`、`>` 用 `\lt`、`\gt` 写，避免被 HTML 解析器当成标签；全页搜一遍 `∑ ∏ ∫ √ ≤ ≥ ≠ → ∈` 这类字符，确认都换成了 LaTeX 命令。

## 图示

- 需要图形时优先**内联绘制**：状态转移图、树、链表、坐标系用 SVG 最省事，流程/对比用 flex/grid，动画用 canvas。图与正文文字同色系，线条用 `var(--line)`。
- 引用外部图片必须确认可访问；页面断网时正文仍要能读（图是加分项，不是理解的前提）。

## 页面形式

- 单个 `.html` 文件，双击即可打开：内联 CSS/JS，`<meta charset="utf-8">`，不引 viewport。
- 版式：浅色背景、常规字体（`system-ui, "Segoe UI", "Microsoft YaHei", sans-serif`）、正文约 16px、标题层级清楚；代码块等宽字体 + 浅色底 + `overflow-x: auto` 横向滚动。
- 支持深色模式：用 CSS 变量 + `@media (prefers-color-scheme: dark)` 只换颜色，字号与排版完全不变。
- 建议结构：目录（锚点跳转）→ 知识点逐节讲解 → 代码与逐行注释 → 例题 → 易错点 → 总结（含"合上页面再想一遍"的自测清单）。
- 文件命名与位置：与源文件同目录，`<源文件名>-讲解.html`。

骨架（复制后按需增补，不要改字号体系）：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
<meta charset="utf-8">
<title>主题 · 讲解</title>
<style>
  :root { --bg:#fff; --fg:#1f2328; --muted:#5b6470; --line:#d8dee4; --code-bg:#f6f8fa; --accent:#0b5cad; }
  @media (prefers-color-scheme: dark) {
    :root { --bg:#16181c; --fg:#e6e6e6; --muted:#a2abb5; --line:#30363d; --code-bg:#1f242b; --accent:#79b8ff; }
  }
  body { margin:0 auto; max-width:860px; padding:32px 20px 64px;
         background:var(--bg); color:var(--fg); font-size:16px; line-height:1.75;
         font-family: system-ui, "Segoe UI", "Microsoft YaHei", sans-serif; }
  h1 { font-size:1.6rem; } h2 { font-size:1.3rem; margin-top:2.2em;
       border-bottom:1px solid var(--line); padding-bottom:.3em; } h3 { font-size:1.1rem; }
  a { color:var(--accent); }
  pre { background:var(--code-bg); border:1px solid var(--line); border-radius:6px;
        padding:12px 14px; overflow-x:auto; line-height:1.6; font-size:.92rem; }
  code, pre { font-family: "Cascadia Mono", Consolas, "Courier New", monospace; }
  code { background:var(--code-bg); padding:.1em .3em; border-radius:4px; }
  pre code { background:none; padding:0; }
  table { border-collapse:collapse; } th, td { border:1px solid var(--line); padding:6px 10px; }
</style>
<script>
  window.MathJax = { tex: { inlineMath: [['$','$']], displayMath: [['$$','$$']] }, svg: { fontCache: 'global' } };
</script>
<script async src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-svg.js"></script>
</head>
<body>
  <h1>标题</h1>
  <nav><ol><li><a href="#s1">第一节</a></li></ol></nav>
  <section id="s1"><h2>1. 概念</h2></section>
</body>
</html>
```

## 源材料有问题时

不静默修补，也不照抄错误：在页面相应位置用中性说明指出"原文此处有误/缺漏/前后矛盾"，紧接着给出修正后的正确版本，并在总结里汇总成一张"原文 vs 修正"小表。

## 收尾：只留网页文件

页面写完后，产物目录里除了网页不应该再有别的东西。但对 git 只做**增量**处理：**已有的版本控制状态一律保持原状**——原来被忽略的继续忽略，原来被跟踪的继续跟踪，不要为了"干净"去重写 .gitignore。

1. **删掉本轮中间产物**：PDF 转出的 `.txt`、编译产物（`.exe` / `.o` / `a.out`）、测试输入输出、一次性脚本、截图、备份副本。用户自己的源材料（讲义、题解）不要删。
2. **更新 .gitignore（仅当当前文件夹已有 `.gitignore`）**：原有规则一行都不动，**只在末尾追加**覆盖本轮新产物的行，例如：

   ```gitignore
   # 讲解页生成过程中产生，不纳入版本控制
   *.exe
   *.o
   a.out
   <中间文件名>.txt
   ```

   禁止写 `*` 之类的全局忽略，那会把原本跟踪的文件一起踢出仓库。
3. **刷新索引**：`git rm -r --cached .`（只清索引，不删工作区文件）→ `git add .`（按当前 .gitignore 重新加回）。旧规则保留，所以结果应是：原有跟踪的文件照旧、原有忽略的照旧、新增的只有网页。最后 `git status --short` 核对，应只看到网页文件是新增/修改。
4. 若某个老文件本来"既被跟踪又被忽略"，第 3 步会让它变成未跟踪；`git status` 里发现这种变化时，用 `git add -f 文件名` 单独加回来，保持原状。
5. 当前文件夹不是 git 仓库（没有 `.git`）或没有 `.gitignore` 时，跳过第 2、3、4 步，只做第 1 步清理。

## 交付前清单

- [ ] 覆盖清单中每个概念、代码段、公式都有对应小节
- [ ] 每段代码都用 `#include <bits/stdc++.h>` + `using namespace std;` 开头，`g++ -std=c++14` 通过，样例输出与页面一致
- [ ] 每行代码都有对应注释
- [ ] 页面打开后公式显示正确，没有裸露的 `$$` 或 `\frac`
- [ ] 整页搜不到 `∑ ∏ ∫ √ ≤ ≥ ≠ → ∈` 这类 Unicode 数学符号，全部是 LaTeX 命令
- [ ] 全站字号、字体、行距统一；无大字模式/高对比/响应式字号/移动端适配/无 viewport；强调只用常规加粗与一两种强调色
- [ ] 正文没有出现"轻度智力障碍""为了你""因为你是初学者"这类讲读者情况或适配理由的话
- [ ] 目录锚点全部可跳转，长代码行可横向滚动，深色模式下可读
- [ ] 材料中的错误已指出并给出修正版
- [ ] 产物目录只剩网页文件，中间产物已删除
- [ ] 若存在 `.gitignore`：只追加了本轮产物的忽略规则、原有规则未改，并执行过 `git rm -r --cached .` + `git add .`；`git status` 显示只有网页被新增，原有跟踪/忽略状态未变
