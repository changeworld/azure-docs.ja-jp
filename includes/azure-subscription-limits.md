---
title: インクルード ファイル
description: インクルード ファイル
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 03/30/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 99766ca4cc9d77927030f81cff6bb9c009874f89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2018
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのコア数<sup>1</sup> |20 |10,000 |
| [共同管理者](../articles/billing-add-change-azure-subscription-administrator.md) 数 |200 |200 |
| サブスクリプション<sup>2</sup>あたりのリージョンごとの[ストレージ アカウント](../articles/storage/common/storage-create-storage-account.md)数 |200 |250 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |20 |200 |
| [ローカル ネットワーク](http://msdn.microsoft.com/library/jj157100.aspx) 数 |10 |500 |
| サブスクリプションあたりの SQL Database サーバー数 |6 |150 |
| サブスクリプションあたりの DNS サーバー数 |9 |100 |
| サブスクリプションあたりの予約済み IP 数 |20 |100 |
| サブスクリプションあたりのホストされるサービスの証明書数 |199 |199 |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |256 |256 |


<sup>1</sup>XS のインスタンスで、使用するコア数が 1 コアに満たなくても、コア制限を上限として 1 コアとカウントされます。

<sup>2</sup>ストレージ アカウントの制限には、Standard および Premium 両方のストレージ アカウントが含まれます。 単一リージョンで 200 個を超えるストレージ アカウントが必要な場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、最大 250 個のストレージ アカウントが与えられます。 

