---
title: latex深入拓展
date: 2024-06-06 16:30:11
tags: latex
---

## 1.原因

在用latex写作用参考文献时出现了错误，所以深入分析了一下latex的文件内容，顺便写一下latex相关的知识总结

## 2.latex运行过程

我的latex工程文件如下图所示

![image-20240606173530199](http://typora-sdj.oss-cn-chengdu.aliyuncs.com/img/image-20240606173530199.png)

其中红圈中的为重点文件。

### 2.1 main.tex

main.tex是我编写内容的主要文件其中的结构如下:

```latex
\documentclass[master]{thesis-uestc}
\title{}{}
\author{}{}
\advisor{}{}
\school{}{}
\major{}{}
\studentnumber{}

\begin{document}
	\makecover
	\begin{chineseabstract}
	\chinesekeyword{}
	\end{chineseabstract}
	
	\begin{englishabstract}
	\englishkeyword{}
	\end{englishabstract}
	
	\thesistableofcontents
	
	\chapter{}
		\section{}
            \subsection{}
                \subsection{}
                    \subsubsection{}
                  
   \thesisbibliography{reference}
   \thesisaccomplish{publications}
```

可以看到本文档的文档格式非常规，而是自行定义了一个叫**thesis-uestc**的名字，其表示我们使用了自定义的模板，模板的内容存放在**thesis-uestc.cls**文档中。

文档下方用**\thesisbibliography**，**\thesisaccomplish**取代了常规的**\bibliography**

### 2.2thesis-uestc.cls

该文件内容过多，足足有1100多行，其部分内容如下如下：

```latex
\NeedsTeXFormat{LaTeX2e} %指定需要的 LaTeX 格式
\ProvidesClass{thesis-uestc}   %定义该文档类的名称。

\LoadClass[12pt, openany, twoside]{book}  %加载基础类，定义文档的基本设置

\RequireXeTeX   %指定文档必须使用XeTeX引擎进行编译
 %加载必要的宏包
\RequirePackage{ifxetex}

%设置长度参数 （\lengthname要设置的长度参数名。length赋给该参数的长度值）
\setlength{\heavyrulewidth}{0.9pt}
 %自定类选项
\DeclareMathAlphabet{\mathbf}{\eu@enc}{\eu@mathrm}{\bfdefault}{it}
\DeclareMathAlphabet{\mathbd}{\eu@enc}{\eu@mathrm}{\bfdefault}{n}

\setCJKmainfont[AutoFakeBold=true]{SimSun} %设置文档的主要中文字体

%声明新的数学字体字母表
\DeclareMathAlphabet{\mathbf}{\eu@enc}{\eu@mathrm}{\bfdefault}{it}

\newcommand{\bm}{\mathbf}  %创建自己的自定义命令（\name：新命令的名称；definition：新命令的定义内容。）

\graphicspath{{./pic/}} %指定图像文件的搜索路径
\captionsetup[subfigure]{font=small, belowskip=6pt, width=36pt}%设置浮动对象（如图表）的标题格式和样式。
\renewcommand{\thesubfigure}{(\alph{subfigure})} %重新定义已存在命令的命令。
\urlstyle{rm} %设置 URL 在文本中的显示样式。
\raggedbottom  %设置页面的底部对齐方式。
\pagestyle{fancy} %设置页面样式的命令
\linespread{1.391}  %设置行距
\titlespacing{\chapter}{0pt}{0pt}{18pt} %调整标题（包括章节标题、图表标题等）与其上下文之间的间距
\AtBeginEnvironment{figure}{% 在特定环境开始时执行给定的命令
  \def\@floatboxreset{\centering}%
}
\setcounter{secnumdepth}{3} %设置计数器
\ding{173}\or\ding{174}\or\ding{175}\or\ding{176}\or\ding{177}\or  %插入特定的字符
```

该文件主要定义了文档的整体布局和格式。

### 3 thesis-uestc.bst

该文件是BibTeX 样式文件，用于定义参考文献的格式和排版方式。其主要内容如下：

```latex
%定义了各种参考文献类型的格式
ENTRY
{
  author
  title
  publisher
  journal
  address
  year
  date
  pages
  translator
  booktitle
  institution
  country
  url
  volume
  number
  type
  id
  note
  language
  eprint
}
{}
{label}
%包括一些用于格式化文献条目的函数，比如格式化作者名字、标题等的函数。
FUNCTION {not}
{   { #0 }
    { #1 }
  if$
}
%定义了一些字符串变量，用于在文献条目中引用固定的文本，比如月份的英文缩写、页码的前缀等。
STRINGS { s t}
```

下面可以通过硕士论文引用来介绍功能：

首先在reference.bib文件中

```markdown
@masterthesis{shangyabo,
  author={尚亚博},
  title={ 基于蚁群算法协同空战决策研究 },
  school={东北大学},
  year={2009},
  type={硕士论文},
  month={6},
  pages = {48-64},
  institution = {知网},
}
```

其格式由theis-uestc.bst中定义

```latex
FUNCTION {masterthesis}
{
    thesis
}
```

说明他的格式和thesis一样

```markdown
FUNCTION {thesis}
{
    bibitem.begin
    format.authors write$ add.period
    format.title "[D]" * write$ add.period
    address missing$
    'skip$
    {
        format.address write$ ": " write$
    } if$
    format.institution write$ add.comma
    format.year write$ add.comma
    format.pages write$
    "." write$
    newline$
}
```

