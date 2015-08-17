<properties
	pageTitle="Machine Learning アプリ: センチメント分析 | Microsoft Azure"
	description="テキスト分析 API は、Azure Machine Learning で構築されたテキスト分析スイートです。API を使用して、センチメント分析やキー フレーズの抽出などのタスクの非構造化テキストを分析することができます。"
	services="machine-learning"
	documentationCenter=""
	authors="LuisCabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/28/2015"
	ms.author="luisca"/>


# Machine Learning アプリ: センチメント分析を実行するためのテキスト分析サービス#
##概要
テキスト分析 API は、Azure Machine Learning で構築されたテキスト分析 [Web サービス](https://datamarket.azure.com/dataset/amla/text-analytics)です。API を使用して、センチメント分析やキー フレーズの抽出などのタスクの非構造化テキストを分析することができます。この API を使用するためにトレーニング データは必要ありません。自分のテキスト データを使用するだけです。現時点では英語のみサポートしています。この API は、内部での高度な自然言語の処理手法を使用します。

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]
 
## センチメント分析##
API は、0 と 1 の間の数値スコアを返します。1 に近いスコアは正のセンチメントを表し、0 に近いスコアは負のセンチメントを表します。センチメントのスコアは、分類の手法を使用して生成されます。分類子への入力機能には、会話の一部であることを示すタグと単語の埋め込みから生成される、n-gram 機能が含まれます。
 
## キー フレーズの抽出##
API は、入力テキストの要点を示す文字列のリストを返します。Microsoft Office の高度な自然言語処理ツールキットの手法を採用しています。

## API の定義##

###GetSentiment###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment

**要求の例**

次の GET 呼び出しでは、語句 *Hello World* のセンチメントを要求しています。

    GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentiment?Text=hello+world

ヘッダー:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

アカウント キーは[こちら](https://datamarket.azure.com/account/keys)で取得します。

**応答の例**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
		"Score":1.0
	}

---

###GetKeyPhrases###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases

**要求の例**

次の GET 呼び出しでは、テキスト*ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした*のキー フレーズのセンチメントを要求しています。

	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrases?
	Text=It+was+a+wonderful+hotel+to+stay+at,+with+unique+decor+and+friendly+staff

ヘッダー:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

アカウント キーは[こちら](https://datamarket.azure.com/account/keys)で取得します。


**応答の例**

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata","KeyPhrases":[
	    "wonderful hotel","unique decor","friendly staff"]
	}
 
---

###GetSentimentBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch

**要求の例**

次の POST 呼び出しでは、要求の本文に、Hello World、Hello Foo World、Hello My World のフレーズのセンチメントを要求しています。

    POST https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetSentimentBatch 

本文:

	{"Inputs":
	[
	    {"Id":"1","Text":"hello world"},
    	{"Id":"2","Text":"hello foo world"},
    	{"Id":"3","Text":"hello my world"},
	]}


ヘッダー:

	Authorization: Basic <creds>
	Accept: application/json

	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  


アカウント キーは[こちら](https://datamarket.azure.com/account/keys)で取得します。

**応答の例**

次の応答では、テキスト ID に関連付けられているスコアの一覧を取得します。

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata", "SentimentBatch":
		[{"Score":0.9549767,"Id":"1"},
		 {"Score":0.7767222,"Id":"2"},
		 {"Score":0.8988889,"Id":"3"}
		],  
		"Errors":[] 
	}


---

###GetKeyPhrasesBatch###

**URL**

	https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

**要求の例**

次の GET 呼び出しでは、次のテキスト のキー フレーズのセンチメントの一覧を要求しています。

*ユニークな装飾がされていてスタッフも親しみやすく、素晴らしいホテルでした*
 
*非常に興味深い話題が出た素晴らしいビルド会議でした*

*激しい交通渋滞で、空港に到着するのに 3 時間かかりました*



	GET https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/GetKeyPhrasesBatch

本文:

	{"Inputs":
	[
		{"Id":"1","Text":"It was a wonderful hotel to stay at, with unique decor and friendly staff"},
		{"Id":"2","Text":"It was an amazing build conference, with very interesting talks"},
		{"Id":"3","Text":"The traffic was terrible, I spent three hours going to the airport"}
	]}

ヘッダー:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey)

アカウント キーは[こちら](https://datamarket.azure.com/account/keys)で取得します。


**応答の例**

次の応答では、テキスト ID に関連付けられているキー フレーズの一覧を取得します。

	{ "odata.metadata":"https://api.datamarket.azure.com/data.ashx/amla/text-analytics/v1/$metadata",
	 	"KeyPhrasesBatch":
		[
		   {"KeyPhrases":["unique decor","friendly staff","wonderful hotel"],"Id":"1"},
		   {"KeyPhrases":["amazing build conference","interesting talks"],"Id":"2"},
		   {"KeyPhrases":["hours","traffic","airport"],"Id":"3" }
		],
		"Errors":[ ]
	}

---

**バッチ処理に関連する注意**

システムに入力される ID は、システムから返された ID です。Web サービスは、ID が一意であることを確認しません。一意であることを確認するのは呼び出し元の役割です。
 

<!---HONumber=August15_HO6-->