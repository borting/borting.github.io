---
layout: post
title: "Define LaTeX Command with More than One Optional Arguments"
author: "Borting"
categories: journal
tags: [LaTeX]
image: light-of-world.jpg
---

以前自訂 LaTeX command 的時候, 通常是用 `\newcommand` 來做.
`\newcommand` 的缺點是只能定義一個 optional argument, 要定義兩個以上的 optional arguments 就要用 [command relaying](http://www.texfaq.org/FAQ-twooptarg) 的方法, 很不直覺.
Google 了一下, 幾個常見的解決方式有 [twoopt](https://ctan.org/pkg/twoopt), [optparams](https://ctan.org/pkg/optparams), [xargs](https://ctan.org/pkg/xargs), [xparse](https://www.ctan.org/pkg/xparse).
這邊介紹 xparse 的用法.

# xparse

xparse 除了支援定義多個 optional arguments 外, 另一個優點是 optional arguments 不需要寫在 mandatory arguments 前面, 可以交錯定義.

格式為 `\NewDocumentCommand{command_name}{argument_spec}{code}`
- command_name 不能包含數字
- argument_spec 中 `m` 代表 mandatory argument, `o` 代表沒有 default 值的 optional arguemnt, 如要給予 default 值則要用 `O{default_value}`
- 可以用 `\IfNoValueTF` 判斷是否有設值給 optional argument.

下面是一個簡單的範例.

```Tex
\documentclass{article}
\usepackage{xparse}

% 2 mandatory arguments
\NewDocumentCommand{\myCmdA}{ m m }{#1~#2}

% 1st argument is mandatory, and 2nd is optional
\NewDocumentCommand{\myCmdB}{ m o }{#1~#2}

% 1st argument is optional, and 2nd is mandatory
\NewDocumentCommand{\myCmdC}{ o m }{#1~#2}

% 1st argument is optional, and 2nd is mandatory
% If 1st argument is empty, print `New` instead
\NewDocumentCommand{\myCmdD}{ o m }{ %
  \IfNoValueTF{#1}%
    {New~#2}%
    {#1~#2}%
}

% The first 2 arguments are optional with default value
% The last argument is mandatory
\NewDocumentCommand{\myCmdE}{ O{default1} O{default2} m }{#1~#2~#3}

\begin{document}

\begin{itemize}

  \item \textbf{\textbackslash myCmdA\{Hello\}\{World\}} \par
  \myCmdA{Hello}{World}
  
  \item \textbf{\textbackslash myCmdB\{Hello\}[World]} \par
  \myCmdB{Hello}[World]
  
  \item \textbf{\textbackslash myCmdB\{Hello\}} \par
  \myCmdB{Hello}

  \item \textbf{\textbackslash myCmdC[Hello]\{World\}} \par
  \myCmdC[Hello]{World}
  
  \item \textbf{\textbackslash myCmdC\{World\}} \par
  \myCmdC{World}

  \item \textbf{\textbackslash myCmdD[Hello]\{World\}} \par
  \myCmdD[Hello]{World}
  
  \item \textbf{\textbackslash myCmdD\{World\}} \par
  \myCmdD{World}

  \item \textbf{\textbackslash myCmdE\{foo\}} \par
  \myCmdE{foo}

  \item \textbf{\textbackslash myCmdE[nondefault1]\{foo\}} \par
  \myCmdE[nondefault1]{foo}

  \item \textbf{\textbackslash myCmdE[nondefault1][nondefault2]\{foo\}}\par
  \myCmdE[nondefault1][nondefault2]{foo}

\end{itemize}

\end{document}
```

Output 如下

```
• \myCmdA{Hello}{World}
  Hello World

• \myCmdB{Hello}[World]
  Hello World

• \myCmdB{Hello}
  Hello -NoValue-

• \myCmdC[Hello]{World}
  Hello World

• \myCmdC{World}
  -NoValue- World

• \myCmdD[Hello]{World}
  Hello World

• \myCmdD{World}
  New World

• \myCmdE{foo}
  default1 default2 foo

• \myCmdE[nondefault1]{foo}
  nondefault1 default2 foo

• \myCmdE[nondefault1][nondefault2]{foo}
  nondefault1 nondefault2 foo
```

# Reference

- [xparse – A generic document command parser](https://www.ctan.org/pkg/xparse)
- [From \newcommand to \NewDocumentCommand](https://www.texdev.net/2010/05/23/from-newcommand-to-newdocumentcommand/)
- [More than one optional argument for newcommand](https://tex.stackexchange.com/a/29975)
- [\newcommand / \newenvironment - optional parameters](https://stackoverflow.com/a/2918815)
- [Bracket position rule](https://tex.stackexchange.com/a/62914)
