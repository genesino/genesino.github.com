---
title: '一个关于diff的用法&#8211;寻找两个文件的共同行'
author: 悟道
layout: post
categories:
  - bash
  - linux
tags:
  - linux
---

经常需要比较两个文件（排序好的）中序列标号（每个标号占一行）的不同， 来寻找特有的或公有的标号。以往总是自己写程序处理， 偶然一次发现了diff（采用寻找最长公共子序列的算法，详见[wiki][1]）的强大功能，便尝试使用了一下， 未得要领，只好将其函数话来实现傻瓜操作。

首先举一个简单的例子来展示diff的使用  
我们有如下两个文件：

<table>
  <tr>
    <td>
      1
    </td>
    
    <td>
      2
    </td>
  </tr>
  
  <tr>
    <td>
      1<br /> 2<br /> 3<br /> 4<br /> 5<br /> 6<br /> 7
    </td>
    
    <td>
      3<br /> 4<br /> 5<br /> 10<br /> 7<br /> 8<br /> 9
    </td>
  </tr>
</table>

使用diff命令diff 1 2输出结果如下：

1,2d0  
< 1  
< 2  
6c4  
< 6  
&#8212;  
> 10  
7a6,7  
> 8  
> 9

a表示在第一个文件中需要加入的行，d表示在第一个文件中需要除去的航，c表示第一个文件中需要被替换的行。  
我们可以把diff的结果输出到一个文件作为补丁，配合patch实现文件的修补。  
另外，我们可以根据以上解释得出，以>开头的行为第二个文件独有，以<号开头的行为第一个文件独有， 由此引出我们下边的脚本比较。

这里提供了一个bash脚本和一个python程序来实现这个功能，方便操作。

<div class="wp_codebox">
  <table>
    <tr id="p2186">
      <td class="code" id="p218code6">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/bash</span>
&nbsp;
<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">test</span> <span style="color: #007800;">$#</span> <span style="color: #660033;">-ne</span> <span style="color: #000000;">2</span>; <span style="color: #000000; font-weight: bold;">then</span>
	<span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #000000;">1</span><span style="color: #000000; font-weight: bold;">&</span>gt;<span style="color: #000000; font-weight: bold;">&</span>amp;<span style="color: #000000;">2</span> <span style="color: #ff0000;">"Usage $0 first second"</span>
	<span style="color: #7a0874; font-weight: bold;">exit</span> <span style="color: #000000;">1</span>
<span style="color: #000000; font-weight: bold;">fi</span>
&nbsp;
<span style="color: #007800;">FIRST</span>=<span style="color: #007800;">$1</span>
<span style="color: #007800;">SECOND</span>=<span style="color: #007800;">$2</span>
<span style="color: #007800;">tmp</span>=<span style="color: #000000; font-weight: bold;">`</span><span style="color: #c20cb9; font-weight: bold;">date</span> +<span style="color: #000000; font-weight: bold;">%</span>Y-<span style="color: #000000; font-weight: bold;">%</span>m-<span style="color: #000000; font-weight: bold;">%</span>d <span style="color: #000000; font-weight: bold;">`</span>
<span style="color: #666666; font-style: italic;">#找到只存在于第一个里面的</span>
<span style="color: #c20cb9; font-weight: bold;">diff</span> <span style="color: #007800;">$FIRST</span> <span style="color: #007800;">$SECOND</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> <span style="color: #ff0000;">'&lt;'</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">cut</span> <span style="color: #660033;">-d</span> <span style="color: #ff0000;">' '</span> <span style="color: #660033;">-f</span> <span style="color: #000000;">2</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">sort</span> <span style="color: #000000; font-weight: bold;">&</span>gt;<span style="color: #800000;">${FIRST}</span>.<span style="color: #800000;">${tmp}</span>
<span style="color: #666666; font-style: italic;">#找到只存在于第一个里面的</span>
<span style="color: #c20cb9; font-weight: bold;">diff</span> <span style="color: #007800;">$FIRST</span> <span style="color: #800000;">${FIRST}</span>.<span style="color: #800000;">${tmp}</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">grep</span> <span style="color: #ff0000;">'&lt;'</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">cut</span> <span style="color: #660033;">-d</span> <span style="color: #ff0000;">' '</span> <span style="color: #660033;">-f</span> <span style="color: #000000;">2</span> <span style="color: #000000; font-weight: bold;">|</span> <span style="color: #c20cb9; font-weight: bold;">sort</span> <span style="color: #000000; font-weight: bold;">&</span>gt;<span style="color: #800000;">${FIRST}</span>.<span style="color: #800000;">${SECOND}</span>.common
<span style="color: #000000; font-weight: bold;">/</span>bin<span style="color: #000000; font-weight: bold;">/</span><span style="color: #c20cb9; font-weight: bold;">rm</span> <span style="color: #660033;">-f</span> <span style="color: #800000;">${FIRST}</span>.<span style="color: #800000;">${tmp}</span></pre>
      </td>
    </tr>
  </table>
</div>

<div class="wp_codebox">
  <table>
    <tr id="p2187">
      <td class="code" id="p218code7">
        <pre class="python" style="font-family:monospace;"><span style="color: #808080; font-style: italic;">#!/usr/bin/env python</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">import</span> <span style="color: #dc143c;">sys</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">def</span> main<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span>:
    <span style="color: #ff7700;font-weight:bold;">if</span> <span style="color: #008000;">len</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#41;</span> <span style="color: #66cc66;">!</span>= <span style="color: #ff4500;">3</span>:
        <span style="color: #ff7700;font-weight:bold;">print</span> <span style="color: #483d8b;">'Using python %s filename1 filename2'</span> <span style="color: #66cc66;">%</span> <span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;"></span><span style="color: black;">&#93;</span>
        <span style="color: #dc143c;">sys</span>.<span style="color: black;">exit</span><span style="color: black;">&#40;</span><span style="color: #ff4500;"></span><span style="color: black;">&#41;</span>
    alist1 = <span style="color: black;">&#91;</span>line <span style="color: #ff7700;font-weight:bold;">for</span> line <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: #008000;">open</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">1</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span><span style="color: black;">&#93;</span>
    <span style="color: #dc143c;">sys</span>.<span style="color: black;">stdout</span>.<span style="color: black;">writelines</span><span style="color: black;">&#40;</span><span style="color: black;">&#91;</span>line <span style="color: #ff7700;font-weight:bold;">for</span> line <span style="color: #ff7700;font-weight:bold;">in</span> <span style="color: #008000;">open</span><span style="color: black;">&#40;</span><span style="color: #dc143c;">sys</span>.<span style="color: black;">argv</span><span style="color: black;">&#91;</span><span style="color: #ff4500;">2</span><span style="color: black;">&#93;</span><span style="color: black;">&#41;</span> \
            <span style="color: #ff7700;font-weight:bold;">if</span> line <span style="color: #ff7700;font-weight:bold;">in</span> alist1<span style="color: black;">&#93;</span><span style="color: black;">&#41;</span>
&nbsp;
<span style="color: #ff7700;font-weight:bold;">if</span> __name__ == <span style="color: #483d8b;">'__main__'</span>:
    main<span style="color: black;">&#40;</span><span style="color: black;">&#41;</span></pre>
      </td>
    </tr>
  </table>
</div>

刚才还发现了有个diff3可以同时比较三个文件。

 [1]: http://en.wikipedia.org/wiki/Diff
