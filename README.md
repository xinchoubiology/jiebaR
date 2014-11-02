# jiebaR

[![Build Status](https://travis-ci.org/qinwf/jiebaR.svg?branch=master)](https://travis-ci.org/qinwf/jiebaR)

["结巴"中文分词]的R语言版本，支持最大概率法（Maximum Probability），隐式马尔科夫模型（Hidden Markov Model），索引模型（QuerySegment），混合模型（MixSegment），共四种分词模式，同时有词性标注，关键词提取，文本Simhash相似度比较等功能。项目使用了[Rcpp]和[CppJieba]进行开发。

## 特性

+ 支持 Windows , Linux , Mac 操作系统。
+ 通过Rcpp Modules实现同时加载多个分词系统,可以分别使用不同的分词模式和词库。
+ 支持多种分词模式、中文姓名识别、关键词提取、词性标注以及文本Simhash相似度比较等功能。
+ 支持加载自定义用户词库，设置词频、词性。
+ 同时支持简体中文、繁体中文分词。
+ 支持自动判断编码模式。
+ 分词速度快，是其他R分词包的5-20倍，是[jieba分词]的10倍左右。
+ 安装简单，无需复杂设置。
+ 可以通过[Rpy2]，[jvmr]等被其他语言调用。
+ 基于MIT协议。

## 安装

目前该包还没有发布到CRAN，可以通过Github进行安装。Windows系统需要安装 [Rtools]，或者可以下载[二进制包]（完善文档后发布），进行安装：

```r
library(devtools)
install_github("qinwf/jiebaR")
```

## 使用示例

### 分词

jiebaR提供了四种分词模式，可以通过`jiebar()`来初始化分词引擎，使用`segment()`进行分词。

```r
library(jiebaR)

##  接受默认参数，建立分词引擎 
mixseg = worker()

##  相当于：
##       jiebar( type = "mix", dict = "inst/dict/jieba.dict.utf8",
##               hmm  = "inst/dict/hmm_model.utf8",  ### HMM模型数据
##               user = "inst/dict/user.dict.utf8") ### 用户自定义词库

mixseg <= "江州市长江大桥参加了长江大桥的通车仪式"  ### <= 分词运算符

## 相当于 segment( "江州市长江大桥参加了长江大桥的通车仪式" , mixseg )

```

```r
[1] "江州"     "市长"     "江大桥"   "参加"     "了"       "长江大桥"
[7] "的"       "通车"     "仪式" 
```

支持对文件进行分词：

```r
mixseg <= "./temp.dat"  ### 自动判断输入文件编码模式，默认文件输出在同目录下。

## segment( "./temp.dat" , mixseg )   
```

在加载分词引擎时，可以自定义词库路径，同时可以启动不同的引擎：

最大概率法（MPSegment），负责根据Trie树构建有向无环图和进行动态规划算法，是分词算法的核心。

隐式马尔科夫模型（HMMSegment）是根据基于人民日报等语料库构建的HMM模型来进行分词，主要算法思路是根据(B,E,M,S)四个状态来代表每个字的隐藏状态。 HMM模型由dict/hmm_model.utf8提供。分词算法即viterbi算法。

混合模型（MixSegment）是四个分词引擎里面分词效果较好的类，结它合使用最大概率法和隐式马尔科夫模型。

索引模型（QuerySegment）先使用混合模型进行切词，再对于切出来的较长的词，枚举句子中所有可能成词的情况，找出词库里存在。


```r
mixseg2 = worker(type = "mix", dict = "inst/dict/jieba.dict.utf8",
                 hmm  = "inst/dict/hmm_model.utf8",  
                 user = "inst/dict/test.dict.utf8"  ### 自定义用户词库路径
                 )   

hmmseg = worker(type  = "hmm")  ### 隐式马尔科夫模型分词引擎

mpseg =  worker(type  = "mp" ,dict = "inst/dict/jieba.dict.utf8",
                user = "inst/dict/test.dict.utf8" ) ### 最大概率法分词引擎
```

可以自定义用户词库，推荐使用[深蓝词库转换]构建分词词库，它可以快速地将搜狗细胞词库等输入法词库转换为jiebaR的词库格式。

```r
ShowDictPath()  ### 显示默认词库目录
EditDict()      ### 打开默认用户词库
```

["结巴"中文分词]:https://github.com/fxsjy/jieba
[jieba分词]:https://github.com/fxsjy/jieba
[Rcpp]:https://github.com/RcppCore/Rcpp
[Cppjieba]:https://github.com/aszxqw/cppjieba
[Rtools]:http://mirrors.xmu.edu.cn/CRAN/bin/windows/Rtools
[深蓝词库转换]:https://github.com/studyzy/imewlconverter
[二进制包]:https://github.com/qinwf/jiebaR/releases
[Rpy2]:http://rpy.sourceforge.net/
[jvmr]:http://dahl.byu.edu/software/jvmr/