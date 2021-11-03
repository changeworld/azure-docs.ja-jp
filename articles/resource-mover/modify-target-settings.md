---
title: Azure Resource Mover を使用してリージョン間で Azure VM を移動するときに宛先の設定を変更する
description: Azure Resource Mover を使用してリージョン間で Azure VM を移動するときに宛先の設定を変更する方法について説明します。
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 54c2594e823e2e78f7a3fd62afba1d0d023ff347
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048123"
---
# <a name="modify-destination-settings"></a>宛先の設定を変更する

この記事では、[Azure Resource Mover](overview.md) を使用して Azure リージョン間でリソースを移動するときに宛先の設定を変更する方法について説明します。


## <a name="modify-vm-settings"></a>VM 設定の変更

Azure VM と関連リソースを移動するときに、宛先の設定を変更できます。 以下のことが推奨されます。

- 宛先の設定は、移動コレクションが検証された後にのみ変更する。 ただし、
    - ソース リソースを移動する場合は、通常、移動の開始プロセスを開始するまで、これらの設定を変更できます。
    - ソース リージョンに既存のリソースを割り当てる場合は、移動コミットが完了するまで宛先の設定を変更できます。
- リソースを準備する前に設定を変更する。準備が完了した後、一部の宛先プロパティは編集できない場合があるためです。

### <a name="settings-you-can-modify"></a>変更できる設定

変更できる構成設定は、表にまとめられています。

**リソース** | **[オプション]** 
--- | --- 
**VM 名** | 宛先のオプション:<br/><br/> - 宛先リージョンに同じ名前で新しい VM を作成します。<br/><br/> - 宛先リージョンに別の名前で新しい VM を作成します。<br/><br/> - 宛先リージョンの既存の VM を使用します。<br/><br/> 新しい VM を作成すると、変更した設定を除いて、新しい宛先 VM にはソースと同じ設定が割り当てられます。
**VM 可用性ゾーン** | 宛先 VM が配置される可用性ゾーン。 ソース設定を変更したくない場合や、VM を可用性ゾーンに配置したくない場合は、 **[適用しない]** を選択します。
**VM の SKU** | 宛先 VM に使用される (宛先リージョンで利用可能な) [VM の種類](https://azure.microsoft.com/pricing/details/virtual-machines/series/)。<br/><br/> 選択した宛先 VM は、ソース VM より小さいものにはできません。
**VM 可用性セット** | 宛先 VM が配置される可用性セット。 ソース設定を変更したくない場合や、VM を可用性セットに配置したくない場合は、 **[適用しない]** を選択します。
**VM キー コンテナー** | VM で Azure Disk Encryption を有効にした場合に関連付けられるキー コンテナー。
**ディスク暗号化セット** | VM でサーバー側の暗号化にカスタマー マネージド キーを使用する場合に関連付けられるディスク暗号化セット。
**リソース グループ** | 宛先 VM が配置されるリソース グループ。
**ネットワーク リソース** | ネットワーク インターフェイス、仮想ネットワーク (VNets/)、ネットワーク セキュリティ グループ/ネットワーク インターフェイスのオプション:<br/><br/> - 宛先リージョンに同じ名前で新しいリソースを作成します。<br/><br/> - 宛先リージョンに別の名前で新しいリソースを作成します。<br/><br/> - 宛先リージョンの既存のネットワーク リソースを使用します。<br/><br/> 新しい宛先リソースを作成すると、変更した設定を除いて、ソース リソースと同じ設定がそれに割り当てられます。
**パブリック IP アドレス名、SKU、ゾーン** | 標準パブリック IP アドレスの名前、[SKU](../virtual-network/ip-services/public-ip-addresses.md#sku)、[ゾーン](../virtual-network/ip-services/public-ip-addresses.md#standard)を指定します。<br/><br/> ゾーン冗長にする場合は、「**Zone redundant**」と入力します。
**ロード バランサー名、SKU、ゾーン** | ロード バランサーの名前、SKU (Basic または Standard)、ゾーンを指定します。<br/><br/> Standard SKU を使用することをお勧めします。<br/><br/> ゾーン冗長にする場合は、 **[ゾーン冗長]** を指定します。
**リソースの依存関係** | 各依存関係のオプション:<br/><br/>- リソースでは、ソースの依存リソース (これらは宛先リージョンに移動されます) が使用されます。<br/><br/> - リソースでは、宛先リージョンにあるさまざまな依存リソースが使用されます。 この場合は、宛先リージョンの同様のリソースから選択できます。

### <a name="edit-vm-destination-settings"></a>VM の宛先の設定を編集する

ソース リージョンから宛先に依存リソースを移動したくない場合は、他にもオプションがいくつかあります。

- 宛先リージョンに新しいリソースを作成します。 別の設定を指定しない限り、新しいリソースの設定はソース リソースと同じになります。
- 宛先リージョンの既存のリソースを使用します。

正確な動作は、リソースの種類によって異なります。 宛先の設定の変更について[詳細を確認してください](modify-target-settings.md)。

リソースの宛先の設定を変更するには、リソース移動コレクションの **[Destination configuration]\(宛先の構成\)** エントリを使用します。 

設定を変更するには: 

1. **[Across regions]\(複数のリージョン間\)** ページ > **[Destination configuration]\(宛先の構成\)** 列で、リソース エントリのリンクをクリックします。
2. **[構成設定]** で、宛先リージョンに新しい VM を作成できます。
3. 新しい可用性ゾーン、可用性セット、または SKU を宛先 VM に割り当てます。 **可用性ゾーン** と **SKU**。

変更は、編集しているリソースに対してのみ行われます。 依存リソースはすべて、個別に更新する必要があります。


## <a name="modify-sql-settings"></a>SQL 設定の変更

Azure SQL Database リソースを移動するときに、移動の宛先設定を変更できます。 

- SQL データベースの場合:
    - それらの移動の準備を行う前に、宛先の構成設定を変更することをお勧めします。
    - 宛先データベースと、そのデータベースのゾーン冗長の設定を変更できます。
- エラスティック プールの場合:
    -  移動を開始する前であればいつでも、宛先の構成を変更できます。
    - 宛先エラスティック プールと、そのプールのゾーン冗長を変更できます。 

### <a name="sql-settings-you-can-modify"></a>変更できる SQL の設定

**設定** | **SQL データベース** | **エラスティック プール**
--- | --- | ---
**名前** | 宛先リージョンに同じ名前で新しいデータベースを作成します。<br/><br/> 宛先リージョンに別の名前で新しいデータベースを作成します。<br/><br/> 宛先リージョンの既存のデータベースを使用します。 | 宛先リージョンに同じ名前で新しいエラスティック プールを作成します。<br/><br/> 宛先リージョンに別の名前で新しいエラスティック プールを作成します。<br/><br/> 宛先リージョンの既存のエラスティック プールを使用します。
**ゾーン冗長性** | ゾーン冗長性をサポートしているリージョンからそうではないリージョンに移動するには、ゾーン設定で「**Disable**」と入力します。<br/><br/> ゾーン冗長性をサポートしていないリージョンからサポートしているリージョンに移動するには、ゾーン設定で「**Enable**」と入力します。 | ゾーン冗長性をサポートしているリージョンからそうではないリージョンに移動するには、ゾーン設定で「**Disable**」と入力します。<br/><br/> ゾーン冗長性をサポートしていないリージョンからサポートしているリージョンに移動するには、ゾーン設定で「**Enable**」と入力します。

### <a name="edit-sql-destination-settings"></a>SQL の宛先の設定を編集する

Azure SQL Database リソースの宛先の設定を変更するには、次のようにします。 

1. **[Across regions]\(複数のリージョン間\)** で、変更するリソースの **[Destination configuration]\(宛先の構成\)** エントリをクリックします。
2. **[構成設定]** で、上記の表にまとめられている宛先設定を指定します。


## <a name="modify-settings-in-powershell"></a>PowerShell 内で設定を変更する

PowerShell 内で設定を変更できます。

1)  プロパティを編集する移動リソースを取得します。 たとえば、VM 実行を取得するには、次のようにします。

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  リソース設定をターゲット リソース設定オブジェクトにコピーします。

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  ターゲット リソース設定オブジェクト内でパラメーターを設定します。 たとえば、宛先 VM の名前を変更するには、次のようにします。

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  移動リソースの宛先の設定を更新します。 この例では、VM の名前を *PSDemoVM* から *PSDemoVMTarget* に変更します。

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    **出力**
    ![宛先の設定を変更した後の出力](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>次の手順

[Azure VM を別のリージョンに移動する](tutorial-move-region-virtual-machines.md)。