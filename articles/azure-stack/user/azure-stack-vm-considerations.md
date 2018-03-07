---
title: "Azure Stack の仮想マシンに関する相違点と考慮事項 | Microsoft Docs"
description: "Azure Stack で仮想マシンを操作する際の相違点と考慮事項について説明します。"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 6613946D-114C-441A-9F74-38E35DF0A7D7
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2018
ms.author: brenduns
ms.openlocfilehash: 2b39ff3665a4cc3aeddf81b83e0c90c7f770da72
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2018
---
# <a name="considerations-for-virtual-machines-in-azure-stack"></a>Azure Stack の仮想マシンに関する考慮事項

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*

仮想マシンは、Azure Stack によって提供される、オンデマンドのスケーラブルなコンピューティング リソースです。 仮想マシンを使用する場合は、Azure で使用できる機能と Azure Stack で使用できる機能には違いがあることを理解する必要があります。 この記事では、Azure Stack の仮想マシンとその機能に固有の考慮事項の概要を示します。 Azure Stack と Azure の違いの概要については、「[主な考慮事項](azure-stack-considerations.md)」をご覧ください。

## <a name="cheat-sheet-virtual-machine-differences"></a>チート シート: 仮想マシンの相違点

| Feature | Azure (グローバル) | Azure Stack |
| --- | --- | --- |
| 仮想マシン イメージ | Azure Marketplace には、仮想マシンの作成に使用できるイメージが含まれています。 Azure Marketplace で入手できるイメージの一覧を参照するには、[Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/compute?subcategories=virtual-machine-images&page=1) のページを参照してください。 | 既定では、Azure Stack Marketplace に使用可能なイメージはありません。 Azure Stack のクラウド管理者は、ユーザーがイメージを使用する前に、Azure Stack Marketplace に対してイメージの発行またはダウンロードを行う必要があります。 |
| 仮想マシン サイズ | Azure では、幅広いサイズの仮想マシンがサポートされます。 提供されているサイズとオプションの詳細については、[Windows 仮想マシンのサイズ](../../virtual-machines/virtual-machines-windows-sizes.md)と [Linux 仮想マシンのサイズ](../../virtual-machines/linux/sizes.md)に関するトピックを参照してください。 | Azure Stack は、Azure で使用できる仮想マシンのサイズのサブセットをサポートしています。 サポートされているサイズの一覧を参照するには、この記事の[仮想マシンのサイズ](#virtual-machine-sizes)についてのセクションを参照してください。 |
| 仮想マシンのクォータ | [クォータ制限](../../azure-subscription-service-limits.md#service-specific-limits)は Microsoft によって設定されます | Azure Stack のクラウド管理者は、仮想マシンをユーザーに提供する前にクォータを割り当てる必要があります。 |
| 仮想マシン拡張機能 |Azure では、幅広い仮想マシン拡張機能がサポートされます。 使用可能な拡張機能については、「[Windows 用の仮想マシン拡張機能とその機能](../../virtual-machines/windows/extensions-features.md)」をご覧ください。| Azure Stack は、Azure で使用できる拡張機能のサブセットをサポートしており、拡張機能それぞれに特定のバージョンがあります。 Azure Stack のクラウド管理者は、ユーザーにどの拡張機能を提供するかを選択することができます。 サポートされている拡張機能の一覧を参照するには、この記事の[仮想マシンの拡張機能](#virtual-machine-extensions)についてのセクションを参照してください。 |
| 仮想マシンのネットワーク | テナントの仮想マシンに割り当てられたパブリック IP アドレスは、インターネット経由でアクセスできます。<br><br><br>Azure の仮想マシンには固定の DNS 名がある | テナントの仮想マシンに割り当てられたパブリック IP アドレスにアクセスできるのは、Azure Stack Development Kit 環境内のみです。 ユーザーは、Azure Stack で作成される仮想マシンに接続するためには、[RDP](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) または [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) 経由で Azure Stack Development Kit にアクセスできる必要があります。<br><br>特定の Azure Stack インスタンス内に作成された仮想マシンには、クラウド管理者が構成した値に基づく DNS 名があります。 |
| 仮想マシンのストレージ | [管理ディスク](../../virtual-machines/windows/managed-disks-overview.md)をサポートしています。 | Azure Stack では、管理ディスクはまだサポートされていません。 |
| API のバージョン | Azure では常に、すべての仮想マシン機能について最新の API のバージョンが用意されます。 | Azure Stack では特定の Azure サービスがサポートされ、それらのサービスについて特定の API バージョンがサポートされます。 サポートされている API バージョンの一覧を参照するには、この記事の [API バージョン](#api-versions)についてのセクションを参照してください。 |
|仮想マシン可用性セット|複数の障害ドメイン (リージョンあたり 2 または 3)<br>複数の更新ドメイン<br>管理ディスクのサポート|単一の障害ドメイン<br>単一の更新ドメイン<br>管理ディスクのサポートなし|
|仮想マシン スケール セット|自動スケールがサポートされる|自動スケールはサポートされない。<br>ポータル、Resource Manager テンプレート、または PowerShell を使用してスケール セットにより多くのインスタンスを追加します。

## <a name="virtual-machine-sizes"></a>仮想マシン サイズ
Azure は、リソースの過剰消費を防ぐため、複数の方法でリソースの制限を適用します (サーバー ローカルおよびサービス レベル)。 テナントのリソース消費量に何らかの制限を設けないと、"うるさい隣人" がリソースを過剰に消費してテナントのパフォーマンスが低下することがあります。 
- VM からのネットワーク送信には、帯域幅の上限が設けられます。 Azure Stack での上限は、Azure での上限と一致します。  
- ストレージ リソースの場合、テナントによるストレージ アクセスのためのリソースの基本的な過剰消費を防ぐため、Azure Stack によってストレージ IOP 制限が実装されます。 
- 複数のデータ ディスクが接続された VM では、各個別データ ディスクの最大スループットは、HHD で 500 IOPS、SSD で 2300 IOPS です。

次の表は、Azure Stack でサポートされている VM とその構成の一覧です。

| type           | サイズ          | サポートされるサイズの範囲 |
| ---------------| ------------- | ------------------------ |
|汎用 |Basic A        |[A0 - A4](azure-stack-vm-sizes.md#basic-a)                   |
|汎用 |Standard A     |[A0 - A7](azure-stack-vm-sizes.md#standard-a)              |
|汎用 |D シリーズ       |[D1 - D4](azure-stack-vm-sizes.md#d-series)              |
|汎用 |Dv2 シリーズ     |[D1_v2 - D5_v2](azure-stack-vm-sizes.md#ds-series)        |
|汎用 |DS シリーズ      |[DS1 - DS4](azure-stack-vm-sizes.md#dv2-series)            |
|汎用 |DSv2 シリーズ    |[DS1_v2 - DS5_v2](azure-stack-vm-sizes.md#dsv2-series)      |
|メモリ最適化|D シリーズ       |[D11 - D14](azure-stack-vm-sizes.md#mo-d)            |
|メモリ最適化|DS シリーズ      |[DS11 - DS14](azure-stack-vm-sizes.md#mo-ds)|
|メモリ最適化|Dv2 シリーズ     |[D11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dv2)     |
|メモリ最適化|DSv2 シリーズ  |[DS11_v2 - DS14_v2](azure-stack-vm-sizes.md#mo-dsv2)    |

仮想マシンのサイズと、それに関連付けられるリソースの量は、Azure Stack と Azure の間で一貫しています。 たとえば、この一貫性には、作成できるメモリの量、コアの数、データ ディスクの数やサイズが含まれます。 ただし、Azure Stack 内で VM サイズが同じ場合のパフォーマンスは、基になっている特定の Azure Stack 環境の特性によって異なります。

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

 Azure Stack には、拡張機能の小規模なセットが含まれています。 更新プログラムおよび追加の拡張機能は、Marketplace シンジケーションを通じて入手できます。

実際の Azure Stack 環境で利用できる仮想マシン拡張機能の一覧を取得するには、次の PowerShell スクリプトを使用します。

```powershell
Get-AzureRmVmImagePublisher -Location local | `
  Get-AzureRmVMExtensionImageType | `
  Get-AzureRmVMExtensionImage | `
  Select Type, Version | `
  Format-Table -Property * -AutoSize
```

## <a name="api-versions"></a>API のバージョン

Azure Stack の仮想マシン機能は、次の API バージョンをサポートしています。

![VM リソースの種類](media/azure-stack-vm-considerations/vm-resoource-types.png)

次の PowerShell スクリプトを使用して、実際の Azure Stack 環境で利用できる仮想マシン機能の API バージョンを取得することができます。

```powershell
Get-AzureRmResourceProvider | `
  Select ProviderNamespace -Expand ResourceTypes | `
  Select * -Expand ApiVersions | `
  Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} | `
  where-Object {$_.ProviderNamespace -like “Microsoft.compute”}
```
サポートされるリソースの種類と API バージョンの一覧は、クラウド オペレーターが Azure Stack 環境を新しいバージョンに更新した場合は異なっている可能性があります。

## <a name="next-steps"></a>次の手順

[Azure Stack で PowerShell を使用して Windows 仮想マシンを作成する](azure-stack-quick-create-vm-windows-powershell.md)
