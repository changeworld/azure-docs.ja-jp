---
title: Azure API for FHIR で再インデックスジョブを実行する方法
description: この記事では、インデックス作成ジョブを実行して、データベースにまだインデックスが作成されていないすべての検索または並べ替えパラメーターにインデックスを作成する方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: b4ede817b3babfb9221ac8fa982acc0322c9d7b2
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379674"
---
# <a name="running-a-reindex-job"></a>再インデックスジョブの実行

Azure API for FHIR でまだインデックスが作成されていない検索パラメーターまたは並べ替えパラメーターがある場合があります。 これは、独自の検索パラメーターを定義する場合に特に関連します。 検索パラメーターのインデックスが作成されるまで、検索では使用できません。 この記事では、インデックス作成ジョブを実行して、データベースにまだインデックスが作成されていないすべての検索または並べ替えパラメーターにインデックスを作成する方法の概要について説明します。

> [!Warning]
> 作業を開始する前に、この記事全体を読むことが重要です。 再インデックスジョブは、非常にパフォーマンスに大きな影響を与える可能性があります。 この記事には、再インデックスジョブを調整および制御する方法のオプションが含まれています。

## <a name="how-to-run-a-reindex-job"></a>インデックス再作成ジョブの実行方法 

再インデックスジョブを開始するには、次のコード例を使用します。

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

要求が成功した場合は、" **201** " という状態が返されます。 このメッセージの結果は次のようになります。

```json
HTTP/1.1 201 Created 
Content-Location: https://{{FHIR URL}}/_operations/reindex/560c7c61-2c70-4c54-b86d-c53a9d29495e 

{
  "resourceType": "Parameters",
  "id": "560c7c61-2c70-4c54-b86d-c53a9d29495e",
  "meta": {
    "versionId": "\"4c0049cd-0000-0100-0000-607dc5a90000\""
  },
  "parameter": [
    {
      "name": "id",
      "valueString": "560c7c61-2c70-4c54-b86d-c53a9d29495e"
    },
    {
      "name": "queuedTime",
      "valueDateTime": "2021-04-19T18:02:17.0118558+00:00"
    },
    {
      "name": "totalResourcesToReindex",
      "valueDecimal": 0.0
    },
    {
      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 0.0
    },
    {
      "name": "progress",
      "valueDecimal": 0.0
    },
    {
      "name": "status",
      "valueString": "Queued"
    },
    {
      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {
      "name": "resources",
      "valueString": ""
    },
    {
      "name": "searchParams",
      "valueString": ""
    }
  ]
}
```

> [!NOTE]
> またはの状態を確認して再インデックス作成ジョブを取り消すには、reindex ID が必要です。 これは、結果として得られるパラメーターリソースの ID です。 上記の例では、再インデックスジョブの ID はになり `560c7c61-2c70-4c54-b86d-c53a9d29495e` ます。

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>再インデックスジョブの状態を確認する方法

インデックスの再作成ジョブを開始したら、次のようにしてジョブの状態を確認できます。

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

再インデックスジョブの結果の状態を次に示します。

```json
{

  "resourceType": "Parameters",
  "id": "b65fd841-1c62-47c6-898f-c9016ced8f77",
  "meta": {

    "versionId": "\"1800f05f-0000-0100-0000-607a1a7c0000\""
  },
  "parameter": [

    {

      "name": "id",
      "valueString": "b65fd841-1c62-47c6-898f-c9016ced8f77"
    },
    {

      "name": "startTime",
      "valueDateTime": "2021-04-16T23:11:35.4223217+00:00"
    },
    {

      "name": "queuedTime",
      "valueDateTime": "2021-04-16T23:11:29.0288163+00:00"
    },
    {

      "name": "totalResourcesToReindex",
      "valueDecimal": 262544.0
    },
    {

      "name": "resourcesSuccessfullyReindexed",
      "valueDecimal": 5754.0
    },
    {

      "name": "progress",
      "valueDecimal": 2.0
    },
    {

      "name": "status",
      "valueString": "Running"
    },
    {

      "name": "maximumConcurrency",
      "valueDecimal": 1.0
    },
    {

      "name": "resources",
      "valueString": 
      "{LIST OF IMPACTED RESOURCES}"
    },
    {
```

次の情報が reindex ジョブの結果に表示されます。

* **Totalresourcestoreindex**: ジョブの一部としてインデックスされているリソースの合計数が含まれます。

* **resourcesSuccessfullyReindexed**: 既に正常にインデックスされている合計。

* **進行状況**: 再インデックスジョブの完了率。 は、resourcesSuccessfullyReindexed/totalResourcesToReindex x 100 に相当します。

* **status**: 再インデックスジョブがキューに入っている、実行中、完了、失敗、またはキャンセルされた場合に表示されます。

* **リソース**: 再インデックスジョブによって影響を受けるすべてのリソースの種類が一覧表示されます。

## <a name="delete-a-reindex-job"></a>再インデックスジョブの削除

再インデックスジョブを取り消す必要がある場合は、delete 呼び出しを使用して、reindex ジョブ ID を指定します。

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

再インデックスジョブは、非常にパフォーマンスに大きな影響を与える可能性があります。 ここでは、インデックス作成ジョブをデータベースで実行する方法を管理するのに役立つ調整コントロールを実装しました。

> [!NOTE]
> 大規模なデータセットでは、再インデックスジョブを日にわたって実行することは珍しくありません。 3000万万個のリソースを含むデータベースの場合、データベース全体のインデックスを再使用するために、10万 Ru で4-5 日がかかったことがわかりました。

使用可能なパラメーター、既定値、および推奨される範囲を次の表に示します。 これらのパラメーターを使用して、プロセスを高速化するか (コンピューティングをさらに使用する)、プロセスの速度を低下させることができます (少ないコンピューティングを使用します)。 たとえば、インデックスの再作成ジョブを低トラフィック時間で実行し、コンピューティングを増やして短時間で完了することができます。 代わりに、設定を使用して、コンピューティングの使用率が非常に低いことを確認し、バックグラウンドでその設定を実行することができます。 

| **パラメーター**                     | **説明**              | **[Default]**        | **推奨される範囲**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | これは、再インデックスジョブ中に開始されるリソースのバッチ間の遅延です。 | 500ミリ秒 (.5 秒) | 50から 5000:50 によって再インデックスジョブが高速化され、5000によって既定の処理速度が低下します。 |
| MaximumResourcesPerQuery  | これは、インデックスするリソースのバッチに含まれるリソースの最大数です。  | 100 | 1-500 |
| MaximumConcurrency  | これは、一度に実行されるバッチの数です。  | 1 | 1 - 5 |
| Targetdatastoreの割合 | これにより、インデックス再作成ジョブに使用するデータストアの割合を指定できます。 たとえば、50% を指定すると、最大インデックス作成ジョブで Cosmos DB で使用可能な Ru の50% が使用されるようになります。  | 存在しません。これは、最大100% を使用できることを意味します。 | 1-100 |

上記のいずれかのパラメーターを使用する場合は、インデックスの再作成ジョブを開始するときに、パラメーターリソースに渡すことができます。

```json
{
  "resourceType": "Parameters",
  "parameter": [
    {
      "name": "maximumConcurrency",
      "valueInteger": "3"
    },
    {
      "name": "targetDataStoreUsagePercentage",
      "valueInteger": "20"
    },
    {
      "name": "queryDelayIntervalInMilliseconds",
      "valueInteger": "1000"
    },
    {
      "name": "maximumNumberOfResourcesPerQuery",
      "valueInteger": "1"
    }
  ]
}
```

## <a name="next-steps"></a>次の手順

この記事では、reindex ジョブを開始する方法について説明しました。 Reindex ジョブを必要とする新しい検索パラメーターを定義する方法については、「」を参照してください。 

>[!div class="nextstepaction"]
>[カスタム検索パラメーターの定義](how-to-do-custom-search.md)

         
     
