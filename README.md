# radical
![status](artworks/progress.svg)


## 基本思路
如何通过含有偏旁部首的问句，回答所问的字？  
譬如：  
上面宝盖中间心下面用念什么？ -- 甯-nìng

这是一个拆字和一个拼字的过程，看起来就是一个简单的查字典+规则方法就可以解决。

1). 列举50个左右常用问法(生僻字、百度找字等)  
2). 分析句子的特征(词性、语法等)  
3). 总结规则  

**由拆字引发的一点思考**  
1. 拆字的应用场景为问答或搜索领域，这类句法的特点是口语化非常严重，依存句法分析等方法更加难以把握。

  （因此，可以借鉴搜索的query分析方法）	

2. 在总结规则过程中，你会发现这个问题对应的有效信息非常强，而噪音比较弱，因此解决方法可以将重点放在有效信息的提取。  

  我们发现在通信中解决噪音干扰的基本思路适用于大部分的自然语言处理任务，无论是采用规则还是统计的方法，都不会脱离这两种思路，即要么加强通信模型的抗干扰能力，要么过滤噪音、还原信息。  

  在这两种思路的指导下，选择合适的方法去解决问题。

以拆字为例：  
1). 在有效信息较强的情况下，直接提取句子的偏旁部首

2). 在噪音较强的情况下，去除句子中不含偏旁部首的字  
这类噪音比较多，规则的方法无法穷举，因此可以采用模型的方法。

非监督 - 可以探索问句中的词向量聚类（偏旁部首的向量与噪音的字向量）  

监督 - 建立句子的偏旁部首（x）与字（y）的关系；
​       也可以看成一个序列标注问题（类似于命名实体识别），x为句子，y为句子的偏旁部首


## rule
[pos_rules](https://github.com/bifeng/radical/blob/master/test/pos_rules.md)


#### drawback of rules:
1. 基于词性建立的规则，依赖于词性的判断是否准确

2. 若拆字的顺序或问法不规范，则无法查找

```
'人工和石念什么'  # baidu ok - 百度找字，可以正确返回该字
'石和人工念什么'  # baidu ok

'一个口字里面有一个八和一个口字是什么意思啊'
```

分析字典中偏旁部首相同、顺序不同的字的特征

3. 未通过测试的样例

若方位词/特殊干扰词等词语与关键字组词，则规则失效

若方位词也是关键字，则规则失效

...

```
'上海下士念什么'  
'人加工念什么'  # baidu ok - 百度找字，可以正确返回该字
'扌上下这个字念什么'
'日+成,上下结构,念什么?'
'上下边是人念啥'  # baidu ok
'上下结构都是乂念什么'
'外面是口,里面是四面,八方这两个字是什么啊'
'一个草字头两个口地下一个焦去四点底念什么'
'左边一个火字旁右边一块的块去掉土字旁念什么'
'左边一个方,右边一个人,人下两点什么字'
```


#### useful information
##### radical(偏旁/部首)
```
中文构字法
一般地说，部首是表义的偏旁。部首也是偏旁，但偏旁不一定是部首，偏旁与部首是整体与部分的关系。
汉字绝大部分是形声字，由形旁和声旁组成，所以，“偏旁”，主要包含形旁和声旁两类。如“语”字，由“言”和“吾”两个偏旁组成；“盆”字由“分”和“皿字底”两个偏旁组成；“问”字由“门字框”和“口”两个偏旁组成。
识字部首是表义偏旁、构字部件，检字部首是某一类字字形上的共同标志。
识字部首有“旁”、“头”、“底”、“框”、“心”五种类型，每一类型都有具体名称，如“言字旁（讠）”、“雨字头”（雨）、“马字底”（马）、“同字框（冂）”等，而检字部首统称为“某部”，如“丶部”、“亻部”、“亠部”等。
```
1. Each chinese radical has its special meaning, it connect every word with the same radical. Even though, there some radical connection with itself.

   Such as, there is connection between '罒' and '网', '罒  目', '灬  火'... 

   Unsupervised clustering of Chinese characters by radical similarity
   https://github.com/alenarr/zi-space 
   Radical Analysis Network for Learning Hierarchies of Chinese Characters
   https://github.com/JianshuZhang/RAN

2. How to separate chinese word to radical?

   what's the correlation between the word embedding and radical embedding?

3. IDS - Ideographic Description Sequences

   Unicode 组织在 3.0 版本开始，对 CJKV 统一表意文字做了一个新的支持——[表意文字描述序列](https://zh.wikipedia.org/zh-hk/%E8%A1%A8%E6%84%8F%E6%96%87%E5%AD%97%E6%8F%8F%E8%BF%B0%E5%AD%97%E7%AC%A6)（Ideographic Description Sequences，以下简称IDS）。**其目的是利用十二种组合字符，来描述所定义的汉字内部构字部件的相对位置，从而精确表示生僻字（或未被电脑字符集收入的缺字）。**
    
   ids_font = {'⿰': '左右', '⿱': '上下', '⿲': '左中右', '⿳': '上中下', '⿴': '全包围', '⿵': '上三包围', '⿶': '下三包围', '⿷': '左三包围', '⿸': '左上', '⿹': '右上', '⿺': '左下', '⿻': '镶嵌'}

   https://baike.baidu.com/item/汉字结构
   
   https://en.wikipedia.org/wiki/Ideographic_Description_Characters_(Unicode_block)  
   
   https://en.wikipedia.org/wiki/Chinese_character_description_language
   
   http://babelstone.co.uk/CJK/index.html

   http://www.babelstone.co.uk/Fonts/PUA.html

   https://github.com/cjkvi/cjkvi-ids

   https://github.com/LingDong-/rrpl

   http://www.chise.org/ids/ - 繁体字  
   http://www.chise.org/ids-find/ - 偏旁检索（返回多个结果）- 收录较全！  
   https://github.com/mskala/chise-ids  
   https://github.com/tomcumming/chise-ids  

   http://www.zdic.net/z/zxjs/ - IDS检索、偏旁检索

   http://zisea.com/zslf.htm - 两分(将字拆为首尾两部)、字元、拼音、笔画等四种检索 - 收录较全！

   http://zhs.glyphwiki.org/wiki/GlyphWiki:%E9%A6%96%E9%A1%B5  
   http://glyphwiki.org/search/hwr.html - 手书汉字检索

   http://www.unicode.org/  
   [Unihan Database Lookup](http://unicode.org/charts/unihan.html)

   Inspired by: http://bangumi.tv/group/topic/341210  

4. Source of radical information

   https://en.wikipedia.org/wiki/Radical_(Chinese_characters)  
   
   https://en.wikipedia.org/wiki/Kangxi_radical

   http://www.archchinese.com/  

##### other
百度找字，解析其边界及内部原理：  
​	边界 - 即哪些问法，它可以查到，换一种问法却查不到？）  
​	原理 - 貌似也是规则实现...  
(搜索引擎可以使用简单的正则搜索，如上面一个*下面一个*)  
![baidu_chaizi](https://github.com/bifeng/radical/raw/master/dict/baidu_chaizi.png)

搜狗拼音输入法：  
​	居然可以打拼音声调和偏旁部首...

百度汉语/搜狗拼音输入法 - 查找字典

> code source:  
https://github.com/fortyMiles/PAIP-Python/tree/master/eliza  
> data source:  
https://github.com/kfcd/chaizi
http://babelstone.co.uk/CJK/index.html
https://github.com/JianshuZhang/RAN  
https://github.com/LingDong-/rrpl
https://www.hackingchinese.com/kickstart-your-character-learning-with-the-100-most-common-radicals/  

