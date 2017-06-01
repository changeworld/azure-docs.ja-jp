---
title: "Azure Monitoring と Windows 仮想マシン | Microsoft Docs"
description: "チュートリアル - Azure PowerShell を使用した Windows 仮想マシンの監視"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>Azure PowerShell を使用した Windows 仮想マシンの監視

Azure Monitoring では、エージェントを使用して Azure VM からブートとパフォーマンス データを収集し、Azure Storage にこのデータを格納し、ポータル、Azure PowerShell モジュールと Azure CLI でアクセスできるようにします。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * VM ホストのメトリックを表示する
> * 診断拡張機能をインストールする
> * VM のメトリックを表示する
> * アラートを作成する
> * 高度な監視をセットアップする

このチュートリアルには、Azure PowerShell モジュール バージョン 3.6 以降が必要です。 バージョンを確認するには、` Get-Module -ListAvailable AzureRM` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-azurerm-ps)に関するページを参照してください。

このチュートリアルの例を完了するには、既存の仮想マシンが必要です。 必要に応じて、この[サンプル スクリプト](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)で仮想マシンを作成できます。 このチュートリアルを実行するときは、リソース グループ、VM の名前、場所を適宜置き換えてください。

## <a name="view-boot-diagnostics"></a>ブート診断を表示する

Windows 仮想マシンが起動すると、ブート診断エージェントは画面出力をキャプチャします。これをトラブルシューティングに使用することができます。 この機能は既定で有効になっています。 キャプチャしたスクリーン ショットは、既定で作成される Azure Storage アカウントに格納されます。 

[Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) コマンドを使用してブートの診断データを取得できます。 次の例では、ブート診断は *c:\* ドライブのルートにダウンロードされています。 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>ホストのメトリックを表示する

Azure には、Windows VM と連動する専用のホスト VM があります。 メトリックは、そのホストを対象に自動的に収集され、Azure Portal に表示されます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. ホスト VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかのホスト メトリックを選択します。

    ![ホストのメトリックを表示する](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>診断拡張機能をインストールする

基本的なホスト メトリックは利用できますが、さらに粒度の細かい VM 固有のメトリックを表示するためには、Azure 診断拡張機能を VM にインストールする必要があります。 Azure 診断拡張機能を通じて、より詳しい監視データと診断データを VM から取得することができます。 これらのパフォーマンス メトリックを確認したり、VM のパフォーマンスに基づくアラートを作成したりすることができます。 診断拡張機能は、次のように Azure Portal からインストールします。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. **[診断の設定]** をクリックします。 *[ブート診断]* は、前のセクションで既に有効にしたので、そのように表示されています。 *[基本メトリック]* のチェック ボックスをオンにします。
3. **[ゲスト レベルの監視を有効にする]** ボタンをクリックします。

    ![診断メトリックを表示する](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>VM のメトリックを表示する

VM のメトリックは、ホスト VM のメトリックと同じ方法で表示できます。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM の実行状況を確認するには、VM ブレードの **[メトリック]** をクリックし、**[利用可能なメトリック]** からいずれかの診断メトリックを選択します。

    ![VM のメトリックを表示する](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>アラートを作成する

特定のパフォーマンス メトリックに基づいてアラートを作成することができます。 平均 CPU 使用率が特定のしきい値を超えたときや、空きディスク領域が特定の容量を下回ったときなどに、アラートによって通知を受け取ることができます。 アラートは、Azure Portal に表示されるほか、電子メールで送信することもできます。 アラートが生成されたことへの対応として、Azure Automation Runbook または Azure Logic Apps をトリガーすることもできます。

平均 CPU 使用率のアラートを作成する例を次に示します。

1. Azure Portal で **[リソース グループ]** をクリックし、**[myResourceGroup]** を選択して、リソース一覧から **[myVM]** を選択します。
2. VM ブレードの **[アラート ルール]** をクリックし、アラート ブレード上部にある **[メトリック アラートの追加]** をクリックします。
4. **[名前]** にアラートの名前を入力します (例: *myAlertRule*)。
5. CPU の割合が 1.0 を超えた状態が 5 分間続いたときにアラートをトリガーするために、それ以外の選択肢はすべて既定値のままにします。
6. 必要に応じて *[所有者、共同作成者、閲覧者に電子メールを送信]* のチェック ボックスをオンにして、電子メール通知を送信することもできます。 既定のアクションでは、ポータルに通知が表示されます。
7. **[OK]** ボタンをクリックします。

## <a name="advanced-monitoring"></a>高度な監視 

[Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) を使用すると、VM に対してさらに高度な監視を行うことができます。 まだサインアップしていない場合は、Operations Management Suite の[無料試用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)にサインアップしてください。

OMS ポータルにアクセスすると、[設定] ブレードにワークスペース キーとワークスペース識別子が表示されます。 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) コマンドを使用して、OMS 拡張機能を VM に追加します。 以下のサンプルの変数値を更新して、お使いの OMS ワークスペース キーとワークスペース ID を反映させます。  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

しばらくすると、OMS ワークスペースに新しい VM が表示されます。 

![OMS ブレード](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、Azure Security Center で VM を構成して確認しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * 仮想ネットワークの作成
> * リソース グループと VM を作成する 
> * VM でブート診断を有効にする
> * ブート診断を表示する
> * ホストのメトリックを表示する
> * 診断拡張機能をインストールする
> * VM のメトリックを表示する
> * アラートを作成する
> * 高度な監視をセットアップする

次のチュートリアルに進み、Azure Security Center について学習してください。

> [!div class="nextstepaction"]
> [VM のセキュリティの管理](./tutorial-azure-security.md)
