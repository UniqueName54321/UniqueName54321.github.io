-------------------------------------------
layout: post
title: "optimizing https://ha.mr/ even more"
categories: projects, oped, work
--------------------------------------------

I love URL shorteners. Always have. Ever since I was 12, I saw the potential use in shortening URLs. At the time, I was only aware of a few shorteners, all of which rely on databases.

And then, just today, I have been made aware of a project called https://ha.mr/, which is the exact opposite. Unlike others, ha.mr is entirely locally ran and *compresses* your URLS (rather than shortening them). The difference is really technical, and if you're interested then [this video is worth a watch for you](https://www.youtube.com/watch?v=TOr1Vvji6jA).

However, I watched that video, and went "hmm... I could do better". So, I am writing this blog post to show you how I optimized it.

## Part 0 - The Basics

I'm going to be testing both ha.mr and my own URL shortener via 4 URLS:

* http://uw.edu, the shortest url in [the dataset of URLs I'm using](https://raw.githubusercontent.com/ada-url/url-dataset/refs/heads/main/out.txt)
* https://www.youtube.com/watch?v=TOr1Vvji6jA and https://rickgriffinstudios.com/housepets/comic/0614/ as the kinds of URLs people are going to be typically shortening
* [THIS](https://gist.github.com/UniqueName54321/2b313925b5a9074be620ef4d233f872d) url, which is the longest url in [the same dataset](https://raw.githubusercontent.com/ada-url/url-dataset/refs/heads/main/out.txt)

I'm also going to be testing them on my URL shortener (obviously), as well as ha.mr on both the text-only and the emoji-only versions. There *is* t.ha.mr

## Part 0.5 - Baseline Testing

Without doing anything, here's the results straight from ha.mr for the URLs:

<table>
<thead>
<tr>
<th>Without emojis</th>
<th>With emojis</th>
</tr>
</thead>
<tbody>

<tr>
<td>
<a href="http://ha.mr/#GkIU">http://ha.mr/#GkIU</a> (66% larger)
</td>
<td>
<a href="http://ha.mr#🧑🏻‍🎓🧍👨🏻‍❤️‍💋‍👨🏻">http://ha.mr#🧑🏻‍🎓🧍👨🏻‍❤️‍💋‍👨🏻</a> (50% larger)
</td>
</tr>

<tr>
<td>
<a href="http://ha.mr#b~9nFB([Ccz4dv.A*PQW#">http://ha.mr#b~9nFB([Ccz4dv.A*PQW#</a> (23% smaller)
</td>
<td>
<a href="http://ha.mr#🙅🏼‍♀️🧍🏻🌋🥂🖐🏻🤜🏼🧑🏻‍🦼🪛😒🧑🏻‍🐰‍🧑🏽🏃🏼‍♀️">http://ha.mr#🙅🏼‍♀️🧍🏻🌋🥂🖐🏻🤜🏼🧑🏻‍🦼🪛😒🧑🏻‍🐰‍🧑🏽🏃🏼‍♀️</a> (52% smaller)
</td>
</tr>

<tr>
<td>
<a href="http://ha.mr#jr#is=w@8Rzg8wecY+gQ@(hw~,4_/ys)!">http://ha.mr#jr#is=w@8Rzg8wecY+gQ@(hw~,4_/ys)!</a> (14% smaller)
</td>
<td>
<a href="http://ha.mr#🐑👳🏽‍♂️🤾🙇🏾‍♂️🧏🏻‍♀️👩🏿‍⚕️👈🏼🕢🗜️🌩️👰🏼‍♀️📙🛍️💇🧚🏼🔢👩🏽‍❤️‍👩🏽">http://ha.mr#🐑👳🏽‍♂️🤾🙇🏾‍♂️🧏🏻‍♀️👩🏿‍⚕️👈🏼🕢🗜️🌩️👰🏼‍♀️📙🛍️💇🧚🏼🔢👩🏽‍❤️‍👩🏽</a> (48% smaller)
</td>
</tr>

<tr>
<td>
<a href="http://ha.mr#*5w~gM?pXu+vLK56EC8I97o-k-~7Fw:U~Gk~x-CmG;.Xm5JlM?Z;kM-9!6bDoMQ$~wxCX*X~Z$Qe~;~lTqn=3v@,7_0vrioqD!i6VfQhqChMbAo9n2g9caWc*]DI$87EG$I*:HVLV?Rq$(Q1S&amp;b)'5SO8AxBaV(tF/,BZ(0R!o~rF-MJ=5?A4ut(a$Po#fMyIs+kGgBX'odJ!Lt*P@xcZh-K0JV**ne:K8bI]jty=1r(j*AF0?[;LNvwMGYyTc(Lyf)7~S]zUL7+gc?VQF8faQ8.?vkQ9A&amp;:D-9RXu5uu)9[I2(F!YSv~.G'_Ph:?G',OMiTXFcmgxIF-zG!n$i$($Qf:uimoQCGnaa&amp;(z17Ge?!fWZsNH=-G$(YTDD.gH[fZraP9KC1k5WjTJV0P][Ox$W*x&amp;kIDj_hHZprHZFn31gBduIX.[puraO4Qcuk~)Y+yAmq5ZY[3otD?:rkQp;2T$K92@:R@2epS1lD.Dai(?0yLW6di2i$Ldt-R[,u4K,(KeMH/E'aN.ax-ZCYLfA$ULCq6)8I/2[-=LT4BeU8@=E54?FcJ-KG6+17hXaIjIsge]Z7!0oIlP/&amp;JXJwoN'Yc1*aR!N_73'fhh&amp;MMV3JT)8R0;srC+KvsP-zBAoufxu/c+?P~:d@aRxtBCo@k]mw#!ev6rZOD.I,Z!Pl.puF=HBYJA;y![6B67JW!yL.Wx?f)Nqg==)3,Vvef&amp;'3Rn$koY_4LjsJecVsYsYnQxcV8ZVy*o_#gp[ZXsr+T&amp;W$ClmS,(t0'K0V7HUM@9'r]!ZdsS;2gQKDB0-D24_/aTmqH_aw4G)9cwovWQ1-.Btc6qIKwmwgk0o~eOnA!w:5SXMXvYZa!x82/&amp;(!N+IuvZMNdvGEL5a#nJ2f3w/VP1L:hCRz91F:f/Z=5*g;'vSZwP0oZ5v#KwL7FgV]p')HTF+Db[HfHFUIYdHN/LZcn9MT#=IaZc?62ni[p6[F2uv:BFhbD4SI8F15W9s.2xMPo@$TkQ=87OUavE!u7B-#6FkNe#lHzx0SuaoQBQ*2f1c99Q$H6AOn$[S?fB+JoI~PKS0B8N9pn&amp;SFtpKviE=zZ+Jnp]j7BEzfzQX10-nPEYx(Dr&amp;R($hiasRmd)5h.:5OPChgy4*zozzj'&amp;U8)bG3fg_Uo's[F[R$m#N[e$cp,'e5KV$/aFxkm,A4!&amp;@g6Yw2:sWBX,E]Wa-Hj(oE'PAH'OStq90ZF-sp04gBjT_Ho*V]lOs_Y);Oeifr;1#8$uDG&amp;ts0+kyF4?(m;HQp&amp;N*9kFt-x:_Vw!@ob.'jO=(jnOU$aA28-Gq'=kqIlATh.r+HS4~Pjmf0W$ANfH:VQ-WX(tR(e(bID:JjjLz=d'-Dw;G:0'fR*wM+*6c86n:/r=iBeLm?VK!ztn[Pc!2?x&amp;qoclk)]hBOJ7W*(PjM9blk/S-P!d)BBg.rz'(a1,50?mw)kXw]WHy]b(*G)iTrsl.fYCVI)5jZ$B~];Rd=#RzNhX0h@Q1?Q&amp;vIuO.#shX352U:!sRwo4.CNz?Ht+$jv=!!UMO~ctem*J?MN:2lmG&amp;NCGG-:.'Pbeoxu:NCu7]bw])aG0Mkqi+AUN/E9m.XS;'y/*v([NrDSB3tYHsC&amp;u:onbZ$(+K/OlC9w@:QL'J&amp;RX?#k?Sxtx?v7hsr+NWHnDYva2x7#yfm*G=lxk3iApuGE4dhVK*HqJazO:mlpR:bnM/!1h">http://ha.mr#*5w~gM?pXu+vLK56EC8I97o-k-~7Fw:U~Gk~x-CmG;.Xm5JlM?Z;kM-9!6bDoMQ$~wxCX*X~Z$Qe~;~lTqn=3v@,7_0vrioqD!i6VfQhqChMbAo9n2g9caWc*]DI$87EG$I*:HVLV?Rq$(Q1S&amp;b)'5SO8AxBaV(tF/,BZ(0R!o~rF-MJ=5?A4ut(a$Po#fMyIs+kGgBX'odJ!Lt*P@xcZh-K0JV**ne:K8bI]jty=1r(j*AF0?[;LNvwMGYyTc(Lyf)7~S]zUL7+gc?VQF8faQ8.?vkQ9A&amp;:D-9RXu5uu)9[I2(F!YSv~.G'_Ph:?G',OMiTXFcmgxIF-zG!n$i$($Qf:uimoQCGnaa&amp;(z17Ge?!fWZsNH=-G$(YTDD.gH[fZraP9KC1k5WjTJV0P][Ox$W*x&amp;kIDj_hHZprHZFn31gBduIX.[puraO4Qcuk~)Y+yAmq5ZY[3otD?:rkQp;2T$K92@:R@2epS1lD.Dai(?0yLW6di2i$Ldt-R[,u4K,(KeMH/E'aN.ax-ZCYLfA$ULCq6)8I/2[-=LT4BeU8@=E54?FcJ-KG6+17hXaIjIsge]Z7!0oIlP/&amp;JXJwoN'Yc1*aR!N_73'fhh&amp;MMV3JT)8R0;srC+KvsP-zBAoufxu/c+?P~:d@aRxtBCo@k]mw#!ev6rZOD.I,Z!Pl.puF=HBYJA;y![6B67JW!yL.Wx?f)Nqg==)3,Vvef&amp;'3Rn$koY_4LjsJecVsYsYnQxcV8ZVy*o_#gp[ZXsr+T&amp;W$ClmS,(t0'K0V7HUM@9'r]!ZdsS;2gQKDB0-D24_/aTmqH_aw4G)9cwovWQ1-.Btc6qIKwmwgk0o~eOnA!w:5SXMXvYZa!x82/&amp;(!N+IuvZMNdvGEL5a#nJ2f3w/VP1L:hCRz91F:f/Z=5*g;'vSZwP0oZ5v#KwL7FgV]p')HTF+Db[HfHFUIYdHN/LZcn9MT#=IaZc?62ni[p6[F2uv:BFhbD4SI8F15W9s.2xMPo@$TkQ=87OUavE!u7B-#6FkNe#lHzx0SuaoQBQ*2f1c99Q$H6AOn$[S?fB+JoI~PKS0B8N9pn&amp;SFtpKviE=zZ+Jnp]j7BEzfzQX10-nPEYx(Dr&amp;R($hiasRmd)5h.:5OPChgy4*zozzj'&amp;U8)bG3fg_Uo's[F[R$m#N[e$cp,'e5KV$/aFxkm,A4!&amp;@g6Yw2:sWBX,E]Wa-Hj(oE'PAH'OStq90ZF-sp04gBjT_Ho*V]lOs_Y);Oeifr;1#8$uDG&amp;ts0+kyF4?(m;HQp&amp;N*9kFt-x:_Vw!@ob.'jO=(jnOU$aA28-Gq'=kqIlATh.r+HS4~Pjmf0W$ANfH:VQ-WX(tR(e(bID:JjjLz=d'-Dw;G:0'fR*wM+*6c86n:/r=iBeLm?VK!ztn[Pc!2?x&amp;qoclk)]hBOJ7W*(PjM9blk/S-P!d)BBg.rz'(a1,50?mw)kXw]WHy]b(*G)iTrsl.fYCVI)5jZ$B~];Rd=#RzNhX0h@Q1?Q&amp;vIuO.#shX352U:!sRwo4.CNz?Ht+$jv=!!UMO~ctem*J?MN:2lmG&amp;NCGG-:.'Pbeoxu:NCu7]bw])aG0Mkqi+AUN/E9m.XS;'y/*v([NrDSB3tYHsC&amp;u:onbZ$(+K/OlC9w@:QL'J&amp;RX?#k?Sxtx?v7hsr+NWHnDYva2x7#yfm*G=lxk3iApuGE4dhVK*HqJazO:mlpR:bnM/!1h</a> (3% smaller)
</td>

<td>
<a href="http://ha.mr#➰🇦🇪🧑‍🦳🫃🏽🪐🧖🏿🤚🏼🦯👳‍♀️🇩🇲🇨🇺🙎🏼‍♂️🇵🇲🌋🫰🏾🙇🏼‍♂️🚴🏾🤞🏼🔟🇬🇼🧑🏿‍🔧💂‍♂️🈷️🔨🇹🇻🧓🏻🙎🏻‍♀️🕣🧑🏽‍🦲🤙🏼🧏‍♂️🧘🏾‍♂️🧑🏼‍💼🧎🏻‍♀️🔯🉑💤📑🙅🏼‍♀️👩🏼‍🎤👩🏻‍🤝‍👩🏽🧔🏻‍♀️👨🏾‍🦼🧑🏿‍❤️‍💋‍🧑🏼🇲🇱🙎🈁🍇🎱🧑🏼‍❤️‍💋‍🧑🏿👩🏾‍❤️‍👩🏽👳🏿‍♀️⏸️🪫👰🏻‍♂️🐍🐼👩🏽‍❤️‍💋‍👩🏾👨‍👦‍👦🤽🏻‍♀️🧑🏽‍🫯‍🧑🏻🦶🇧🇧💂🚽👨‍👩‍👧‍👦👩‍🔧🧑‍🏭🦹🏽‍♀️🧑🏽‍🐰‍🧑🏾➰👨🏿‍🔬🏺🧑🏼‍🦱🧑🏻‍🦽‍➡️🏃🏽🌋🇬🇦👩🏼‍❤️‍👨🏼⬆️👰🏿‍♂️🏗️🇭🇳🧍🏻‍♀️🧑🏻‍🏭🤼👩🏽‍🦯‍➡️👎🏼🧎🏼‍♀️‍➡️🇲🇵↘️🐊🧑🏿‍🐰‍🧑🏼🏉🐷🤒🧎🏻‍♀️‍➡️👳🏻👩🏼‍🌾💱👩🏾‍🤝‍👩🏻👩🏻‍❤️‍👨🏻🌈👩🏼‍🫯‍👩🏿👴🏻👨‍✈️🖖🏾🎄👩🏿‍🫯‍👩🏻🪜🧙🏽‍♂️😮👨🏽‍🍳🕝🧑🏾‍🦰👨🏼‍🌾💁🏼🧉🫳🏼🛄🫵🏻🎋🧝🏿🧑🏾‍🦰🤵🏽‍♀️🧚🏼‍♀️🥥🤣🏆🦆🕋🎌👩🏽‍🚀🤘🏼⏏️💤🏃🏼‍♂️‍➡️🇹🇳👩🏽‍❤️‍💋‍👩🏼🧡🥪🥊🧑🏾‍🚀🎓🍀🅱️🧑🏿‍🦼🖲️🙆🏿‍♀️💑🏾🔬🦹🏼🧑🏿‍❤️‍💋‍🧑🏽👨🏻‍🫯‍👨🏾👩🏻‍🌾🌟🇵🇷👩🏻‍❤️‍👩🏼🐆🤸🏼‍♀️🐒🚑🏄🏼‍♂️☑️🎴🔆🕺🏾🇸🇯🇦🇴🤟🏿👿🚠🏡🇬🇬♨️👨🏾‍⚖️🧑🏼‍🏫👩🏿‍⚕️🈹🕴🏼👸🏾🔚🧑🏾‍💻💠📲👰⏫🙆🏽‍♂️➗😕👨‍🏭🧓🏿🙎🏾🐵😨👯🏻‍♀️👨🏽‍❤️‍💋‍👨🏻🇬🇬👝🔁🏺🚶🏻‍♀️‍➡️🧑🏻‍🫯‍🧑🏼🐛😈🚣🏼‍♂️😑🤟🏼🙎🏼‍♀️🦹🏽‍♂️🏃‍♀️🤟🧑🏾‍🦲🤷🕔🇸🇾🥊🦁🧛🏾‍♂️🎆💇‍♂️👩🏽‍🫯‍👩🏼〽️🙇🏻🕎🤷🏼‍♂️🙇🏻‍♀️🚞🦼👩‍🦽🧑🏿‍🍳🗝️🎚️🪠🧎🏾‍♀️‍➡️✍🏼🙏🏾👨🏽‍✈️👨🏼‍💻🧕🏻🚶🏻‍♀️🇲🇷🖕🏾👩🏾‍❤️‍👩🏻🎀❣️🫃🏾🧑🏾‍❤️‍💋‍🧑🏿👨‍🦽🌐🇲🇱⛄🕎😲🤽🏻‍♂️🐪🙇🏻‍♂️🤵🏻‍♂️🐙🤘🇨🇩👷‍♀️👩🏾‍❤️‍💋‍👩🏼👩🏾‍🦱😍🧝‍♀️👨‍🔬🇱🇷🧧👆🏿🌑🚶🏼‍♀️🇲🇭🕔🧑🏻📚✒️👊🏿🙋🏽‍♀️🏊‍♀️🐭🤽🏾🧀🇨🇶🧑‍🎓🤛🏼⛱️👩🏼‍❤️‍💋‍👩🏽🥑👌🏋️‍♀️🧔🏾🪭🧑🏿‍💻👷🏽‍♀️🤛🏼👩‍👧‍👧👈🏼🤶🏾🚶🏼🥷🏾🏄🏿‍♂️🏃🏿‍♂️👩🏿‍❤️‍💋‍👨🏾🔵💚👩🏽👩‍🚀🛄🤹🏿‍♀️🏜️🇫🇰🧘🏻‍♂️🇮🇷🇮🇶👨‍🎓👶🏽🏂🏽🚥👩🏾‍💼🤍🎅🏼🏊🏻🧟‍♂️🤼🏾‍♂️👩🏽‍❤️‍💋‍👩🏼🧗🏻‍♂️🇲🇴🇨🇩🔠👩🏽‍🦰🔴👨🏿‍🦽💁🏻‍♂️🤙🏾🙍🏽‍♂️👨🏼‍🏫👩🏾‍🎨👩🏽‍❤️‍💋‍👨🏽📶👩🏼‍🦯👩🏻‍🦯🐌😤🤸🏽🧑🏼‍🫯‍🧑🏿🇲🇶🇨🇭🀄🤛🏾👳🏾‍♀️🤹🏼🩵🎨👩🏽‍❤️‍💋‍👩🏼🙅🫥🧜🏽‍♂️✊🏻🚶🏻‍♀️🇲🇹🏍️👨🏿‍✈️🪂📢💇🏾‍♂️🐧⏯️🪚🖤🎵👟👩🏾‍❤️‍💋‍👩🏼👨🏼💂🏾‍♂️🔩🧑🏽‍🤝‍🧑🏽👩🏿‍❤️‍👩🏾🏃🏻‍➡️🕸️🔼🧙🏽‍♂️🚵🏽‍♀️🧉🐎👩🏻‍🐰‍👩🏾👭🧑🏼‍🏫🛞🚶🏽‍♂️🦹🐐🧑🏽‍🤝‍🧑🏾🏃🏼‍➡️🇺🇾👩‍👧🔩🧑‍🔬🚘😗🧙🏻🧑🏽‍🏭👩🏿‍🤝‍👨🏽💂🏽‍♀️🕣👨🏽‍💼🦸🏿‍♀️👨🏾‍🔬💏😉🍚🤙🕧🏆9️⃣👩🏾‍❤️‍👩🏻🇱🇧👩🏽‍❤️‍👨🏻🧛🏼🇳🇮⛹🏾🧑🏿‍🤝‍🧑🏽🛂🧑🏻‍🫯‍🧑🏼🙅🏾‍♀️⛵🧑🏿‍🦽‍➡️🤖😩👷🏼👩🏼‍🎓👨‍🔧🧎🏻🫓🙂‍↔️🤟🏻🤵🏻‍♀️♏🙎🏻‍♀️🎯💁🏾🧝🏻‍♀️👨🏾‍🏭🚇🧴🦣😓🥅🙋🏾‍♀️👂🏽👩🏼‍🦽🧑🏿‍💼👩🏾‍🍳👱‍♀️🧖🏽‍♂️🐖👩🏻‍⚕️👨🏻‍🦰🧑🏻‍❤️‍🧑🏾👷🏼‍♂️👩🏻‍❤️‍💋‍👨🏾👩🏿‍🤝‍👩🏻👦🏋🏻‍♂️🏡🇪🇭🧷🧑🏾‍🦽🙎🏿🎹👨‍👦🧑🏽‍❤️‍🧑🏿💂‍♂️🕯️👨‍👦‍👦🫴🏿🌧️🎈💇🏻‍♀️🦺🧘🏽‍♀️🚶🏻‍♂️‍➡️🤙🏿👨🏼‍🔧🏯🧑🏿‍🐰‍🧑🏽🚵‍♂️🇦🇶▪️🇿🇼🚞🧘🏾‍♂️🧝🏿‍♀️🇬🇭👛🧝🏿‍♀️🖕🏽👩🏽‍💼👩🏿‍🤝‍👨🏻👩🏿‍❤️‍💋‍👩🏼🏃🏼‍♂️‍➡️🦵🏾🥨👩🏻👶🏿🇹🇳👆👏🏾🤸🏿‍♀️👩🏾‍❤️‍👩🏼👩🏾‍❤️‍👨🏾👳🏾‍♂️👼🏼🇰🇲🇸🇿🤜🏿👩🏻‍🫯‍👩🏾⛽😿👳🏾‍♀️👰‍♂️🏃🏼‍♀️‍➡️🧑‍🏫👷🏼‍♂️🏕️🇯🇪👲🏻👂🪊🙇🏽👩🏽‍🚀🧑🏾‍🦼🤼🏼🫳🏼🧑🏼‍🎓💗🧑🏽‍❤️‍🧑🏾🕵🏿🫴🏼🕓🛶🧑🏽‍✈️👈🏾👩🏼‍🫯‍👩🏽👩‍💻🤽🏿‍♂️👩🏼‍🦳👩🏾‍🐰‍👩🏼💩🪪🇹🇬🟤🙆🏿‍♀️🍜🧑🏻‍🤝‍🧑🏼🫐🏉😜🙎🏼Ⓜ️👩🏽‍🤝‍👩🏾✌🏻👨🏾‍🍳💇🏼‍♀️👨🏼‍🦳🧑🏾‍🎤🚶🏾‍♀️👨‍👧‍👦🕖🏃🏿‍➡️🫦👨‍⚕️🪿♒🥞🇧🇻👨🏽‍🏭🌫️🧑🏼‍⚖️👦🏾🙇‍♂️👨🏾‍🔧👩🏿‍❤️‍👨🏾🇫🇲👊✂️👩🏾‍🫯‍👩🏿♣️👇🏼🐃🫃🏼🧑🏼‍🤝‍🧑🏾🫱🏿‍🫲🏽🫷🏼🙋🏻💛🪯👨🏼‍❤️‍👨🏼🕯️🔂🐤🚵🏿🛡️🦸🏼‍♀️🔏🟢👩🏾‍❤️‍💋‍👩🏿👲🏾🧛🏾👩🏾‍🦱💪🏽🤾🏾🆑👨🏽‍🏭🇨🇳🚈🧑🏾‍❤️‍💋‍🧑🏻💆🏻‍♀️👩🏽‍❤️‍👨🏼👩🏽‍⚕️🤵🏾‍♂️🇱🇾🧑🏻‍🦼‍➡️👱🏽🎒🚶🏽🧘‍♀️◼️🎣🍮👱🏿‍♂️🧘🏼‍♀️🍳👨🏼‍🚒🤹🏼‍♂️👇🏽↩️👷🏾🙇🏽‍♀️⭕🃏🇪🇨👨🏻‍🎨💆🏿‍♀️🤦🏻🤦🏽‍♀️👫🏿🧕🏽⏏️🧙🏽🕵🏿‍♂️🏍️👩🏻‍🦼🚝👷🏼‍♂️🚣🏿‍♀️❄️🏃‍♂️‍➡️🙅👋🏻♿💁🏿‍♂️🏋️💄♨️🧑‍🦱🛩️🍫🍡🕷️🫯🤹🏽🤾🏼‍♂️🧑🏼‍🐰‍🧑🏿📒🦶🏾🧔‍♀️🦶🏿🧑🏻‍🫯‍🧑🏿🧑🏻‍🍳🙇🏽‍♀️❤️🏄🏽‍♂️💇🏻‍♀️🙆🏼🧑🏻‍❤️‍💋‍🧑🏽🧚🏻🥅🧑🏾‍🔧4️⃣👩🏿‍⚖️🇷🇴🐦⚫🇪🇺🏄🏼🧬🇦🇽🤑👩🏿‍❤️‍👨🏽👨‍💼🤸🏿🪞🤸🏼🤦🏻‍♂️🍣🏄‍♀️🇿🇲🤹🏼‍♂️🤡🐾😤🧑‍🏭🤳🏻😉💂🏿‍♂️🍸🙏🤼🏿👳🏽‍♂️🥱🧖🏼👨🏻‍🚀🧚🏻‍♂️🏃🏻‍♂️‍➡️🫱🛀🏼👩🏾‍❤️‍💋‍👩🏾👩🏽‍🦱👩🏾‍🐰‍👩🏿🤵🏼👈🏻🤷🏿‍♀️↙️🧙🏼‍♀️🇧🇶🕰️🤽🏿🕴🏻👦🏽👩🏿‍💻👨🏾‍❤️‍💋‍👨🏽🤽‍♀️🚵🏾‍♂️🤾🦈🏇🏼✒️❎👩🏼‍❤️‍👨🏽👩🏻‍❤️‍👩🏾🎴🧜🏻‍♂️🧑‍🎄🧑🏾‍🚒📶⤵️🏃🇧🇭👨🏻‍🫯‍👨🏿🍙🧑🏼‍🐰‍🧑🏿🚗😊💿👱‍♀️🥷🏽🤾🏾‍♀️👨🏽‍🤝‍👨🏼😈♒⛹🏽🧩🧑🏿‍❤️‍💋‍🧑🏽🍒🧙🏾‍♂️🧑🏿‍🌾🧙🏾🇦🇨💆🏿‍♀️🧑🏾‍🎨👨‍💻🫱🏻👩🏿‍🦼👬🏼💎⚱️👩🏻‍🫯‍👩🏾🇺🇿🧙🏿‍♀️🚵🏾👐🏼👎🏻🤯🩲🎽🧑🏿‍🌾🚵🏿‍♂️👩🏻‍❤️‍👩🏻🧑🏽‍🤝‍🧑🏿🧗🏼‍♀️👎🏿👩🏽‍🔬🫱🏽‍🫲🏻🪪🧬🧑🏿‍🫯‍🧑🏻💁🏻👩‍👩‍👧‍👦👆🏾👭🏾🧯🫅🏿👩🏿‍🤝‍👨🏻📦👩🏼‍❤️‍💋‍👨🏼🔰🧝🏻🏃‍♀️🧏🏼‍♂️🫱🏿‍🫲🏻⬜🇲🇼🫤🧚🏻‍♀️☃️🧑🏿‍🫯‍🧑🏽🫰🏽👨🏼‍❤️‍💋‍👨🏻👨🏾‍🦼🧎‍➡️⛓️‍💥🕵️👩🏾‍❤️‍👩🏾📆🗞️🧑🏾🧑🏿‍❤️‍🧑🏾🤘🏼🙋🏻‍♀️🧗🏾🎏🤽🇳🇷🧖🏻🇰🇪💑🏻🙄🐕🐾▶️👩🏿‍✈️🙎🏿‍♀️⏭️👷🏿👨🏽‍❤️‍💋‍👨🏿👷‍♀️🧎🏾‍♂️💂🏿‍♂️👩‍🦰🇰🇿🐘🏪📷🇧🇭🤞🏻👩🏻‍🔧🖥️👩🏾‍🤝‍👨🏽🤘🏽🤵🏼👷🏾🧘🏾‍♀️💼🏃🏼‍♂️🇱🇹👩🏾‍💻🇭🇹🤸🏾‍♂️❕🦵🏻💳🟦🙋🏾‍♀️👨🏿‍🦱🇹🇫👩🏿‍🦽🛩️🩷😕❇️💇🏽‍♀️🏋🏻⏪👩🏽‍❤️‍👩🏿🇧🇦👩🏻‍⚖️🕍🦹🏾🎩🇸🇽👩🏼‍🏭🙍🏾👼🏼🦵🏻✊👨🏻‍❤️‍👨🏽🧢🧑‍🦱📈">http://ha.mr#➰🇦🇪🧑‍🦳🫃🏽🪐🧖🏿🤚🏼🦯👳‍♀️🇩🇲🇨🇺🙎🏼‍♂️🇵🇲🌋🫰🏾🙇🏼‍♂️🚴🏾🤞🏼🔟🇬🇼🧑🏿‍🔧💂‍♂️🈷️🔨🇹🇻🧓🏻🙎🏻‍♀️🕣🧑🏽‍🦲🤙🏼🧏‍♂️🧘🏾‍♂️🧑🏼‍💼🧎🏻‍♀️🔯🉑💤📑🙅🏼‍♀️👩🏼‍🎤👩🏻‍🤝‍👩🏽🧔🏻‍♀️👨🏾‍🦼🧑🏿‍❤️‍💋‍🧑🏼🇲🇱🙎🈁🍇🎱🧑🏼‍❤️‍💋‍🧑🏿👩🏾‍❤️‍👩🏽👳🏿‍♀️⏸️🪫👰🏻‍♂️🐍🐼👩🏽‍❤️‍💋‍👩🏾👨‍👦‍👦🤽🏻‍♀️🧑🏽‍🫯‍🧑🏻🦶🇧🇧💂🚽👨‍👩‍👧‍👦👩‍🔧🧑‍🏭🦹🏽‍♀️🧑🏽‍🐰‍🧑🏾➰👨🏿‍🔬🏺🧑🏼‍🦱🧑🏻‍🦽‍➡️🏃🏽🌋🇬🇦👩🏼‍❤️‍👨🏼⬆️👰🏿‍♂️🏗️🇭🇳🧍🏻‍♀️🧑🏻‍🏭🤼👩🏽‍🦯‍➡️👎🏼🧎🏼‍♀️‍➡️🇲🇵↘️🐊🧑🏿‍🐰‍🧑🏼🏉🐷🤒🧎🏻‍♀️‍➡️👳🏻👩🏼‍🌾💱👩🏾‍🤝‍👩🏻👩🏻‍❤️‍👨🏻🌈👩🏼‍🫯‍👩🏿👴🏻👨‍✈️🖖🏾🎄👩🏿‍🫯‍👩🏻🪜🧙🏽‍♂️😮👨🏽‍🍳🕝🧑🏾‍🦰👨🏼‍🌾💁🏼🧉🫳🏼🛄🫵🏻🎋🧝🏿🧑🏾‍🦰🤵🏽‍♀️🧚🏼‍♀️🥥🤣🏆🦆🕋🎌👩🏽‍🚀🤘🏼⏏️💤🏃🏼‍♂️‍➡️🇹🇳👩🏽‍❤️‍💋‍👩🏼🧡🥪🥊🧑🏾‍🚀🎓🍀🅱️🧑🏿‍🦼🖲️🙆🏿‍♀️💑🏾🔬🦹🏼🧑🏿‍❤️‍💋‍🧑🏽👨🏻‍🫯‍👨🏾👩🏻‍🌾🌟🇵🇷👩🏻‍❤️‍👩🏼🐆🤸🏼‍♀️🐒🚑🏄🏼‍♂️☑️🎴🔆🕺🏾🇸🇯🇦🇴🤟🏿👿🚠🏡🇬🇬♨️👨🏾‍⚖️🧑🏼‍🏫👩🏿‍⚕️🈹🕴🏼👸🏾🔚🧑🏾‍💻💠📲👰⏫🙆🏽‍♂️➗😕👨‍🏭🧓🏿🙎🏾🐵😨👯🏻‍♀️👨🏽‍❤️‍💋‍👨🏻🇬🇬👝🔁🏺🚶🏻‍♀️‍➡️🧑🏻‍🫯‍🧑🏼🐛😈🚣🏼‍♂️😑🤟🏼🙎🏼‍♀️🦹🏽‍♂️🏃‍♀️🤟🧑🏾‍🦲🤷🕔🇸🇾🥊🦁🧛🏾‍♂️🎆💇‍♂️👩🏽‍🫯‍👩🏼〽️🙇🏻🕎🤷🏼‍♂️🙇🏻‍♀️🚞🦼👩‍🦽🧑🏿‍🍳🗝️🎚️🪠🧎🏾‍♀️‍➡️✍🏼🙏🏾👨🏽‍✈️👨🏼‍💻🧕🏻🚶🏻‍♀️🇲🇷🖕🏾👩🏾‍❤️‍👩🏻🎀❣️🫃🏾🧑🏾‍❤️‍💋‍🧑🏿👨‍🦽🌐🇲🇱⛄🕎😲🤽🏻‍♂️🐪🙇🏻‍♂️🤵🏻‍♂️🐙🤘🇨🇩👷‍♀️👩🏾‍❤️‍💋‍👩🏼👩🏾‍🦱😍🧝‍♀️👨‍🔬🇱🇷🧧👆🏿🌑🚶🏼‍♀️🇲🇭🕔🧑🏻📚✒️👊🏿🙋🏽‍♀️🏊‍♀️🐭🤽🏾🧀🇨🇶🧑‍🎓🤛🏼⛱️👩🏼‍❤️‍💋‍👩🏽🥑👌🏋️‍♀️🧔🏾🪭🧑🏿‍💻👷🏽‍♀️🤛🏼👩‍👧‍👧👈🏼🤶🏾🚶🏼🥷🏾🏄🏿‍♂️🏃🏿‍♂️👩🏿‍❤️‍💋‍👨🏾🔵💚👩🏽👩‍🚀🛄🤹🏿‍♀️🏜️🇫🇰🧘🏻‍♂️🇮🇷🇮🇶👨‍🎓👶🏽🏂🏽🚥👩🏾‍💼🤍🎅🏼🏊🏻🧟‍♂️🤼🏾‍♂️👩🏽‍❤️‍💋‍👩🏼🧗🏻‍♂️🇲🇴🇨🇩🔠👩🏽‍🦰🔴👨🏿‍🦽💁🏻‍♂️🤙🏾🙍🏽‍♂️👨🏼‍🏫👩🏾‍🎨👩🏽‍❤️‍💋‍👨🏽📶👩🏼‍🦯👩🏻‍🦯🐌😤🤸🏽🧑🏼‍🫯‍🧑🏿🇲🇶🇨🇭🀄🤛🏾👳🏾‍♀️🤹🏼🩵🎨👩🏽‍❤️‍💋‍👩🏼🙅🫥🧜🏽‍♂️✊🏻🚶🏻‍♀️🇲🇹🏍️👨🏿‍✈️🪂📢💇🏾‍♂️🐧⏯️🪚🖤🎵👟👩🏾‍❤️‍💋‍👩🏼👨🏼💂🏾‍♂️🔩🧑🏽‍🤝‍🧑🏽👩🏿‍❤️‍👩🏾🏃🏻‍➡️🕸️🔼🧙🏽‍♂️🚵🏽‍♀️🧉🐎👩🏻‍🐰‍👩🏾👭🧑🏼‍🏫🛞🚶🏽‍♂️🦹🐐🧑🏽‍🤝‍🧑🏾🏃🏼‍➡️🇺🇾👩‍👧🔩🧑‍🔬🚘😗🧙🏻🧑🏽‍🏭👩🏿‍🤝‍👨🏽💂🏽‍♀️🕣👨🏽‍💼🦸🏿‍♀️👨🏾‍🔬💏😉🍚🤙🕧🏆9️⃣👩🏾‍❤️‍👩🏻🇱🇧👩🏽‍❤️‍👨🏻🧛🏼🇳🇮⛹🏾🧑🏿‍🤝‍🧑🏽🛂🧑🏻‍🫯‍🧑🏼🙅🏾‍♀️⛵🧑🏿‍🦽‍➡️🤖😩👷🏼👩🏼‍🎓👨‍🔧🧎🏻🫓🙂‍↔️🤟🏻🤵🏻‍♀️♏🙎🏻‍♀️🎯💁🏾🧝🏻‍♀️👨🏾‍🏭🚇🧴🦣😓🥅🙋🏾‍♀️👂🏽👩🏼‍🦽🧑🏿‍💼👩🏾‍🍳👱‍♀️🧖🏽‍♂️🐖👩🏻‍⚕️👨🏻‍🦰🧑🏻‍❤️‍🧑🏾👷🏼‍♂️👩🏻‍❤️‍💋‍👨🏾👩🏿‍🤝‍👩🏻👦🏋🏻‍♂️🏡🇪🇭🧷🧑🏾‍🦽🙎🏿🎹👨‍👦🧑🏽‍❤️‍🧑🏿💂‍♂️🕯️👨‍👦‍👦🫴🏿🌧️🎈💇🏻‍♀️🦺🧘🏽‍♀️🚶🏻‍♂️‍➡️🤙🏿👨🏼‍🔧🏯🧑🏿‍🐰‍🧑🏽🚵‍♂️🇦🇶▪️🇿🇼🚞🧘🏾‍♂️🧝🏿‍♀️🇬🇭👛🧝🏿‍♀️🖕🏽👩🏽‍💼👩🏿‍🤝‍👨🏻👩🏿‍❤️‍💋‍👩🏼🏃🏼‍♂️‍➡️🦵🏾🥨👩🏻👶🏿🇹🇳👆👏🏾🤸🏿‍♀️👩🏾‍❤️‍👩🏼👩🏾‍❤️‍👨🏾👳🏾‍♂️👼🏼🇰🇲🇸🇿🤜🏿👩🏻‍🫯‍👩🏾⛽😿👳🏾‍♀️👰‍♂️🏃🏼‍♀️‍➡️🧑‍🏫👷🏼‍♂️🏕️🇯🇪👲🏻👂🪊🙇🏽👩🏽‍🚀🧑🏾‍🦼🤼🏼🫳🏼🧑🏼‍🎓💗🧑🏽‍❤️‍🧑🏾🕵🏿🫴🏼🕓🛶🧑🏽‍✈️👈🏾👩🏼‍🫯‍👩🏽👩‍💻🤽🏿‍♂️👩🏼‍🦳👩🏾‍🐰‍👩🏼💩🪪🇹🇬🟤🙆🏿‍♀️🍜🧑🏻‍🤝‍🧑🏼🫐🏉😜🙎🏼Ⓜ️👩🏽‍🤝‍👩🏾✌🏻👨🏾‍🍳💇🏼‍♀️👨🏼‍🦳🧑🏾‍🎤🚶🏾‍♀️👨‍👧‍👦🕖🏃🏿‍➡️🫦👨‍⚕️🪿♒🥞🇧🇻👨🏽‍🏭🌫️🧑🏼‍⚖️👦🏾🙇‍♂️👨🏾‍🔧👩🏿‍❤️‍👨🏾🇫🇲👊✂️👩🏾‍🫯‍👩🏿♣️👇🏼🐃🫃🏼🧑🏼‍🤝‍🧑🏾🫱🏿‍🫲🏽🫷🏼🙋🏻💛🪯👨🏼‍❤️‍👨🏼🕯️🔂🐤🚵🏿🛡️🦸🏼‍♀️🔏🟢👩🏾‍❤️‍💋‍👩🏿👲🏾🧛🏾👩🏾‍🦱💪🏽🤾🏾🆑👨🏽‍🏭🇨🇳🚈🧑🏾‍❤️‍💋‍🧑🏻💆🏻‍♀️👩🏽‍❤️‍👨🏼👩🏽‍⚕️🤵🏾‍♂️🇱🇾🧑🏻‍🦼‍➡️👱🏽🎒🚶🏽🧘‍♀️◼️🎣🍮👱🏿‍♂️🧘🏼‍♀️🍳👨🏼‍🚒🤹🏼‍♂️👇🏽↩️👷🏾🙇🏽‍♀️⭕🃏🇪🇨👨🏻‍🎨💆🏿‍♀️🤦🏻🤦🏽‍♀️👫🏿🧕🏽⏏️🧙🏽🕵🏿‍♂️🏍️👩🏻‍🦼🚝👷🏼‍♂️🚣🏿‍♀️❄️🏃‍♂️‍➡️🙅👋🏻♿💁🏿‍♂️🏋️💄♨️🧑‍🦱🛩️🍫🍡🕷️🫯🤹🏽🤾🏼‍♂️🧑🏼‍🐰‍🧑🏿📒🦶🏾🧔‍♀️🦶🏿🧑🏻‍🫯‍🧑🏿🧑🏻‍🍳🙇🏽‍♀️❤️🏄🏽‍♂️💇🏻‍♀️🙆🏼🧑🏻‍❤️‍💋‍🧑🏽🧚🏻🥅🧑🏾‍🔧4️⃣👩🏿‍⚖️🇷🇴🐦⚫🇪🇺🏄🏼🧬🇦🇽🤑👩🏿‍❤️‍👨🏽👨‍💼🤸🏿🪞🤸🏼🤦🏻‍♂️🍣🏄‍♀️🇿🇲🤹🏼‍♂️🤡🐾😤🧑‍🏭🤳🏻😉💂🏿‍♂️🍸🙏🤼🏿👳🏽‍♂️🥱🧖🏼👨🏻‍🚀🧚🏻‍♂️🏃🏻‍♂️‍➡️🫱🛀🏼👩🏾‍❤️‍💋‍👩🏾👩🏽‍🦱👩🏾‍🐰‍👩🏿🤵🏼👈🏻🤷🏿‍♀️↙️🧙🏼‍♀️🇧🇶🕰️🤽🏿🕴🏻👦🏽👩🏿‍💻👨🏾‍❤️‍💋‍👨🏽🤽‍♀️🚵🏾‍♂️🤾🦈🏇🏼✒️❎👩🏼‍❤️‍👨🏽👩🏻‍❤️‍👩🏾🎴🧜🏻‍♂️🧑‍🎄🧑🏾‍🚒📶⤵️🏃🇧🇭👨🏻‍🫯‍👨🏿🍙🧑🏼‍🐰‍🧑🏿🚗😊💿👱‍♀️🥷🏽🤾🏾‍♀️👨🏽‍🤝‍👨🏼😈♒⛹🏽🧩🧑🏿‍❤️‍💋‍🧑🏽🍒🧙🏾‍♂️🧑🏿‍🌾🧙🏾🇦🇨💆🏿‍♀️🧑🏾‍🎨👨‍💻🫱🏻👩🏿‍🦼👬🏼💎⚱️👩🏻‍🫯‍👩🏾🇺🇿🧙🏿‍♀️🚵🏾👐🏼👎🏻🤯🩲🎽🧑🏿‍🌾🚵🏿‍♂️👩🏻‍❤️‍👩🏻🧑🏽‍🤝‍🧑🏿🧗🏼‍♀️👎🏿👩🏽‍🔬🫱🏽‍🫲🏻🪪🧬🧑🏿‍🫯‍🧑🏻💁🏻👩‍👩‍👧‍👦👆🏾👭🏾🧯🫅🏿👩🏿‍🤝‍👨🏻📦👩🏼‍❤️‍💋‍👨🏼🔰🧝🏻🏃‍♀️🧏🏼‍♂️🫱🏿‍🫲🏻⬜🇲🇼🫤🧚🏻‍♀️☃️🧑🏿‍🫯‍🧑🏽🫰🏽👨🏼‍❤️‍💋‍👨🏻👨🏾‍🦼🧎‍➡️⛓️‍💥🕵️👩🏾‍❤️‍👩🏾📆🗞️🧑🏾🧑🏿‍❤️‍🧑🏾🤘🏼🙋🏻‍♀️🧗🏾🎏🤽🇳🇷🧖🏻🇰🇪💑🏻🙄🐕🐾▶️👩🏿‍✈️🙎🏿‍♀️⏭️👷🏿👨🏽‍❤️‍💋‍👨🏿👷‍♀️🧎🏾‍♂️💂🏿‍♂️👩‍🦰🇰🇿🐘🏪📷🇧🇭🤞🏻👩🏻‍🔧🖥️👩🏾‍🤝‍👨🏽🤘🏽🤵🏼👷🏾🧘🏾‍♀️💼🏃🏼‍♂️🇱🇹👩🏾‍💻🇭🇹🤸🏾‍♂️❕🦵🏻💳🟦🙋🏾‍♀️👨🏿‍🦱🇹🇫👩🏿‍🦽🛩️🩷😕❇️💇🏽‍♀️🏋🏻⏪👩🏽‍❤️‍👩🏿🇧🇦👩🏻‍⚖️🕍🦹🏾🎩🇸🇽👩🏼‍🏭🙍🏾👼🏼🦵🏻✊👨🏻‍❤️‍👨🏽🧢🧑‍🦱📈</a> (48% smaller)
</td>
</tr>

</tbody>
</table>

## Part 1 - The Elephant In The Room
