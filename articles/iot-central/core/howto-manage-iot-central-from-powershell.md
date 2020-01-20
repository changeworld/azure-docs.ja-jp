---
title: Azure PowerShell から IoT Central を管理する | Microsoft Docs
description: この記事では、Azure PowerShell から IoT Central アプリケーションを作成し、管理する方法について説明します。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c731dae02e9013fc436d6f30d8c8b2ab384968a0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453990"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell から IoT Central を管理する

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central アプリケーション マネージャー](https://aka.ms/iotcentral) Web サイト上で IoT Central アプリケーションを作成および管理するのではなく、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) を使用してアプリケーションを管理できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

ローカル コンピューターで Azure PowerShell を実行する場合は、「[Install the Azure PowerShell module (Azure PowerShell モジュールのインストール)](https://docs.microsoft.com/powershell/azure/install-az-ps)」を参照してください。 Azure PowerShell をローカルで実行する場合は、この記事のコマンドレットを試す前に、**Connect-AzAccount** コマンドレットを使用して Azure にサインインします。

## <a name="install-the-iot-central-module"></a>IoT Central モジュールのインストール

次のコマンドを実行して、[IoT Central モジュール](https://docs.microsoft.com/powershell/module/az.iotcentral/)がお使いの PowerShell 環境にインストールされていることを確認します。

```powershell
Get-InstalledModule -name Az.I*
```

インストール済みモジュールのリストに **Az.IotCentral** が含まれていない場合は、次のコマンドを実行します。

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>アプリケーションの作成

[New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) コマンドレットを使用して、Azure サブスクリプションで IoT Central アプリケーションを作成します。 次に例を示します。

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

このスクリプトでは、まずアプリケーション用に米国東部の場所にリソース グループを作成します。 次の表は、**New-AzIotCentralApp** コマンドで使用されるパラメーターの説明です。

|パラメーター         |[説明] |
|------------------|------------|
|ResourceGroupName |そのアプリケーションを含むリソース グループ。 サブスクリプションにこのリソース グループが既に存在している必要があります。 |
|Location |既定で、このコマンドレットにはリソース グループの場所が使用されます。 現時点では、IoT Central アプリケーションは、**米国**、**オーストラリア**、**アジア太平洋**、または **ヨーロッパ** の場所で作成できます。  |
|Name              |Azure portal 内のアプリケーションの名前。 |
|Subdomain         |アプリケーションの URL のサブドメイン。 この例では、アプリケーションの URL は https://mysubdomain.azureiotcentral.com です。 |
|Sku               |現在使用できる値は **S1** (Standard レベル) のみです。 「[Azure IoT Central の価格](https://azure.microsoft.com/pricing/details/iot-central/)」を参照してください。 |
|Template          | 使用するアプリケーション テンプレート。 詳細については、後の表を参照してください。 |
|DisplayName       |UI に表示されるアプリケーションの名前。 |

**一般公開されている機能を備えたアプリケーション テンプレート**

| テンプレート名            | [説明] |
| ------------------------ | ----------- |
| iotc-default@1.0.0       | 独自のデバイス テンプレートおよびデバイスにデータを入力するための空のアプリケーションを作成します。


**パブリック プレビュー機能を備えたアプリケーション テンプレート**

| テンプレート名            | [説明] |
| ------------------------ | ----------- |
| iotc-pnp-preview@1.0.0   | 独自のデバイス テンプレートとデバイスにデータを入力するための空のプラグ アンド プレイ プレビュー アプリケーションを作成します。 |
| iotc-condition@1.0.0     | ストア内分析 (条件監視テンプレート) を含むアプリケーションを作成します。 このテンプレートを使用して、ストア環境に接続して監視します。 |
| iotc-consumption@1.0.0   | 水消費量監視テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、水流の監視と制御を行います。 |
| iotc-distribution@1.0.0  | デジタル配布テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、主要な資産と作業をデジタル化することで、倉庫からの出荷の効率を向上させます。 |
| iotc-inventory@1.0.0     | スマート インベントリ管理テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、入荷、製品の移動、循環棚卸、およびセンサーの追跡を自動化します。 |
| iotc-logistics@1.0.0     | 接続された物流テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、場所と条件を監視して、航空輸送、船舶輸送、および陸上輸送での出荷をリアルタイムで追跡します。 |
| iotc-meter@1.0.0         | スマート メーター監視テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、エネルギー消費量やネットワークの状態を監視し、カスタマー サポートとスマート メーターの管理を向上させるための傾向を識別します。  |
| iotc-patient@1.0.0       | 患者の継続的なモニタリング テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、患者の治療、再入院、および病名を管理します。 |
| iotc-power@1.0.0         | ソーラー パネル監視テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、ソーラー パネルの状態やエネルギー生成の傾向を監視します。 |
| iotc-quality@1.0.0       | 水質監視テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、水質をデジタルに監視します。|
| iotc-store@1.0.0         | ストア内分析 (チェックアウト テンプレート) を備えたアプリケーションを作成します。 このテンプレートを使用して、ストア内のチェックアウト フローの監視と管理を行います。 |
| iotc-waste@1.0.0         | 接続された廃棄物管理テンプレートを含むアプリケーションを作成します。 このテンプレートを使用して、ゴミ箱を監視し、現場作業員を派遣します。 |

> [!NOTE]
> 現在、プレビュー アプリケーション テンプレートは、**ヨーロッパ**と**米国**のリージョンでのみ利用できます。

## <a name="view-your-iot-central-applications"></a>IoT Central アプリケーションの表示

[Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) コマンドレットを使用して、IoT Central アプリケーションを一覧表示し、メタデータを表示します。

## <a name="modify-an-application"></a>アプリケーションの変更

[Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) コマンドレットを使用して、IoT Central アプリケーションのメタデータを更新します。 アプリケーションの表示名を変更する場合の例を次に示します。

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>アプリケーションの削除

[Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) コマンドレットを使用して、IoT Central アプリケーションを削除します。 次に例を示します。

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>次のステップ

ここでは、Azure PowerShell から Azure IoT Central アプリケーションを管理する方法について説明しました。推奨される次の手順は以下のとおりです。

> [!div class="nextstepaction"]
> [アプリケーションを管理する](howto-administer.md)
