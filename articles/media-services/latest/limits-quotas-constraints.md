---
title: Azure Media Services v3 のクォータと制限 | Microsoft Docs
description: このトピックでは、Azure Media Services v3 のクォータと制限について説明します
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/17/2018
ms.author: juliako
ms.openlocfilehash: b50ba825f675c84f551f9a1d191aa93eaed9a628
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070869"
---
# <a name="quotas-and-limitations-in-azure-media-services-v3"></a>Azure Media Services v3 のクォータと制限

この記事では、Azure Media Services v3 のクォータと制限について説明します

| Resource | 既定の制限 | 
| --- | --- | 
| Azure Media Services アカウントあたりの資産 | 1,000,000|
| 動的マニフェスト フィルター|100|
| Job あたりの JobInput | 50 (固定)|
| 変換での Job あたりの JobOutput/TransformOutput | 20 (固定) |
| JobInput あたりのファイル|10 (固定)|
| ファイル サイズ| シナリオによっては、Media Services での処理についてサポートされている最大ファイル サイズに制限があります。 <sup>(1)</sup> |
| Media Services アカウントあたりの Job | 500,000 <sup>(2)</sup> (固定)|
| 変換の一覧表示|応答の改ページ処理 (ページあたり 1,000 変換)|
| ジョブの一覧表示|応答の改ページ処理 (ページあたり 500 ジョブ)|
| Media Services アカウントあたりの LiveEvent |5|
| 1 つのサブスクリプション内の Media Services アカウント | 25 (固定) |
| LiveEvent あたりの実行状態にある LiveOutput |3|
| LiveEvent あたりの停止状態にある LiveOutput |50|
| ストレージ アカウント | 100<sup>(4)</sup> (固定) |
| Media Services アカウントあたりの実行状態にあるストリーミング エンドポイント|2|
| StreamingPolicy | 100 <sup>(3)</sup> |
| Media Services アカウントあたりの Transform | 100 (固定)|
| 1 つの資産に同時に関連付けられる一意 の StreamingLocator | 100<sup>(5)</sup> (固定) |

<sup>1</sup> Azure Blob Storage では現在、1 つの BLOB でサポートされる最大サイズは 5 TB です。 ただし、Azure Media Services ではさらに、サービスで使用される VM サイズに基づく別の制限が適用されます。 ソース ファイルが 260 GB を超える場合、Job は失敗する可能性があります。 260 GB の制限を超える 4K コンテンツがある場合は、必要なシナリオ実現に向けた状況の改善策について、amshelp@microsoft.com までお問い合わせください。

<sup>2</sup> この数には、キューに置かれた Job、終了した Job、アクティブな Job、および取り消された Job が含まれます。 削除された Job は含まれません。 

レコードの合計数が最大クォータより小さい場合でも、アカウント内の 90 日前より古いすべての Job レコードは自動的に削除されます。 

<sup>3</sup> カスタム [StreamingPolicy](https://docs.microsoft.com/rest/api/media/streamingpolicies) を使うときは、Media Service アカウントに対してこのようなポリシーの限られたセットを設計し、同じ暗号化オプションとプロトコルが必要なときは常に、お使いの StreamingLocator に対してそのセットを再利用する必要があります。 StreamingLocator ごとに新しい StreamingPolicy を作成しないでください。

<sup>4</sup> ストレージ アカウントは、同じ Azure サブスクリプションからのものである必要があります。

<sup>5</sup> StreamingLocator はユーザーごとのアクセス制御を管理するようには設計されていません。 個々のユーザーに異なるアクセス権限を付与するには、デジタル著作権管理 (DRM) ソリューションを使用します。

## <a name="support-ticket"></a>サポート チケット

固定されていないリソースについては、[サポート チケット](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開いてクォータの増加を依頼できます。 要求には、目的のクォータの変更、ユース ケース シナリオ、および必要なリージョンに関する詳細情報を含めてください。 <br/>上限を高くするために追加の Azure Media Services アカウントを作成することは**しないでください**。

## <a name="next-steps"></a>次の手順

[概要](media-services-overview.md)
