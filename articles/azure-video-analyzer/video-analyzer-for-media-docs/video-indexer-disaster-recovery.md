---
title: Azure Video Analyzer for Media (旧称 Video Indexer) のフェールオーバーとディザスター リカバリー
titleSuffix: Azure Media Services
description: 地域のデータセンターの障害や災害が発生した場合に、セカンダリ Azure Video Analyzer for Media (旧称 Video Indexer) アカウントにフェールオーバーする方法について説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: e3f1f09f58d79ce3927b137cae20db5e1d55af14
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386863"
---
# <a name="video-analyzer-for-media-failover-and-disaster-recovery"></a>Video Analyzer for Media のフェールオーバーとディザスター リカバリー

Azure Video Analyzer for Media (旧称 Video Indexer) では、地域のデータセンターの停止や障害が発生した場合、サービスの即時フェールオーバーは提供されません。 この記事では、アプリの最適な可用性と、災害が発生した場合の復旧時間の最小化を確実にするため、フェールオーバー用に環境を構成する方法について説明します。

リージョン ペアの間で事業継続とディザスター リカバリー (BCDR) を構成して、Azure の分離と可用性のポリシーを活用することをお勧めします。 詳細については、「[Azure のペアになっているリージョン](../../best-practices-availability-paired-regions.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプション。 Azure サブスクリプションがまだない場合は、[Azure 無料試用版](https://azure.microsoft.com/free/)にサインアップしてください。

## <a name="failover-to-a-secondary-account"></a>セカンダリ アカウントへのフェールオーバー

BCDR を実装するには、冗長性を扱うために 2 つの Video Analyzer for Media アカウントが必要です。

1. Azure に接続された Video Analyzer for Media アカウントを 2 つ作成します (「[Media アカウント用の Video Analyzer を作成する](connect-to-azure.md)」を参照してください)。 1 つはプライマリ リージョン用、もう 1 つはペアになっている Azure リージョン用にアカウント用に作成します。
1. プライマリ リージョンで障害が発生した場合は、セカンダリ アカウントを使用してインデックス作成に切り替えます。

> [!TIP]
> BCDR を自動化するには、「[サービス通知のアクティビティ ログ アラートを作成する」](../../service-health/alerts-activity-log-service-notifications-portal.md)に従って、サービス正常性通知のアクティビティ ログ アラートを設定します。

複数のテナントを使用する方法については、「[複数のテナントの管理](manage-multiple-tenants.md)」を参照してください。 BCDR を実装するには、「[テナントごとの Video Analyzer for Media アカウント](./manage-multiple-tenants.md#video-analyzer-for-media-account-per-tenant)」と「[テナントごとの Azure サブスクリプション](./manage-multiple-tenants.md#azure-subscription-per-tenant)」という 2 つの方法のいずれかを選択します。

## <a name="next-steps"></a>次の手順

[Azure に接続されている Video Analyzer for Media アカウントを管理します](manage-account-connected-to-azure.md)。
