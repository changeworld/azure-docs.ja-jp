<properties
	pageTitle="カスタム アナライザー (Azure Search REST API バージョン 2015-02-28-Preview) | Microsoft Azure"
	description="カスタム アナライザー (Azure Search REST API バージョン 2015-02-28-Preview)"
	services="search"
	documentationCenter=""
	authors="Yahnoosh"
	manager="pablocas"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.author="jlembicz"
	ms.date="02/18/2016" />

# アナライザー (Azure Search REST API バージョン 2015-02-28-Preview)

> [AZURE.NOTE] カスタム アナライザーの機能は現在プレビュー段階です。この機能を利用するためには、Azure Search サービス REST API の 2015-02-28-Preview バージョンを使用している必要があります。プレビュー機能はまだ .NET SDK に追加されていません。プレビュー版 REST API の使用は、現時点では任意であり、プログラミング上、必要に応じてご使用ください。

## 概要

フルテキスト検索エンジンの役割は、簡単に言えば、情報を効率よく照会して取得できるようにドキュメントを加工して保存することです。重要な単語をドキュメントから抽出してインデックスに追加し、そのインデックスを使って、特定のクエリの単語と一致するドキュメントを検索する、というのが大まかな流れです。ドキュメントや検索クエリから単語を抽出するプロセスを "字句解析" といいます。字句解析を実行するコンポーネントは、"アナライザー" と呼ばれます。Azure Search では、[事前定義された言語非依存のアナライザー](#Analyzers)と[言語固有のアナライザー](https://msdn.microsoft.com/library/azure/dn879793.aspx)が複数用意されており、その中から必要なアナライザーを選ぶことができます。カスタム アナライザーを独自に定義することもできます。カスタム アナライザーを使用すると、テキストをインデックス可能なトークンまたは検索可能なトークンに変換するプロセスを制御できます。カスタム アナライザーは、あらかじめ定義された単一のトークナイザと 1 つまたは複数のトークン フィルターとから成るユーザー定義の構成です。トークナイザの役割は、テキストをトークンに分割することです。一方、トークナイザによって生成されたトークンを加工するのは、トークン フィルターの役割となります。

カスタム アナライザーが利用される一般的なシナリオの例を次に示します。

- 音声検索 - 発音フィルターを追加することによって、単語の綴りではなく発音に基づく検索に対応します。
- 字句解析の無効化 - 解析の対象外とする検索可能なフィールドをキーワード アナライザーで作成します。
- 高速なプレフィックス/サフィックス検索 - Edge N-gram トークン フィルターを追加し、単語のプレフィックスをインデックス化することによって、高速なプレフィックス マッチングに対応します。リバース トークン フィルターを組み合わせることで、サフィックス マッチングを行うことができます。
- カスタム トークン化 - たとえば空白文字を区切りとして文をトークンに分割するには、Whitespace トークナイザを使用します。
- ASCII フォールディング - 検索語句中の分音記号 (ö、ê など) を正規化するには、ASCII フォールディング フィルターを追加します。

複数のカスタム アナライザーを定義してフィルターの組み合わせを変えることはできますが、インデックス作成時の解析と検索時の解析に使用できるアナライザーは、各フィールドにつきそれぞれ 1 つだけです。
 
このページでは、サポートされているアナライザーとトークナイザ、トークン フィルターの一覧を掲載しています。また、インデックスの定義に対する変更の方法を使用例と共に紹介しています。シナリオの概要と応用については、「[Custom Analyzers in Azure Search (Azure Search のカスタム アナライザー)](link)」を参照してください。Azure Search の実装に利用されている基礎技術の背景については、[解析パッケージの概要 (Lucene)](http://lucene.apache.org/core/4_10_3/core/org/apache/lucene/analysis/package-summary.html) を参照してください。


## 既定のアナライザー

既定のアナライザーは [Apache Lucene Standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html) アナライザーです。

## 検証規則

カスタム アナライザー、トークナイザ、トークン フィルターの名前は一意であることが必要です。定義済みのアナライザー、トークナイザ、トークン フィルターと重複しないようにしてください。
 

## カスタム アナライザーを含むインデックスの定義 

カスタム アナライザーは、インデックスの作成時に定義します。インデックスの定義におけるカスタム解析コンポーネントの構文は次のとおりです。完全な例については、[こちら](#Example)をご覧ください。

	{
	  	. . .
	 	(standard create index request body)
	 	. . .  
		"analyzers": (optional) [ 
		    {  
		       "name": "name of analyzer",  
		       "@odata.type": "#Microsoft.Azure.Search.CustomAnalyzer", 
		       "tokenizer": "tokenizer_name", (tokenizer_name is the name of a tokenizer from the [Tokenizers](#Tokenizers) table)
		       "tokenFilters": [
				  "token_filter_name", (token_filter_name is the name of a token filter from the [Token Filters](#TokenFilters) table)
				  "token_filter_name"
				] (token filters will be applied from left to right)
		    },
			{  
		       "name": "name of analyzer",  
		       "@odata.type": "#analyzer_type", 
		       "option1": value1,
		       "option2": value2,
		       ...  
		    }
		],  
		"tokenizers": (optional) [ 
		    {  
		       "name": "tokenizer_name",  
		       "@odata.type": "#tokenizer_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		],
		"tokenFilters": (optional) [ 
		    {  
		       "name": "token_filter_name",  
		       "@odata.type": "#token_filter_type", 
		       "option1": value1,  
		       "option2": value2, 
		       ...  
		    }
		]	
	}

> [AZURE.NOTE] 独自に作成したカスタム アナライザーは Azure ポータルに公開されません。カスタム アナライザーの追加は、インデックスを定義する際に REST API を呼び出すコードを介して行う必要があります。これが唯一の方法となります。

##インデックスのカスタム アナライザー、トークナイザ、トークン フィルターに使用される属性 

ここでは、インデックス定義のアナライザー、トークナイザ、トークン フィルターの各セクションで使用される構成プロパティについて説明します。

###アナライザー

<table>
  <tbody>
    <tr>
      <td>
        <b>名前</b>
      </td>
      <td>
		使用できるのは、文字、数字、空白、ダッシュ、アンダースコアのみです。先頭と末尾は英数字にする必要があります。文字数の上限は 128 文字となります。
      </td>
    </tr>
    <tr>
      <td>
        <b>型</b>
      </td>
      <td>
		有効な値は "#Microsoft.Azure.Search.CustomAnalyzer" またはサポートされているいずれかのアナライザーの名前です。「[アナライザー](#Analyzers)」に記載した表の <b>analyzer_type</b> 列を参照してください。
      </td>
	</tr>
    <tr>
      <td>
        <b>Tokenizer</b> (#Microsoft.Azure.Search.CustomAnalyzer でのみ有効)
      </td>
      <td>
		必須。「[トークナイザ](#Tokenizers)」の表に記載した事前に定義されているいずれかのトークナイザを指定するか、インデックス定義内に記述したいずれかのカスタム トークナイザを指定する必要があります。
      </td>
	</tr>
    <tr>
      <td>
        <b>TokenFilters</b> (#Microsoft.Azure.Search.CustomAnalyzer でのみ有効)
      </td>
      <td>
		すべてのトークン フィルターは、「[トークン フィルター](#TokenFilters)」の表に記載した事前に定義されているいずれかのトークン フィルターか、インデックス定義内に記述したいずれかのカスタム トークン フィルターに該当する必要があります。
      </td>
	</tr>
    <tr>
      <td>
        <b>オプション</b>
      </td>
      <td>
		事前に定義されている (非カスタムの) アナライザーの [有効なオプション](#Analyzers) を指定する必要があります。
      </td>
	</tr>
  </tbody>
</table>

###トークナイザ

トークナイザは、文を単語に分割するなど、連続するテキストを一連のトークンに分割します。カスタム アナライザーにはそれぞれトークナイザを 1 つだけ指定できます。複数のトークナイザが必要である場合は、複数のカスタム アナライザーを作成し、インデックス スキーマの中で、それらをフィールド単位で割り当ててください。カスタム アナライザーは、事前に定義されているトークナイザを使用できます。トークナイザのオプションは、既定値のまま使用することもカスタマイズすることもできます。

<table>
  <tbody>
    <tr>
      <td>
        <b>名前</b>
      </td>
      <td>
		使用できるのは、文字、数字、空白、ダッシュ、アンダースコアのみです。先頭と末尾は英数字にする必要があります。文字数の上限は 128 文字となります。
      </td>
    </tr>
    <tr>
      <td>
        <b>型</b>
      </td>
      <td>
		トークナイザの名前 (サポートされているトークナイザの一覧に基づく名前)。「[トークナイザ](#Tokenizers)」に記載した表の <b>tokenizer_type</b> 列を参照してください。
      </td>
	</tr>
     <tr>
      <td>
        <b>オプション</b>
      </td>
      <td>
		特定のトークナイザ タイプで [有効なオプション](#Tokenizers) を指定する必要があります。
      </td>
	</tr>
  </tbody>
</table>

###トークン フィルター

トークン フィルターは、トークナイザによって生成されたトークンを除外したり加工したりする目的で使用されます。たとえば、すべての文字を小文字に変換する lowercase フィルターを指定することができます。トークン フィルターは、カスタム アナライザーの中で複数割り当てることができます。トークン フィルターは、その指定順に実行されます。

<table>
  <tbody>
    <tr>
      <td>
        <b>名前</b>
      </td>
      <td>
		使用できるのは、文字、数字、空白、ダッシュ、アンダースコアのみです。先頭と末尾は英数字にする必要があります。文字数の上限は 128 文字となります。
      </td>
    </tr>
    <tr>
      <td>
        <b>型</b>
      </td>
      <td>
		トークン フィルターの名前 (サポートされているトークン フィルターの一覧に基づく名前)。「[トークン フィルター](#TokenFilters)」に記載した表の <b>token_filter_type</b> 列を参照してください。
      </td>
	</tr>
     <tr>
      <td>
        <b>オプション</b>
      </td>
      <td>
		特定のトークン フィルター タイプで [有効なオプション](#TokenFilters) を指定する必要があります。
      </td>
	</tr>
  </tbody>
</table>

<a name="Example"></a>
##インデックス定義の例

この例のインデックス定義には、カスタム アナライザー "my\_analyzer" を使用したフィールドが 1 つ含まれており、この my\_analyzer には、カスタマイズされた標準トークナイザ "my\_standard\_tokenizer" と 2 つのトークン フィルター (lowercase およびカスタム ASCII フォールディング フィルター "my\_asciifolding") が使用されています。

	{
	   "name":"myindex",
	   "fields":[
	      {
	         "name":"id",
	         "type":"Edm.String",
	         "key":true,
	         "searchable":false
	      },
	      {
	         "name":"text",
	         "type":"Edm.String",
	         "searchable":true,
	         "analyzer":"my_analyzer"
	      }
	   ],
	   "analyzers":[
	      {
	         "name":"my_analyzer",
	         "@odata.type":"#Microsoft.Azure.Search.CustomAnalyzer",
	         "tokenizer":"my_standard_tokenizer",
	         "tokenFilters":[
	            "my_asciifolding",
	            "lowercase"
	         ]
	      }
	   ],
	   "tokenizers":[
	      {
	         "name":"my_standard_tokenizer",
	         "@odata.type":"#Microsoft.Azure.Search.StandardTokenizer",
	         "maxTokenLength":20
	      }
	   ],
	   "tokenFilters":[
	      {
	         "name":"my_asciifolding",
	         "@odata.type":"#Microsoft.Azure.Search.AsciiFoldingTokenFilter",
	         "preserveOriginal":true
	      }
	   ]
	}

<a name="Analyzers"></a>
##アナライザー

<table>
  <tbody>
	<thead>
	<tr>
		<td>analyzer_name</td>
		<td>analyzer_type</td>
		<td>説明</td>
		<td>オプション</td>
	</tr>
	</thead>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordAnalyzer</td>
	  <td>フィールドの内容全体を 1 つのトークンとして扱います。郵便番号や ID、製品名といったデータに利用できます。</td>
	  <td></td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/PatternAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.PatternAnalyzer</td>
	  <td>正規表現パターンを使用した柔軟な規則に基づいてテキストを語句に分割します。</td>
	  <td>
		- lowercase - 型: bool - 語句を小文字にします (既定値: true)。- [pattern](http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html?is-external=true) - 型: string - トークンの区切り記号と一致する正規表現パターン (既定値: \w+)。 - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 型: string - 正規表現フラグ (既定値: 空の文字列)。 - stopwords - 型: 文字列配列 - ストップワードのリスト (既定値: 空のリスト)。
	  </td>
    </tr>
    <tr>
      <td>[simple](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/SimpleAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SimpleAnalyzer</td>	 
	  <td>文字以外の位置でテキストを分割し、それらを小文字に変換します。</td>
	  <td></td>
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.SnowballAnalyzer</td>
	  <td>[Snowball のステミング フィルター](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html) を備える標準アナライザー。</td>
	  <td>
		- language - 型: string - 指定できる値: danish、dutch、english、finnish、french、german、hungarian、italian、norwegian、portuguese、russian、spanish、swedish。 - stopwords - 型: 文字列配列 - ストップワードのリスト (既定値: 空のリスト)。
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StandardAnalyzer</td>
	  <td>標準 Lucene アナライザー。標準的なトークナイザ、小文字フィルター、ストップ フィルターから成ります。</td>
	  <td>
		- maxTokenLength - 型: int - トークンの最大長 (既定値: 255)。最大長を超えるトークンは分割されます。- stopwords - 型: 文字列配列 - ストップワードのリスト (既定値: 空のリスト)。
	  </td>
    </tr>
    <tr>
      <td>standardasciifolding.lucene</td>
      <td>#Microsoft.Azure.Search.StandardAsciiFoldingAnalyzer</td>	  
	  <td>ASCII フォールディング フィルターを備えた標準アナライザー。</td>
	  <td></td>
    </tr>
    <tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.StopAnalyzer</td>
	  <td>文字以外の位置でテキストを分割し、小文字フィルターとストップワード トークン フィルターを適用します。</td>
	  <td>
		- stopwords - 型: 文字列配列 - ストップワードのリスト (既定値: 空のリスト)。
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceAnalyzer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceAnalyzer</td>
	  <td>Whitespace トークナイザを使用するアナライザー。</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="Tokenizers"></a>
##トークナイザ

<table>
  <tbody>
	<thead>
	<tr>
		<td>tokenizer_name</td>
		<td>tokenizer_type</td>
		<td>説明</td>
		<td>オプション</td>
	</tr>
	</thead>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenizer</td>
	  <td>文法ベースのトークナイザ。ほとんどの欧州言語のドキュメント処理に利用できます。</td>
	  <td>
		- maxTokenLength - 型: int - トークンの最大長 (既定値: 255)。最大長を超えるトークンは分割されます。
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenizer</td>
	  <td>エッジからの入力を特定のサイズの n-gram にトークン化します。</td>
	  <td>
		- minGram - 型: int - 既定値: 1。 - maxGram - 型: int - 既定値: 2。 - tokenChars - 型: 文字列配列 - トークンとして維持する文字クラス (指定できる値: letter、digit、whitespace、punctuation、symbol)。
	  </td>	  
    </tr>
    <tr>
      <td>[keyword](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/KeywordTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.KeywordTokenizer</td>
	  <td>入力全体を 1 つのトークンとして出力します。</td>
	  <td>
		- bufferSize - 型: int - バッファー サイズを読み取ります (既定: 256)。
	  </td>
    </tr>
    <tr>
      <td>[letter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LetterTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LetterTokenizer</td>
	  <td>文字以外の位置でテキストを分割します。</td>
	  <td></td>
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenizer</td>
	  <td>文字以外の位置でテキストを分割し、それらを小文字に変換します。</td>
	  <td></td>
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenizer</td>
	  <td>入力を特定のサイズの n-gram にトークン化します。</td>
	  <td>
		- minGram - 型: int - 既定値: 1。 - maxGram - 型: int - 既定値: 2。 - tokenChars - 型: 文字列配列 - トークンとして維持する文字クラス (指定できる値: letter、digit、whitespace、punctuation、symbol)。
	  </td>
    </tr>
    <tr>
      <td>[path_hierarchy](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/path/PathHierarchyTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PathHierarchyTokenizer</td>
	  <td>パスに似た階層のトークナイザ</td>
	  <td>
		- delimiter - 型: string - 既定値: '/'。 - replacement - 型: string - 設定した場合、区切り文字を置換します (既定値: delimiter)。 - bufferSize - 型: int - 既定値: 1024。 - reverse - 型: bool - true の場合、トークンを逆順に生成します (既定値: true)。 - skip - 型: bool - スキップする初期トークン (既定値: 0)。
	  </td>
    </tr>
    <tr>
      <td>[pattern](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.PatternTokenizer</td>
	  <td>このトークナイザは、正規表現パターンのマッチングを使用して特定のトークンを構築します。</td>
	  <td>
		 - pattern - 型: string - 正規表現パターン (既定値: \w+)。 - [flags](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html#field_summary) - 型: string - 正規表現フラグ (既定値: 空の文字列)。 - group - 型: int - トークンに抽出するグループ (既定値: -1 (分割))。
	  </td>
    </tr>
    <tr>
      <td>[standard](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/StandardTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.StandardTokenizer</td>
	  <td>[Unicode テキスト セグメンテーション規則](http://unicode.org/reports/tr29/) に従ってテキストを分割します。</td>
	  <td>
		 - maxTokenLength - 型: int - トークンの最大長 (既定値: 255)。最大長を超えるトークンは分割されます。
	  </td>
    </tr>
    <tr>
      <td>[uax_url_email](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/UAX29URLEmailTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.UaxEmailUrlTokenizer</td>
	  <td>URL と電子メールを 1 つのトークンとしてトークン化します。</td>
	  <td>
		- maxTokenLength - 型: int - トークンの最大長 (既定値: 255)。最大長を超えるトークンは分割されます。
	  </td>
    </tr>
    <tr>
      <td>[whitespace](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/WhitespaceTokenizer.html)</td>
      <td>#Microsoft.Azure.Search.WhitespaceTokenizer</td>
	  <td>テキストを空白文字で分割します。</td>
	  <td></td>
    </tr>
  </tbody>
</table>

<a name="TokenFilters"></a>
##トークン フィルター

<table>
  <tbody>
	<thead>
	<tr>
		<td>token_filter_name</td>
		<td>token_filter_type</td>
		<td>説明</td>
		<td>オプション</td>
	</tr>
	</thead>
    <tr>
      <td>[arabic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ar/ArabicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ArabicNormalizationTokenFilter</td>
	  <td>アポストロフィから後のすべての文字を除去します (アポストロフィ自体を含みます)</td>
    </tr>
    <tr>
      <td>[apostrophe](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/tr/ApostropheFilter.html)</td>
      <td>#Microsoft.Azure.Search.ApostropheTokenFilter</td>
	  <td>アポストロフィから後のすべての文字を除去します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[asciifolding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ASCIIFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.AsciiFoldingTokenFilter</td>
	  <td>アルファベット、数字、記号の Unicode 文字が、ASCII の 0 ～ 127 の範囲 ("Basic Latin" Unicode ブロック) にない場合、対応する ASCII 文字があれば、その文字に変換します。</td>
	  <td>
		- preserveOriginal - 型: bool - true の場合、元のトークンが維持されます (既定値: false)。
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_bigram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKBigramFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkBigramTokenFilter</td>
	  <td>StandardTokenizer から生成された CJK の語句を整形してバイグラムを作成します。</td>
	  <td>
		- ignoreScripts - 型: bool - 無視するスクリプト (指定できる値: han、hiragana、katakana、hangul) (既定値: 空のリスト)。- outputUnigrams 型: bool -- 常にユニグラムとバイグラムの両方を出力する場合は true に設定します (既定値: false)。
	  </td>	  
    </tr>
    <tr>
      <td>[cjk_width](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/cjk/CJKWidthFilter.html)</td>
      <td>#Microsoft.Azure.Search.CjkWidthTokenFilter</td>
	  <td>CJK の全角半角の違いを正規化します。全角 ASCII 文字を対応する Basic Latin に変換し、半角カタカナ文字を対応するひらがな文字に変換します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[classic](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/standard/ClassicFilter.html)</td>
      <td>#Microsoft.Azure.Search.ClassicTokenFilter</td>
	  <td>英語の所有格を除去し、頭字語からはドットを除去します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[common_grams](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/commongrams/CommonGramsFilter.html)</td>
      <td>#Microsoft.Azure.Search.CommonGramTokenFilter</td>
	  <td>インデックス作成の過程で頻出する語句についてバイグラムを構築します。単語も、バイグラムとは別にインデックスの対象となります。</td>
	  <td>
		- commonWords - 型: 文字列配列 - 一連の一般語 (既定値: 空のリスト)。 - ignoreCase - 型: bool - true にした場合、一般語の大文字と小文字が区別されません (既定値: false)。 - queryMode - 型: bool - バイグラムを生成したうえで、一般語と一般語に先行する単語を削除します (既定値: false)。
	  </td>	  
    </tr>
    <tr>
      <td>[delimited_payload_filter](https://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/payloads/DelimitedPayloadTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DelimitedPayloadTokenFilter</td>
	  <td>区切り記号の前の文字が "トークン" で、区切り記号の後の文字がペイロードとなります。たとえば区切り記号が "|" であるとき、"hello|world" という文字列の "hello" はトークン、"world" はペイロードと見なされます。ペイロードを適切な方法で (文字からバイトに) 変換するエンコーダーを含めることもできます。</td>
	  <td>
		- delimiter - 型: char - 既定値: '|' - encoding - 型: string - 指定できる値: int、float、identity (既定値: float)。
	  </td>	  
    </tr>
    <tr>
      <td>[dictionary_decompounder](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/compound/DictionaryCompoundWordTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.DictionaryDecompounderTokenFilter</td>
	  <td>多くのゲルマン語で見られる複合語を分解します。</td>
	  <td>
		- wordList - 型: 文字列配列 - マッチングする単語のリスト (既定値: 空のリスト)。 - minWordSize - 型: int - この値よりも長い単語だけが処理の対象となります (既定値: 5)。 - minSubwordSize - 型: int - この値より長いサブワードだけが出力されます (既定値: 2)。 - maxSubwordSize - 型: int - この値より短いサブワードだけが出力されます (既定値: 15)。 - onlyLongestMatch - 型: bool - 最長の一致するサブワードのみを出力に追加します (既定値: false)。 
	  </td>	  
    </tr>
    <tr>
      <td>[edgeNGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/EdgeNGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.EdgeNGramTokenFilter</td>
	  <td>入力トークンの前方または後方を起点として指定されたサイズの n-gram を生成します。</td>
	  <td>
		- minGram - 型: int - 既定値 : 1。 - maxGram - 型: int - 既定値: 2。 - side - 型: string - 入力のどちら側から n-gram を生成するかを指定します (指定できる値: front、back)。
	  </td>	  
    </tr>   
    <tr>
      <td>[elision](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/util/ElisionFilter.html)</td>
      <td>#Microsoft.Azure.Search.ElisionTokenFilter</td>
	  <td>省略記号を削除します。たとえば、"l'avion" (the plane) は "avion" (plane) に変換されます。</td>
	  <td>
		- articles - 型: 文字列配列 - 削除する一連の冠詞 (既定値: 空のリスト)。
	  </td>	  
    </tr>
    <tr>
      <td>[german_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/de/GermanNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.GermanNormalizationTokenFilter</td>
	  <td>[German2 snowball algorithm](http://snowball.tartarus.org/algorithms/german2/stemmer.html) のヒューリスティックに従ってドイツ語の文字を正規化します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[hindi_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/hi/HindiNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.HindiNormalizationTokenFilter</td>
	  <td>ヒンディー語のテキストを正規化し、スペルのバリエーションの差異を取り除きます。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[indic_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/in/IndicNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.IndicNormalizationTokenFilter</td>
	  <td>インドの言語でのテキストの Unicode 表現を正規化します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[keep](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeepWordFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTokenFilter</td>
	  <td>指定された一連の単語に該当するテキストを含んだトークンのみを保持するトークン フィルター。</td>
	  <td>
		- keepWords - 型: 文字列配列 - 維持する単語のリスト (既定値: 空のリスト)。 - keepWordsCase - 型: bool - true の場合、最初にすべての単語を小文字にします (既定値: false)。
	  </td>	  
    </tr>
    <tr>
      <td>[keep_types](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/TypeTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeepTypesTokenFilter</td>
	  <td>許可の対象として指定された一連の型に該当するトークンを維持します。</td>
	  <td>
		- types - 型: 文字列配列 - 維持する型のリスト。
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_marker](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordMarkerFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordMarkerTokenFilter</td>
	  <td>語句をキーワードとしてマークします。</td>
	  <td>
		- keywords - 型: 文字列配列 - キーワードとしてマークする単語のリスト (既定値: 空のリスト)。 - ignoreCase - 型: bool - true の場合、最初にすべての単語を小文字にします (既定値: false)。
	  </td>	  
    </tr>
    <tr>
      <td>[keyword_repeat](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/KeywordRepeatFilter.html)</td>
      <td>#Microsoft.Azure.Search.KeywordRepeatTokenFilter</td>
	  <td>それぞれの入力トークンを 2 回出力します。1 回はキーワードとして、もう 1 回は非キーワードとして出力します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[kstem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/en/KStemFilter.html)</td>
      <td>#Microsoft.Azure.Search.KStemTokenFilter</td>
	  <td>英語用のハイパフォーマンス kstem フィルター。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[length](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LengthFilter.html)</td>
      <td>#Microsoft.Azure.Search.LengthTokenFilter</td>
	  <td>長すぎる単語や短すぎる単語を除去します。</td>
	  <td>
		- min - 型: int - 最小数 (既定値: 0)。 - max - 型: int - 最大数 (既定値: 整数の最大値)。
	  </td>	  
    </tr>
    <tr>
      <td>[limit](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/LimitTokenCountFilter.html)</td>
      <td>#Microsoft.Azure.Search.LimitTokenFilter</td>
	  <td>インデックス作成時におけるトークンの数を制限します。</td>
	  <td>
		- maxTokenCount - 型: int - 生成するトークンの最大数 (既定値: 1)。 - consumeAllTokens - 型: bool - maxTokenCount に達しても、入力されたすべてのトークンを取り込むかどうか (既定値 : false)。
	  </td>	  
    </tr>
    <tr>
      <td>[lowercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/LowerCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.LowercaseTokenFilter</td>
	  <td>トークンのテキストを小文字に正規化します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[nGram](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ngram/NGramTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.NGramTokenFilter</td>
	  <td>指定されたサイズの n-gram を生成します。</td>
	  <td>
		- minGram - 型: int - 既定値: 1。 - maxGram - 型: int - 既定値: 2。
	  </td>	  
    </tr>
    <tr>
      <td>[pattern_capture](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/pattern/PatternCaptureGroupTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.PatternCaptureTokenFilter</td>
	  <td>Java の正規表現を使用して複数 (パターン内のキャプチャ グループごとに 1 つ) のトークンを出力します。</td>
	  <td>
		- patterns - 型: 文字列配列 - 各トークンと照合するパターンのリスト。 - preserveOriginal - 型: bool - true に設定すると、いずれかのパターンが一致したとしても、元のトークンが返されます。
	  </td>	  
    </tr>
    <tr>
      <td>[persian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/fa/PersianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.PersianNormalizationTokenFilter</td>
	  <td>ペルシャ語の正規化を適用します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[phonetic]https://lucene.apache.org/core/4_10_3/analyzers-phonetic/org/apache/lucene/analysis/phonetic/package-tree.html)</td>
      <td>#Microsoft.Azure.Search.PhoneticTokenFilter</td>
	  <td>音声検索用のトークンを作成します。</td>
	  <td>
		- encoder - 型: string - 使用する Phonetic エンコーダー (指定できる値: metaphone、doublemetaphone、soundex、refinedsoundex、caverphone1、caverphone2、cologne、nysiis、koelnerphonetik、haasephonetik、beidermorse)。既定値: metaphone。 - replace - 型: bool - エンコードしたトークンで元のトークンを置き換える場合は true、同義語として追加する場合は false (既定値: true)。
	  </td>	  
    </tr>
    <tr>
      <td>[porter_stem](http://lucene.apache.org/core/4_10_3/analyzers-common/org/tartarus/snowball/ext/PorterStemmer.html)</td>
      <td>#Microsoft.Azure.Search.PorterStemTokenFilter</td>
	  <td>[Porter ステミング アルゴリズム](http://tartarus.org/~martin/PorterStemmer/) に従ってトークン ストリームを変換します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[reverse](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/reverse/ReverseStringFilter.html)</td>
      <td>#Microsoft.Azure.Search.ReverseTokenFilter</td>
	  <td>トークンの文字列を反転させます。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianNormalizationTokenFilter</td>
	  <td>代替可能なスカンジナビア文字の使用を正規化します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[scandinavian_folding](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/ScandinavianFoldingFilter.html)</td>
      <td>#Microsoft.Azure.Search.ScandinavianFoldingNormalizationTokenFilter</td>
	  <td>スカンジナビア文字 åÅäæÄÆ を a に、öÖøØ を o に変換します。二重母音 (aa、ae、ao、oe、oo) の使用を認識し、最初の文字のみを残します。</td>
	  <td></td>	  
    </tr>
    <tr>
      <td>[shingle](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/shingle/ShingleFilter.html)</td>
      <td>#Microsoft.Azure.Search.ShingleTokenFilter</td>
	  <td>トークンの組み合わせを単一のトークンとして作成します。</td>
	  <td>
		- maxShingleSize - 型: int - 既定値: 2。- minShingleSize - 型: int - 既定値: 2。 - outputUnigrams - 型: bool - true の場合、出力ストリームには入力トークン (ユニグラム) と shingle が追加されます (既定値: true)。 - outputUnigramsIfNoShingles - 型: bool - true の場合、shingle が利用できない場合の outputUnigrams==false の動作を上書きします (既定値: false)。 - tokenSeparator - 型: string - 隣接するトークンを結合して shingle を形成するときに使用する文字列 (既定値: " ")。 - filterToken - 型: string - トークンが存在しない各位置に挿入する文字列 (既定値: "_")。
	  </td>	  
    </tr>
    <tr>
      <td>[snowball](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/snowball/SnowballFilter.html)</td>
      <td>#Microsoft.Azure.Search.SnowballTokenFilter</td>
	  <td>Snowaball ステミング トークン フィルター。</td>
	  <td>
		- language - 型: string - 指定できる値: Armenian、Basque、Catalan、Danish、Dutch、English、Finnish、French、German、German2、Hungarian、Italian、Kp、Lovins、Norwegian、Porter、Portuguese、Romanian、Russian、Spanish、Swedish、Turkish。
	  </td>	  
    </tr>
	<tr>
      <td>[sorani_normalization](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/ckb/SoraniNormalizationFilter.html)</td>
      <td>#Microsoft.Azure.Search.SoraniNormalizationTokenFilter</td>
	  <td>ソラニ語のテキストの Unicode 表現を正規化します。</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>stemmer</td>
      <td>#Microsoft.Azure.Search.StemmerTokenFilter</td>
	  <td>言語固有のステミング フィルター。</td>
	  <td>
		- language - 型: string - 指定できる値: - [arabic](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ar/ArabicStemmer.html) - [armenian](http://snowball.tartarus.org/algorithms/armenian/stemmer.html) - [basque](http://snowball.tartarus.org/algorithms/basque/stemmer.html) - [brazilian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/br/BrazilianStemmer.html) - [bulgarian](http://members.unine.ch/jacques.savoy/Papers/BUIR.pdf) - [catalan](http://snowball.tartarus.org/algorithms/catalan/stemmer.html) - [czech](http://portal.acm.org/citation.cfm?id=1598600) - [danish](http://snowball.tartarus.org/algorithms/danish/stemmer.html) - [dutch](http://snowball.tartarus.org/algorithms/dutch/stemmer.html) - [dutch_kp](http://snowball.tartarus.org/algorithms/kraaij_pohlmann/stemmer.html) - [english](http://snowball.tartarus.org/algorithms/porter/stemmer.html) - [light_english](http://ciir.cs.umass.edu/pubfiles/ir-35.pdf) - [minimal_english](http://www.researchgate.net/publication/220433848_How_effective_is_suffixing) - [possessive_english](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/en/EnglishPossessiveFilter.html) - [porter2](http://snowball.tartarus.org/algorithms/english/stemmer.html) - [lovins](http://snowball.tartarus.org/algorithms/lovins/stemmer.html) - [finnish](http://snowball.tartarus.org/algorithms/finnish/stemmer.html) - [light_finnish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [french](http://snowball.tartarus.org/algorithms/french/stemmer.html) - [light_french](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_french](http://dl.acm.org/citation.cfm?id=318984) - [galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [minimal_galician](http://bvg.udc.es/recursos_lingua/stemming.jsp) - [german](http://snowball.tartarus.org/algorithms/german/stemmer.html) - [german2](http://snowball.tartarus.org/algorithms/german2/stemmer.html) - [light_german](http://dl.acm.org/citation.cfm?id=1141523) - [minimal_german](http://members.unine.ch/jacques.savoy/clef/morpho.pdf) - [greek](http://sais.se/mthprize/2007/ntais2007.pdf) - [hindi](http://computing.open.ac.uk/Sites/EACLSouthAsia/Papers/p6-Ramanathan.pdf) - [hungarian](http://snowball.tartarus.org/algorithms/hungarian/stemmer.html) - [light_hungarian](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [indonesian](http://www.illc.uva.nl/Publications/ResearchReports/MoL-2003-02.text.pdf) - [irish](http://snowball.tartarus.org/otherapps/oregan/intro.html) - [italian](http://snowball.tartarus.org/algorithms/italian/stemmer.html) - [light_italian](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [sorani](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/ckb/SoraniStemmer.html) - [latvian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/lv/LatvianStemmer.html) - [norwegian](http://snowball.tartarus.org/algorithms/norwegian/stemmer.html) - [light_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_norwegian](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [light_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianLightStemmer.html) - [minimal_nynorsk](http://lucene.apache.org/core/4_9_0/analyzers-common/org/apache/lucene/analysis/no/NorwegianMinimalStemmer.html) - [portuguese](http://snowball.tartarus.org/algorithms/portuguese/stemmer.html) - [light_portuguese](http://dl.acm.org/citation.cfm?id=1141523&amp;dl=ACM&amp;coll=DL&amp;CFID=179095584&amp;CFTOKEN=80067181) - [minimal_portuguese](http://www.inf.ufrgs.br/~buriol/papers/Orengo_CLEF07.pdf) - [portuguese_rslp](http://www.inf.ufrgs.br//~viviane/rslp/index.htm) - [romanian](http://snowball.tartarus.org/algorithms/romanian/stemmer.html) - [russian](http://snowball.tartarus.org/algorithms/russian/stemmer.html) - [light_russian](http://doc.rero.ch/lm.php?url=1000%2C43%2C4%2C20091209094227-CA%2FDolamic_Ljiljana_-_Indexing_and_Searching_Strategies_for_the_Russian_20091209.pdf) - [spanish](http://snowball.tartarus.org/algorithms/spanish/stemmer.html) - [light_spanish](http://www.ercim.eu/publication/ws-proceedings/CLEF2/savoy.pdf) - [swedish](http://snowball.tartarus.org/algorithms/swedish/stemmer.html) - [light_swedish](http://clef.isti.cnr.it/2003/WN_web/22.pdf) - [turkish](http://snowball.tartarus.org/algorithms/turkish/stemmer.html)
	  </td>	  
    </tr>
	<tr>
      <td>[stemmer_override](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/StemmerOverrideFilter.html)</td>
      <td>#Microsoft.Azure.Search.StemmerOverrideTokenFilter</td>
	  <td>辞書ベースでステミングされる語句が、下流のステマーによって語幹抽出されないよう、キーワードとしてマークされます。すべてのステミング フィルターの前に配置する必要があります。</td>
	  <td>
		- rules - 型: 文字列配列 - "word => stem" (例: "ran => run") 形式のステミング規則 (既定値: 空のリスト)。
	  </td>	  
    </tr>
	<tr>
      <td>[stop](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/StopFilter.html)</td>
      <td>#Microsoft.Azure.Search.StopTokenFilter</td>
	  <td>トークン ストリームからストップワードを削除します。</td>
	  <td>
		- stopwords - 型: 文字列配列 - ストップワードのリスト (既定値: 空のリスト)。 - stopwords_list - 型: string - 事前に定義されているストップワードのリスト。指定できる値は _arabic_、_armenian_、_basque_、_brazilian_、_bulgarian_、_catalan_、_czech_、_danish_、_dutch_、_english_、_finnish_、_french_、_galician_、_german_、_greek_、_hindi_、_hungarian_、_indonesian_、_irish_、_italian_、_latvian_、_norwegian_、_persian_、_portuguese_、_romanian_、_russian_、_sorani_、_spanish_、_swedish_、_thai_、_turkish_ です (既定値: _english_)。 - ignoreCase - 型: bool - true にした場合、すべての単語が最初に小文字に変換されます (既定値: false)。 - removeTrailing - 型: bool - true にすると、最後の検索語がストップワードである場合、その検索語が無視されます (既定値: true)。
	  </td>	  
    </tr>
	<tr>
      <td>[synonym](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/synonym/SynonymFilter.html)</td>
      <td>#Microsoft.Azure.Search.SynonymTokenFilter</td>
	  <td>トークン ストリームから 1 つまたは複数の単語から成る同義語を検索します。</td>
	  <td>
		- synonyms - 型: 文字列配列 - 同義語のリスト。「incredible, unbelievable, fabulous => amazing」(=> 記号の左側にあるすべての語句が右側の語句に置き換えられる) 形式と「incredible, unbelievable, fabulous, amazing」(等価と見なす単語のコンマ区切りリスト) 形式という 2 とおりの指定形式があります。このリストの解釈を変更するには、expand オプションを設定します。- ignoreCase - 型: bool - 照合する入力文字列の大文字と小文字を区別しません (既定値: false)。 - expand - 型: bool - - true の場合、同義語リスト (=> 形式の表記を使用しない場合) に含まれるすべての単語が相互にマッピングされます。「incredible, unbelievable, fabulous, amazing」は「incredible, unbelievable, fabulous, amazing => incredible, unbelievable, fabulous, amazing」と等価と見なされます。 - false の場合、「incredible, unbelievable, fabulous, amazing」は「incredible, unbelievable, fabulous, amazing => incredible」と等価と見なされます。   
	  </td>	  
    </tr>
	<tr>
      <td>[trim](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TrimFilter.html)</td>
      <td>#Microsoft.Azure.Search.TrimTokenFilter</td>
	  <td>トークンの先頭と末尾の空白文字をトリミングします。</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[truncate](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/TruncateTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.TruncateTokenFilter</td>
	  <td>語句を特定の長さに切り詰めます。</td>
	  <td>
		- length - 型: int - このオプションは省略できません。
	  </td>	  
    </tr>
	<tr>
      <td>[unique](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/RemoveDuplicatesTokenFilter.html)</td>
      <td>#Microsoft.Azure.Search.UniqueTokenFilter</td>
	  <td>トークンのテキストが前のトークンと同じである場合、そのトークンを除外します。</td>
	  <td>
		- onlyOnSamePosition - 型: bool - 設定した場合、同じ位置にある重複のみを除外します (既定値: true)。
	  </td>	  
    </tr>
    <tr>
      <td>[uppercase](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/core/UpperCaseFilter.html)</td>
      <td>#Microsoft.Azure.Search.UppercaseTokenFilter</td>
	  <td>トークン テキストを大文字に正規化します。</td>
	  <td></td>	  
    </tr>
	<tr>
      <td>[word_delimiter](http://lucene.apache.org/core/4_10_3/analyzers-common/org/apache/lucene/analysis/miscellaneous/WordDelimiterFilter.html)</td>
      <td>#Microsoft.Azure.Search.WordDelimiterTokenFilter</td>
	  <td>単語をサブワードに分割し、必要に応じてサブワードのグループに変換を適用します。</td>
	  <td>
		- generateWordParts - 型: bool - 単語の構成要素を生成します (例:"AzureSearch" -> "Azure" "Search")(既定値: true)。 - generateNumberParts - 型: bool - 数字のサブワードを生成します (既定値: true)。 - catenateWords - 型: bool - 単語の連なりの最長部分を連結します (例: "Azure-Search" -> "AzureSearch") (既定値: false)。 - catenateNumbers - 型: bool - 数字の連なりの最長部分を連結します (例:"1-2" -> "12") (既定値: false)。 - catenateAll - 型: bool - すべてのサブワード要素を連結します (例: "Azure-Search-1" -> "AzureSearch1")(既定値: false)。 - splitOnCaseChange - 型: bool - true の場合、大文字と小文字が変化する位置で単語を分割します (例:"AzureSearch" -> "Azure" "Search")(既定値: true) - preserveOriginal - 元の単語を維持し、サブワードのリストに追加します (既定値: false)。 - splitOnNumerics - 型: bool - true の場合、数値で分割します (例: "Azure1Search" -> "Azure" "8" "Search")(既定値: true)。 - stemEnglishPossessive - 型: bool - サブワードごとに末尾の "'s" を削除します (既定値: true)。 - protectedWords - 型: 文字列配列 - 区切り処理から保護するトークン (既定値: 空のリスト)。
	  </td>	  
    </tr>
  </tbody>
</table>

**関連項目** MSDN の「[Azure Search Service REST API (Azure Search サービスの REST API)](http://msdn.microsoft.com/library/azure/dn798935.aspx)」 <br/> MSDN の「[Create Index (Azure Search API)](http://msdn.microsoft.com/library/azure/dn798941.aspx)」<br/>

<!---HONumber=AcomDC_0224_2016-->