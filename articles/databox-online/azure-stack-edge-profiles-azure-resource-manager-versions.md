---
title: Azure Stack Edge のリソース プロバイダー API プロファイルのバージョン | Microsoft Docs
description: Azure Stack Edge のプロファイルでサポートされている Azure Resource Manager API のバージョンについて説明します。
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 05/17/2021
ms.author: alkohli
ms.openlocfilehash: 485b1f8dccf104a9d31abb9aa79fd1ea4eb3940a
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111965353"
---
# <a name="resource-provider-api-profile-versions-for-azure-stack-edge"></a>Azure Stack Edge のリソース プロバイダー API プロファイルのバージョン


[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

この記事では、Azure Stack Edge で使用される各 API プロファイルのリソース プロバイダーとバージョン番号を確認できます。 この記事の表には、各リソース プロバイダーでサポートされているバージョンと、プロファイルの API バージョンが一覧表示されています。 各リソース プロバイダーには、一連リソースの種類と特定のバージョン番号が含まれています。

API プロファイルでは、次の 3 つの名前付け規則を使用します。

- **latest**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**


## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>2019-03-01 ハイブリッド プロファイルの概要

|リソース プロバイダー                                   |API バージョン |
|----------------------------------------------------|------------|
|Microsoft.Compute                                   |2017-12-01  |
|Microsoft.Network                                   |2017-10-01<br>VPN Gateway は 2017-10-01 |
|Microsoft.Storage (データ プレーン)                      |2019-07-07  |
|Microsoft.Storage (コントロール プレーン)                   |2019-06-01  |
|Microsoft.Resources (Azure Resource Manager 自体) |2020-06-01  |
|Microsoft.Authorization (ポリシーの操作)         |2016-09-01  |

API プロファイルのプロバイダー用リソースの種類ごとのバージョン一覧については、「[2019-03-01 ハイブリッド プロファイルの詳細](/azure-stack/user/azure-stack-profiles-azure-resource-manager-versions?view=azs-2008&preserve-view=true#details-for-the-2019-03-01-hybrid-profile)」を参照してください。

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>2019-03-01 ハイブリッド プロファイルの詳細

### <a name="microsoftcompute"></a>Microsoft.Compute

Azure コンピューティング API によって、仮想マシンおよび仮想マシンでサポートされるリソースに、プログラムからアクセスできるようになります。 詳細については、[Azure Compute](/rest/api/compute/)」 (Azure コンピューティング) をご覧ください。

|リソースの種類               |API バージョン |
|----------------------------|------------|
|場所                   |2017-12-01  |
|場所/vm サイズ           |2017-12-01  |
|Virtual Machines            |2017-12-01  |
|仮想マシン/拡張機能 |2017-12-01  |

### <a name="microsoftnetwork"></a>Microsoft.Network

操作の呼び出し結果は、使用可能なネットワーク クラウド操作のリストを表しています。 詳細については、「[Operation REST API](/rest/api/operation/)」 (操作の REST API) をご覧ください。

|リソースの種類     |API のバージョン|
|-------------------|------------|
|ネットワーク インターフェイス |2017-10-01  |
|仮想ネットワーク   |2017-10-01  |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager を使用して、Azure ソリューション用のインフラストラクチャをデプロイおよび管理することができます。 関連するリソースはリソース グループにまとめ、JSON テンプレートを使って必要なリソースをデプロイします。 Resource Manager によるリソースのデプロイと管理の概要については、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」をご覧ください。

|リソースの種類                        |API のバージョン|
|--------------------------------------|------------|
|デプロイ                           |2020-06-01  |
|デプロイ/操作                |2020-06-01  |
|リンク                                 |2020-06-01  |
|場所                             |2020-06-01  |
|操作                            |2020-06-01  |
|プロバイダー                             |2020-06-01  |
|ResourceGroups                        |2020-06-01  |
|リソース                             |2020-06-01  |
|サブスクリプション                         |2018-09-01  |
|Subscriptions/locations               |2018-09-01  |
|Subscriptions/operationresults        |2020-06-01  |
|サブスクリプション/プロバイダー               |2020-06-01  |
|Subscriptions/ResourceGroups          |2020-06-01  |
|サブスクリプション/リソース グループ/リソース|2020-06-01  |
|サブスクリプション/リソース               |2020-06-01  |
|サブスクリプション/タグ名                |2020-06-01  |
|サブスクリプション/タグ名/タグの値      |2020-06-01  |
|テナント                               |2018-09-01  |

### <a name="microsoftstorage"></a>Microsoft.Storage

ストレージ リソース プロバイダー (SRP) では、ストレージ アカウントとキーをプログラムで管理することができます。 詳細については、「[Azure Storage Resource Provider REST API Reference](/rest/api/storagerp/)」(Azure ストレージ リソース プロバイダー REST API リファレンス) をご覧ください。

|リソースの種類       |API のバージョン|
|---------------------|------------|
|CheckNameAvailability|2019-06-01  |
|場所            |2019-06-01  |
|場所/クォータ     |2019-06-01  |
|操作           |2019-06-01  |
|ストレージ アカウント      |2019-06-01  |
|使用法               |2019-06-01  |

## <a name="next-steps"></a>次のステップ

- [Windows PowerShell を使用して Azure Stack Edge Pro GPU デバイスを管理する](azure-stack-edge-gpu-connect-powershell-interface.md)