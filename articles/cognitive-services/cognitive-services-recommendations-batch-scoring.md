<properties
	pageTitle="複数のレコメンデーションの一括取得: Machine Learning Recommendations API | Microsoft Azure"
	description="Azure Machine Learning Recommendations - 複数のレコメンデーションの一括取得"
	services="cognitive-services"
	documentationCenter=""
	authors="luiscabrer"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="cognitive-services"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/24/2016"
	ms.author="luisca"/>

# 複数のレコメンデーションの一括取得

>[AZURE.NOTE] 複数のレコメンデーションを一括で取得することは、レコメンデーションの結果を 1 件ずつ取得するよりも難易度の高い作業です。1 回の要求で複数のレコメンデーションを取得するための API については、以下のページをご覧ください。<br> [アイテムとアイテムの結び付きに基づく Item-to-Item レコメンデーション](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3d4)<br> [ユーザーとアイテムの結び付きに基づく User-to-Item レコメンデーション](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3dd)
>
> バッチ スコアリングが正しく機能するのは、2016 年 7 月 21 日より後に作成されたビルドだけです。


複数のアイテムのレコメンデーションを一度に取得しなければならない場合があります。たとえば何かの都合で、レコメンデーションのキャッシュを作成したり、取得するレコメンデーションの種類に関する分析を行ったりするときです。

バッチ スコアリング操作は非同期の処理です。要求を送信し、操作の完了を待って、その結果を収集する必要があります。その具体的な手順は次のとおりです。

1.	Azure Storage コンテナーを作成します (まだ存在しない場合)。
2.	Azure Blob Storage に個々のレコメンデーション要求を記述した入力ファイルをアップロードします。
3.	スコアリング バッチ ジョブを開始します。
4.	非同期操作の完了を待機します。
5.	完了したら、その結果を Azure Blob Storage から収集します。

以上の各手順を個別に見ていきましょう。

## Azure Storage コンテナーを作成する (まだ存在しない場合)

まだストレージ アカウントを作成していない場合は、[Azure 管理ポータル](https://portal.azure.com)に移動して新しいストレージ アカウントを作成します。*[+新規]*、[*データ* + *ストレージ*]、*[ストレージ アカウント]* の順に選択してください。

ストレージ アカウントの準備が整ったら、バッチ実行の入力と出力の保存場所となる BLOB コンテナーを作成する必要があります。

Azure BLOB ストレージに個々のレコメンデーション要求を記述した入力ファイル (ここでは input.json と呼びます) をアップロードします。コンテナーを作成したら、Recommendations Service から実行する各要求を記述したファイルをアップロードする必要があります。1 回のバッチで実行できるのは、特定のビルドからの 1 種類の要求だけです。この情報を定義する方法については、次のセクションで説明します。さしあたって、ここでは特定のビルドからアイテムのレコメンデーションを実施するものとします。そこで入力ファイルには、個々の要求の入力情報 (このケースではシード アイテム) を記述します。

次に示したのは input.json ファイルの記述例です。

    {
      "requests": [
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F34-03453" ] },
      { "SeedItems": [ "D16-3244" ] },
      { "SeedItems": [ "C9F-00163", "FKF-00689" ] },
      { "SeedItems": [ "F43-01467" ] },
      { "SeedItems": [ "BD5-06013" ] },
      { "SeedItems": [ "P45-00163", "FKF-00689" ] },
      { "SeedItems": [ "C9A-69320" ] }
      ]
    }
    
ご覧のように、このファイルは JSON 形式になっています。個々の要求には、レコメンデーション要求を送信するうえで必要な情報が記述されています。満たすべき要求に関して同様の JSON ファイルを作成し、先ほど Blob Storage に作成したコンテナーにそれをコピーします。

## バッチ ジョブを開始する

次の手順は、新しいバッチ ジョブの送信です。[こちら](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/)の API リファレンスをご覧ください。

入力ファイルや出力ファイル、エラー ファイルの保存先とそこにアクセスするために必要な資格情報は、API の要求本文で定義する必要があります。加えてバッチ全体に適用するいくつかのパラメーターを指定する必要があります (要求するレコメンデーションの種類、使用するモデル/ビルド、1 回の呼び出しで取得する結果の件数など)。

要求本文の具体的な記述例を次に示します。

    {
      "input": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchInput.json",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "output": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/batchOutput.json ",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "error": {
        "authenticationType": "PublicOrSas",
        "baseLocation": "https://mystorage1.blob.core.windows.net/",
        "relativeLocation": "container1/errors.txt",
        "sasBlobToken": "?sv=2015-07_restofToken_…4Z&sp=rw"
      },
      "job": {
        "apiName": "ItemRecommend",
        "modelId": "9ac12a0a-1add-4bdc-bf42-c6517942b3a6",
        "buildId": 1015703,
        "numberOfResults": 10,
        "includeMetadata": true,
        "minimalScore": 0.0
      }
    }

次の点に注意してください。

-	現在 AuthenticationType は必ず PublicOrSas に設定する必要があります。
-	Blob Storage アカウントからの読み取りと Blob Storage アカウントへの書き込みを Recommendations API に許可するためには、Shared Access Signature (SAS) トークンを取得する必要があります。SAS トークンの生成について詳しくは、[こちら](../storage/storage-dotnet-shared-access-signature-part-1.md)をご覧ください。
-	現在サポートされている *apiName* は "ItemRecommend" のみです。アイテムとアイテムの結び付きに基づく Item-to-Item レコメンデーションに使用されます。ユーザーとアイテムの結び付きに基づく User to Item レコメンデーションは現在、バッチ処理ではサポートされません。

## 非同期操作の完了を待機する

バッチ操作を開始すると、その操作を追跡するうえで必要な情報を伝える Operation-Location ヘッダーが応答で返されます。バッチ操作の追跡には、ビルド操作を追跡するときと同様、[Retrieve Operation Status API](https://westus.dev.cognitive.microsoft.com/docs/services/Recommendations.V4.0/operations/56f30d77eda5650db055a3da) を使用します。

## 結果を取得する

操作が完了したら (操作でエラーが発生しなかった場合)、出力 Blob Storage から結果を収集することができます。

以下のサンプルは、実際の出力内容の例です。簡潔にするために、この例では 2 つの要求から成るバッチの結果を示しています。

    {
      "results":
      [   
        {
          "request": { "seedItems": [ "DAF-00500", "P3T-00003" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "F5U-00011",
                  "name": "L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.722,
              "reasoning": [ "People who like the selected items also like 'L2 Ethernet Adapter-Win8Pro SC EN/XD/XX Hdwr'" ]
            },
            {
              "items": [
                {
                  "itemId": "G5Y-00001",
                  "name": "Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr",
                  "metadata": ""
                }
              ],
              "rating": 0.718,
              "reasoning": [ "People who like the selected items also like 'Docking Station for Srf Pro/Pro2 SC EN/XD/ES Hdwr'" ]
            }
          ]
        },
        {
          "request": { "seedItems": [ "C9F-00163" ] },
          "recommendations": [
            {
              "items": [
                {
                  "itemId": "C9F-00172",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Green",
                  "metadata": ""
                }
              ],
              "rating": 0.649,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Green'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00171",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Orange",
                  "metadata": ""
                }
              ],
              "rating": 0.647,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Orange'" ]
            },
            {
              "items": [
                {
                  "itemId": "C9F-00170",
                  "name": "Nokia 2K Shell for Nokia Lumia 630/635 - Yellow",
                  "metadata": ""
                }
              ],
              "rating": 0.646,
              "reasoning": [ "People who like 'MOZO Flip Cover for Nokia Lumia 635 - White' also like 'Nokia 2K Shell for Nokia Lumia 630/635 - Yellow'" ]
            }       
          ]
        }
    ]}


## 制限事項

-	一度に呼び出すことのできるバッチ ジョブは各サブスクリプションにつき 1 つだけです。
-	バッチ ジョブの入力ファイルは 2 MB 以下にする必要があります。

<!---HONumber=AcomDC_0727_2016-->