# Latex

主要分为导言区和正文区

## 导言区

导言区通常用于定义文档的格式、语言等（全局设置），以下是常用命令

### \documentclass



`\documentclass[options]{class} `

![image-20250624104927211](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624104927211.png)

![image-20250624104945581](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624104945581.png)

举例：`\documentclass[12pt, a4paper]{article}`

### \usepackage

设置在编译LaTex文件时要导入的扩展包

### 封面格式

设置所要生成文档的封面内容: 文档名，作者，日期等(这只是设置了封面格式，生成封面的是\maketitle命令)。它需要放在正文区

```
\title{My First Paper} % 文章名字
\author{WSKH} % 作者姓名
\date{\today} % 日期```
```

## 正文区

一个LaTeX文件中只能有一个document

```
\begin{document}
	\maketitle % 将标题打印出来
	Hello \TeX
\end{document}
```

## 字体设置

一个字体有5种属性：字体编码、字体系列、字体族、字体形状、字体大小

```latex
% 导言区
\documentclass[12pt,a4paper]{ctexart}
%这边是在自定义字体，来应对复杂字体情况
\newcommand{\myfont}{\huge{\textbf{\textit{Hello}}}}

% 正文区（文稿区）
\begin{document}
	% 字体族设置（罗马字体、打字机字体等）
	\textrm{罗马字体}
	\textsf{无衬线字体}
	\texttt{打字机字体}
	
	% 用大括号限定字体族生效范围
	{\rmfamily 这里面的全都是罗马字体}
	{\ttfamily 这里面的全都是打字机字体}
	
	% 字体大小设置
	{\tiny Hello} \\
	{\scriptsize Hello} \\
	{\footnotesize Hello} \\
	{\small Hello} \\
	{\normalsize Hello} \\
	{\large Hello} \\
	{\Large Hello} \\
	{\LARGE Hello} \\
	{\huge Hello} \\
	{\Huge Hello} \\
	
	% 中文字号设置
	\zihao{5} 你好
	
	% 英文字体格式设置
	\textbf{Hello} % 加粗
	\textit{Hello} % 斜体
	
	% 复杂字体设置，这个\\表示这个内容也用于下面
	{\huge{\textbf{\textit{Hello}}}} \\
	\myfont
	
\end{document}

```

## 篇章结构

\section构建小节，加一个sub就是低一级，sub个数不做限制

```latex
	\section{引言}
	\section{问题背景}
	\section{数学模型的建立与求解}
	\subsection{符号说明}
	\subsection{模拟退火算法模型建立}
	\subsubsection{参数设置}
	\subsubsection{领域动作算子}
	\subsubsection{评价函数}
	\subsection{模拟退火算法模型求解}
	\section{结论}

```

`\tableofcontents`生成目录

## 特殊字符

### 空白字符

1.中文中加空格不会生成空格，不能使用中文全角空格

2.英文中加不管多少个空格都只生成一个空格，

3.空行分段，多个空行等同于一个

4.自动缩进，不能用空格代替

5.汉字与其他字符的间距会自动有XelateX处理

还可以通过命令控制空格

| 命令         | 宽度                       | 用途                         |
| ------------ | -------------------------- | ---------------------------- |
| `\quad`      | `1em`（约等于 "M" 的宽度） | 中等间距（如公式分隔）       |
| `\qquad`     | `2em`（`\quad` 的两倍）    | 更大间距（如公式中的分隔）   |
| `\,`         | `3/18em`（小间距）         | 微调（如微分符号 `dx` 中）   |
| `\:` 或 `\>` | `4/18em`                   | 中等小间距（如公式中的分隔） |
| `\;`         | `5/18em`                   | 较大的小间距                 |
| `\ `（空格） | 可变（根据上下文）         | 普通单词间距                 |

根据参数产生指定空格

`这是一段文字\hspace{2cm}这里间隔2厘米。`直接指定宽度，并且带有单位

根据指定字符的宽度产生空白

`a\hphantom{1}b`这个花括号里面写多长，就空多长

### 控制符

`\#   \$   \%   \{\}   \~{}   \_{}   \^{}   \textbackslash   \&`

能够输出：`# $ % {} ~ _ ^ \ &`

### 标志符号

导言区需要先引入宏包

```latex
\usepackage{xltxtra}
\usepackage{texnames}
\usepackage{mflogo}
```

正文区写：

```latex
	\subsection{LateX标志符号}
	\subsubsection{默认自带}
	\TeX{}   \LaTeX{}   \LaTeXe{}
	\subsubsection{xltxtra宏包提供}
	\XeLaTeX
	\subsubsection{texnames宏包提供}
	\AmSTeX{}    \AmS-\LaTeX{}
	\BibTeX{}    \LuaTeX{}
	\subsubsection{mflogo宏包提供}
	\METAFONT{}    \MF{}    \MP{}
```

![image-20250624112240932](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624112240932.png)

### 引号

```latex
`   '   ``   ''    ``你好''    `好'
```

输出：‘’ “” “你好” ‘好’

### 连字符

`-    --    ---`

输出：![image-20250624112624199](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624112624199.png)

## 插图

导言区引入包`\usepackage{graphicx}  %插图需要引用的包`

然后指定路径，可以是单个路径，也可以有多个路径，如果就在当前文件夹下，就不需要指定

```latex
\graphicspath{{fig/}}  %指定图片在当前文件夹下的fig文件夹内
\graphicspath{{fig1/},{fig2/}} % 指定多个路径

```

插入图片语法：`\includegraphics[scale=0.1,width=6cm,height=6cm][xxx.jpg]`

一般正式文本中都要用\begin{figure}包裹一下，还可以在一张图中插入两张子图，示例如下：

```latex
\begin{figure}[!h]
    \centering% 整体居中
    \subfigure[人工目视]{ %[]里面写子图名字
        \begin{minipage}[b]{0.48\linewidth} % 子图框架的大小，对比整体
        \centering % 子图在框架中居中
        \includegraphics......

```



## 表格

基本语法：

![image-20250624113915868](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624113915868.png)

示例：

```latex
\begin{tabular}{l|c|p{1.5cm}|c|r}  %l:左对齐 c：居中 r：右对齐 |：在该位置增加竖线 p{1.5cm}指定宽度
		\hline %\hline为表格增加一条横线
		姓名 & 语文 & 数学 & 外语 & 备注 \\ %\\结束这一行表格的书写
		\hline
		张三 & 98 & 96 & 97 & 优秀 \\
		\hline
		李四 & 85 & 87 & 83 & 良好 \\
		\hline
		王五 & 73 & 74 & 77 & 一般 \\
		\hline
		赵六 & 62 & 66 & 64 & 及格 \\
		\hline
	\end{tabular}
```

上面这个示例，就是：指定了一个表格，并直接对他做了列格式说明，总共5列，第一列左对齐，第二列居中，第三列指定宽度为1.5cm，第四列居中，第五列右对齐，并且每列之间都有一个|表示在列之间加一竖；

最终效果如下：

![image-20250624115136733](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624115136733.png)

一般用\begin{table}包裹一下的

## 浮动体

htbp是一个优先级列表，会告诉latex可以尝试放在哪些地方，这四个字母分别表示here（当前位置）；top（当前页顶部）；bottom（当前页底部）；page of floats（单独的浮动页）；！强制调整。

一般不指定的话，默认就是tbp的顺序，h一般被忽略，而h！表示必须放在当前位置

示例：

```latex
\begin{figure}[htbp] % [htbp] 浮动格式
		\centering % 让图片居中显示
		\includegraphics[scale=0.1]{xxx.jpg}
		\caption{图片标题} % 设置图片标题，并自动编号
		\label{f1}
	\end{figure}
```

## 数学公式

### 行内公式`a+b=b+a`

1.$a+b=b+a$

2.\\(a+b=b+a\\)

3.\begin{math}a+b=b+a\end{math}

### 上下标

上标：^

下标：_

### 希腊字母

```latex
	\section{希腊字母}
	$\alpha$
	$\beta$
	$\gamma$
	$\epsilon$
	$\pi$
	$\omega$
	
	$\Gamma$
	$\Delta$
	$\Theta$
	$\Pi$
	$\Omega$

```

输出：![image-20250624122344620](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624122344620.png)

### 三角函数

```latex
	\section{数学函数}
	$\log$
	$\sin$
	$\cos$
	$\arcsin$
```

### 分式

`$\frac{1}{5}$`

### 行间公式`f(x)=x^2+x+1`

1.$$f(x)=x^2+x+1$$

2.\\[f(x)=x^2+x+1\\]

3.\begin{displaymath} 

​	f(x)=x^2+x+1

 \end{displaymath}

4.\begin{equation} 	

f(x)=x^2+x+1 \label{e1}   自动编号

\end{equation}

### 简单矩阵

要引入包`\usepackage{amsmath}`

```latex
	\section{矩阵}
	$\begin{matrix}
		0 & 1 \\
		1 & 0
	\end{matrix}$

	$\begin{pmatrix}
		0 & 1 \\
		1 & 0
	\end{pmatrix}$

	$\begin{bmatrix}
		0 & 1 \\
		1 & 0
	\end{bmatrix}$

	$\begin{Bmatrix}
	0 & 1 \\
	1 & 0
	\end{Bmatrix}$

	$\begin{vmatrix}
		0 & 1 \\
		1 & 0
	\end{vmatrix}$

	$\begin{Vmatrix}
	0 & 1 \\
	1 & 0
	\end{Vmatrix}$
```

输出如下：

![image-20250624122959739](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624122959739.png)

### 多行公式

先引入包

```latex
\usepackage{amsmath}
\usepackage{amssymb}
```

```latex
\section{多行公式}
	% gather环境
	\subsection{gather带编号}
	\begin{gather}
		a+b=b+a \\
		f(x)=x^2+x+1
	\end{gather}
	\subsection{gather*不带编号}
	\begin{gather*}
		a+b=b+a \\
		f(x)=x^2+x+1
	\end{gather*}
	% align环境，用&进行对齐
	\subsection{align}
	\begin{align}
		a+b&=b+a \\
		f(x)&=x^2+x+1
	\end{align}
	\subsection{align*}
	\begin{align*}
		a+b&=b+a \\
		f(x)&=x^2+x+1
	\end{align*}
	% split环境，对其采用align环境对齐方式，编号在中间
	\subsection{split}
	\begin{equation}
		\begin{split}
			a+b&=b+a \\
			f(x)&=x^2+x+1
		\end{split}
	\end{equation}
	% cases环境，每行公式用&分割为两个部分，通常表示为值和后面的条件
	\subsection{cases}
	\begin{equation}
		f(x)=\begin{cases}
			1,&x>0 \\
			0,&x=0 \\
			-1,&x<0
		\end{cases}
	\end{equation}

```

输出如下

![image-20250624123316772](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624123316772.png)

![image-20250624123322752](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624123322752.png)

## 参考文献

先创建一个.bib文件，然后在正文中引用就行，示例如下：

```latex
\documentclass{article}

\usepackage{ctex}

\bibliographystyle{plain} % 指定参考文献样式

\title{LaTeX中的参考文献BibTex}
\author{WSKH}
\date{\today}

\begin{document}
	这是一个参考文献的引用:\cite{mittelbach2004}%这里cite后面的这个就是这篇参考文献的标签
	
	% 根据当前目录下的10.bib文件，生成参考文献章节
	% \nocite{*} % 默认情况下，只有被引用的参考文献才会被列出，如果想列出没被引用的参考文献可以使用\nocite{*}命令
	\bibliography{10}
\end{document}
```

在正文中想要引用，就用\cite{标签}即可，需要注意，bib文件内容格式：

```latex
@article{ref1,%这里就是上面说的cite后面的哪个标签
    author  = "Author A",
    title   = "Title of the paper",
    journal = "Journal Name",
    year    = "2023",
}
```

![image-20250716173618889](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250716173618889.png)

## 自定义命令以及环境

```latex
\documentclass{article}

\usepackage{ctex}

% 无参数定义新命令
\newcommand\PRC{啦啦啦啦啦啦}

% 有参数定义新命令 [n] n个参数
\newcommand\loves[2]{#1 喜欢 #2}
\newcommand\hate[2]{#1 讨厌 #2}

% 定义摘要环境
\newenvironment{myabstract}[1][摘要]
{\small
\begin{center}
	\bfseries #1
\end{center}%
\begin{quotation}}%
{\end{quotation}}

\title{LaTeX中的参考文献BibTex}
\author{WSKH}
\date{\today}

\begin{document}
	\PRC
	
	\loves{WSKH}{吃西瓜}
	
	\hate{西瓜}{WSKH}
	
	\begin{myabstract}[我的摘要]
		这是一段自定义格式的摘要
	\end{myabstract}
\end{document}

```

输出：![image-20250624131536479](C:\Users\Only one\AppData\Roaming\Typora\typora-user-images\image-20250624131536479.png)

