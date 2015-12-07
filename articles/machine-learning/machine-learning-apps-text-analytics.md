<properties
	pageTitle="Machine Learning API: テキスト分析 | Microsoft Azure"
	description="Azure Machine Learning のテキスト分析 APIこれを使用し、センチメント分析、キー フレーズの抽出、言語検出の非構造化テキストを分析できます。"
	services="machine-learning"
	documentationCenter=""
	authors="onewth"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/17/2015"
	ms.author="onewth"/>


# Machine Learning API: センチメントのテキスト分析、キー フレーズ抽出、言語検出

## 概要

テキスト分析 API は、Azure Machine Learning で構築されたテキスト分析 [Web サービス](https://datamarket.azure.com/dataset/amla/text-analytics)です。API を使用して、センチメント分析、キー フレーズの抽出、言語検出などのタスクの非構造化テキストを分析することができます。この API を使用するためにトレーニング データは必要ありません。自分のテキスト データを使用するだけです。この API は、高度な自然言語の処理手法を使用し、このクラスで最高の予測機能を提供します。

[デモ サイト](https://text-analytics-demo.azurewebsites.net/)で実際のテキスト分析をご覧いただけます。このサイトには、C# と Python のテキスト分析方法に関する[サンプル](https://text-analytics-demo.azurewebsites.net/Home/SampleCode)もあります。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

---

## センチメント分析

API は、0 と 1 の間の数値スコアを返します。1 に近いスコアは正のセンチメントを表し、0 に近いスコアは負のセンチメントを表します。センチメントのスコアは、分類の手法を使用して生成されます。分類子への入力機能には、会話の一部であることを示すタグと単語の埋め込みから生成される、n-gram 機能が含まれます。現在、サポートされている言語は英語だけです。
 
## キー フレーズの抽出

API は、入力テキストの要点を示す文字列のリストを返します。Microsoft Office の高度な自然言語処理ツールキットの手法を採用しています。現在、サポートされている言語は英語だけです。

## 言語検出

この API は、検出した言語と 0 と 1 の間の数値スコアを返します。1 に近いスコアは、100% の確実性で正しい言語が特定されたことを示します。合計で 120 種類の言語がサポートされています。

---

## API の定義

### ヘッダー

要求には次のような正しいヘッダーを必ず追加します。

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

アカウントのアカウント キーは [Azure データ マーケット](https://datamarket.azure.com/account/keys)で確認できます。

---

## シングル リソース API

### GetSentiment

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**要求の例**

次の呼び出しでは、語句 "Hello World" のセンチメント分析を要求しています。

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

次のような応答が返されます。

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

### GetKeyPhrases

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**要求の例**

次の呼び出しでは、テキスト "ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした" のキー フレーズを要求しています。

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

次のような応答が返されます。

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	  "KeyPhrases":[
	    "wonderful hotel",
	    "unique decor",
	    "friendly staff"
	  ]
	}
 
---

### GetLanguage

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguage

**要求の例**

次の GET 呼び出しでは、テキスト *Hello World* のキー フレーズのセンチメントを要求しています。

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguages?
	Text=Hello+World

次のような応答が返されます。

	{
	  "UnknownLanguage": false,
	  "DetectedLanguages": [{
	    "Name": "English",
	    "Iso6391Name": "en",
	    "Score": 1.0
	  }]
	}

**省略可能なパラメーター**

`NumberOfLanguagesToDetect` は省略可能なパラメーターです。既定値は 1 です。

---

## Batch API

Text Analytics サービスでは、センチメントとキー フレーズの抽出をバッチ モードで実行できます。スコアが付けられたレコードはそれぞれ 1 つのトランザクションとして数えられることに注意してください。たとえば、1 回の呼び出しで 1000 個のレコードのセンチメントを要求する場合、1000 のトランザクションが推論されます。

システムに入力された ID はシステムから返された ID であることに注意してください。Web サービスは、ID が一意であることを確認しません。一意であることを確認するのは呼び出し元の役割です。


### GetSentimentBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**要求の例**

次の POST 呼び出しでは、要求の本文に、"Hello World"、"Hello Foo World"、"Hello My World" のフレーズのセンチメントを要求しています。

	POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

要求本文:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	    {"Id":"2","Text":"hello foo world"},
    	    {"Id":"3","Text":"hello my world"},
	]}

次の応答では、テキスト ID に関連付けられているスコアの一覧を取得します。

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", 
	  "SentimentBatch":
	  [
		{"Score":0.9549767,"Id":"1"},
		{"Score":0.7767222,"Id":"2"},
		{"Score":0.8988889,"Id":"3"}
	  ],  
	  "Errors":[]
	}


---

### GetKeyPhrasesBatch

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**要求の例**

この例では、次のテキストのキー フレーズのセンチメントの一覧を要求しています。

* "ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした"
* "非常に興味深い話題が出た素晴らしいビルド会議でした"
* "激しい交通渋滞で、空港に到着するのに 3 時間かかりました"

この要求は、エンドポイントへの POST 呼び出しとして作成されています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

要求本文:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

次の応答では、テキスト ID に関連付けられているキー フレーズの一覧を取得します。

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[]
	}

---

### GetLanguageBatch

下の POST 呼び出しでは、2 つのテキスト入力の言語検出を要求しています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetLanguageBatch

要求本文:

    {
      "Inputs": [
        {"Text": "hello world", "Id": "1"},
        {"Text": "c'est la vie", "Id": "2"}
      ]
    }

これで次の応答が返されます。英語が最初の入力で検出され、フランス語が 2 つ目の入力で検出されます。

    {
       "LanguageBatch": [{
         "Id": "1",
         "DetectedLanguages": [{
            "Name": "English",
            "Iso6391Name": "en",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       },
       {
         "Id": "2",
         "DetectedLanguages": [{
            "Name": "French",
            "Iso6391Name": "fr",
            "Score": 1.0
         }],
         "UnknownLanguage": false
       }],
       "Errors": []
    }

<!---HONumber=AcomDC_1125_2015-->