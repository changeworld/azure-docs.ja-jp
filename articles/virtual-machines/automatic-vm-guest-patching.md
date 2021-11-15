---
title: Azure VM での VM ゲストの自動パッチ適用
description: Azure の仮想マシンに自動的にパッチを適用する方法について説明します。
author: mayanknayar
ms.service: virtual-machines
ms.subservice: maintenance
ms.workload: infrastructure
ms.topic: how-to
ms.date: 10/20/2021
ms.author: manayar
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 91d172cf1d3ba5bf78feb8e18382631464619c04
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459731"
---
# <a name="automatic-vm-guest-patching-for-azure-vms"></a>Azure VM での VM ゲストの自動パッチ適用

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット

Azure VM での VM ゲストの自動パッチ適用を有効にすると、安全かつ自動的に仮想マシンにパッチを適用することで、容易に更新を管理し、セキュリティ コンプライアンスを維持することができます。

VM ゲストの自動パッチ適用には、次の特性があります。
- *[重大]* または *[セキュリティ]* に分類されているパッチは、自動的にダウンロードされ、VM に適用されます。
- パッチは、VM のタイムゾーンでピーク外の時間帯に適用されます。
- パッチ オーケストレーションが Azure によって管理され、[可用性優先の原則](#availability-first-updates)に従ってパッチが適用されます。
- プラットフォーム正常性シグナルによって特定された仮想マシンの正常性は、パッチ適用の失敗を検出するために監視されます。
- すべての VM サイズで機能します。

## <a name="how-does-automatic-vm-guest-patching-work"></a>VM ゲストの自動パッチ適用はどのように機能しますか?

VM で VM ゲストの自動パッチ適用が有効になっている場合、使用可能な *[重大]* および *[セキュリティ]* パッチが自動的にダウンロードされて VM に適用されます。 このプロセスは、毎月新しいパッチがリリースされるたびに自動的に開始されます。 パッチの評価とインストールは自動的に実行され、このプロセスでは必要に応じて VM の再起動が行われます。

VM は、その VM に適用可能なパッチを確認するため、数日ごと、または 30 日以内に複数回、定期的に評価されます。 パッチは、VM のピーク外の時間帯であればいつでも VM にインストールされる可能性があります。 この自動評価により、不足しているパッチができるだけ早く検出されるようになります。

次に説明する可用性優先のオーケストレーションに従って、パッチは毎月のパッチ リリースから 30 日以内にインストールされます。 パッチは、VM のタイムゾーンに応じて、VM のピーク外の時間帯にのみインストールされます。 パッチが自動的にインストールされるようにするには、ピーク外の時間帯に VM が実行されている必要があります。 定期的な評価の際に VM の電源がオフになっている場合、VM の電源がオンになっている次の定期的な評価中 (通常は数日以内) に VM が自動的に評価され、該当するパッチが自動的にインストールされます。

"*重大*" または "*セキュリティ*" として分類されていない定義の更新プログラムや他のパッチは、VM ゲストの自動パッチ適用ではインストールされません。 他のパッチの分類が適用されたパッチをインストールしたり、独自のカスタム メンテナンス期間内でパッチをインストールするようスケジュールしたりするには、[Update Management](./windows/tutorial-config-management.md#manage-windows-updates) を使用します。

### <a name="availability-first-updates"></a>可用性優先の更新

パッチのインストール プロセスは、VM ゲストの自動パッチ適用が有効になっているすべての VM で、Azure によってグローバルにオーケストレーションされています。 このオーケストレーションは、Azure で提供されるさまざまなレベルの可用性において、可用性優先の原則に従います。

更新中の仮想マシンのグループについては、Azure プラットフォームで更新がオーケストレーションされます。

**複数のリージョン間:**
- 毎月の更新プログラムは、グローバル展開の失敗を防ぐために、Azure 全体で段階的にオーケストレーションされます。
- 1 つのフェーズには 1 つ以上のリージョンを含めることができ、更新は、フェーズ内の対象となる VM が正常に更新された場合にのみ、次のフェーズに移動します。
- geo ペア リージョンは同時に更新されず、同じリージョンのフェーズに置くことはできません。
- 更新の成功は、更新後に VM の正常性を追跡することによって測定できます。 VM の正常性は、VM のプラットフォーム正常性インジケーターによって追跡されます。

**リージョン内:**
- 異なる Availability Zones にある VM が、同じ更新で同時に更新されることはありません。
- 可用性セットの一部ではない VM は、サブスクリプション内のすべての VM が同時に更新されることを回避するために、ベスト エフォート方式でバッチ処理されます。

**可用性セット内:**
- 共通の可用性セット内のすべての VM が同時に更新されることはありません。
-   共通の可用性セット内の VM は更新ドメインの境界内で更新され、複数の更新ドメインにまたがる VM は同時に更新されません。

特定の VM のパッチのインストール日は、月ごとに異なる可能性があります。これは、特定の VM が毎月のパッチ適用サイクルの異なるバッチで選択される可能性があるためです。

### <a name="which-patches-are-installed"></a>インストールされるパッチ
インストールされるパッチは、VM のロールアウト ステージによって異なります。 毎月、新しいグローバル ロールアウトが開始され、個々の VM に対して評価されたすべてのセキュリティおよび重大パッチが、その VM 用にインストールされます。 ロールアウトは、バッチ内のすべての Azure リージョン間でオーケストレーションされます (可用性優先のパッチ適用に関する前のセクションを参照)。

インストールされるパッチの正確なセットは、OS の種類や評価のタイミングなどの VM の構成によって異なります。 パッチ オーケストレーションが異なるタイミングで異なるリージョンに達すると、程度の差はあってもパッチが利用可能な場合、異なるリージョンにある 2 つの同一の VM に異なるパッチがインストールされる可能性があります。 頻度は低くなりますが、同じように、同じリージョン内の VM であっても、(可用性ゾーンや可用性セットのバッチが異なるために) 評価されるタイミングが異なると、異なるパッチを適用される可能性があります。

VM ゲストの自動パッチ適用でパッチのソースは構成されないため、2 つの類似した VM に異なるパッチ ソースが構成されていると (パブリック リポジトリとプライベート リポジトリなど)、やはりインストールされるパッチの正確なセットが異なることがあります。

一定の周期でパッチがリリースされる OS の種類の場合、OS のパブリック リポジトリに構成されている VM は、ある月の異なるロールアウト フェーズで同じパッチのセットを受け取ることが予想されます。 たとえば、パブリック Windows Update リポジトリに構成されている Windows VM などです。

毎月新しいロールアウトがトリガーされるので、オフピーク時間帯に VM の電源がオンになっている場合、VM は毎月少なくとも 1 つのパッチ ロールアウトを受け取ります。 このプロセスにより、VM には、使用可能な最新のセキュリティおよび重大パッチが毎月適用されます。 インストールされるパッチのセットの整合性を保証するには、独自のプライベート リポジトリにあるパッチを評価してダウンロードするように、VM を構成することができます。

## <a name="supported-os-images"></a>サポート対象の OS イメージ
現在、特定の OS プラットフォーム イメージから作成された VM のみがサポートされています。 カスタム イメージは現在サポートされていません。

現時点では、以下のプラットフォーム SKU がサポートされています (定期的に追加されます)。

| Publisher               | OS 製品      |  Sku               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| 正規  | 0001-com-ubuntu-pro-bionic | pro-18_04-lts |
| 正規  | 0001-com-ubuntu-server-focal | 20_04-lts |
| 正規  | 0001-com-ubuntu-pro-focal | pro-20_04-lts |
| Redhat  | RHEL | 7.2、7.3、7.4、7.5、7.6、7.7、7.8、7_9、7-RAW、7-LVM |
| Redhat  | RHEL | 8、8.1、8.2、8_3、8_4、8-LVM |
| Redhat  | RHEL-RAW | 8-raw |
| OpenLogic  | CentOS | 7.2、7.3、7.4、7.5、7.6、7.7、7_8、7_9、7-LVM |
| OpenLogic  | CentOS | 8.0、8_1、8_2、8_3、8-lvm |
| SUSE  | sles-12-sp5 | gen1、gen2 |
| SUSE  | sles-15-sp2 | gen1、gen2 |
| MicrosoftWindowsServer  | WindowsServer | 2008-R2-SP1 |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

> [!NOTE]
>VM ゲストの自動パッチ適用、オンデマンドのパッチ評価、およびオンデマンドのパッチのインストールは、サポート対象の OS イメージ一覧に記載された発行元、製品、sku の正確な組み合わせのイメージから作成された VM 上でのみサポートされます。 カスタム イメージまたはその他の発行元、製品、sku の組み合わせはサポートされていません。

## <a name="patch-orchestration-modes"></a>パッチ オーケストレーションのモード
Azure 上の VM により、次のパッチ オーケストレーション モードがサポートされるようになりました。

**AutomaticByPlatform:**
- このモードは、Linux と Windows の両方の VM でサポートされています。
- このモードを使用すると、仮想マシンで VM ゲストの自動パッチ適用が有効になり、それ以降のパッチのインストールは Azure によってオーケストレーションされます。
- このモードは、可用性優先のパッチを適用するために必要です。
- このモードは、上記のサポートされている OS プラットフォーム イメージを使用して作成された VM でのみサポートされています。
- Windows VM の場合、このモードを設定すると、重複を避けるために、Windows 仮想マシンのネイティブ自動更新も無効になります。
- Linux VM でこのモードを使用するには、VM テンプレートでプロパティ `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` を設定します。
- Windows VM でこのモードを使用するには、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` を設定します。

**AutomaticByOS:**
- このモードは、Windows VM についてのみサポートされます。
- このモードでは、Windows 仮想マシンで自動更新が有効になり、自動更新によってパッチが VM にインストールされます。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- Windows VM で他のパッチ モードが指定されていない場合は、このモードが既定で設定されます。
- Windows VM でこのモードを使用するには、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates=true` を設定し、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` を設定します。

**Manual:**
- このモードは、Windows VM についてのみサポートされます。
- このモードでは、Windows 仮想マシンの自動更新が無効になります。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- カスタム パッチ適用ソリューションを使用する場合は、このモードに設定する必要があります。
- Windows VM でこのモードを使用するには、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates=false` を設定し、VM テンプレートでプロパティ `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` を設定します。

**ImageDefault:**
- このモードは、Linux VM についてのみサポートされます。
- このモードは、可用性優先のパッチ適用をサポートしていません。
- このモードを指定すると、VM の作成に使用されたイメージでの既定のパッチ構成が使用されます。
- Linux VM で他のパッチ モードが指定されていない場合は、このモードが既定で設定されます。
- Linux VM でこのモードを使用するには、VM テンプレートでプロパティ `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` を設定します。

> [!NOTE]
>Windows VM の場合、プロパティ `osProfile.windowsConfiguration.enableAutomaticUpdates` は VM の最初の作成時にのみ設定できます。 これは、特定のパッチ モードの切り替えに影響します。 AutomaticByPlatform モードと Manual モードの切り替えは、`osProfile.windowsConfiguration.enableAutomaticUpdates=false` である VM でサポートされています。 同様に、AutomaticByPlatform モードと AutomaticByOS モードの切り替えは、`osProfile.windowsConfiguration.enableAutomaticUpdates=true` である VM でサポートされています。 AutomaticByOS モードと Manual モードの切り替えはサポートされていません。

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>VM ゲストの自動パッチ適用を有効にするための要件

- 仮想マシンには、[Windows](./extensions/agent-windows.md) または [Linux](./extensions/agent-linux.md) 用の Azure VM エージェントがインストールされている必要があります。
- Linux VM の場合、Azure Linux エージェントはバージョン 2.2.53.1 以降である必要があります。 現在のバージョンが必要なバージョンより古い場合は、[Linux エージェントを更新します](./extensions/update-linux-agent.md)。
- Windows VM の場合、Windows Update サービスが仮想マシンで実行されている必要があります。
- 仮想マシンから構成されている更新エンドポイントにアクセスできる必要があります。 Linux 用のプライベート リポジトリまたは Windows VM 用の Windows Server Update Services (WSUS) を使用するように仮想マシンが構成されている場合は、関連する更新エンドポイントにアクセスできる必要があります。
- オンデマンド評価やオンデマンドのパッチ適用などのすべての機能にアクセスするために、Compute API バージョン 2021-03-01 以降を使用してください。
- カスタム イメージは現時点ではサポートされていません。

## <a name="enable-automatic-vm-guest-patching"></a>VM ゲストの自動パッチ適用を有効化する
VM ゲストの自動パッチ適用は、サポート対象のプラットフォーム イメージから作成された任意の Windows または Linux VM で有効にすることができます。 Windows VM で VM ゲストの自動パッチ適用を有効にするには、プロパティ *osProfile.windowsConfiguration.enableAutomaticUpdates* が VM テンプレート定義で *true* に設定されていることを確認します。 このプロパティは、VM の作成時にのみ設定できます。 この追加のプロパティは、Linux VM には適用されません。

### <a name="rest-api-for-linux-vms"></a>Linux VM 用の REST API
次の例では、VM ゲストの自動パッチ適用を有効にする方法について説明します。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Windows VM 用の REST API
次の例では、VM ゲストの自動パッチ適用を有効にする方法について説明します。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "location": "<location>",
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Windows VM 用 Azure PowerShell
VM を作成または更新する際に VM ゲストの自動パッチ適用を有効にするには、[Set-AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) コマンドレットを使用します。

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Windows VM 用の Azure CLI
新しい VM を作成するときに VM ゲストの自動パッチ適用を有効にするには、[az vm create](/cli/azure/vm#az_vm_create) を使用します。 次の例では、*myResourceGroup* という名前のリソース グループ内の *myVM* という名前の VM に対して、VM ゲストの自動パッチ適用を構成します。

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

既存の VM を変更するには、[az vm update](/cli/azure/vm#az_vm_update) を使用します

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>有効化と評価

> [!NOTE]
>VM で VM のゲストの自動更新を有効にするには 3 時間以上かかることがあります。これは、有効化が VM のピーク外の時間帯に行われるためです。 評価とパッチのインストールは、ピーク外の時間帯にのみ行われるため、パッチを適用するには、ピーク外の時間帯でも VM が実行されている必要があります。

VM で VM ゲストの自動パッチ適用が有効になっていると、Linux VM には種類 `Microsoft.CPlat.Core.LinuxPatchExtension` の VM 拡張機能が、また Windows VM には種類 `Microsoft.CPlat.Core.WindowsPatchExtension` の VM 拡張機能が、それぞれインストールされます。 この拡張機能は、VM ゲストの自動パッチ適用プロセスの一環として Azure プラットフォームによって管理されているため、手動でインストールまたは更新する必要はありません。

VM で VM のゲストの自動更新を有効にするには 3 時間以上かかることがあります。これは、有効化が VM のピーク外の時間帯に行われるためです。 この拡張機能も、VM のピーク外の時間帯にインストールおよび更新されます。 有効化が完了する前に VM のピーク外の時間が終了した場合、有効化プロセスは、次に利用可能なピーク外の時間帯に再開されます。

自動更新はほとんどのシナリオで無効になっており、パッチのインストールは今後の拡張機能によって行われます。 次の条件が適用されます。
- Windows VM でこれまで AutomaticByOS パッチ モードにより Automatic Windows Update がオンになっていた場合、拡張機能がインストールされると VM の Automatic Windows Update はオフになります。
- Ubuntu VM の場合、VM ゲストの自動パッチ適用の有効化が完了すると、既定の自動更新は自動的に無効になります。
- RHEL の場合、自動更新を手動で無効にする必要があります。 次のように実行します。

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

VM ゲストの自動パッチ適用が完了していて、パッチ拡張が VM にインストールされているかどうかを確認するには、VM のインスタンス ビューを確認します。 有効化プロセスが完了すると、拡張機能がインストールされ、`patchStatus` の下に VM の評価結果が表示されます。 VM のインスタンス ビューには、次に示すように複数の方法でアクセスできます。

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
`-Status` パラメーターを指定して [Get-AzVM](/powershell/module/az.compute/get-azvm) コマンドレットを使用して、VM のインスタンス ビューにアクセスします。

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

現在、PowerShell ではパッチの拡張機能に関する情報のみが提供されています。 `patchStatus` に関する情報は、PowerShell を通じて間もなく提供されます。

### <a name="azure-cli"></a>Azure CLI
[az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) を使用して、VM のインスタンス ビューにアクセスします。

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>VM のパッチの状態について理解する

インスタンス ビューの応答の `patchStatus` セクションでは、最新の評価と VM の最後のパッチのインストールに関する詳細が提供されます。

VM の評価結果は、`availablePatchSummary` セクションで確認できます。 評価は、VM ゲストの自動パッチ適用が有効になっている VM に対して定期的に実行されます。 評価後に使用可能なパッチの数は `criticalAndSecurityPatchCount` と `otherPatchCount` の結果として表示されます。 VM ゲストの自動パッチ適用により、 *[重大]* および *[セキュリティ]* パッチの分類で評価されたすべてのパッチがインストールされます。 その他の評価済みのパッチはスキップされます。

VM のパッチのインストール結果は、`lastPatchInstallationSummary` セクションで確認できます。 このセクションでは、インストール済み、保留中、失敗、またはスキップされたパッチの数などを含む、VM で最後に試行されたパッチのインストールの詳細について説明します。 パッチは、VM のピーク外の時間帯のメンテナンス期間中にのみインストールされます。 保留中および失敗したパッチは、次のピーク外の時間帯のメンテナンス期間中に自動的に再試行されます。

## <a name="disable-automatic-vm-guest-patching"></a>VM ゲストの自動パッチ適用を無効化する
VM ゲストの自動パッチ適用は、VM の[パッチ オーケストレーション モード](#patch-orchestration-modes)を変更することで無効にできます。

Linux VM で VM ゲストの自動パッチ適用を無効にするには、パッチ モードを `ImageDefault` に変更します。

Windows VM では VM ゲストの自動パッチ適用を有効にするために、`osProfile.windowsConfiguration.enableAutomaticUpdates` プロパティによって VM でどのパッチ モードを設定できるかが決定されています。このプロパティは VM の最初の作成時にのみ設定できます。 これは、特定のパッチ モードの切り替えに影響します。
- `osProfile.windowsConfiguration.enableAutomaticUpdates=false` となっている VM の場合、パッチ モードを `Manual` に変更すると、VM ゲストの自動パッチ適用が無効になります。
- `osProfile.windowsConfiguration.enableAutomaticUpdates=true` となっている VM の場合、パッチ モードを `AutomaticByOS` に変更すると、VM ゲストの自動パッチ適用が無効になります。
- AutomaticByOS モードと Manual モードの切り替えはサポートされていません。

API、PowerShell、CLI の使用例については、この記事の[有効化](#enable-automatic-vm-guest-patching)のセクションに記載された例を使用して、必要なパッチ モードを設定してください。

## <a name="on-demand-patch-assessment"></a>オンデマンドのパッチ評価
VM に対して VM ゲストの自動パッチ適用が既に有効になっている場合は、VM のピーク外の時間帯に定期的なパッチの評価が実行されます。 このプロセスは自動的に行われ、このドキュメントで既に説明したように、VM のインスタンス ビューで最新の評価の結果を確認できます。 また、いつでも VM に対してオンデマンドのパッチ評価をトリガーすることもできます。 パッチの評価が完了するまで数分かかることがあります。また、最新の評価の状態は、VM のインスタンス ビューに更新されます。

> [!NOTE]
>オンデマンドのパッチ評価では、パッチ インストールは自動的にトリガーされません。 VM ゲストの自動パッチ適用を有効にすると、評価された適用可能な VM のパッチは、このドキュメントで既に説明した可用性優先のパッチ適用プロセスに従って、VM のピーク外の時間帯にインストールされます。

### <a name="rest-api"></a>REST API
[Assess Patches](/rest/api/compute/virtual-machines/assess-patches) API を使用して、仮想マシンで利用可能なパッチを評価します。
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[Invoke-AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) コマンドレットを使用して、仮想マシンで利用可能なパッチを評価します。

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
[az vm assess-patches](/cli/azure/vm#az_vm_assess_patches) を使用して、仮想マシンで利用可能なパッチを評価します。

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="on-demand-patch-installation"></a>オンデマンドのパッチ インストール
VM に対して VM ゲストの自動パッチ適用が既に有効になっている場合は、VM のピーク外の時間帯にセキュリティおよび重大パッチの定期的なインストールが実行されます。 このプロセスは自動的に行われ、このドキュメントで既に説明したように、VM のインスタンス ビューで最新のインストールの結果を確認できます。

また、いつでも VM に対して、オンデマンドのパッチ インストールをトリガーすることもできます。 パッチのインストールが完了するまで数分かかることがあります。また、最新のインストールの状態は、VM のインスタンス ビューで更新されます。

オンデマンドのパッチ インストールを使用して、1 つ以上のパッチ分類のすべてのパッチをインストールできます。 また、Linux 用の特定のパッケージまたは Windows 用の特定の KB ID を含めるか、除外するかを選択することもできます。 オンデマンドのパッチ インストールをトリガーする場合は、少なくとも 1 つのパッチ分類または少なくとも 1 つのパッチ (Linux の場合はパッケージ、Windows の場合は KB ID) をインクルード リストに指定してください。

### <a name="rest-api"></a>REST API
[Install Patches](/rest/api/compute/virtual-machines/install-patches) API を使用して、仮想マシンにパッチをインストールします。

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/installPatches?api-version=2020-12-01`
```

Linux での要求本文の例:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "linuxParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

Windows での要求本文の例:
```json
{
  "maximumDuration": "PT1H",
  "rebootSetting": "IfRequired",
  "windowsParameters": {
    "classificationsToInclude": [
      "Critical",
      "Security"
    ]
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[Invoke-AzVMInstallPatch](/powershell/module/az.compute/invoke-azvminstallpatch) コマンドレットを使用して、仮想マシンにパッチをインストールします。

Linux VM に特定のパッケージをインストールする例:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Linux -ClassificationToIncludeForLinux "Security" -PackageNameMaskToInclude ["package123"] -PackageNameMaskToExclude ["package567"]
```

すべての重大パッチを Windows VM にインストールする例:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT2H" -RebootSetting "Never" -Windows   -ClassificationToIncludeForWindows Critical
```

Windows VM にすべてのセキュリティ パッチをインストールする一方で、特定の KB ID のパッチを含め、および除外し、再起動を必要とするパッチを除外する例:
```azurepowershell-interactive
Invoke-AzVmInstallPatch -ResourceGroupName "myResourceGroup" -VMName "myVM" -MaximumDuration "PT90M" -RebootSetting "Always" -Windows -ClassificationToIncludeForWindows "Security" -KBNumberToInclude ["KB1234567", "KB123567"] -KBNumberToExclude ["KB1234702", "KB1234802"] -ExcludeKBsRequiringReboot
```

### <a name="azure-cli"></a>Azure CLI
[az vm install-patches](/cli/azure/vm#az_vm_install_patches) を使用して、仮想マシンにパッチをインストールします。

Linux VM にすべての重大パッチをインストールする例:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-linux Critical
```

再起動が必要なパッチを除外しながら、Windows VM にすべての重大およびセキュリティのパッチをインストールする例:
```azurecli-interactive
az vm install-patches --resource-group myResourceGroup --name myVM --maximum-duration PT2H --reboot-setting IfRequired --classifications-to-include-win Critical Security --exclude-kbs-requiring-reboot true
```

## <a name="next-steps"></a>次のステップ
> [!div class="nextstepaction"]
> [Windows 仮想マシンの作成と管理について説明します](./windows/tutorial-manage-vm.md)
