---
title: 'チュートリアル: Cognitive Service を使用する Translator'
description: Azure Synapse Analytics で Translator を使用する方法について説明します。
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: ''
ms.date: 11/02/2021
author: ruixinxu
ms.author: ruxu
ms.custom: ignite-fall-2021
ms.openlocfilehash: e0ae2aea56e7bfa255daba30c99b49bc66b5b4b5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092449"
---
# <a name="tutorial-translator-with-cognitive-service"></a>チュートリアル: Cognitive Service を使用する Translator

[Translator](../../cognitive-services/Translator/index.yml) は、言語翻訳やその他の言語関連の操作を実行できるようにする [Azure Cognitive Service](../../cognitive-services/index.yml) です。 このチュートリアルでは、[Translator](../../cognitive-services/Translator/index.yml) を使用して、Azure Synapse Analytics でインテリジェントな多言語ソリューションを構築する方法について説明します。

このチュートリアルでは、Translator と [MMLSpark](https://github.com/Azure/mmlspark) を使用して、以下の処理を行う方法を示します。

> [!div class="checklist"]
> - テキストを翻訳する
> - テキストを表記変換する
> - 言語を検出する
> - 文を分割する
> - 辞書検索
> - 辞書の例

Azure サブスクリプションをお持ちでない場合は、[開始する前に無料アカウントを作成](https://azure.microsoft.com/free/)してください。


## <a name="prerequisites"></a>前提条件

- Azure Data Lake Storage Gen2 ストレージ アカウントが既定のストレージとして構成されている [Azure Synapse Analytics ワークスペース](../get-started-create-workspace.md)。 使用する Data Lake Storage Gen2 ファイル システムの "*Storage Blob データ共同作成者*" である必要があります。
- Azure Synapse Analytics ワークスペースの Spark プール。 詳細については、[Azure Synapse での Spark プールの作成](../quickstart-create-sql-pool-studio.md)に関する記事を参照してください。
- [Azure Synapse での Cognitive Services の構成](tutorial-configure-cognitive-services-synapse.md)に関するチュートリアルで説明されている事前構成手順。

## <a name="get-started"></a>開始
Synapse Studio を開き、新しいノートブックを作成します。 はじめに、[MMLSpark](https://github.com/Azure/mmlspark) をインポートします。 

```python
import mmlspark
from mmlspark.cognitive import *
from notebookutils import mssparkutils
from pyspark.sql.functions import col, flatten
```

## <a name="configure-translator"></a>Translator の構成

[構成前の手順](tutorial-configure-cognitive-services-synapse.md)で構成した、リンクされた Translator を使用します。 

```python
cognitive_service_name = "<Your linked service for translator>"
```

## <a name="translate-text"></a>テキストの翻訳
Translator サービスの主要な操作は、テキストの翻訳です。 

```python
df = spark.createDataFrame([
  (["Hello, what is your name?", "Bye"],)
], ["text",])

translate = (Translate()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setToLanguage(["zh-Hans", "fr"])
    .setOutputCol("translation")
    .setConcurrency(5))

display(translate
      .transform(df)
      .withColumn("translation", flatten(col("translation.translations")))
      .withColumn("translation", col("translation.text"))
      .select("translation"))
```

### <a name="expected-results"></a>予想される結果

```json
["你好，你叫什么名字？","Bonjour, quel est votre nom?","再见","Au revoir"]
```

## <a name="transliterate-text"></a>テキストの表記変換

表記変換は、音声の類似性に基づいて、単語や語句の書記法 (アルファベット) を、ある言語から別の言語のものに変換するプロセスです。

```python
transliterateDf =  spark.createDataFrame([
  (["こんにちは", "さようなら"],)
], ["text",])

transliterate = (Transliterate()
    .setLinkedService(cognitive_service_name)
    .setLanguage("ja")
    .setFromScript("Jpan")
    .setToScript("Latn")
    .setTextCol("text")
    .setOutputCol("result"))

display(transliterate
    .transform(transliterateDf)
    .withColumn("text", col("result.text"))
    .withColumn("script", col("result.script"))
    .select("text", "script"))
```

### <a name="expected-results"></a>予想される結果

|text|script|
|--|--|
|"["Kon'nichiwa","sayonara"]"|"["Latn","Latn"]"|

## <a name="detect-language"></a>言語の検出
翻訳する必要があるのに、Translator サービスに送信するテキストの言語がわからない場合は、言語の検出操作を使用できます。 

```python
detectDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

detect = (Detect()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(detect
    .transform(detectDf)
    .withColumn("language", col("result.language"))
    .select("language"))
```

### <a name="expected-results"></a>予想される結果

```json
"["en"]"
```

## <a name="break-sentence"></a>文の分割

文章内で文の境界の位置を識別します。

```python
bsDf =  spark.createDataFrame([
  (["Hello, what is your name?"],)
], ["text",])

breakSentence = (BreakSentence()
    .setLinkedService(cognitive_service_name)
    .setTextCol("text")
    .setOutputCol("result"))

display(breakSentence
    .transform(bsDf)
    .withColumn("sentLen", flatten(col("result.sentLen")))
    .select("sentLen"))
```

### <a name="expected-results"></a>予想される結果

```json
"[25]"
```

## <a name="dictionary-lookup-alternate-translations"></a>辞書検索 (翻訳の代替候補)
エンドポイントを使用すると、単語または語句の翻訳の代替候補を取得できます。 

```python
dictDf = spark.createDataFrame([
  (["fly"],)
], ["text",])

dictionaryLookup = (DictionaryLookup()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextCol("text")
    .setOutputCol("result"))

display(dictionaryLookup
    .transform(dictDf)
    .withColumn("translations", flatten(col("result.translations")))
    .withColumn("normalizedTarget", col("translations.normalizedTarget"))
    .select("normalizedTarget"))
```

### <a name="expected-results"></a>予想される結果

|normalizedTarget|
|----|
|"["volar","mosca","operan","pilotar","moscas","marcha"]"|

## <a name="dictionary-examples-translations-in-context"></a>辞書の例 (コンテキスト内の翻訳)

辞書検索を実行した後、ソース テキストと翻訳を dictionary/examples エンドポイントに渡すことによって、文または語句のコンテキスト内で両方の用語が現れる例の一覧を取得できます。

```python
dictDf = spark.createDataFrame([
  ([("fly", "volar")],)
], ["textAndTranslation",])

dictionaryExamples = (DictionaryExamples()
    .setLinkedService(cognitive_service_name)
    .setFromLanguage("en")
    .setToLanguage("es")
    .setTextAndTranslationCol("textAndTranslation")
    .setOutputCol("result"))

display(dictionaryExamples
    .transform(dictDf)
    .withColumn("examples", flatten(col("result.examples")))
    .select("examples"))
```

### <a name="expected-results"></a>予想される結果

```json

[{"sourcePrefix":"I mean, for a guy who could ","sourceSuffix":".","targetPrefix":"Quiero decir, para un tipo que podía ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"Now it's time to make you ","sourceSuffix":".","targetPrefix":"Ahora es hora de que te haga ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"One happy thought will make you ","sourceSuffix":".","targetPrefix":"Uno solo te hará ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"They need machines to ","sourceSuffix":".","targetPrefix":"Necesitan máquinas para ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"That should really ","sourceSuffix":".","targetPrefix":"Eso realmente debe ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"It sure takes longer when you can't ","sourceSuffix":".","targetPrefix":"Lleva más tiempo cuando no puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I have to ","sourceSuffix":" home in the morning.","targetPrefix":"Tengo que ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" a casa por la mañana."},{"sourcePrefix":"You taught me to ","sourceSuffix":".","targetPrefix":"Me enseñaste a ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I think you should ","sourceSuffix":" with the window closed.","targetPrefix":"Creo que debemos ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":" con la ventana cerrada."},{"sourcePrefix":"They look like they could ","sourceSuffix":".","targetPrefix":"Parece que pudieran ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But you can ","sourceSuffix":", for instance?","targetPrefix":"Que puedes ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":", por ejemplo."},{"sourcePrefix":"At least until her kids can be able to ","sourceSuffix":".","targetPrefix":"Al menos hasta que sus hijos sean capaces de ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I thought you could ","sourceSuffix":".","targetPrefix":"Pensé que podías ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"I was wondering what it would be like to ","sourceSuffix":".","targetPrefix":"Me preguntaba cómo sería ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."},{"sourcePrefix":"But nobody else can ","sourceSuffix":".","targetPrefix":"Pero nadie puede ","targetTerm":"volar","sourceTerm":"fly","targetSuffix":"."}]
```
## <a name="clean-up-resources"></a>リソースをクリーンアップする
Spark インスタンスがシャットダウンされるようにするには、接続されているセッション (ノートブック) を終了します。 プールは、Apache Spark プールに指定されている **アイドル時間** に達したときにシャットダウンされます。 また、ノートブックの右上にあるステータス バーから **[セッションの停止]** を選択することもできます。

![セッションの停止を示すスクリーンショット](./media/tutorial-build-applications-use-mmlspark/stop-session.png)

## <a name="next-steps"></a>次のステップ

* [Synapse サンプル ノートブックをチェックアウトする](https://github.com/Azure-Samples/Synapse/tree/main/MachineLearning) 
* [MMLSpark GitHub リポジトリ](https://github.com/Azure/mmlspark)
