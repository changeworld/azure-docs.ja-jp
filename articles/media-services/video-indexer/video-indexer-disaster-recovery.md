---
title: Video Indexer の事業継続とディザスター リカバリー - Azure
description: 地域のデータセンターの停止や障害が発生した場合に、セカンダリ Video Indexer アカウントにフェールオーバーする方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 2f54c340226a9ea78643df8e0a984c8ed8475c94
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513577"
---
# <a name="handle-video-indexer-business-continuity-and-disaster-recovery"></a>Video Indexer の事業継続とディザスター リカバリーを処理する

Azure Media Services Video Indexer では、地域のデータセンターの停止や障害が発生した場合、サービスの即時フェールオーバーは提供されません。 この記事では、アプリケーションの最適な可用性と、災害が発生した場合の復旧時間の最小化を実現できるように、フェールオーバー用に環境を構成する方法について説明します。

リージョン ペアの間で事業継続とディザスター リカバリー (BCDR) を構成して、Azure の分離と可用性のポリシーを活用することをお勧めします。 詳細については、「[Azure のペアになっているリージョン](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)」をご覧ください。

## <a name="prerequisites"></a>前提条件 

Azure サブスクリプション。 Azure サブスクリプションがまだない場合は、[Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップしてください。

## <a name="failover-to-a-secondary-account"></a>セカンダリ アカウントへのフェールオーバー

BCDR を実装するには、冗長性を扱うために 2 つの Video Indexer アカウントが必要です。

1. Azure に接続された 2 つの Video Indexer アカウントを作成します ([アカウントの作成](connect-to-azure.md)に関する記事を参照)。 1 つはプライマリ リージョン用で、もう 1 つはペアになっている Azure リージョン用です。 
1. プライマリ リージョンで障害が発生した場合は、セカンダリ アカウントを使用してインデックス作成に切り替えます。

> [!TIP]
> BCDR を自動化するには、「[サービス通知のアクティビティ ログ アラートを作成する」](../../service-health/alerts-activity-log-service-notifications.md)に従って、サービス正常性通知のアクティビティ ログ アラートを設定します。

複数のテナントを使用する方法については、「[複数のテナントの管理](manage-multiple-tenants.md)」を参照してください。 BCDR を実装するには、次の 2 つのオプションのいずれかを選択します。[テナントごとの Video Indexer アカウント](manage-multiple-tenants.md#video-indexer-account-per-tenant)または[テナントごとの Azure サブスクリプション](manage-multiple-tenants.md#azure-subscription-per-tenant)。

## <a name="next-steps"></a>次のステップ

[Azure に接続された Video Indexer アカウントを管理する](manage-account-connected-to-azure.md)。
