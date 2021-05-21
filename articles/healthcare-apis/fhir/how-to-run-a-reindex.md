---
title: Azure API for FHIR で再インデックスジョブを実行する方法
description: この記事では、インデックス作成ジョブを実行して、データベースにまだインデックスが作成されていないすべての検索または並べ替えパラメーターにインデックスを作成する方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 4/23/2021
ms.author: cavoeg
ms.openlocfilehash: 5285de1a8481f37238ce3e3f3038d0d4f4c3d54a
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/19/2021
ms.locfileid: "110078539"
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

* **totalResourcesToReindex:** ジョブの一部としてインデックスを再作成するリソースの総数が含まれます。

* **resourcesSuccessfullyReindexed:** 既に正常にインデックスが再作成された合計。

* **progress:** ジョブのインデックスの再作成の完了率。 resourcesSuccessfullyReindexed/totalResourcesToReindex x 100 と等しくなります。

* **status:** インデックスの再作成ジョブがキューに登録されている場合、実行中、完了した場合、失敗した場合、または取り消された場合に状態になります。

* **resources:** インデックスの再作成ジョブの影響を受け、すべてのリソースの種類が一覧表示されます。

## <a name="delete-a-reindex-job"></a>インデックスの再作成ジョブを削除する

インデックスの再作成ジョブを取り消す必要がある場合は、削除呼び出しを使用し、インデックス再作成ジョブ ID を指定します。

`Delete {{FHIR URL}}/_operations/reindex/{{reindexJobId}`

## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

インデックスの再作成ジョブは、パフォーマンスが非常に高い場合があります。 データベースでインデックスの再作成ジョブを実行する方法を管理するために、いくつかの調整制御を実装しました。

> [!NOTE]
> 大規模なデータセットでは、インデックスの再作成ジョブが何日も実行されるのも珍しくありません。 30,000,000 万のリソースを持つデータベースの場合、データベース全体のインデックスを再作成するために 100,000 の 100,000 の RUs で 4 ~ 5 日かかっているのに気付きでした。

使用可能なパラメーター、既定値、および推奨範囲を示す表を次に示します。 これらのパラメーターを使用して、プロセスを高速化 (より多くのコンピューティングを使用) するか、プロセスを遅くすることができます (使用するコンピューティングの量を減らします)。 たとえば、トラフィックの少ない時間にインデックスの再作成ジョブを実行し、コンピューティングを増やして、より迅速に完了することができます。 代わりに、設定を使用してコンピューティングの使用率を非常に低くし、バックグラウンドで何日間も実行することができます。 

| **パラメーター**                     | **説明**              | **[Default]**        | **推奨される範囲**           |
| --------------------------------- | ---------------------------- | ------------------ | ------------------------------- |
| QueryDelayIntervalInMilliseconds  | これは、インデックスの再作成ジョブ中に開始されるリソースの各バッチ間の遅延です。 | 500 MS (.5 秒) | 50から 5000:50 によって再インデックスジョブが高速化され、5000によって既定の処理速度が低下します。 |
| MaximumResourcesPerQuery  | これは、インデックスするリソースのバッチに含まれるリソースの最大数です。  | 100 | 1-500 |
| MaximumConcurreny  | これは、一度に実行されるバッチの数です。  | 1 | 1 - 5 |
| targetDataStoreUsagePercentrage | これにより、インデックス再作成ジョブに使用するデータストアの割合を指定できます。 たとえば、50% を指定すると、最大インデックス作成ジョブで Cosmos DB で使用可能な Ru の50% が使用されるようになります。  | 存在しません。これは、最大100% を使用できることを意味します。 | 1-100 |

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

## <a name="next-steps"></a>次のステップ

この記事では、reindex ジョブを開始する方法について説明しました。 Reindex ジョブを必要とする新しい検索パラメーターを定義する方法については、「」を参照してください。 

>[!div class="nextstepaction"]
>[カスタム検索パラメーターの定義](how-to-do-custom-search.md)

         
     