---
layout: page
hero_text: about Page!
title: Release Strategy
section: About Rock
---
<div class="content2">

<div class="content2-pagetitle">Release Strategy</div>

<div class="content2-container line-box">
<div class="content2-container-1col">



<p>Rock does <strong>not</strong> have fixed-point release.</p>

<p>Rock is maintained on a rolling-release basis. Each package provides three
branches or &lsquo;flavors&rsquo;</p>

<ul>
 <li>the &lsquo;master&rsquo; branch on which the development is made</li>
 <li>the &lsquo;next&rsquo; branch on which changes are applied from &lsquo;master&rsquo; to make sure
everything works fine before &hellip;</li>
 <li>.. the &lsquo;stable&rsquo; branch is updated from &lsquo;next&rsquo;</li>
</ul>

<p>More specifically, the whole process works on the basis of the following cycle:</p>

<ol>
 <li>&lsquo;next&rsquo; gets open for updates during a week. After this week, the only changes
that can be pushed to &lsquo;next&rsquo; are bugfixes and documentation updates.
Developers are required to publicize any API-breaking changes on the rock-dev
mailing list BEFORE this merge window.</li>
 <li>when &lsquo;next&rsquo; is ready, i.e. if no known critical bug exists on next <em>after</em>
at least a 3-week period, changes on &lsquo;next&rsquo; are pushed to &lsquo;stable&rsquo; and &lsquo;next&rsquo;
gets open for new updates.</li>
 <li>LOOP 1</li>
</ol>

<p>This strategy will be the main release mechanism for Rock. There <strong>will</strong> be
some exceptions, when some in-depth changes require to change a lot of packages
at the same time.</p>

<p>In this case, the changes will be made on a separate branch (&lsquo;topic branch&rsquo;),
and tested. Once they are deemed of a good-enough quality, they will be
first publicized to rock-dev and then merged into master (and, later on, to next
and finally to stable).</p>

<p>Since they are pervasive changes, it is important for us that people can prepare
themselves by branching or by avoiding updates for a while, i.e. that they can&rsquo;t
break existing systems unknowingly.</p>



</div>
</div>
</div>
