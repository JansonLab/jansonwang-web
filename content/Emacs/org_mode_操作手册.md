---
title: "ORG MODE 操作手册"
author: ["ubuntu"]
date: 2025-10-20T16:01:00+08:00
draft: false
---

## Emacs Org-mode 的常见用法 {#emacs-org-mode-的常见用法}

Org-mode 是 Emacs
中的一个强大工具，主要用于结构化文本处理。它支持从简单笔记到复杂项目管理的多种场景。以下是基于官方文档和用户实践总结的常见用法，我将它们分类列出，并附带简要说明和示例。每个用法都附带引用来源（来自网络搜索结果）。


### 笔记和文档创建（Note-taking and Document Creation） {#笔记和文档创建-note-taking-and-document-creation}

-   描述：Org-mode
    擅长纯文本笔记，支持标题层级、列表和链接，便于组织想法和知识库。适合日常记录或长文写作。
-   示例：使用 \`\* 标题=创建大纲，按=C-c C-t\` 切换 TODO 状态。
-   引用：常见于用户笔记实践和初学者教程。


#### 标题 {#标题}

这里是 Org 语法的代码块示例：
\`\* 一级标题\`
\`\*\* 二级标题\`
\`\*\*\* 三级标题\`


#### 文本格式 {#文本格式}

```org
*粗体*
/斜体/
_下划线_
+删除线+
=等宽文本=
~代码~
```


#### 列表 {#列表}

```org
- 无序列表项
+ 另一种无序列表
1. 有序列表项
- [ ] 复选框任务
- [X] 已完成任务
```


#### 链接 {#链接}

```org
[[https://example.com][描述文本]]
[[file:/path/to/file][文件链接]]
```

{{< figure src="./jobs.jpeg" >}}


#### 表格 {#表格}

```org
| 姓名 | 年龄 | 职业   |
|------+------+--------|
| 张三 |   25 | 工程师 |
```


#### 引用和注释 {#引用和注释}

```org
#+BEGIN_QUOTE
引用文本
#+END_QUOTE

#+BEGIN_COMMENT
注释内容（导出时隐藏）
#+END_COMMENT
```


#### 元数据 {#元数据}

```org
#+TITLE: 文档标题
#+AUTHOR: 作者
#+DATE: 日期
#+TAGS: 标签1 标签2
```


#### 时间戳 {#时间戳}

```org
<2024-01-01 周一>
<2024-01-01 周一 10:00>
```


#### 脚注 {#脚注}

```org
这是一个句子[fn:1]。

[fn:1] 这是脚注内容。
```


### 任务管理和规划（TODOs and Planning） {#任务管理和规划-todos-and-planning}

-   描述：内置 TODO 关键词（如 TODO/DONE），支持优先级、标签和截止日期。结合议程视图（Agenda）可生成每日/周任务列表。
-   示例： `* TODO 任务`  <code>[2/3]</code>=表示进度，=SCHEDULED: <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-10 五&gt;</span></span>\`
    添加日期。
-   引用：广泛用于个人任务和项目规划。


#### 任务管理 {#任务管理}

任务通常以一级标题开头，并在标题前添加关键字TODO来标记状态。Org Mode 不需要单独的文件来管理 TODO，而是将它们嵌入笔记中。

-   基本的 TODO 语法：
    -   标记为 TODO
        -   示例: `* TODO 任务名称`
            ```org
            ​* TODO 完成报告
              任务描述...
            ```
    -   完成任务: `* DONE 任务名称`
    -   切换状态: 使用快捷键 `C-c C-t` 在标题上循环(默认从TODO到DONE)
-   扩展 TODO 状态：
    -   默认只有 TODO 和 DONE，但可以自定义序列（如工作流：TODO → NEXT → DONE）。
    -   配置示例（在 Emacs 中设置 org-todo-keywords）：
        ```emacs-lisp
        (setq org-todo-keywords
        '((sequence "TODO(t!)" "NEXT(n)" "|" "DONE(d!)" "CANCELED(c)")))
        ```

        -   t! 表示快速选择键（t 为 TODO），! 表示切换时记录日志。
    -   语法：标题前添加关键字，如 \*\* NEXT 子任务。
    -   快速访问：按 C-c C-t 后输入字母键（如 t for TODO）。

-   优先级：
    -   在标题后添加 [#A]（A 为最高，B 为中等，C 为最低）。
    -   示例：\* TODO 紧急任务 [#A]
    -   设置快捷键：C-c , 在标题上。
-   日志记录（Logging）：
    -   当标记为 DONE 时自动记录时间或笔记。
    -   配置：org-log-done 'time（只记录时间）或 'note（提示输入笔记）。
    -   示例输出：在 DONE 后添加 CLOSED: <span class="timestamp-wrapper"><span class="timestamp">[2025-10-09 四 10:00]</span></span>。
-   任务依赖：
    -   使用属性 :ORDERED: t 确保父任务在所有子任务完成前不能标记为 DONE。
    -   示例：
-   全局 TODO 列表
    -   使用 Agenda 视图：M-x org-agenda t 显示所有未完成任务。
    -   过滤特定状态：C-c / t 后输入关键字（如 "TODO|NEXT"）。


#### 规划（Planning with Timestamps） {#规划-planning-with-timestamps}

Org Mode 使用时间戳（&lt;YYYY-MM-DD Day&gt;）来规划任务。时间戳可以是活动的（显示在 Agenda 中）或非活动的（[YYYY-MM-DD Day]）。

-   基本时间戳语法：
    -   活动时间戳：~<span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-09 四&gt;</span></span>~
    -   范围：~<span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-09 四&gt;&#x2013;&lt;2025-10-15 三&gt;</span></span>~
    -   插入快捷键：C-c .（带时间）或 C-c !（非活动）。
-   调度（Scheduling）
    -   表示任务开始工作的日期，使用 SCHEDULED: 关键字。
    -   语法：在标题下一行添加 SCHEDULED: <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-09 四&gt;</span></span>
    -   示例：

    -   插入： `C-c C-s` ， 支持相对日期如 +1d（明天）或 +2w（两周后）。
    -   在 Agenda 中，从调度日期起显示，直到完成。

-   截止日期（Deadline）：
    -   表示任务到期日期，使用 DEADLINE: 关键字。
    -   语法：在标题下一行添加 `DEADLINE:<2025-10-15>`
    -   示例：
    -   插入： `C-c C-d`
    -   警告：默认提前 org-deadline-warning-days（通常 14 天）在 Agenda 中显示警告。可自定义警告期，如 DEADLINE: <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-15 三 -5d&gt;</span></span>（提前 5 天）。

-   重复任务（Repeaters）：
    -   在时间戳后添加重复间隔，如 +1w（每周）、++1m（每月第一个）。
    -   示例：
    -   当标记为 DONE 时，Org Mode 自动推进到下一次日期，并重置为 TODO。
    -   配置日志：org-log-repeat 'note（记录重复时笔记）。

-   时间跟踪（Clocking）：
    -   开始计时：C-c C-x C-i，结束：C-c C-x C-o。
    -   示例：在任务下显示 CLOCK: <span class="timestamp-wrapper"><span class="timestamp">[2025-10-09 四 09:00]&#x2013;[2025-10-09 四 10:00] </span></span> =&gt; 1:00。
    -   报告：使用 :CLOCKTABLE: 动态表格生成时间总结。

-   实用提示
    -   Agenda 视图：核心工具，用于汇总任务。M-x org-agenda 打开，a 显示日程，t 显示 TODO，d 显示截止日期。
    -   稀疏树：快速过滤，如 C-c / d 显示所有 DEADLINE。
    -   自定义：在 .emacs 中设置 org-agenda-files 指定扫描的文件列表。
    -   示例完整任务：


### 项目管理和回顾（Project Management and Retrospectives） {#项目管理和回顾project-management-and-retrospectives}

-   \*描述\*：通过大纲结构管理项目阶段，支持子任务和属性抽屉（PROPERTIES）。适合团队协作或个人项目跟踪。
-   \*示例\*：使用表格记录里程碑：=#+tblname: milestones | Phase | Status |=。
-   \*引用\*：常用于客户工作和项目回顾。

org mode 通过其内置的大纲（outline）结构、属性抽屉（drawer）和表格功能，非常适合项目管理和回顾。它允许将项目分解为阶段、子任务，并使用属性存储元数据（如截止日期、负责人）。回顾（retrospective）通常通过表格或列表总结经验教训，支持团队协作（例如导出为 HTML 或共享 .org 文件）。以下按需求分解常见语法，基于 Org Mode 标准功能（无需额外插件）。


#### 大纲结构管理项目阶段（Outline for Project Phases） {#大纲结构管理项目阶段-outline-for-project-phases}

Org Mode 使用星号（\*）创建分层大纲，支持无限嵌套子任务。每个标题可标记为 TODO 状态，结合调度/截止日期实现阶段跟踪。适合个人或团队：子任务可分配属性。

-   基本语法：
    -   顶级标题：\* 项目名称
    -   示例（完整项目大纲）
        ```org
        ​* 项目X：开发新功能
        :PROPERTIES:
        :START_DATE: <2025-10-09 Thu>
        :END_DATE: <2025-11-09 Sun>
        :RESPONSIBLE: Alice
        :END:

        ** TODO 规划阶段
        SCHEDULED: <2025-10-09 Thu>
        DEADLINE: <2025-10-16 Thu>
        描述：需求分析和资源分配。

        *** NEXT 调研需求
        *** TODO 制定计划

        ** IN_PROGRESS 开发阶段
        SCHEDULED: <2025-10-17 Thu>
        DEADLINE: <2025-11-02 Sat>

        *** TODO 编码实现
        *** TODO 测试

        ** DONE 测试阶段
        CLOSED: [2025-11-03 Sun]
        ```
    -   实用提示：
        -   插入时间戳： `C-c .` （调度/截至）
        -   折叠/展开： `TABle` 切换大纲视图
        -   全局视图： `M-x org-agenda`


#### 支持子任务和属性抽屉（PROPERTIES Drawer） {#支持子任务和属性抽屉-properties-drawer}

属性抽屉用于存储项目元数据，如负责人、预算、标签。语法在标题后立即添加 :PROPERTIES: 块，支持自定义键值对。子任务继承父级属性，或独立设置。

-   基本语法：
    -   抽屉格式：
        ```org
        :PROPERTIES:
        :KEY: value
        :END:
        ```
    -   常见属性：
        -   `:RESPONSIBLE:` ：负责人（e.g., "Alice, Bob"）。
        -   `:BUDGET:` ：预算（e.g., "5000 USD"）。
        -   `TAGS:` ：标签（e.g., "urgent client"，用于 Agenda 过滤 C-c / m）。
        -   `LOGGING` 日志设置（e.g., "note"，DONE 时记录笔记）。
-   示例（子任务与属性）
    ```org
    ​* 项目Y：客户交付
       :PROPERTIES:
       :CLIENT: Acme Corp
       :BUDGET: 10000
       :TAGS: client urgent
       :END:

    ** TODO 需求收集
       :PROPERTIES:
       :RESPONSIBLE: Bob
       :DEADLINE: <2025-10-15 Wed>
       :END:
       子任务描述...

    ** NEXT 实施
       :PROPERTIES:
       :RESPONSIBLE: Alice
       :END:
    ```

-   实用提示：
    -   编辑属性： `C-c C-x p` 在抽屉上
    -   继承：子任务自动继承未覆盖的父属性
    -   导出： `C-c C-e` 生成HTML/PDF,属性转为元数据，便于团队分享


#### 使用表格记录里程碑（Tables for Milestones） {#使用表格记录里程碑-tables-for-milestones}

org Mode 的表格语法简单，支持命名（#+tblname:）和公式计算。适合记录项目里程碑，如阶段、状态、日期。表格可动态更新（e.g., 状态从 TODO 拉取）。

-   基本语法：
    -   创建表格：使用 `|` 分隔列， `-` 作为分隔线。
    -   命名：#+tblname: milestones（便于引用）。
    -   示例（里程碑表格）：

        <a id="table--milestones"></a>

        | Phase | Status      | Start Date                                                                                 | End Date                                                                                   | Notes |
        |-------|-------------|--------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|-------|
        | 规划  | DONE        | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-09 四&gt;</span></span> | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-16 四&gt;</span></span> | 需求确认 |
        | 开发  | IN_PROGRESS | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-10-17 五&gt;</span></span> | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-11-02 日&gt;</span></span> | 编码中 |
        | 测试  | TODO        | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-11-03 一&gt;</span></span> | <span class="timestamp-wrapper"><span class="timestamp">&lt;2025-11-09 日&gt;</span></span> | 待分配资源 |
-   实用提示：
    -   插入行/列： `TAB` 导航， `M-RET` 新行。
    -   公式： `#+TBLFM:` 行计算值（e.g., 总时长）。
    -   链接任务：表格单元格可嵌入链接到大纲标题。
    -   排序/过滤： `C-c ^` 排序，按状态过滤


#### 项目回顾（Retrospectives） {#项目回顾-retrospectives}

回顾通常在项目末尾使用表格或列表总结“做得好/改进点/行动项”。结合属性记录责任人，支持后续跟踪。

-   基本语法：
    -   回顾标题： `* 项目回顾`
    -   表格示例（回顾总结）：
        ```org
        ​* 项目X 回顾
          CLOSED: [2025-11-10 Mon]
          :PROPERTIES:
          :OVERALL_SCORE: 8/10
          :END:

          #+tblname: retrospective
          | Category   | What Went Well          | Improvements Needed     | Action Items                  | Owner  |
          |------------|-------------------------|-------------------------|-------------------------------|--------|
          | 过程       | 团队沟通顺畅            | 工具集成不足            | 引入 Slack 集成               | Alice  |
          | 交付       | 提前完成测试            | 文档不全                | 更新模板，下个项目应用        | Bob    |
          | 风险       | 预算控制好              | 未预见依赖延误          | 添加风险矩阵                  | Team   |
        ```

-   实用提示：
    -   行动项链接：将“Action Items”列设为 TODO 链接，如 `[[* 更新模板][TODO]]` 。
    -   团队协作：使用 `org-attach` 附件（C-c C-a）添加会议录音或反馈PDF。
    -   报告生成：#+BEGIN_EXPORT html 块导出为网页，便于分享回顾。

整体示例：完整项目文件片段

```org
 * 项目概述
    :PROPERTIES:
    :ID: proj-x-001
    :CLIENT: Acme
    :END:

** TODO 阶段1：规划
    #+tblname: milestones
    | Phase | Status |
    |-------|--------|
    | 规划  | TODO   |
* 项目回顾
    #+tblname: retrospective
    | Category | What Went Well |
    |----------|----------------|
    | 过程     | 高效协作      |
```

这些语法使 Org Mode 成为轻量级项目工具，易于从规划到回顾的全流程。结合 Agenda（M-x org-agenda）可实时跟踪进度。


### 文学编程和可重复研究（Literate Programming and Reproducible {#文学编程和可重复研究-literate-programming-and-reproducible}

-   描述：通过 Org-babel 嵌入代码块（支持 100+
    语言），执行、tangle（提取代码）和weave（生成报告）。理想用于科研或开发文档。
-   示例： `#+BEGIN_SRC python ... #+END_SRC` 执行代码， `C-c C-c` 运行。
-   引用：用户常用作可重复研究和编程工具。

Org Mode 通过 Org-Babel（也称 Babel）实现文学编程（Literate Programming），允许在文档中嵌入可执行代码块，支持 100+ 编程语言（如 Python、R、Julia、Shell 等）。这使得文档不仅是叙述，还能生成代码文件（tangle）和报告（weave），非常适合可重复研究（Reproducible Research）：科研人员可嵌入分析代码、数据和结果，确保实验可重现。Babel 需要在 Emacs 配置中启用（默认已支持），并安装相应语言解释器。以下按需求分解常见语法，重点在代码块的嵌入、执行、提取和生成报告。示例基于标准 Org-Babel 功能。


#### 嵌入代码块（Code Blocks） {#嵌入代码块-code-blocks}

代码块使用 `#+BEGIN_SRC` 和 `#+END_SRC` 包围，支持语言指定、参数和结果显示。块内代码可与叙述混合，适合科研文档中解释分析步骤。

-   基本语法：
    -   格式：
        ```org
        #+BEGIN_SRC 语言 [选项]
        代码内容...
        #+END_SRC
        ```
    -   选项：
        -   `:results output` ：显示输出（默认）。
        -   `:results value` ：显示返回值（如数字、表格）。
        -   `:exports both` ：导出时包含代码和结果（默认 code）。
        -   `:session` ：共享会话（e.g., `:session *python*` 保持变量状态）。
        -   `:header-args` ：全局设置，如 `#+HEADER: :tangle no` （不提取代码）。
-   示例（Python）
    ```org
    ​* 数据分析示例
    以下代码加载数据并计算均值。

    #+BEGIN_SRC python :results value :exports both
    import numpy as np
    data = np.array([1, 2, 3, 4, 5])
    mean_value = np.mean(data)
    return mean_value
    #+END_SRC

    结果：mean_value = 3.0
    ```

    -   执行：将光标置于块内，按 `C-c C-c` 运行（输出显示在块后）
    -   支持语言：Python、R、Emacs Lisp 等；查询支持列表： `M-x org-babel-load-languages` 。

-   实用提示：
    -   结果类型： `scalar` （单值）、 `table` （表格）、 `graphics` （图像，如 matplotlib 图表）。
    -   变量传递：使用 `srcname` 引用结果，如 `#+NAME: data` 后在下个块中 `data = (get "data")` 。


#### 执行代码（Execution） {#执行代码-execution}

Babel 允许在 Org 文件中直接运行代码，确保结果嵌入文档，实现可重复性。执行后，结果自动更新，支持缓存（:cache yes）避免重复计算。

-   基本语法：
    -   执行快捷键： `C-c C-c` （在块上）
    -   批量执行： `C-c C-v C-b` （所有块）或 `C-c C-v v` （可视化结果）。
    -   错误处理：失败时显示traceback; 用 `:eval never` 禁用执行。

-   示例（R语言统计分析）：
    ```org
    #+BEGIN_SRC R :session *R* :results output
    data <- c(1, 2, 3, 4, 5)
    summary(data)
    #+END_SRC

    #+RESULTS:
    :    Min. 1st Qu.  Median    Mean 3rd Qu.    Max.
    :    1.00   2.00   3.00   3.00   4.00   5.00
    ```

    -   共享会话：同一 `:session` 的块保持状态，便于迭代研究

-   实用提示：
    -   图像结果： `:results graphics` + `:file output.png` ，生成图表文件。
    -   科研应用：嵌入 Jupyter-like 工作流， `C-c C-v C-a` 转义特殊字符。


#### Tangle（提取代码） {#tangle-提取代码}

Tangle 将代码块从文档中提取到独立源文件，支持多语言文件生成。理想用于从叙述中“编织”出完整程序，确保代码与文档同步。

-   基本语法：
    -   块选项： `:tangle 文件名` （e.g., `:tangle main.py` ）。
    -   全局设置： `#+PROPERTY: header-args :tangle yes` （所有块提取）。

-   示例（多块tangle到Python）
    ```org
    *导入部分
    #+BEGIN_SRC python :tangle main.py
    import numpy as np
    #+END_SRC

    * 计算部分
    #+BEGIN_SRC python :tangle main.py
    def calculate_mean(data):
      return np.mean(data)
    #+END_SRC

    * 主函数
    #+BEGIN_SRC python :tangle main.py
    if __name__ == "__main__":
      print(calculate_mean([1,2,3]))
    #+END_SRC
    ```

    -   结果：生成 `main.py` ：
        ```python
        import numpy as np

        def calculate_mean(data):
          return np.mean(data)

        if __name__ == "__main__":
          print(calculate_mean([1,2,3]))
        ```

    -   实用提示：
        -   条件 tangle： `:tangle (if condition "file")` （Emacs Lisp 表达式）。
        -   多文件：不同块指定不同文件名，支持noweb引用（ `<<block-name>>` 插入其他块）。


#### Weave（生成报告） {#weave-生成报告}

Weave 将 Org 文档“编织”成报告（如 PDF、HTML），包括叙述、代码和结果。支持 LaTeX、HTML 等后端，适合发布可重复研究论文。

-   基本语法：
    -   导出： `C-c C-e` 菜单，选择后端（如 l o为LateX PDF）。
    -   块选项： `:exports results` （只显示结果）或 `:noweb yes` （展开引用）。
    -   全局： `#+OPTIONS: ^:{} num:nil` （自定义导出格式）。

-   示例（生成报告）：
    ```org
    #+TITLE: 可重复研究报告
    #+LANGUAGE: zh-CN
    #+EXPORT_FILE_NAME: report

    * 引言
    本报告演示数据分析。

    #+BEGIN_SRC python :results value :exports results
    2 + 2
    #+END_SRC

    #+RESULTS:
    : 4
    ```
    导出后：PDF/HTML 中显示叙述+结果（无代码，如果 `:exports results` ）

-   实用提示：
    -   表格结果：自动转为 LaTeX/HTML 表格。
    -   扩展：结合 `org-publish` 发布网站；科研模板如 Sweave（R）集成。


#### 整体示例 {#整体示例}

```org
#+TITLE: 可重复数据分析
#+AUTHOR: Researcher
#+DATE: <2025-10-10 Fri>

* 摘要
使用 Python 分析数据集。

* 方法+-
加载数据：

#+NAME: load-data
#+BEGIN_SRC python :tangle analysis.py :results value
import pandas as pd
df = pd.read_csv('data.csv')
df.head()
#+END_SRC

#+RESULTS: load-data
| col1 | col2 |
|------|------|
| 1    | a    |

* 结果
计算统计：

#+BEGIN_SRC python :session *py* :noweb yes :tangle analysis.py
mean_val = df['col1'].mean()
<<load-data>>
print(mean_val)
#+END_SRC

* 结论
分析完成，可重复执行。
```

-   Tangle: 生成 `analysis.py`
-   Weave: 导出为报告，包含结果表格。

Org-Babel 使 Org Mode 成为科研和开发的神器，支持从草稿到发布的无缝流程。结合 Git 版本控制，确保完全可重复。


### 演示文稿和导出（Presentations and Exporting） {#演示文稿和导出presentations-and-exporting}

-   \*描述\*：导出到
    HTML、PDF、Beamer（幻灯片）等格式。支持主题和样式自定义。
-   \*示例\*：=C-c C-e l l= 导出为 LaTeX PDF。
-   \*引用\*：适用于书籍、演示和技术文档。

Org Mode 的导出功能（Exporting）是其核心优势之一，支持将 .org 文件无缝转换为多种格式，如 HTML、PDF、LaTeX，甚至 Beamer 幻灯片（适合演示文稿）。导出通过后端（如 ox-html、ox-latex）实现，默认内置；自定义主题和样式使用变量或 CSS/LaTeX 模板。适用于书籍（e.g., 导出为 ePub）、演示（Beamer）和技术文档（HTML with syntax highlighting）。导出快捷键：C-c C-e 打开菜单，选择后端（e.g., l l 为 LaTeX PDF）。

以下按需求分解常见语法，基于 Org Mode 标准导出引擎（ox）。假设 Emacs 配置中已启用相关后端。


#### 导出基础（Basic Exporting） {#导出基础-basic-exporting}

导出从整个文件或子树（ `C-u C-c C-e` 指定范围）开始。Org 文件头部设置全局选项，如 `#+TITLE:、#+AUTHOR:` ，影响所有格式。

-   基本语法：
    -   文件头部：
        ```org
        #+TITLE: 我的演示文稿
        #+AUTHOR: 作者姓名
        #+DATE: <2025-10-10 Fri>
        #+LANGUAGE: zh-CN
        #+OPTIONS: toc:nil num:headline ^:{}  ; toc: 目录；num: 编号；^: 标题后内容；{}: 表格边框
        ```

    -   导出命令：
        -   `C-c C-e` ：菜单选择（e.g., `h h` 为 HTML， `l l` 为 LaTeX PDF， `b b` 为 Beamer PDF）。
        -   命令行： `emacs --batch --load=org.el --eval '(org-latex-export-to-pdf)' file.org`

-   示例（简单导出设置）：
    ```org
    #+TITLE: Org Mode 指南
    #+EXPORT_FILE_NAME: guide
    #+HTML_HEAD: <style>body {font-family: Arial;}</style>  ; HTML 自定义 CSS

    * 第一节
      内容...

    * 第二节
      #+LaTeX: \textbf{粗体}  ; LaTeX 特定语法
    ```

    -   导出结果： `guide.html` 或 `guide.pdf` 。

<!--listend-->

-   实用提示：
    -   子树导出：光标在标题，按 `C-c C-e r` （region）。
    -   预览： `C-c C-e v` （visible only，测试无文件生成）。
    -   钩子：Emacs 配置 `org-export-before-processing-hook` 添加预处理。


#### 导出到HTML（HTML Export） {#导出到html-html-export}

适合网页、技术文档、支持代码高亮（通过htmlize.el）和元素交互。默认使用简单主题，可自定义CSS。

-   基本语法：
    -   头部选项：
        ```org
        #+HTML_DOCTYPE: html5
        #+HTML_CONTAINER_CLASS: wide
        #+HTML_LINK_UP: index.html  ; 导航链接
        #+HTML_MATHJAX: align:"center"  ; 数学公式支持
        ```

    -   块特定： `#+BEGIN_EXPORT html ... #+END_EXPORT` （只在 HTML 中显示）。

-   示例（HTML文档）
    ```org
    #+TITLE: Web 指南
    #+HTML_HEAD: <link rel="stylesheet" type="text/css" href="custom.css">

    * 介绍
      #+BEGIN_EXPORT html
      <div class="alert">警告：测试内容</div>
      #+END_SRC

    #+BEGIN_SRC python :exports code
    print("Hello World")
    #+END_SRC
    ```

    -   导出： `C-c C-e h h` 生成带语法高亮的 `guide.html`

<!--listend-->

-   实用提示：
    -   主题：设置 `org-html-postamble` 添加页脚；用 `org-publish` 批量发布网站。
    -   代码块： `:exports code` 只显示代码， `:results` 显示执行结果。


#### 导出到PDF/LaTeX (PDF/LaTeX Export) {#导出到pdf-latex--pdf-latex-export}

通过 pdflatex 生成 PDF，适合书籍和技术文档。支持目录、交叉引用和浮动元素。

-   基本语法：
    -   头部选项：
        ```org
        #+LATEX_HEADER: \usepackage{ctex}  ; 中文支持
        #+LATEX_HEADER_EXTRA: \usepackage{hyperref}  ; 超链接
        #+OPTIONS: H:3 toc:t num:nil  ; H: 标题级别；toc: 目录；num: 无编号
        ```

    -   LaTeX 特定块： `#+BEGIN_EXPORT latex ... #+END_EXPORT`

<!--listend-->

-   示例（LaTeX PDF）：
    ```org
    #+TITLE: PDF 书籍
    #+LATEX_CLASS: article
    #+LATEX_CLASS_OPTIONS: [a4paper,12pt]

    \tableofcontents  ; 手动目录

    * 第一章
      方程：\( E = mc^2 \)

      #+BEGIN_EXPORT latex
      \begin{equation}
      x = \sum_{i=1}^n i
      \end{equation}
      #+END_EXPORT

    #+RESULTS: 表格结果（从 Babel）
    | Col1 | Col2 |
    | 1    | a    |
    ```

    -   导出： `C-c C-e l l` ，生成 `guide.pdf` （需安装 TeX Live）

<!--listend-->

-   实用提示：
    -   类选择： `org-latex-classes` 配置 Beamer 等。
    -   公式：内联 `$...$` 或 `#+BEGIN_LaTeX` 环境。
    -   书籍：用 `book` 类，章节用 `* Chapter`


#### Beamer 演示文稿（Beamer Presentations） {#beamer-演示文稿-beamer-presentations}

Beamer 是 LaTeX 的幻灯片框架，Org Mode 原生支持。将大纲标题转为幻灯片，子标题为子帧。自定义主题、颜色和过渡。

-   基本语法：
    -   头部选项：
        ```org
        #+BEAMER_HEADER: \usetheme{Madrid}  ; 主题
        #+BEAMER_HEADER_EXTRA: \usecolortheme{beaver}  ; 颜色
        #+OPTIONS: H:2 toc:nil  ; H:2 只前两级为帧
        #+BEAMER_ENVARGS: [plain]  ; 特定帧选项
        ```

    -   帧语法：标题用 `* Frame Title` ，子用 `** Subframe` ； `#+BEAMER: \pause` 添加暂停。

<!--listend-->

-   示例（Beamer 幻灯片）
    ```org
    #+TITLE: Org Mode 演示
    #+BEAMER_THEME: Copenhagen
    #+BEAMER_COLOR_THEME: lily

    * 第一帧：标题页
      #+BEAMER: \centering
      #+BEAMER: \Large{欢迎}

    * 第二帧：内容
      ** 点1
    ​     - 项目符号1
      ** 点2
         #+BEAMER: \pause
    ​     - 项目符号2
         #+BEGIN_EXPORT beamer
         \begin{block}{方块}
         内容...
         \end{block}
         #+END_EXPORT

    * 结束帧
      #+BEAMER: \thankyou
    ```

    -   导出： `C-c C-e b b` ，生成 Beamer PDF（每 \* 为一帧）。

<!--listend-->

-   实用提示：
    -   主题列表： `\usetheme{default|Warsaw|...}`
    -   动画： `\pause、[+]（增量显示列表）` 。
    -   预览： `C-c C-e b v` 测试。


#### 主题和样式自定义（Themes and Styling） {#主题和样式自定义-themes-and-styling}

跨格式自定义使用变量、头部或模板。HTML 用 CSS，LaTeX/Beamer 用包。

-   基本语法：
    -   全局变量（Emacs 配置）：
        ```emacs-lisp
        (setq org-export-with-toc t
              org-html-style "<style>p {margin: 1em;}</style>")
        ```

    -   模板： `org-export-template-functions` 自定义钩子。
    -   块： `#+ATTR_HTML: :class alert` （HTML）或 `#+ATTR_LaTeX: :float t` （浮动）

<!--listend-->

-   示例（自定义样式）：

<!--listend-->

```org
#+HTML_HEAD: <style> h1 {color: blue;} </style>
#+LaTeX_HEADER: \usepackage{xcolor} \definecolor{myblue}{RGB}{0,100,200}

* 自定义标题
  #+ATTR_ORG: :attr_html (class "highlight")
  突出内容
```

实用提示：

-   发布： `M-x org-publish-project` 批量导出，定义 `org-publish-project-alist` 。
-   扩展：ox-md for Markdown，ox-reveal for JS 幻灯片。

完整示例：完整演示文件片段

```org
#+TITLE: Org 导出演示
#+AUTHOR: 示例用户
#+DATE: <2025-10-10 Fri>
#+LANGUAGE: zh-CN
#+BEAMER_THEME: Berlin
#+HTML_HEAD: <style>body {background: lightblue;}</style>
#+LaTeX_HEADER: \usepackage{ctex}

* 幻灯片1：概述
  导出 Org 到多种格式。

* 幻灯片2：示例
  #+BEGIN_SRC emacs-lisp :exports code
  (message "Hello")
  #+END_SRC

#+OPTIONS: toc:t H:2
```

-   多格式导出： `C-c C-e` 选择 h/l/b

Org 导出使文档“活起来”，从笔记到专业输出一键完成。结合 Babel，结果自动嵌入报告。


### 调度和时间管理（Scheduling and Time Tracking） {#调度和时间管理scheduling-and-time-tracking}

-   \*描述\*：处理时间戳、重复事件和日志笔记（Logbook）。Agenda
    视图汇总多个文件的事件。
-   \*示例\*：=C-c C-s= 调度任务，=M-x org-agenda= 查看日程。
-   \*引用\*：内置时间工具支持常见时间用例。

Org Mode 的调度（scheduling）和时间跟踪（time tracking）功能内置于其时间戳系统和 Agenda 视图中，支持时间戳（timestamps）、重复事件（repeaters）和日志笔记（logbook）。这些工具允许处理单次/重复任务、记录状态变化和时长统计，汇总多个 .org 文件的事件。默认无需额外配置；Agenda（M-x org-agenda）是核心视图，提供日历式概览。适合日常时间管理、项目跟踪或习惯养成。

以下按需求分解常见语法，基于 Org Mode 标准功能。时间戳格式统一为 &lt;YYYY-MM-DD [Day] [HH:MM]&gt;，使用 C-c . 插入。


#### 时间戳（Timestamps） {#时间戳-timestamps}

时间戳标记事件日期/时间，支持活动（active，显示在 Agenda 中）和非活动（inactive，[YYYY-MM-DD Day]）类型。范围用 -- 表示。

-   基本语法：
    -   活动时间戳： `<2025-10-10 Fri>`
    -   非活动时间戳： `[2025-10-10 Fri]`
    -   带时间： `<2025-10-10 Fri 14:30>`
    -   插入快捷键： `C-c .` （活动，带日历选择器）； `C-c !` （非活动）。

<!--listend-->

-   示例：
    ```org
    ​* 会议笔记
      会议时间：<2025-10-10 Fri 10:00-11:00>
      总结：讨论了项目进度。
    ```

-   实用提示：
    -   计算： `C-u C-c .` 插入时计算天数差。
    -   日历集成： `C-c .` 打开Emacs日历，支持 `SPC` 选择日期。


#### 调度任务（Scheduling） {#调度任务-scheduling}

调度（SCHEDULED）指定任务开始日期，在 Agenda 中从该日起显示（直到完成）。常用于计划性工作。

-   基本语法：
    -   在标题下一行： `SCHEDULED: <2025-10-10 Fri>`
    -   插入快捷键： `C-c C-s` （选择日期，相对/绝对）。

<!--listend-->

-   示例：
    ```org
    ​* TODO 准备报告
      SCHEDULED: <2025-10-10 Fri>
      任务细节...
    ```

    -   Agenda 显示：从 10 月 10 日起标记，直到 DONE。

-   实用提示：
    -   延迟： `SCHEDULED: <2025-10-10 Fri -1d>` （提前一天显示）。
    -   批量： `C-c C-s` 支持 `+2d` 等相对偏移


#### 截至日期（Deadline） {#截至日期-deadline}

截止日期（DEADLINE）指定任务到期日，在 Agenda 中提前警告（默认 14 天）。

-   基本语法：
    -   在标题下一行： `DEADLINE: <2025-10-17 Fri>`
    -   插入快捷键： `C-c C-d` 。

<!--listend-->

-   示例：
    ```org
    ​* TODO 提交提案
      DEADLINE: <2025-10-17 Fri>
      警告期：默认提前 14 天。
    ```
    自定义警告： `DEADLINE: <2025-10-17 Fri -7d>` （提前 7 天）。

<!--listend-->

-   实用提示：
    -   配置： `org-deadline-warning-days 7` 全局设置警告天数。
    -   视图：Agenda `d` 日显示 DEADLINE。


#### 重复时间（Repeating Events） {#重复时间-repeating-events}

重复器（repeaters）自动推进日期，当任务完成时重置为 TODO。支持周/月/年周期。

-   基本语法：
    -   在时间戳后添加间隔： `+1w` （每周）、 `++2m` （每月第二个）、 `.+1y` （每年）。
    -   `+` ：简单重复； `++` ：特定日子（e.g., 月初）； `.+` ：从当前推进。

<!--listend-->

-   示例：

<!--listend-->

```org
* TODO 每周备份数据
  SCHEDULED: <2025-10-10 Fri +1w>
  DEADLINE: <2025-10-17 Fri +1w>
```

-   完成时：Org 自动推进到下周，并重置状态。

<!--listend-->

-   实用提示：
    -   日历重复： `C-u C-c C-s` 插入时选择重复类型。
    -   限制： `:REPEAT_UNTIL：<2026-01-01>` 属性结束重复。


#### 日志笔记（Logbook） {#日志笔记-logbook}

Logbook 是属性抽屉（:LOGBOOK:），自动记录状态变化、笔记和时钟（clocking）。用于审计时间使用。

-   基本语法：
    -   抽屉：标题下 `:LOGBOOK: ... :END:` 。
    -   配置： `org-log-done 'time` （DONE 时记录时间）； `'note` （提示笔记）。
    -   时钟： `C-c C-x C-i` 开始计时， `C-c C-x C-o` 结束，记录 `CLOCK: [时间] => 时长` 。

<!--listend-->

-   示例：
    ```org
    ​* TODO 编码任务
      :LOGBOOK:
      CLOCK: [2025-10-10 Fri 09:00]--[2025-10-10 Fri 10:30] =>  1:30
    ​  - State "DONE"       from "TODO"       [2025-10-10 Fri 10:30]
      :END:
      开始：C-c C-x C-i
    ```

    -   状态日志：切换状态（ `C-c C-t` ）时自动添加条目

<!--listend-->

-   实用提示：
    -   报告： `::LOGBOOK:` 中用 `C-c C-x C-r` 插入时钟表格。
    -   全局日志： `org-log-states-order` 自定义状态日志。


#### Agenda 视图（Agenda Views） {#agenda-视图-agenda-views}

Agenda 汇总多个文件（org-agenda-files 列表）的事件，提供周/月/日/列表视图。支持过滤和自定义。

-   基本语法：
    -   打开： `M-x org-agenda` 或 `C-c a` 。
    -   视图类型：
        -   `a`: 日程（Agenda,显示 SCHEDULED/DEADLINE）。
        -   `t`: TODO 列表。
        -   `T`: 搜索特定TODO。
        -   `d`: 日视图; `w` 周; `m` 月。

<!--listend-->

-   示例使用：
    -   配置文件: `(setq org-agenda-files '("~/org/tasks.org" "~/org/projects.org"))`
    -   过滤: `C-c /` 稀疏树，或 Agenda 中 `/` 搜索。
    -   自定义: `org-agenda-custom-commands` 添加视图，如周 TODO。

<!--listend-->

-   实用提示：
    -   导航: `n/p` 下一/上项; `j` 跳转日期。
    -   习惯: `org-habit-prioritize` 跟踪连续性（彩色条形图）。
    -   导出: `v` 可见编辑, `x` 保存视图。

整体实用示例：

```org
#+TITLE: 每周计划
#+DATE: <2025-10-10 Fri>

* TODO 日常任务
  SCHEDULED: <2025-10-10 Fri>
  DEADLINE: <2025-10-11 Sat>
  :LOGBOOK:
  CLOCK: [2025-10-10 Fri 08:00]--[2025-10-10 Fri 09:00] =>  1:00
  :END:

* TODO 重复会议
  SCHEDULED: <2025-10-10 Fri +1w>  ; 每周五
  :PROPERTIES:
  :REPEAT_UNTIL: <2025-12-31 Wed>
  :END:

* 事件日志
  <2025-10-10 Fri 14:00-15:00> 项目回顾
```

这些语法覆盖常见时间用例，从简单调度到详细跟踪。Agenda 是可视化核心，按 `M-x org-agenda` 快速启动。


### 配置管理和结构化设置（Configuration Management） {#配置管理和结构化设置configuration-management}

-   \*描述\*：用 Org 结构化 Emacs 配置（如 .emacs），通过
    `org-babel-load-file` 加载 Lisp 块。
-   \*示例\*：在 Org 文件中嵌入 `#+BEGIN_SRC elisp` 配置，tangle 到
    init.el。
-   \*引用\*：适合 Emacs 设置文档化。

Org Mode 通过 Org-Babel 支持文学编程风格的配置管理，特别是针对 Emacs 配置（如 `init.el` 或 `.emacs` ）。这允许将配置代码嵌入文档中，结合叙述解释设置逻辑，便于维护和文档化。核心是使用 Emacs Lisp（elisp）代码块，tangle（提取）到配置文件，然后通过 `org-babel-load-file` 加载。适合 Emacs 用户将散乱的 init.el 转为结构化、可读的 Org 文件。Babel 默认支持 elisp，无需额外安装。

以下按需求分解常见语法，基于 Org Mode 标准功能。假设文件名为 `emacs-config.org` 。


#### 嵌入 Elisp 代码块（Embedding Elisp Code Blocks） {#嵌入-elisp-代码块-embedding-elisp-code-blocks}

在 Org 文件中使用 `#+BEGIN_SRC elisp` 块嵌入配置代码。块可执行（ `C-c C-c` 测试）、包含注释，并与叙述混合。使用 `:tangle` 选项指定提取目标。

-   基本语法：
    ```org
    #+BEGIN_SRC elisp [选项]
    (setq variable value)
    #+END_SRC
    ```

    -   选项：
        -   `:tangle init.el` ：提取到 `init.el` 。
        -   `:tangle no` ：不提取，只用于文档。
        -   `:results silent` ：执行时不显示输出。
        -   `:noweb yes` ：支持 noweb 引用（ `<<block-name>>` 插入其他块）。

<!--listend-->

-   示例（基本设置块）：
    ```org
    ​* Emacs 界面配置
    设置字体和主题。

    #+BEGIN_SRC elisp :tangle init.el :results silent
    (setq default-frame-alist
          '((font . "Monospace-12")
            (vertical-scroll-bars . nil)))
    (load-theme 'modus-vivendi t)
    #+END_SRC
    ```

<!--listend-->

-   实用提示：
    -   测试： `C-c C-c` 在块上执行，验证设置（重启 Emacs 生效）。
    -   变量共享： 用 `:session *emacs*` 保持状态。


#### Tangle 到配置文件（Tangling to Configuration Files） {#tangle-到配置文件-tangling-to-configuration-files}

Tangle 将所有 `:tangle yes` 或指定文件的 elisp 块提取到单一文件（如 `init.el` ），按块顺序连接。支持条件提取和头文件添加。

-   基本语法：
    -   全局设置: `#+PROPERTY: header-args :tangle init.el` （所有 elisp 块默认 tangle）
    -   头文件： `#+BEGIN_SRC elisp :tangle init.el :comments link` （添加块链接注释）
    -   执行： `C-c C-v t` （当前文件）或 `C-c C-v T` （所有）。

<!--listend-->

-   示例（多块tangle）:
    ```org
    #+PROPERTY: header-args :tangle init.el :comments org

    * 包管理
    #+BEGIN_SRC elisp
    (require 'package)
    (add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
    (package-initialize)
    #+END_SRC

    * Org Mode 配置
    #+BEGIN_SRC elisp :noweb yes
    (setq org-startup-folded "overview")
    <<package-org>>
    #+END_SRC

    #+NAME: package-org
    #+BEGIN_SRC elisp
    (use-package org
      :ensure t
      :config
      (setq org-log-done 'time))
    #+END_SRC
    ```

    -   结果 `init.el`
        ```emacs-lisp

        ;; -*- mode: elisp; -*-

        (require 'package)
        (add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
        (package-initialize)

        (setq org-startup-folded "overview")
        (use-package org
          :ensure t
          :config
          (setq org-log-done 'time))
        ```

<!--listend-->

-   实用提示：
    -   注释： `:comments org` 添加Org风格注释（链接回源块）。
    -   条件： `:tangle (if (eq system-type 'gnu/linux) "init.el")` （平台特定）。


#### 加载配置（Loading the Configuration） {#加载配置-loading-the-configuration}

加载方式有两种：直接 tangle 后 `M-x load-file init.el` ，或用 `org-babel-load-file` 动态加载 Org 文件（无需 tangle，执行所有 elisp 块）。

-   基本语法：
    -   动态加载：在 `init.el` 或命令行添加：
        ```emacs-lisp
        (org-babel-load-file "~/.emacs.d/emacs-config.org")
        ```

    -   快捷键： `C-c C-v l` （load 当前文件）

<!--listend-->

-   示例（在init.el中加载）：
    ```emacs-lisp
    ;; 在 init.el 开头
    (require 'org)
    (org-babel-load-file (expand-file-name "emacs-config.org" user-emacs-directory))
    ```

    -   启动 Emacs 时自动加载Org中的所有 elisp 块。

<!--listend-->

-   实用提示：
    -   安全： `org-confirm-babel-evaluate` 配置确认执行。
    -   部分加载：用 `:tangle no` 跳过测试块。


#### 文档和结构化最佳实践（Documentation and Structuring） {#文档和结构化最佳实践-documentation-and-structuring}

用 Org 大纲组织配置，按功能分节（e.g., \* 包管理），添加属性抽屉记录版本或依赖。导出为 HTML/PDF 作为设置手册。

-   基本语法：
    ```org
    ​* 高级功能
      :PROPERTIES:
      :VERSION: 1.0
      :DEPENDENCIES: use-package
      :END:
      解释：此节配置 AI 插件。
    ```

<!--listend-->

-   实用提示：
    -   导出： `C-c C-e h h` 生成HTML文档，包含代码高亮。
    -   版本控制：Git跟踪 `Emacs-config.org` ，忽略生效的 `init.el` 。
    -   扩展：结合 `use-package` 声明式配置，便于模块化。

完整示例：完整配置 Org文件片段

```org
#+TITLE: Emacs 配置手册
#+AUTHOR: 用户
#+DATE: <2025-10-10 Fri>
#+PROPERTY: header-args :tangle ~/.emacs.d/init.el :comments both

* 启动设置
  #+BEGIN_SRC elisp
  (setq inhibit-startup-message t)
  (tool-bar-mode -1)
  #+END_SRC

* 包和 Org 配置
  #+BEGIN_SRC elisp
  (require 'package)
  (package-initialize)
  #+END_SRC

  #+BEGIN_SRC elisp :noweb yes
  (use-package org
    :ensure t
    :config
    (setq org-todo-keywords '((sequence "TODO" "DONE"))))
  <<package>>
  #+END_SRC

  #+NAME: package
  #+BEGIN_SRC elisp
  (add-to-list 'package-archives '("melpa" . "https://melpa.org/packages/") t)
  #+END_SRC

* 加载指令
  在 ~/.emacs.d/init.el 中添加：
  #+BEGIN_SRC elisp :tangle no
  (org-babel-load-file "~/.emacs.d/emacs-config.org")
  #+END_SRC
```

-   Tangle 后：生成完整的 `init.el`
-   加载：重启 Emacs 或 `M-x eval-buffer`

这些用法覆盖了 Org-mode
的核心优势：灵活性和可扩展性。如果您是初学者，推荐从官方教程开始（orgmode.org）。如果需要特定用法的详细配置示例，请提供更多细节！
