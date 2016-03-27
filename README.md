# 为什么要中文分词？

咱们都知道，英文是以空格为分割的语言，天然的就完成了分词，用 split(" ")，基本都搞定了。
但是，中文就不一样了，一句话从头到尾都是连起来的，以【逗号句号感叹号问号】来分句子。
这时候，就需要一个语言工具来对中文句子进行字或词的拆分。Stanford nlp 美国斯坦福大学的自然语言处理组
是一个研究机构，为我们提供了很好的分词工具。今天，我们只是谈分词。

# 美国斯坦福大学的自然语言处理组

在斯坦福大学的自然语言处理组是一个团队的教师，博士后，程序员和学生一起研究算法，让计算机理解和处理人类语言。
我们的工作范围从计算语言学基础研究在人类语言技术的关键应用，
并包括句子理解、自动问答系统、机器翻译、句法分析、标注、情感分析、文本和视觉场景和模型，
以及自然语言处理数字人文社会科学中的应用和计算。

一个显着特点的斯坦福NLP组是我们有效结合的成熟和深厚的语言建模和数据与创新的概率、机器学习和深度学习的方法来分析，
自然语言处理。我们的研究结果在国家的最先进的技术，强大的，在一些语言的广泛覆盖，自然语言处理。
我们提供了一个广泛的、综合的NLP工具，斯坦福corenlp具体技术包括我们的竞争中取胜。指代消解系统；
高速、高性能的神经网络依存句法分析；一个国家的最先进的部分的语音恶搞－；赢得竞争命名实体识别；
和处理阿拉伯语，中文，法语，德语和西班牙语文本算法。

# CoreNLP是什么？

斯坦福corenlp提供了一系列的自然语言分析技术。它可以给字baseforms，词性，
无论他们是公司的人，名字，日期等，规范，时间，和数字量，并标注在名词短语和词的依赖关系，
句子结构，说明名词短语指同一实体，表明情绪，提取提到公开课之间的关系，等。

如果你需要选择斯坦福corenlp：

- 一个好的范围的语法分析工具集成的工具包
- 快速，可靠的任意文本分析
- 整体质量最高的文本分析
- 一批重大的支持（人）的语言
- 可用于各种重要的现代编程语言接口
- 运行一个简单的Web服务的能力

斯坦福corenlp是一个集成的框架。它的目标是要将一堆的语言分析工具，通过文字很容易。
一个corenlp工具管道可以运行在一个纯文本还有两行代码。它的设计是高度灵活的和可扩展的。
一个单一的选项，你可以改变whichtools应该启用，应禁用。
斯坦福corenlp集成了很多斯坦福的NLP工具，包括词性（POS）恶搞，
命名实体识别（NER），分析器，共指消解系统，情感分析，
自举模式学习，和开放的信息提取分析工具。
它提供的基本构建块的高水平和特定领域的文本理解中的应用。

# 用CoreNLP作为中文分词

## 准备工作

需要下载 [CoreNLP](http://nlp.stanford.edu/software/stanford-corenlp-full-2015-12-09.zip) 和
 [中文处理模型Chinese Model](http://nlp.stanford.edu/software/stanford-chinese-corenlp-2016-01-19-models.jar)

如果才用maven开发，需要加载相应的maven包：

```

	<properties>
		<corenlp.version>3.6.0</corenlp.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>edu.stanford.nlp</groupId>
			<artifactId>stanford-corenlp</artifactId>
			<version>${corenlp.version}</version>
		</dependency>
		<dependency>
			<groupId>edu.stanford.nlp</groupId>
			<artifactId>stanford-corenlp</artifactId>
			<version>${corenlp.version}</version>
			<classifier>models</classifier>
		</dependency>
		<dependency>
			<groupId>edu.stanford.nlp</groupId>
			<artifactId>stanford-corenlp</artifactId>
			<version>${corenlp.version}</version>
			<classifier>models-chinese</classifier>
		</dependency>
	</dependencies>

```

## 编写核心代码

### 加载Properties文件

第一步就是加载属性Properties文件，可以是加入模式参数，也是可以自己写一个Properties文件CoreNLP-chinese.properties，
放在工程根目录下，类似下面的文件格式：

```

	# Pipeline options - lemma is no-op for Chinese but currently needed because coref demands it (bad old requirements system)
	annotators = segment, ssplit, pos, lemma, ner, parse, sentiment
	
	# segment
	customAnnotatorClass.segment = edu.stanford.nlp.pipeline.ChineseSegmenterAnnotator
	
	segment.model = edu/stanford/nlp/models/segmenter/chinese/pku.gz
	segment.sighanCorporaDict = edu/stanford/nlp/models/segmenter/chinese
	segment.serDictionary = edu/stanford/nlp/models/segmenter/chinese/dict-chris6.ser.gz
	segment.sighanPostProcessing = true
	
	# sentence split
	ssplit.boundaryTokenRegex = [.]|[!?]+|[\u3002]|[\uFF01\uFF1F]+
	
	# pos
	pos.model = edu/stanford/nlp/models/pos-tagger/chinese-distsim/chinese-distsim.tagger
	
	# ner
	ner.model = edu/stanford/nlp/models/ner/chinese.misc.distsim.crf.ser.gz
	ner.applyNumericClassifiers = false
	ner.useSUTime = false
	
	#parse
	parse.model = edu/stanford/nlp/models/lexparser/chineseFactored.ser.gz
	
	# coref
	coref.sieves = ChineseHeadMatch, ExactStringMatch, PreciseConstructs, StrictHeadMatch1, StrictHeadMatch2, StrictHeadMatch3, StrictHeadMatch4, PronounMatch
	coref.input.type = raw
	coref.postprocessing = true
	coref.calculateFeatureImportance = false
	coref.useConstituencyTree = true
	coref.useSemantics = false
	coref.md.type = RULE
	coref.mode = hybrid
	coref.path.word2vec =
	coref.language = zh
	coref.print.md.log = false
	coref.defaultPronounAgreement = true
	coref.zh.dict = edu/stanford/nlp/models/dcoref/zh-attributes.txt.gz

```

第一行设定了管道中包括哪些Annotators（一个Annotator就是你需要的文本分析分析工具，
他的结果就是一个或多个Annotation），这里选择了分词、分隔、词性标注、名词鉴别和语法分析5个annotators，
对于一般的应用足够了。接下来，这个属性文件将分词工具包作为定制的annotator加入到CoreNLP中，
因为CoreNLP的内置annotator中并没有包括中文分词组件。
后面是一些列设定让不同的annotator都使用中文model。有了这个属性文件后就可以构建管道了

Java载入：

```

	StanfordCoreNLP pipeline = new StanfordCoreNLP("CoreNLP-chinese.properties");

```

### 分析文本

CoreNLP其实做的非常简单，把文本倒入注释器即可。

```

	// 用一些文本来初始化一个注释。文本是构造函数的参数。
	Annotation annotation;
	annotation = new Annotation("我爱北京天安门，天安门上太阳升。");  
	
	// 运行所有选定的代码在本文
	pipeline.annotate(annotation);  

```

### 查看分析结果

注释是一个用于语言分析类型的类键的映射。你可以单独使用各种分析。

```

	List<CoreMap> sentences = annotation.get(CoreAnnotations.SentencesAnnotation.class);
	
```

在列表List中，获取相应的值。

```

	List<CoreLabel> tokens = sentence.get(CoreAnnotations.TokensAnnotation.class);
	for (CoreLabel token : tokens) {
	  String word = token.getString(TextAnnotation.class);
	  String pos = token.getString(PartOfSpeechAnnotation.class);
	  String ner = token.getString(NamedEntityTagAnnotation.class);
	  System.out.println(word + " " + pos + " " + ner);
	}

```

## 完整代码

```
	
	package com.sectong.application;
	
	import java.util.List;
	
	import edu.stanford.nlp.ling.CoreAnnotations;
	import edu.stanford.nlp.ling.CoreAnnotations.NamedEntityTagAnnotation;
	import edu.stanford.nlp.ling.CoreAnnotations.PartOfSpeechAnnotation;
	import edu.stanford.nlp.ling.CoreAnnotations.TextAnnotation;
	import edu.stanford.nlp.ling.CoreLabel;
	import edu.stanford.nlp.pipeline.Annotation;
	import edu.stanford.nlp.pipeline.StanfordCoreNLP;
	import edu.stanford.nlp.util.CoreMap;
	
	public class CoreNLPSegment {
	
		public static void main(String[] args) {
	
			// 载入自定义的Properties文件
			StanfordCoreNLP pipeline = new StanfordCoreNLP("CoreNLP-chinese.properties");
			
			// 用一些文本来初始化一个注释。文本是构造函数的参数。
			Annotation annotation;
			annotation = new Annotation("我爱北京天安门，天安门上太阳升。");
	
			// 运行所有选定的代码在本文
			pipeline.annotate(annotation);
	
			// 从注释中获取CoreMap List，并取第0个值
			List<CoreMap> sentences = annotation.get(CoreAnnotations.SentencesAnnotation.class);
			CoreMap sentence = sentences.get(0);
	
			// 从CoreMap中取出CoreLabel List，逐一打印出来
			List<CoreLabel> tokens = sentence.get(CoreAnnotations.TokensAnnotation.class);
			System.out.println("字/词" + "\t " + "词性" + "\t " + "实体标记");
			System.out.println("-----------------------------");
			for (CoreLabel token : tokens) {
				String word = token.getString(TextAnnotation.class);
				String pos = token.getString(PartOfSpeechAnnotation.class);
				String ner = token.getString(NamedEntityTagAnnotation.class);
				System.out.println(word + "\t " + pos + "\t " + ner);
			}
	
		}
	
	}


```

## 运行结果

```

	字/词	 词性	 实体标记
	-----------------------------
	我爱	 VV	 O
	北京	 NR	 MISC
	天安门	 NR	 MISC
	，	 PU	 O
	天安门	 NR	 O
	上	 LC	 O
	太阳	 NN	 O
	升	 VV	 O
	。	 PU	 O


```

至此，本分词实例已完成。