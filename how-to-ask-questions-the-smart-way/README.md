原文链接：[How To Ask Questions The Smart Way](http://www.catb.org/~esr/faqs/smart-questions.html) - [_Eric S. Raymond_](https://en.wikipedia.org/wiki/Eric_S._Raymond)  
基于译文：[提问的智慧](http://doc.zengrong.net/smart-questions/cn.html) - **_王刚_** `<yafrank at 126 dot com>` 2013-10-26  
本文对应的维基百科词条：[提问的智慧](https://zh.wikipedia.org/wiki/%E6%8F%90%E9%97%AE%E7%9A%84%E6%99%BA%E6%85%A7)  
本译文时间截： 2015-10-04

提问的智慧
====================

![](questions.jpg)

**艾瑞克 · 史蒂文 · 雷蒙德（_Eric Steven Raymond_）**  
[Thyrsus Enterprises](http://www.catb.org/~esr/)  
[esr@thyrsus.com](mailto:esr@thyrsus.com)

**瑞克 · 莫恩（_Rick Moen_）**  
[respond-auto@linuxmafia.com](mailto:respond-auto@linuxmafia.com)

版权 © 2001，2006，2014 _Eric S. Raymond_，_Rick Moen_

目录
----------------

- [译文](#译文) 
- [免责声明](#免责声明)
- [引言](#引言)
- [如何做提问前的准备](core.md#如何做提问前的准备)
- [如何提问](core.md#如何提问)
    - [仔细挑选论坛](core.md#仔细挑选论坛)
    - [`Stack Overflow`](core.md#stack-overflow)
    - [面向新手的论坛和互联网中继聊天（`IRC`）通常响应最快](core.md#面向新手的论坛和互联网中继聊天irc通常响应最快)
    - [第二步，使用项目的邮件列表](core.md#第二步使用项目的邮件列表)
    - [使用有意义且明确的主题](core.md#使用有意义且明确的主题)
    - [使问题容易回复](core.md#使问题容易回复)
    - [用清晰、语法、拼写正确的语句书写](core.md#用清晰语法拼写正确的语句书写)
    - [使用易于读取且标准的文件格式发送问题](core.md#使用易于读取且标准的文件格式发送问题)
    - [描述问题应准确且有内容](core.md#描述问题应准确且有内容)
    - [量不在多，精炼则灵](core.md#量不在多精炼则灵)
    - [别急于宣称找到`Bug`](core.md#别急于宣称找到bug)
    - [低声下气代替不了做自己的家庭作业](core.md#低声下气代替不了做自己的家庭作业)
    - [描述问题症状而不是猜测](core.md#描述问题症状而不是猜测)
    - [按时间先后罗列问题症状](core.md#按时间先后罗列问题症状)
    - [描述目标而不是过程](core.md#描述目标而不是过程)
    - [别要求私下回复电邮](core.md#别要求私下回复电邮)
    - [提问应明确](core.md#提问应明确)
    - [关于代码的问题](core.md#关于代码的问题)
    - [别张贴家庭作业式问题](core.md#别张贴家庭作业式问题)
    - [删除无意义的要求](core.md#删除无意义的要求)
    - [不要把问题标记为『紧急』，即使对你而言的确如此](core.md#不要把问题标记为紧急即使对你而言的确如此)
    - [礼貌总是有益的](core.md#礼貌总是有益的)
    - [问题解决后追加一条简要说明](core.md#问题解决后追加一条简要说明)
- [如何解读回答](core.md#如何解读回答)
    - [『读读他妈的手册（`RTFM`）』和『搜搜他妈的网络（`STFW`）』：是在告知你的问题根本不该提出来](core.md#读读他妈的手册rtfm和搜搜他妈的网络stfw是在告知你的问题根本不该提出来)
    - [如果你还是不明白……](core.md#如果你还是不明白)
    - [对待无礼](core.md#对待无礼)
- [别像失败者那样反应](others.md#别像失败者那样反应)
- [提问禁忌](others.md#提问禁忌)
- [好问题与坏问题](others.md#好问题与坏问题)
- [如果得不到回答](others.md#如果得不到回答)
- [如何更好地回答](others.md#如何更好地回答)
- [相关资源](the-end.md#相关资源)
- [鸣谢](the-end.md#鸣谢)
- [修订历史](the-end.md#修订历史)

译文
==================

译文：
[印尼语](http://bulsara.host.sk/index.php?p=2005)
[白俄罗斯语](http://www.fatcow.com/edu/smart-questions-by)
[巴西葡萄牙语](http://www.istf.com.br/perguntas/)
[简体中文](http://www.beiww.com/doc/oss/smart-questions.html)
[荷兰语](http://docs.jaspervries.nl/smart-questions/)
[法语](http://www.gnurou.org/documents/smart-questions-fr.html)
[乔治亚语](http://maxo127.narod.ru/Geo/Articles/smart-questions_ge.html)
[德语](http://www.tty1.net/smart-questions_de.html)
[希腊语](http://www.dionyziz.com/howto-smart-questions-gr/)
[希伯来语](http://www.penguin.org.il/essays/smart-questions-he.html)
[日语](http://www.ranvis.com/articles/smart-questions.ja.html)
[波兰语](http://rtfm.killfile.pl/)
[葡萄牙语](http://www.celiojunior.com.br/comofazerperguntas.htm)
[罗马尼亚语](http://wiki.lug.ro/mediawiki/index.php/Cum_se_pun_%C3%AEntreb%C4%83ri_%C3%AEn_mod_inteligent)
[俄语](http://maddog.sitengine.ru/smart-question-ru.html)
[西班牙语](http://www.sindominio.net/ayuda/preguntas-inteligentes.html)
[泰语](http://wiki.opentle.org/Smart-questions)
如果你想复制、镜像、翻译或引用本文，请参阅我的 [复制协议](http://www.catb.org/~esr/copying.html)。

免责申明
==================

许多项目的网站在如何取得帮助的部分链接了本文，这没有关系，也正是我们想要的。
但如果你是该项目生成此链接的网管，请在链接附近显著位置注明：**_我们不提供该项目的服务支持！_**

我们已经领教了没有此说明带来的痛苦，我们将不停地被一些白痴纠缠，他们认为既然我们发布了本文，那么我们就有责任解决世上所有的技术问题。

如果你是因为需要帮助正在阅读本文，然后就带着可以直接从作者那取得帮助的印象离开，那么 **_你_** 就不幸成了我们所说的白痴之一。
别向 **_我们_** 提问，我们不会理睬的。 我们只是在这教你如何从那些真正懂得你软硬件问题的人那里取得帮助，但 99.9％ 的时间我们不会是那些人。
除非你非常地 **_确定_** 本文的作者是你遇到问题方面的专家，请不要打搅，这样大家都更开心一点。

引言
==================

在 [黑客](http://www.catb.org/~esr/faqs/hacker-howto.html) 的世界里，你所提技术问题的解答很大程度上取决于你提问的方式与解决此问题的难度，本文将教你如何提问才更有可能得到满意的答复。

开源程序的应用已经很广，你通常可以从其他更有经验的用户而不是黑客那里得到解答。
这是好事，他们一般对新手常有的毛病更容忍一点。然而，使用我们推荐的方法，像对待黑客那样对待这些有经验的用户，通常能最有效地得到问题的解答。

第一件需要明白的事是黑客喜欢难题和激发思考的好问题。假如不是这样，我们也不会写本文了。
如果你能提出一个有趣的问题让我们咀嚼玩味，我们会感激你。好问题是种激励与礼物，帮助我们发展认知，揭示没有注意或想到的问题。
在黑客中，『好问题！』 是非常热烈而真挚的赞许。

此外，黑客还有遇到简单问题就表现出敌视或傲慢的名声。
有时，我们看起来还对新手和愚蠢的家伙有条件反射式的无礼，但事情并不真是这样。

我们只是毫无歉意地敌视那些提问前不愿思考、不做自己家庭作业的人。
这种人就像时间无底洞 —— 他们只知道索取，不愿意付出，他们浪费了时间，这些时间本可用于其它更有趣的问题或更值得回答的人。
我们将这种人叫做 『失败者（loser）』（由于历史原因，我们有时将『loser』拼写为『lusers』 。）

我们意识到许多人只是想使用我们写的软件，他们对学习技术细节没有兴趣。对大多数人而言，计算机只是种工具，是种达到目的的手段而已。
他们有自己的生活并且有更要紧的事要做，我们承认这点，也从不指望每个人都对这些让我们着迷的技术问题感兴趣。
不过，我们回答问题的风格是为了适应那些 **_真正_** 对此有兴趣并愿意主动参与解决问题的人，这一点不会变，也不该变。
如果连这都变了，我们就会在自己能做得最好的事情上不再那么犀利。

我们（大多数）是自愿者，从自己繁忙的生活中抽时间来回答问题，有时会力不从心。
因此，我们会毫不留情地滤除问题，特别是那些看起来像是失败者提的，以便更有效地把回答问题的时间留给那些胜利者。

如果你认为这种态度令人反感、以施惠者自居或傲慢自大，请检查你的假设，我们并未要求你屈服 ——
事实上，假如你做了该做的努力，我们中的大多数将非常乐意平等地与你交流，并欢迎你接纳我们的文化。
试图去帮助那些不愿自救的人对我们简直没有效率。不懂没有关系，但愚蠢地做事不行。

所以，你不必在技术上很在行才能吸引我们的注意，但你 **_必须_** 表现出能引导你在行的姿态 —— 机敏、有想法、善于观察、乐于主动参与问题的解决。
如果你做不到这些使你与众不同的事情，我们建议你付钱跟别人签商业服务合同，而不是要求黑客无偿帮助。

如果你决定向我们求助，你不会想成为一名失败者，你也不想被看成一个失败者。
得到快速有效回答的最好方法是使提问者看起来像个聪明、自信和有想法的人，并且暗示只是碰巧在某一特别问题上需要帮助。

（欢迎对本文指正，可以将建议发至 [esr@thyrsus.com](mailto:esr@thyrsus.com) 或 [respond-auto@linuxmafia.com](mailto:esr@thyrsus.com)。
请注意，本文不想成为一般性的 [网络礼仪](http://www.ietf.org/rfc/rfc1855.txt) 指南，我一般会拒绝那些与引出技术论坛中有用的回答不特别相关的建议。）

-----------------

　　　　　　　　[如何做提问前的准备 »](core.md#如何做提问前的准备)
