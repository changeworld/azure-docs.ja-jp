---
title: 従来の予約済み IP アドレスをパブリック IP アドレスに移行する
titleSuffix: Azure Virtual Network
description: この記事では、クラシック デプロイ モデルの予約済み IP を Azure Resource Manager のパブリック IP アドレスにアップグレードする方法について説明します。
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 05/20/2021
ms.custom: template-how-to
ms.openlocfilehash: a07f18f0bb340b095f8b3d2b3392824e6a7bbf1d
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130251131"
---
# <a name="migrate-a-classic-reserved-ip-address-to-a-public-ip-address"></a>従来の予約済み IP アドレスをパブリック IP アドレスに移行する

Azure Resource Manager の新機能を活用するには、既存の静的パブリック IP アドレス (予約済み IP) をクラシック デプロイ モデルから Azure Resource Manager に移行します。  移行されたパブリック IP は、SKU の種類が Basic になります。 

この記事では、従来の予約済み IP を Basic パブリック IP アドレスにアップグレードする方法について説明します。

## <a name="prerequisites"></a>前提条件

* アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
* ご利用の Azure サブスクリプションが移行対象として登録されました。 詳細については、[PowerShell を使用した Resource Manager への移行](../../virtual-machines/migration-classic-resource-manager-ps.md)に関するページを参照してください。
* クラシック デプロイ モデルの予約済み IP アドレス。
* PowerShell 用にインストールされた Azure PowerShell Service Management モジュール。 詳細については、[Azure PowerShell Service Management モジュールのインストール](/powershell/azure/servicemanagement/install-azure-ps)に関するページをご覧ください。
* Azure CLI 命令用にインストールされた Azure クラシック CLI 詳しくは、「[Azure クラシック CLI のインストール](/cli/azure/install-classic-cli)」をご覧ください。

## <a name="azure-powershell-service-management-module"></a>Azure PowerShell Service Management モジュール

このセクションでは、Azure PowerShell Service Management モジュールを使用して、従来の予約済み IP を Azure Resource Manager の静的パブリック IP に移行します。

> [!NOTE]
> 予約済み IP は、IP アドレスが関連付けられているクラウド サービスから削除する必要があります。

```azurepowershell-interactive
$validate = Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Validate
$validate.ValidationMessages

```

前のコマンドを実行すると、移行をブロックする警告とエラーが表示されます。 検証に成功した場合は、移行を **準備** して **コミット** するために次の手順に進んでください。

```azurepowershell-interactive
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Prepare
Move-AzureReservedIP -ReservedIPName 'myReservedIP' -Commit
```
移行された予約済み IP の名前を使用して Azure Resource Manager に新しいリソース グループが作成されます。 上の例では、リソース グループは **myReservedIP-Migrated** です。

## <a name="azure-classic-cli"></a>Azure クラシック CLI

このセクションでは、Azure クラシック CLI を使用して、従来の予約済み IP を Azure Resource Manager の静的パブリック IP に移行します。

> [!NOTE]
> 予約済み IP は、IP アドレスが関連付けられているクラウド サービスから削除する必要があります。

```azurecli-interactive
azure network reserved-ip validate migration myReservedIP

```
前のコマンドを実行すると、移行をブロックする警告とエラーが表示されます。 検証に成功した場合は、移行を **準備** して **コミット** するために次の手順に進んでください。

```azurecli-interactive
azure network reserved-ip prepare-migration myReservedIP
azure network reserved-ip commit-migration myReservedIP
```
移行された予約済み IP の名前を使用して Azure Resource Manager に新しいリソース グループが作成されます。 上の例では、リソース グループは **myReservedIP-Migrated** です。

## <a name="next-steps"></a>次のステップ


Azure のパブリック IP アドレスの詳細については、次を参照してください。

- [Azure でのパブリック IP アドレス](public-ip-addresses.md)
- [パブリック IP の作成 - Azure portal](./create-public-ip-portal.md)