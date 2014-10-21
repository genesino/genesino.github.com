---
title: bash脚本交换文件的两列
author: 悟道
layout: post
categories:
  - bash
tags:
  - bash
---

<div class="wp_codebox">
  <table>
    <tr id="p1713">
      <td class="code" id="p171code3">
        <pre class="bash" style="font-family:monospace;"><span style="color: #666666; font-style: italic;">#!/bin/bash</span>
<span style="color: #000000; font-weight: bold;">if</span> <span style="color: #7a0874; font-weight: bold;">test</span> <span style="color: #007800;">$#</span> <span style="color: #660033;">-ne</span> <span style="color: #000000;">2</span>; <span style="color: #000000; font-weight: bold;">then</span>
        <span style="color: #7a0874; font-weight: bold;">echo</span> <span style="color: #000000;">1</span><span style="color: #000000; font-weight: bold;">&</span>><span style="color: #000000; font-weight: bold;">&</span>amp;<span style="color: #000000;">2</span> <span style="color: #ff0000;">"Usage rename.sh inputfile outputfile"</span>
        <span style="color: #7a0874; font-weight: bold;">exit</span> <span style="color: #000000;">1</span>
<span style="color: #000000; font-weight: bold;">fi</span>
&nbsp;
<span style="color: #007800;">IN</span>=<span style="color: #007800;">$1</span>
<span style="color: #007800;">OUT</span>=<span style="color: #007800;">$2</span>
<span style="color: #000000; font-weight: bold;">set</span> <span style="color: #660033;">-x</span>
<span style="color: #c20cb9; font-weight: bold;">awk</span> <span style="color: #ff0000;">'BEGIN {FS=" ";OFS=" "}
	{ss=$1; $1=$2; $2=ss;
	print $0;
	}'</span> <span style="color: #007800;">$IN</span> <span style="color: #000000; font-weight: bold;">&</span>gt;<span style="color: #007800;">$OUT</span></pre>
      </td>
    </tr>
  </table>
</div>

This script can be used to swap the first and second column seperated by FS which is space here, and you can specify the OFS which is the new delimeter of the swapped file.

In print, you can also use &#8216;%s&#8217; to output in your wanted format.

such like

<pre lang="”bash”">awk '{printf "%6s %10s" $2, $1}' file</pre>

much like this.
