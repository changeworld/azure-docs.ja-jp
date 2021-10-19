---
title: Azure API for FHIR でインデックス再作成ジョブを実行する方法
description: この記事では、インデックス再作成ジョブを実行して、まだデータベースにインデックスが作成されていない検索パラメーターまたは並べ替えパラメーターにインデックスを付ける方法について説明します。
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 8/23/2021
ms.author: cavoeg
ms.openlocfilehash: 3434a868ac11c90b3ad864f94adf6876e358a8aa
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/24/2021
ms.locfileid: "122772022"
---
# <a name="running-a-reindex-job-in-azure-api-for-fhir"></a>Azure API for FHIR でのインデックス再作成ジョブの実行

まだインデックスが作成されていない検索または並べ替えパラメーターが Azure API for FHIR に存在する場合があります。 このシナリオは、独自の検索パラメーターを定義する場合に関連します。 検索パラメーターは、インデックスが付けられるまでは検索で使用できません。 この記事では、インデックス再作成ジョブを実行して、まだデータベースにインデックスが作成されていない検索パラメーターまたは並べ替えパラメーターにインデックスを付ける方法の概要を説明します。

> [!Warning]
> 作業を始める前に、この記事全体に目を通すことは重要です。 インデックス再作成ジョブには、非常に高いパフォーマンスが要求される可能性があります。 この記事では、インデックス再作成ジョブを調整および制御する方法に関するオプションを説明します。

## <a name="how-to-run-a-reindex-job"></a>インデックス再作成ジョブの実行方法 

インデックス再作成ジョブを開始するには、次のコード例を使用します。

```json
POST {{FHIR URL}}/$reindex 

{ 

“resourceType”: “Parameters”,  

“parameter”: [] 

}
 ```

要求が成功した場合、**201 Created** の状態が返されます。 このメッセージの結果は次のようになります。

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
> インデックス再作成ジョブの状態を確認したり、ジョブをキャンセルしたりするには、インデックス再作成 ID が必要です。 これは、結果として得られる Parameters リソースの ID です。 上記の例では、インデックス再作成ジョブの ID は `560c7c61-2c70-4c54-b86d-c53a9d29495e` になります。

 ## <a name="how-to-check-the-status-of-a-reindex-job"></a>インデックス再作成ジョブを確認する方法

インデックスの再作成ジョブを開始したら、次の呼び出しを使用してジョブの状態を確認できます。

`GET {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

インデックス再作成ジョブの結果の状態を次に示します。

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

インデックス再作成ジョブの結果には、次の情報が表示されます。

* **totalResourcesToReindex**: このジョブでインデックスを再作成するリソースの総数。

* **resourcesSuccessfullyReindexed**: インデックスの再作成が正常に完了したリソースの総数。

* **progress**: インデックス再作成ジョブの完了率 (%)。 resourcesSuccessfullyReindexed/totalResourcesToReindex の 100 倍に等しくなります。

* **status**: 再インデックスジョブがキューに登録されているか、実行中、完了、失敗、または取り消されたかを示します。

* **リソース**: 再インデックスジョブによって影響を受けるすべてのリソースの種類が一覧表示されます。

## <a name="delete-a-reindex-job"></a>インデックス再作成ジョブの削除

インデックス再作成ジョブをキャンセルする必要がある場合は、削除呼び出しを使用して、インデックス再作成ジョブの ID を指定します。

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

インデックス再作成ジョブには、非常に高いパフォーマンスが要求される可能性があります。 データベースでインデックス再作成ジョブをどのように実行するかの管理に役立つ、いくつかの調整コントロールが実装されています。

> [!NOTE]
> 大規模なデータ セットでは、インデックス再作成ジョブが数日にわたって実行されることも珍しくありません。 リソース数 3,000 万のデータベースで、データベース全体のインデックス再作成に 10 万 RU で 4 ～ 5 日を要することが確認されています。

使用可能なパラメーター、既定値、推奨範囲の概要を次の表に示します。 これらのパラメーターを使用して、プロセスを高速化 (コンピューティングをさらに使用) するか、プロセスの速度を低下させることができます (少ないコンピューティングを使用します)。 たとえば、トラフィックの少ない時間帯にインデックス再作成ジョブを実行したり、コンピューティング使用量を増やして実行を高速化したりできます。 代わりに、設定を使用して、コンピューティングの使用率を低くして、バックグラウンドで実行することができます。 

| **パラメーター**                     | **説明**              | **[Default]**        | **使用可能な範囲**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | 再インデックスジョブ中に開始されるリソースのバッチ間の遅延。 数値を小さくすると、ジョブの処理速度が上がり、数値が大きいほど処理が遅くなります。 | 500 ミリ秒 (0.5 秒) | 50-500000 |
| MaximumResourcesPerQuery  | インデックスするリソースのバッチに含まれるリソースの最大数。  | 100 | 1-5000 |
| MaximumConcurrency  | 一度に実行されるバッチの数。  | 1 | 1 ～ 10 |
| targetDataStoreUsagePercentage | 再インデックスジョブに使用するデータストアの割合を指定できます。 たとえば、50% を指定すると、Cosmos DB で使用可能な RU の最大 50% がインデックス再作成ジョブに使用されるようになります。  | 存在しません。これは、最大100% を使用できることを意味します。 | 0 ～ 100 |

上記のパラメーターのいずれかを使用する場合、インデックス再作成ジョブの開始時に Parameters リソースに渡すことができます。

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

## <a name="next-steps"></a>次のステップ

この記事では、インデックス再作成ジョブを開始する方法について説明しました。 インデックス再作成ジョブを必要とする新しい検索パラメーターを定義する方法については、次の記事を参照してください。 

>[!div class="nextstepaction"]
>[カスタム検索パラメーターの定義](how-to-do-custom-search.md)
