---
title: "Azure で Linux VM のカスタム スクリプトを実行する | Microsoft Docs"
description: "カスタム スクリプト拡張機能を使用して Linux VM 構成タスクを自動化する"
services: virtual-machines-linux
documentationcenter: 
author: danielsollondon
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 53adef0f512c54e036a981dbaa0d08453db6b194
ms.sourcegitcommit: a0d2423f1f277516ab2a15fe26afbc3db2f66e33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/16/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Linux 仮想マシンで Azure カスタム スクリプト拡張機能を使用する
カスタム スクリプト拡張機能は、スクリプトをダウンロードし、Azure 仮想マシンで実行します。 この拡張機能は、展開後の構成、ソフトウェアのインストール、その他の構成タスクや管理タスクに役立ちます。 スクリプトは、Azure Storage や他のアクセス可能なインターネットの場所からダウンロードできます。または、実行時に拡張機能に提供することもできます。 

カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合します。 また、Azure CLI、PowerShell、Azure Portal、または Azure Virtual Machines REST API を使って実行することもできます。

この記事では、Azure CLI からカスタム スクリプト拡張機能を使う方法と、Azure Resource Manager テンプレートを使って拡張機能を実行する方法について説明します。 また、Linux システムでのトラブルシューティング手順も示します。

## <a name="extension-configuration"></a>拡張機能の構成
カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。 この構成は、構成ファイルに格納したり、コマンド ラインで指定したり、Azure Resource Manager テンプレートで指定したりできます。 

機微なデータは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。 保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

### <a name="public-configuration"></a>パブリック構成
パブリック構成のスキーマを次に示します。

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

* **commandToExecute** (必須、文字列): 実行するエントリ ポイント スクリプト。
* **fileUris** (省略可能、文字列の配列): ダウンロードするファイルの URL。
* **timestamp** (省略可能、整数): スクリプトのタイムスタンプ。 このフィールドの値は、スクリプトの再実行をトリガーする必要がある場合にのみ変更してください。

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>保護された構成
保護された構成のスキーマを次に示します。

>[!NOTE]
>これらのプロパティ名では大文字と小文字が区別されます。 展開の問題を回避するには、次のような名前を使います。

* **commandToExecute** (省略可能、文字列): 実行するエントリ ポイント スクリプト。 コマンドにパスワードなどの機密情報が含まれている場合は、このフィールドを使います。
* **storageAccountName** (省略可能、文字列): ストレージ アカウントの名前。 ストレージの資格情報を指定する場合は、すべてのファイル URI が Azure BLOB の URL である必要があります。
* **storageAccountKey** (省略可能、文字列): ストレージ アカウントのアクセス キー。

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Azure CLI を使ってカスタム スクリプト拡張機能を実行するときは、1 つまたは複数の構成ファイルを作成します。 構成ファイルには、少なくとも、ファイルの URI とスクリプト実行コマンドが含まれます。

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

必要に応じて、コマンドで JSON 形式の文字列として設定を指定できます。 そのようにすると、実行中に構成を指定することができ、個別の構成ファイルが不要になります。

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Azure CLI の例

#### <a name="public-configuration-with-script-file"></a>スクリプト ファイルのあるパブリック構成

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>スクリプト ファイルのないパブリック構成

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>保護されたパブリック構成ファイル

スクリプト ファイルの URI を指定するには、パブリック構成ファイルを使います。 実行するコマンドを指定するには、保護された構成ファイルを使います。

パブリック構成ファイル:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

保護された構成ファイル:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI コマンド:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Resource Manager テンプレート
Resource Manager テンプレートを使うことで、仮想マシンの展開時に Azure カスタム スクリプト拡張機能を実行できます。 そうするには、適切な形式の JSON をデプロイ テンプレートに追加します。

### <a name="resource-manager-examples"></a>Resource Manager の例

#### <a name="public-configuration"></a>パブリック構成

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>保護された構成内の実行コマンド

```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

完全な例については、[.NET Music Store のデモ](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)をご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング
カスタム スクリプト拡張機能を実行すると、スクリプトが次の例のようなディレクトリに作成されるかダウンロードされます。 コマンドの出力も、このディレクトリの `stdout` および `stderr` ファイルに保存されます。

```bash
/var/lib/waagent/custom-script/download/0/
```

Azure スクリプト拡張機能が生成するログも、ここにあります。

```bash
/var/log/azure/custom-script/handler.log
```

また、Azure CLI を使って、カスタム スクリプト拡張機能の実行状態を取得できます。

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

出力は次のテキストのようになります。

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>次の手順
他の VM スクリプト拡張機能については、[Linux 用の Azure スクリプト拡張機能の概要](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

