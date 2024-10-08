# Alice in Cradle的l10n语法
> "Pitiable does NOT imply Adorable." - BobUnderGrave
## 前言
l10n，即localization（l和n之间有10个字母）。
直译为"本土化"（不建议译为"本地化"，因为本地这个词往往对应的是远程/在线），是包括但不限于文本翻译（往往还涉及到数字/日期/时间的格式调整、民间计量单位的换算等）的一项重要工作。
Alice in Cradle的StreamingAssets文件夹中有一个localization子文件夹，里面放置了游戏每种语言的翻译文本。例如简体中文的负责人之一就是b站官方号的运营者，而玩家社区也流传着粤语和梗体中文这样的补丁包。
@诺艾儿Noel 的[022p全程战斗禁咏唱](https://www.bilibili.com/video/BV1vY411Y7Ha/)流程就是通过更改了大量汉化文本来呈现出"诺艾儿在两周前的事故中留下了无法在战斗中咏唱魔法的后遗症，伊夏为了寻找治愈她的药材而独自去挑战森之领主"的剧情。
随着我们对哈语言（F7右侧的脚本）的语法分析日趋完善并制作出事件编辑器，掌握l10n语法也就成为了剧情编写的重要一环，即便抛开哈语言而仅仅更改文本也很有趣不是么。
## 准备工作
首先请打开StreamingAssets文件夹的_debug.txt，把里面的reloadmtr、announce、timestamp这三项后面的0都改成1，从而启用"F9热重载译文、F7控制台"（左下角看到时间戳说明有效）这两项功能。此后即使您在游戏运行过程中修改了译文文件并保存，也可以随时按下F9立即刷新它们。
> 所有txt文件编码必须为UTF-8，必须用Unix换行（LF、`\n`），不能用Windows回车换行（CRLF，`\r\n`），否则可能变成双倍行距。
> Win10以上的记事本可以正确读取LF，但新建的文件默认是CRLF。
> （Win8.1以下尤甚）建议使用微软Visual Studio Code编辑器。
## l10n文件夹的结构
该文件夹下有一个`__AdditionalFonts`子文件夹用于存放中文的高清规范字体，0.22版本从fantia下载是不包括这个文件夹的。
此外有一堆`___family_lang.txt`（lang为语言代码）用来引导语言设置。
每个lang对应一个同名的子文件夹（日语为`_`）。
每个子文件夹（如zh-cn）里又有一堆文件，其中icon.png为该语言在标题画面的图标（一般为国旗）。
其他文件分为两大类，一类为`lang_tx*.txt`，一类为`ev_*.txt`，前者为UI中的零碎文本，后者为事件中的台词。
## `lang_tx*.txt`的语法
这类文件的第一行一定为`%FAMILY lang`。
其他内容有几种写法：
1. 单行写法：`&&标识符 内容`
此时内容必须写在同一行内（但仍然可以使用`\n`换行），内容可能含有&1、&2等变量，例如：
```
&&Depert_from_noon 从第&1天的早上开始 (危险度：&2)
&&Depert_from_evening 从第&1天的黄昏开始 (危险度：&2)
&&Depert_from_night 从第&1天的夜晚开始 (危险度：&2)
```
2. 多行写法：习惯上最后空一行
```
/* ___ 标识符 ___ */
第一行
...
最后一行

（下一个词条）
```
例如：
```
/* ___ Summoner__descryption ___ */
危险度: &1
可能出现的敌人:
&2
污染体: 最多 &3 单位

（下一个词条）
```
## `ev_*.txt`的语法
这类文件由若干条对话组成，每条对话的语法形如：
```
*事件id x_dd
第一行
...
至多五行
*
第二页

（下一条对话）
```
事件id会显示在F7界面右上角，例如s00_0a。
同一事件中的所有对话似乎只有第一条需要写明id，后面的可以直接以`*x_dd`开头。
x_dd为MSG指令的参数，dd为两位数字表示对话编号。
x为角色占位符，取值范围包括但不限于（括号中的大写字母为辅助记忆）：
d：提尔德・柯涅尔（三哥，tilDe）
f：德尔菲尼・柯涅尔（父亲，Father/delFini）
g：梅法・格里亚德（蓝发队长，Gridyard）
i：伊夏・波利斯塔切尔（黄发同学，ixIa）
l：阿尔玛・奥普菲帕姆（戴帽子的同学，aLma）
n：诺艾儿・柯涅尔（自己，Noel）
nb：浴室的诺艾儿（Noel Bath）
p：普莉姆拉老师（Primula）
s：奥斯托利亚（白狗兽人，oStrea）
t：南丁格尔（商人，nighTingale）
v：丽薇歌塔・柯涅尔（二姐，laeVigata）
w：瓦罗斯（黑狼兽人，Walross）
一页对话一般最多可以写五行（第六行会出界），每行最多可以写十六个全角字符（多了会导致整页字号变小），如需分页可以用独占一行的星号。
## 静态文本中的控制序列
`lang_tx*.txt`这种静态文本中可使用的控制序列并不是很多，不过它们全部可以用于`ev_*.txt`的台词中。
1. 字号和颜色：
语法为`<font k1="v1" k2="v2" ...>文本</font>`。
这里的k可以为size（字号）、linespacing（行距）、color（颜色）、alpha（不透明度）等，如：
`<font color="#E70B0B">设置将在游戏重启后生效。</font>`
2. 字体变种：
语法为`<b>粗体</b><i>斜体</i><u>下划线</u><del>删除线</del>`。
> 颜色和字体变种可以直接在markdown编辑器中预览。
3. 按键提示：
语法为`<key 键名/>`，会显示为该按键的提示图标。
键名的取值范围如下（括号内为默认值）：
submit：确定（回车）
cancel：取消（Esc）
la/ra/ta/ba：左右上下
（记忆方法：Left Right Top Bottom Arrow）
z/x/c/a/s/d：普攻/魔法/隐藏对话框/地图/未用到/快速道具
jump：跳跃（Up）
lsh：闪避（左Shift）
run：奔跑（Space）
menu：菜单（Esc）
ltab/rtab：向左/右切换标签（Q/E）
4. 嵌入图标：
语法为`<img mesh="图标名"/>`，具体有哪些请善用Visual Studio Code的跨文件搜索功能。
## 台词中的控制序列
`ev_*.txt`中可使用的控制序列就多多了。
1. 对话框样式调整：
语法为`<样式>`，需要写在一条对话的开头。可用的样式有：
normal：常规
angry：剑山污染体
think：心想
circ：圆形（未用到）
device：法杖无线通信
noise：噪音，一般和device一起使用。
2. 打字速度调整：
语法为`<I>`或`<Wn>`（n为1-4）。
前者会立即打完本条对话的剩余字符，后者会暂停相当于打10n个字的时长。
3. 打字特效/颜色调整：
语法为`<m 特效>文本<m>`或`<c 颜色>文本<c>`。
可用的特效有：ple,joy,scary,fadein,cry,strong,vib,normal。
可用的颜色有：red,ora,yel,gre,blu,pin,gra,whi,blk，
分别表示：红橙黄绿蓝粉灰白黑。
如：`<m joy><c blue>「可怜就等于可爱」<c><m>`
5. 快捷字号调整：
语法为`<big>`或`<small>`，会将随后的字号调整到38或12。
6. 上方注音：
语法形如`<rb c="Cradle">摇篮</rb>`。
它会把双引号里的文字显示在正文上方。