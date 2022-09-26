---
layout: post
title: "[PNUCSE] What is LaTeX?"
categories: text
tags: [editor]
author:
  - Jinseop Sim
toc: true
---
이 글은 부산대학교 한상곤 강사님의 강의를 바탕으로 작성되었습니다.  

개발자라고 과연 평생 코딩만하고 글을 쓸 일이 없을까?  
아니다. 우리는 언젠간 어떤 형태로든 반드시 글을 쓸 일이 있을 것이다.  

어떤 프로젝트가 끝났을 때, 완료 보고서를 제출을 해야할 수도 있을 것이고,  
특정 대상에게 형식을 갖추어 메일을 보내야 할 때도 글을 쓰는 것이라고 볼 수 있다.  

그럼 어떤 에디터를 사용해 글을 쓸 수 있을까? 한글? 워드? Google Docs?  
과거부터 외국에서 사용되어온 강력한 에디터인 __LaTeX__를 소개한다.  

### What is LaTeX?
외국에서는 회사에서 메일이나 논문을 작성할 때 특정한 형식을 지켜 사용한다.  
그 형식에 대한 표준을 제공하는, 가장 많이 사용되는 문서 에디터가 __LaTeX__ 이다.  
수식이나 그래프, 다이어그램 등을 많이 그려야 하는 학자들에게 매우 유용한 도구이다.  

또한, ```LaTeX```는 HTML이나 MD 같은 Markup, Markdown 언어의 조상님이라고 볼 수 있다.  

### LaTeX with Overleaf
```LaTeX```를 사용할 수 있는 많은 에디터가 있지만, 그 중 가장 보편적인 __Overleaf__ 를 사용해보기로 했다.  

{% highlight latex %}
\documentclass[a4paper]{article}
\usepackage{kotex}

% define the title
\title{라텍스란 무엇인가?}
\author{진섭 심}
\date{September 26, 2022}

% begin the document
\begin{document}

\maketitle

\section{서론}
우리는 \LaTeX를 배우기 시작했다.

두       칸 이상의 공백은 들지 않는다.
한 줄 떨어지지 않은 줄바꿈은 들지 않는다.
\section{What is Latex?}
\section{Hello Latex}

\end{document}
% end the document
{% endhighlight %}