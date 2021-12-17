---
title: 'クロステナント Vnet をハブに接続する: PowerShell'
titleSuffix: Azure Virtual WAN
description: この記事は、PowerShell を使用してクロステナント Vnet を仮想ハブに接続することに役立ちます。
services: virtual-wan
author: wtnlee
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/28/2020
ms.author: wellee
ms.openlocfilehash: dc065e68d513850f9e006371861d087cccc6e155
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669855"
---
# <a name="connect-cross-tenant-vnets-to-a-virtual-wan-hub"></a>クロステナント Vnet を Virtual WAN に接続する

この記事は、Virtual WAN を使用して VNet を別のテナントの仮想ハブに接続することに役立ちます。 このアーキテクチャは、同じネットワークに接続する必要があるのに異なるテナント上に置かれたクライアント ワークロードがある場合に便利です。 たとえば、次の図に示すように、Contoso 以外の VNet (リモート テナント) を Contoso の仮想ハブ (親テナント) に接続できます。

:::image type="content" source="./media/cross-tenant-vnet/connectivity.png" alt-text="ルーティング構成の設定" :::

この記事では、次のことについて説明します。

* Azure サブスクリプションの共同作成者として別のテナントを追加します。
* クロステナント VNet を仮想ハブに接続します。

この構成の手順は、Azure portal と PowerShell を組み合わせて使用して実行します。 ただし、機能自体は PowerShell と CLI でのみ提供されています。

>[!NOTE]
> テナント間の Virtual Network 接続は、PowerShell または CLI を使用してのみ管理できることに注意してください。 Azure portal では、テナント間の Virtual Network 接続を管理することは **できません**。
> 
## <a name="before-you-begin"></a>はじめに

### <a name="prerequisites"></a>前提条件

この記事の手順を使用するには、環境内に以下の構成が既に設定されている必要があります。

* 親サブスクリプション内の仮想 WAN と仮想ハブ。
* 別の (リモート) テナントのサブスクリプション内で構成されている仮想ネットワーク。
* リモート テナントの VNet アドレス空間が、親の仮想ハブに既に接続されている他の Vnet 内のどのアドレス空間とも重複していないことを確認してください。

### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="assign-permissions"></a><a name="rights"></a>アクセス許可の割り当て

仮想ハブを持つ親サブスクリプションを管理するユーザーが、リモート テナント内の仮想ネットワークを変更してアクセスできるようにするには、このユーザーに、**共同作成者** のアクセス許可を割り当てる必要があります。 このユーザーに **共同作成者** のアクセス許可を割り当てるには、リモート テナントの VNET のサブスクリプションで行います。

1. その管理者 (仮想 WAN ハブの管理に使用する管理者) に、**共同作成者** ロールの割り当てを追加します。 PowerShell または Azure portal のいずれかを使用して、このロールを割り当てることができます。 手順については以下の、**ロールの割り当てを追加または削除する** 方法に関する記事を参照してください。

   * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
   * [ポータル](../role-based-access-control/role-assignments-portal.md)

1. 次に、リモート テナント サブスクリプションと親テナント サブスクリプションを PowerShell の現在のセッションに追加します。 次のコマンドを実行します。 親にサインインしている場合は、リモート テナントに対してのみコマンドを実行する必要があります。

   ```azurepowershell-interactive
   Connect-AzAccount -SubscriptionId "[subscription ID]" -TenantId "[tenant ID]"
   ```

1. 親の資格情報を使用して Azure PowerShell にログインし、次のコマンドを実行することで、ロールの割り当てが成功したことを確認します。

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. アクセス許可が親に正しく伝達され、セッションに追加されている場合は、親の **と** リモート テナントが所有するサブスクリプションは、両方ともコマンドの出力に表示されます。

## <a name="connect-vnet-to-hub"></a><a name="connect"></a>VNet をハブに接続する

以下の手順では、仮想ネットワークを仮想ハブにリンクするときに、2 つのサブスクリプションのコンテキストを切り替えます。 例の値は、実際の環境が反映されるように置き換えてください。

1. 次のコマンドを実行して、リモート アカウントのコンテキストになっていることを確認します。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[remote ID]"
   ```

1. ハブに接続する仮想ネットワークのメタデータを格納するためのローカル変数を作成します。

   ```azurepowershell-interactive
   $remote = Get-AzVirtualNetwork -Name "[vnet name]" -ResourceGroupName "[resource group name]"
   ```

1. 切り替えて親アカウントに戻ります。

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionId "[parent ID]"
   ```

1. VNet をハブに接続します。

   ```azurepowershell-interactive
   New-AzVirtualHubVnetConnection -ResourceGroupName "[parent resource group name]" -VirtualHubName "[virtual hub name]" -Name "[name of connection]" -RemoteVirtualNetwork $remote
   ```

1. 新しい接続は、PowerShell または Azure portal のいずれかで表示できます。

   * **PowerShell:** 接続が正常に作成された場合は、新しく作成された接続のメタデータが PowerShell コンソールに表示されます。
   * **Azure portal:** 仮想ハブの **[接続] > [仮想ネットワーク接続]** に移動します。 接続へのポインターを表示できます。 実際のリソースを表示するには、適切なアクセス許可が必要です。
   
## <a name="troubleshooting"></a><a name="troubleshoot"></a>トラブルシューティング

* $remote (前の[セクション](#connect)のもの) 内のメタデータが、Azure portal の情報と一致していることを確認します。
* リモート テナント リソース グループの IAM 設定を使用するか、Azure PowerShell コマンド (Get-AzSubscription) を使用して、アクセス許可を確認できます。
* リソース グループの名前やその他の環境固有の変数の周囲が引用符で囲まれていることを確認します (例: "VirtualHub1" または "VirtualNetwork1")。

## <a name="next-steps"></a>次の手順

Virtual WAN の詳細については、次を参照してください。

* Virtual WAN の [FAQ](virtual-wan-faq.md)
