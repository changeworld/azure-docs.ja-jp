---
title: "Windows 用 Azure パフォーマンス診断 VM 拡張機能 | Microsoft Docs"
description: "Windows 用の Azure パフォーマンス診断 VM 拡張機能について説明します。"
services: virtual-machines-windows'
documentationcenter: 
author: genlin
manager: cshepard
editor: na
tags: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/29/2017
ms.author: genli
ms.openlocfilehash: 85d4764534c77ea0e4d999e249abe456d0234d75
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2017
---
# <a name="azure-performance-diagnostics-vm-extension-for-windows"></a>Windows 用 Azure パフォーマンス診断 VM 拡張機能

## <a name="summary"></a>概要
Azure パフォーマンス診断 VM 拡張機能を使うと、Windows VM からパフォーマンス診断データを収集し、分析を実行して、仮想マシンでのパフォーマンスの問題を特定して解決するための結果と推奨事項のレポートを提供することができます。 この拡張機能では、[PerfInsights](http://aka.ms/perfinsights) と呼ばれるトラブルシューティング ツールがインストールされます。

## <a name="prerequisites"></a>前提条件
### <a name="operating-systems"></a>オペレーティング システム
この拡張機能は、Windows Server 2008 R2、2012、2012 R2、2016、および Windows 8.1、Windows 10 の各オペレーティング システムにインストールできます。

## <a name="extension-schema"></a>拡張機能のスキーマ
次に示す JSON は、Azure パフォーマンス診断拡張機能のスキーマです。 この拡張機能では、診断の出力とレポートを保存するために、ストレージ アカウントの名前とキーが必要です。 これらの値は機密性が高く、保護された設定構成内に格納する必要があります。 Azure VM 拡張機能の保護された設定データは暗号化され、ターゲットの仮想マシンでのみ、暗号化が解除されます。 storageAccountName と storageAccountKey は大文字と小文字が区別されることに注意してください。 他の必要なパラメーターについては、以下のセクションで示します。

```JSON
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
```

### <a name="property-values"></a>プロパティ値

|   **名前**   |**値/例**|       **説明**      |
|--------------|-------------------|----------------------------|
|apiVersion|2015-06-15|API のバージョン
|publisher|Microsoft.Azure.Performance.Diagnostics|拡張機能の発行元名前空間
|type|AzurePerformanceDiagnostics|VM 拡張機能の種類
|typeHandlerVersion|1.0|拡張機能ハンドラーのバージョン
|performanceScenario|basic|データをキャプチャするパフォーマンス シナリオ。 有効な値: **basic**、**vmslow**、**azurefiles**、**custom**。
|traceDurationInSeconds|300|いずれかのトレース オプションを選択した場合、トレースの期間。
|DiagnosticsTrace|d|診断のトレースを有効にするオプション。 有効な値: **d**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|perfCounterTrace|p|パフォーマンス カウンターのトレースを有効にするオプション。 有効な値: **p**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|networkTrace|n|Netmon のトレースを有効にするオプション。 有効な値: **n**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|xperfTrace|○|XPerf のトレースを有効にするオプション。 有効な値: **x**、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|storPortTrace|s|StorPort のトレースを有効にするオプション。 有効な値: s、または空の値。 このトレースをキャプチャしない場合は、値を空のままにします。
|srNumber|123452016365929|サポート チケット番号 (ある場合)。 ない場合は空のままにします。
|requestTimeUtc|9/2/2017 11:06:00 PM|現在の日時 (UTC)。 ポータルを使ってこの拡張機能をインストールしている場合は、この値を指定する必要はありません。
|storageAccountName|mystorageaccount|診断ログと結果を格納するストレージ アカウントの名前。
|storageAccountKey|lDuVvxuZB28NNP…hAiRF3voADxLBTcc==|ストレージ アカウントのキー。

## <a name="install-the-extension"></a>拡張機能のインストール

Windows 仮想マシンに VM 拡張機能をインストールするには、以下の手順のようにします。

1. [Azure Portal](http://portal.azure.com) にログオンします。
2. この拡張機能をインストールする仮想マシンを選びます。

    ![仮想マシンを選択します](media/performance-diagnostics-vm-extension/select-the-virtual-machine.png)
3. **[拡張機能]** ブレードを選び、**[追加]** ボタンをクリックします。

    ![拡張機能を選択](media/performance-diagnostics-vm-extension/select-extensions.png)
4. Azure パフォーマンス診断拡張機能を選び、使用条件を確認して、**[作成]** ボタンをクリックします。

    ![Azure パフォーマンス診断拡張機能を作成する](media/performance-diagnostics-vm-extension/create-azure-performance-diagnostics-extension.png)
5. インストールのパラメーター値を指定し、**[OK]** をクリックして拡張機能をインストールします。 サポートされているトラブルシューティング シナリオについて詳しくは、[こちら](how-to-use-perfInsights.md#supported-troubleshooting-scenarios)をご覧ください。 

    ![拡張機能のインストール](media/performance-diagnostics-vm-extension/install-the-extension.png)
6. 正常にインストールされると、プロビジョニングが成功したことを示すメッセージが表示されます。

    ![プロビジョニング成功メッセージ](media/performance-diagnostics-vm-extension/provisioning-succeeded-message.png)

    > [!NOTE]
    > プロビジョニングが正常に済むと、拡張機能の実行が開始され、基本的なシナリオの実行が完了するまでに 2 ～ 3 分かかります。 他のシナリオの場合は、インストール中に指定した期間だけ実行されます。

## <a name="remove-the-extension"></a>拡張機能を削除する
仮想マシンから拡張機能を削除するには、以下の手順のようにします。

1. [Azure Portal](http://portal.azure.com) にログオンし、この拡張機能を削除する仮想マシンを選んで、[拡張機能] ブレードを選びます。 
2. 一覧でパフォーマンス診断拡張機能エントリの **[...]** をクリックして、アンインストールを選びます。

    ![拡張機能をアンインストールする](media/performance-diagnostics-vm-extension/uninstall-the-extension.png)

    > [!NOTE]
    > 拡張機能のエントリを選び、[アンインストール] オプションを選んでもかまいません。

## <a name="template-deployment"></a>テンプレートのデプロイ
Azure VM 拡張機能は、Azure Resource Manager テンプレートでデプロイできます。 前のセクションで詳しく説明した JSON スキーマを Azure Resource Manager テンプレートで使うと、Azure Resource Manager テンプレートのデプロイ時に Azure パフォーマンス診断拡張機能を実行できます。 テンプレートのデプロイで使うことができるテンプレートのサンプルを次に示します。

````
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vmName": {
      "type": "string",
      "defaultValue": "yourVMName"
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus"
    },
    "storageAccountName": {
      "type": "securestring"
      "defaultValue": "yourStorageAccount"
    },
    "storageAccountKey": {
      "type": "securestring"
      "defaultValue": "yourStorageAccountKey"
    },
    "performanceScenario": {
      "type": "string",
      "defaultValue": "basic"
    },
    "srNumber": {
      "type": "string",
      "defaultValue": ""
    },
    "traceDurationInSeconds": {
      "type": "int",
    "defaultValue": 300
    },
    "diagnosticsTrace": {
      "type": "string",
      "defaultValue": "d"
    },
    "perfCounterTrace": {
      "type": "string",
      "defaultValue": "p"
    },
    "networkTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "xperfTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "storPortTrace": {
      "type": "string",
      "defaultValue": ""
    },
    "requestTimeUtc": {
      "type": "string",
      "defaultValue": "10/2/2017 11:06:00 PM"
    }       
  },
  "resources": [
    {
      "name": "[concat(parameters('vmName'),'/AzurePerformanceDiagnostics')]",
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "location": "[parameters('location')]",
      "apiVersion": "2015-06-15",
      "properties": {
        "publisher": "Microsoft.Azure.Performance.Diagnostics",
        "type": "AzurePerformanceDiagnostics",
        "typeHandlerVersion": "1.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
            "performanceScenario": "[parameters('performanceScenario')]",
                  "traceDurationInSeconds": "[parameters('traceDurationInSeconds')]",
                  "diagnosticsTrace": "[parameters('diagnosticsTrace')]",
                  "perfCounterTrace": "[parameters('perfCounterTrace')]",
                  "networkTrace": "[parameters('networkTrace')]",
                  "xperfTrace": "[parameters('xperfTrace')]",
                  "storPortTrace": "[parameters('storPortTrace')]",
            "srNumber": "[parameters('srNumber')]",
            "requestTimeUtc":  "[parameters('requestTimeUtc')]"
        },
          "protectedSettings": {
            "storageAccountName": "[parameters('storageAccountName')]",
            "storageAccountKey": "[parameters('storageAccountKey')]"        
            }
      }
    }
  ]
}
````

## <a name="powershell-deployment"></a>PowerShell でのデプロイ
`Set-AzureRmVMExtension` コマンドを使って、Azure パフォーマンス診断仮想マシン拡張機能を既存の仮想マシンにデプロイすることができます。 このコマンドを実行する前に、パブリック構成とプライベート構成を PowerShell ハッシュ テーブルに格納しておく必要があります。

PowerShell

````
$PublicSettings = @{ "performanceScenario" = "basic"; "traceDurationInSeconds" = 300; "diagnosticsTrace" = "d"; "perfCounterTrace" = "p"; "networkTrace" = ""; "xperfTrace" = ""; "storPortTrace" = ""; "srNumber" = ""; "requestTimeUtc" = "2017-09-28T22:08:53.736Z" }
$ProtectedSettings = @{"storageAccountName" = "mystorageaccount" ; "storageAccountKey" = "mystoragekey"}

Set-AzureRmVMExtension -ExtensionName "AzurePerformanceDiagnostics" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Performance.Diagnostics" `
    -ExtensionType "AzurePerformanceDiagnostics" `
    -TypeHandlerVersion 1.0 `
    -Settings $PublicSettings `
    -ProtectedSettings $ProtectedSettings `
    -Location WestUS `
````

## <a name="information-on-the-data-captured"></a>キャプチャされるデータについて
PerfInsights ツールは、選んだシナリオに応じて、さまざまなログ、構成、診断データなどを収集します。 シナリオごとに収集されるデータについて詳しくは、[PerfInsights のドキュメント](http://aka.ms/perfinsights)をご覧ください。

## <a name="view-and-share-the-results"></a>結果を表示および共有する

既定では、拡張機能の出力は一時ドライブ (通常は D:\log_collection) の下の log_collection という名前のフォルダーに格納されます。 このフォルダーに、診断ログおよび結果と推奨事項に関するレポートを含む zip ファイルがあります。

作成された zip ファイルは、インストール時に指定したストレージ アカウントにもアップロードされ、[Shared Access Signatures (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) を使って 30 日間共有されます。 log_collection フォルダーには *zipfilename*_saslink.txt という名前のテキスト ファイルも作成されます。 このファイルには、zip ファイルをダウンロードするために作成された SAS リンクが含まれます。 このリンクがあれば、誰でも zip ファイルをダウンロードできます。

サポート チケットの作業を行う Microsoft のサポート エンジニアは、この SAS リンクを使って診断データをダウンロードして詳しく調査します。

レポートを表示するには、zip ファイルを抽出して **PerfInsights Report.html** ファイルを開きます。

拡張機能を選んで、ポータルから zip ファイルを直接ダウンロードすることもできます。

![詳細な状態を表示する](media/performance-diagnostics-vm-extension/view-detailed-status.png)

> [!NOTE]
> ポータルに表示される SAS リンクは、エンコードおよびデコード操作中に (特殊文字が原因で) URL が不正な形式になったため、機能しない場合があります。 これを回避するには、VM の *_saslink.txt ファイルからリンクを直接取得します。

## <a name="troubleshoot-and-support"></a>トラブルシューティングとサポート
### <a name="troubleshoot"></a>トラブルシューティング

- 拡張機能が正常にプロビジョニングされた場合でも、拡張機能のデプロイの状態 (通知領域) に "デプロイは進行中です" と表示されることがあります。

    拡張機能の状態で拡張機能が正常にプロビジョニングされたことが示されている限り、この問題は無視しても安全です。
- インストール中の問題の一部は、拡張ログを使ってトラブルシューティングできます。 拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

        C:\Packages\Plugins\Microsoft.Azure.Performance.Diagnostics.AzurePerformanceDiagnostics

### <a name="support"></a>サポート

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムと Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、[サポートの要求] をクリックします。 Azure サポートの使用方法の詳細については、「 [Microsoft Azure サポートに関する FAQ](https://azure.microsoft.com/support/faq/)」を参照してください。
