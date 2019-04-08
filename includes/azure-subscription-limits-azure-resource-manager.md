---
title: インクルード ファイル
description: インクルード ファイル
services: billing
author: rothja
ms.service: billing
ms.topic: include
ms.date: 10/19/2018
ms.author: jroth
ms.custom: include file
ms.openlocfilehash: a1645a8471f75f1d56f3e61c0adfc3fadee14560
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554015"
---
| リソース | 既定の制限 | 上限 |
| --- | --- | --- |
|  [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md) |リージョンあたり 25,000<sup>1</sup>。 |リージョンあたり 25,000。 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりの VM の合計コア数 |リージョンあたり 20<sup>1</sup>。 | サポートにお問い合せください。 |
| [サブスクリプション](../articles/billing-buy-sign-up-azure-subscription.md)あたりのシリーズ (Dv2 や F など) ごとの VM のコア数 |リージョンあたり 20<sup>1</sup>。 | サポートにお問い合せください。 |
| サブスクリプションあたりの[共同管理者](../articles/billing-add-change-azure-subscription-administrator.md)数 |無制限。 |無制限。 |
| サブスクリプションあたりのリージョンごとの[ストレージ アカウント数](../articles/storage/common/storage-quickstart-create-account.md) |200 |200<sup>2</sup> |
| サブスクリプションあたりの[リソース グループ数](../articles/azure-resource-manager/resource-group-overview.md) |980 |980 |
| サブスクリプションあたりの[可用性セット](../articles/virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy)数 |リージョンあたり 2,000。 |リージョンあたり 2,000。 |
| Azure Resource Manager API 要求サイズ |4,194,304 バイト。 |4,194,304 バイト。 |
| サブスクリプションあたりのタグ数<sup>3</sup> |無制限。 |無制限。 |
| サブスクリプションあたりの一意のタグの計算<sup>3</sup> | 10,000 | 10,000 |
| [クラウド サービス](../articles/cloud-services/cloud-services-choose-me.md) 数 |N/A<sup>4</sup> |N/A<sup>4</sup> |
| [アフィニティ グループ](../articles/virtual-network/virtual-networks-migrate-to-regional-vnet.md) 数 |N/A<sup>4</sup> |N/A<sup>4</sup> |
| 場所あたりの[サブスクリプション レベルのデプロイ](../articles/azure-resource-manager/deploy-to-subscription.md)数 | 800 | 800 |

<sup>1</sup>既定の制限は、オファー カテゴリの種類 (無料試用版や従量課金制など) とシリーズ (Dv2、F、G など) によって異なります。

<sup>2</sup>Standard ストレージ アカウントと Premium ストレージ アカウントの両方が含まれます。 必要なストレージ アカウントが 200 個を超える場合は、[Azure サポート](https://azure.microsoft.com/support/faq/)からリクエストを送信してください。 Azure Storage チームがお客様のビジネス ケースを確認します。承認された場合、最大 250 個のストレージ アカウントが与えられます。

<sup>3</sup>1 つのサブスクリプションで適用できるタグの数に制限はありません。 リソースまたはリソース グループあたりのタグの数は 15 に制限されています。 サブスクリプションに存在する[一意のタグ名と値のリスト](/rest/api/resources/tags)が Resource Manager から返されるのは、タグの数が 10,000 以下の場合に限られます。 タグの数が 10,000 を超える場合でも、タグでリソースを検出することはできます。  

<sup>4</sup>これらの機能は、Azure リソース グループと Resource Manager では必要なくなりました。

> [!NOTE]
> 仮想マシンのコア数には、リージョン別総数の制限があります。 さらに、リージョンごとにサイズ シリーズ (Dv2 や F など) あたりの制限もあります。これらの制限は別々に適用されます。 たとえば、米国東部で VM のコア上限が 30、A シリーズのコア上限が 30、D シリーズのコア上限が 30 のサブスクリプションがあるとします。 このサブスクリプションでは、30 個の A1 VM、または 30 台の D1 VM、または合計 30 個のコア数を超えないようにこれらの 2 つを組み合わせでデプロイすることができます。 組み合わせの例としては、10 個の A1 VM と 20 個の D1 VM が考えられます。  
> <!-- -->
> 
> 

