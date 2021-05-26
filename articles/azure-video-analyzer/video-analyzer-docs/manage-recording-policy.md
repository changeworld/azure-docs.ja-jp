---
title: Azure Video Analyzer を使用してレコーディング ポリシーを管理する
description: このトピックでは、Azure Video Analyzer を使用してレコーディング ポリシーを管理する方法について説明します。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 04/04/2021
ms.openlocfilehash: a85f56dde890b5bf587c81bb5fb71407e46986f8
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110388152"
---
# <a name="manage-recording-policy-with-video-analyzer"></a>Video Analyzer を使用してレコーディング ポリシーを管理する

Azure Video Analyzer を使用して、数週間 、数か月、または数年におよびライブ ビデオをクラウドに[記録](video-recording.md)できます。 この記録は、[連続](continuous-video-recording.md)である場合も、スパースまたは[イベント ベース](event-based-video-recording-concept.md)となることもできます。 どちらの場合も、レコーディングは年にまたがって行う可能性があります。 Azure ストレージに組み込まれている[ライフサイクル管理ツール](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal)を使用して、そのクラウド アーカイブの長さ (日数) を管理できます。  

Video Analyzer アカウントは Azure Storage アカウントにリンクされ、ビデオ リソースに記録すると、メディア データがストレージ アカウント内のコンテナーに書き込まれます。 ライフサイクル管理を使用すると、ストレージ アカウントの[ポリシー](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#policy)を定義できます。ここで、次のような[ルール](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#rules)を指定できます。

```
{
  "rules": [
    {
      "name": "NinetyDayRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "delete": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

上記のルールは:

* ストレージ アカウントのすべてのブロック BLOB に適用されます。
* BLOB の期間が 30 日を超えたら、[ホット アクセス層からクール アクセス層](../../storage/blobs/storage-blob-storage-tiers.md?tabs=azure-portal)に移動することを指定します。
* また、期間が 90 日を超えると、その BLOB は削除されます。

Video Analyzer を使用してビデオ リソースに記録する場合は、ストレージ アカウントに書き込まれる前に集計するビデオの最小再生時間 (秒) を指定する `segmentLength` プロパティを指定します。 ビデオ リソースには一連のセグメントが含まれ、各セグメントの作成タイムスタンプは前のものより `segmentLength` だけ新しくなります。 ライフサイクル管理ポリシーが開始されると、指定したしきい値よりも古いセグメントは削除されます。 ただし、引き続き Video Analyzer API を使用して、残りのセグメントにアクセスして再生することができます。 詳細については、「[記録の再生](playback-recordings-how-to.md)」を参照してください。 

## <a name="limitations"></a>制限事項

次に、ライフサイクル管理に関する既知の制限事項をいくつか示します。

* ポリシー内には最大で 100 のルールを設定でき、ルールごとに最大 10 個のコンテナーを指定できます。 したがって、異なる記録ポリシーを持つ必要がある場合 (たとえば、駐車場に面したカメラには 3 日間のアーカイブ、発送センターのカメラには 30 日間、レジ カウンターの背後にあるカメラには 180 日間)、1 つの ストレージ アカウントを使用して、最大 1,000 台のカメラのルールをカスタマイズできます。
* ライフサイクル管理ポリシーの更新はすぐには行われません。 詳細については、[この FAQ セクション](../../storage/blobs/storage-lifecycle-management-concepts.md?tabs=azure-portal#faq)を参照してください。
* BLOB をクール層に移動するポリシーを適用する場合は、アーカイブのその部分の再生が影響を受ける可能性があります。 追加の待機時間、または散発的なエラーが表示されることがあります。 Video Analyzer は、アーカイブ層のコンテンツの再生をサポートしていません。

## <a name="next-steps"></a>次のステップ

[記録の再生](playback-recordings-how-to.md)
