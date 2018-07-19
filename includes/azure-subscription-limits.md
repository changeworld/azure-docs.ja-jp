---
title: インクルード ファイル
description: インクルード ファイル
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 05/18/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: 3daf0e214c2c10d682e908ca430f4852df105926
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755871"
---
| Resource | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの vCPU <sup>1</sup> |20 |10,000 |
| [共同管理者](../articles/billing-add-change-azure-subscription-administrator.md) 数 |200 |200 |
| サブスクリプション<sup>2</sup>あたりのリージョンごとの[ストレージ アカウント](../articles/storage/common/storage-create-storage-account.md)数 |200 |250 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |20 |200 |
| [ローカル ネットワーク](http://msdn.microsoft.com/library/jj157100.aspx) 数 |10 |500 |
| サブスクリプションあたりの SQL Database サーバー数 |6 |200 |
| サブスクリプションあたりの DNS サーバー数 |9 |100 |
| サブスクリプションあたりの予約済み IP 数 |20 |100 |
| サブスクリプションあたりのホストされるサービスの証明書数 |199 |199 |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |256 |256 |


<sup>1</sup>XS インスタンスは、使用する CPU コア数が 1 コアに満たなくても、vCPU 制限を上限として 1 vCPU とカウントされます。

<sup>2</sup>ストレージ アカウントの制限には、Standard および Premium 両方のストレージ アカウントが含まれます。 指定したリージョンで 200 個を超えるストレージ アカウントが必要な場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)からリクエストを送信してください。 Azure Storage チームがビジネス ケースを確認します。承認された場合、指定したリージョンに対して最大 250 個のストレージ アカウントが与えられます。 

