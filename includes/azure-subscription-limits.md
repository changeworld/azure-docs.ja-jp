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
ms.openlocfilehash: be75b982d3e63ecb1edd7398e32150156a06995d
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/04/2018
ms.locfileid: "52886309"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの vCPU <sup>1</sup> |20 |10,000 |
| [共同管理者](../articles/billing-add-change-azure-subscription-administrator.md) 数 |200 |200 |
| サブスクリプションあたりの [Storage アカウント](../articles/storage/common/storage-create-storage-account.md) 数<sup>2</sup> |100 |100 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |20 |200 |
| [ローカル ネットワーク](https://msdn.microsoft.com/library/jj157100.aspx) 数 |10 |500 |
| サブスクリプションあたりの DNS サーバー数 |9 |100 |
| サブスクリプションあたりの予約済み IP 数 |20 |100 |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |256 |256 |


<sup>1</sup>XS インスタンスは、使用する CPU コア数が 1 コアに満たなくても、vCPU 制限を上限として 1 vCPU とカウントされます。

<sup>2</sup>ストレージ アカウントの制限には、Standard および Premium 両方のストレージ アカウントが含まれます。 

