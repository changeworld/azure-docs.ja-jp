---
author: rothja
ms.service: media-services
ms.topic: include
ms.date: 09/16/2020
ms.author: jroth
ms.openlocfilehash: 10de63c842eeb60f5e7e725457c88961581370f0
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2021
ms.locfileid: "129063697"
---
>[!NOTE]
>固定されないリソースの場合は、サポート チケットを開いてクォータの増加を要求してください。 上限を高くするために追加の Azure Media Services アカウントを作成することはしないでください。

| リソース | 制限 | 
| --- | --- | 
| 1 つのサブスクリプション内の Azure Media Services アカウント | 25 (固定) | 
| Media Services アカウントあたりの Job | 50,000<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept> |
| ジョブあたりのチェーン タスク数 | 30 (固定) |
| Media Services アカウントあたりのアセット数 | 1,000,000|
| タスクあたりのアセット数 | 50 |
| ジョブあたりのアセット数 | 100 |
| 一度に 1 つのアセットに関連付けられる一意のロケーター数 | 5<bpt id="p1"><sup></bpt>4<ept id="p1"></sup></ept> |
| Media Services アカウントあたりのライブ チャンネル数 |5<sup>5</sup>|
| チャネルあたりの停止状態のプログラム数 |50|
| チャネルあたりの実行状態のプログラム数 |3|
| Media Services アカウントあたりの、停止または実行しているストリーミング エンドポイント数|2|
| ストリーミング エンドポイントあたりのストリーミング ユニット数 |10 |
| ストレージ アカウント | 100<sup>6</sup> (固定) |
| ポリシー | 1,000,000<sup>7</sup> |
| ファイル サイズ| シナリオによっては、Media Services での処理についてサポートされている最大ファイル サイズに制限があります。<sup>8</sup> |

<bpt id="p1"><sup></bpt>1<ept id="p1"></sup></ept> 種類を変更した場合 (S2 から S1 など)、最大予約ユニット数はリセットされます。

<bpt id="p1"><sup></bpt>2<ept id="p1"></sup></ept> この数には、キューに置かれたジョブ、終了したジョブ、アクティブなジョブ、および取り消されたジョブが含まれます。 削除されたジョブは含まれません。 <bpt id="p1">**</bpt>IJob.Delete<ept id="p1">**</ept> または <bpt id="p2">**</bpt>DELETE<ept id="p2">**</ept> HTTP 要求を使用して、古いジョブを削除できます。

2017 年 4 月 1 日以降、アカウント内の 90 日前より古いジョブ レコードすべてが、関連付けられているタスク レコードと共に自動的に削除されます。 レコードの合計数が最大クォータより小さい場合でも、自動的な削除が行われます。 ジョブとタスクの情報をアーカイブするには、<bpt id="p1">[</bpt>Media Services .NET SDK でのアセットの管理<ept id="p1">](../articles/media-services/previous/media-services-dotnet-manage-entities.md)</ept>に関する記事で説明されているコードを使用します。

<bpt id="p1"><sup></bpt>3<ept id="p1"></sup></ept> ジョブ エンティティの一覧を要求すると、要求ごとに最大 1,000 件のジョブが返されます。 送信したすべてのジョブを追跡するには、<bpt id="p1">[</bpt>OData システム クエリ オプション<ept id="p1">](/previous-versions/dynamicscrm-2015/developers-guide/gg309461(v=crm.7))</ept>に関する記事で説明されている top または skip クエリを使用します。

<bpt id="p1"><sup></bpt>4<ept id="p1"></sup></ept> ロケーターはユーザーごとのアクセス制御を管理するようには設計されていません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。 詳しくは、<bpt id="p1">[</bpt>Azure Media Services でのコンテンツの保護<ept id="p1">](../articles/media-services/latest/drm-content-protection-concept.md)</ept>に関する記事をご覧ください。

<sup>5</sup> 選択したストリーミング ユース ケースとリージョナル データセンターに基づき、AMS では、Media Services アカウントにつき 5 つを超えるライブ チャンネルに対応できます。 アカウント クォータを増やすには、サポート リクエストを提出してください。

<sup>6</sup> ストレージ アカウントは、同じ Azure サブスクリプションからのものである必要があります。

<sup>7</sup> さまざまな Media Services ポリシーには、1,000,000 ポリシーの制限があります。 たとえば、ロケーター ポリシーや ContentKeyAuthorizationPolicy です。 

>[!NOTE]
> 常に同じ日数およびアクセス許可を使用する場合は、同じポリシー ID を使用します。 説明と例については、<bpt id="p1">[</bpt>Media Services .NET SDK でのアセットの管理<ept id="p1">](../articles/media-services/previous/media-services-dotnet-manage-entities.md#limit-access-policies)</ept>に関する記事をご覧ください。

<sup>8</sup> Azure Blob Storage では現在、1 つの BLOB でサポートされる最大サイズは 5 TB です。 Media Services ではさらに、サービスで使用される VM サイズに基づく別の制限が適用されます。 サイズの上限は、アップロードするファイルのほかに、Media Services 処理 (エンコードまたは分析) の結果として生成されるファイルにも適用されます。 ソース ファイルが 260 GB を超える場合、Job は失敗する可能性があります。 

