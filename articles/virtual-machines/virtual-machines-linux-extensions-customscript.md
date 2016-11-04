---
title: Linux VM 上のカスタム スクリプト | Microsoft Docs
description: カスタム スクリプト拡張機能を使用して Linux VM 構成タスクを自動化する
services: virtual-machines-linux
documentationcenter: ''
author: neilpeterson
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: nepeters

---
# Azure カスタム スクリプト拡張機能と Linux 仮想マシンの使用
カスタム スクリプト拡張機能は、Azure 仮想マシンでスクリプトをダウンロードし、実行します。この拡張機能は、デプロイ後の構成、ソフトウェアのインストール、その他の構成や管理タスクに役立ちます。スクリプトは、Azure ストレージやその他のアクセス可能なインターネットの場所からダウンロードできます。また、実行時に拡張機能に提供することもできます。カスタム スクリプト拡張機能は Azure Resource Manager テンプレートと統合されており、Azure CLI、PowerShell、Azure Portal、または Azure 仮想マシン REST API を使用して実行することもできます。

このドキュメントでは、Azure CLI からカスタム スクリプト拡張機能を使用する方法と、Azure Resource Manager テンプレートを使用する方法について詳しく説明します。また、Linux システムでのトラブルシューティング手順についても詳しく説明します。

## 拡張機能の構成
カスタム スクリプト拡張機能の構成では、スクリプトの場所や、実行するコマンドなどを指定します。この構成は、コマンド ラインで指定する構成ファイルや、Azure Resource Manager テンプレートに格納できます。機密データは、保護された構成に格納できます。この構成は暗号化され、仮想マシン内だけで復号化されます。保護された構成は、実行コマンドにパスワードなどの機密情報が含まれている場合に便利です。

### パブリック構成
スキーマ:

* **commandToExecute**: (必須、文字列) 実行するエントリ ポイント スクリプト
* **fileUris**: (省略可能、文字列の配列) ダウンロードするファイルの URL。
* **timestamp**: (省略可能、整数) このフィールドの値を変更してスクリプトの再実行をトリガーする場合にだけ、このフィールドを使用します。

```none
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### 保護された構成
スキーマ:

* **commandToExecute**: (省略可能、文字列) 実行するエントリ ポイント スクリプト。コマンドにパスワードなどの機密情報が含まれている場合は、代わりにこのフィールドを使用します。
* **storageAccountName**: (省略可能、文字列) ストレージ アカウントの名前。ストレージの資格情報を指定する場合は、すべての fileUris が Azure BLOB の URL である必要があります。
* **storageAccountKey**: (省略可能、文字列) ストレージ アカウントのアクセス キー。

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## Azure CLI
カスタム スクリプト拡張機能を実行するために Azure CLI を使用している場合は、少なくともファイルの URI およびスクリプト実行コマンドが含まれている構成ファイルを作成します。

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /scirpt-config.json
```

必要に応じて、`--public-config` および `--private-config` オプションを使用してコマンドを実行できます。そのようにすると、実行中に構成を指定することができ、個別の構成ファイルが不要になります。

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config '{"fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],"commandToExecute": "./hello.sh"}'
```

### Azure CLI の例
**例 1** - スクリプト ファイルを持つパブリック構成。

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
  "commandToExecute": "./hello.sh"
}
```

Azure CLI コマンド:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**例 2** - スクリプト ファイルを持たないパブリック構成。

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI コマンド:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path /public.json
```

**例 3** - スクリプト ファイルの URI を指定するためにパブリック構成ファイルを使用し、実行するコマンドを指定するために、保護された構成ファイルを使用します。

パブリック構成ファイル:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"],
}
```

保護された構成ファイル:

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI コマンド:

```none
azure vm extension set <resource-group> <vm-name> CustomScript Microsoft.Azure.Extensions 2.0 --auto-upgrade-minor-version --public-config-path ./public.json --private-config-path ./protected.json
```

## Resource Manager テンプレート
Azure カスタム スクリプト拡張機能は、Resource Manager テンプレートを使用して、仮想マシンのデプロイ時に実行できます。そうするには、適切な形式の JSON をデプロイ テンプレートに追加します。

### Resource Manager の例
**例 1** - パブリック構成。

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

**例 2** - 保護された構成内の実行コマンド。

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
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

完全な例については、[.Net Core Music Store Demo](https://github.com/neilpeterson/nepeters-azure-templates/tree/master/dotnet-core-music-linux-vm-sql-db) を参照してください。

## トラブルシューティング
カスタム スクリプト拡張機能を実行すると、スクリプトが次の例のようなディレクトリに作成されるかダウンロードされます。コマンドの出力も、このディレクトリの `stdout` および `stderr` ファイルに保存されます。

```none
/var/lib/azure/custom-script/download/0/
```

Azure スクリプト拡張機能ではログが生成され、そのログもここに保存されます。

```none
/var/log/azure/customscript/handler.log
```

カスタム スクリプト拡張機能の実行状態も、Azure CLI で取得できます。

```none
azure vm extension get <resource-group> <vm-name>
```

出力は次のテキストのようになります。

```none
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## 次のステップ
他の VM スクリプト拡張機能については、[Linux 用の Azure スクリプト拡張機能の概要](virtual-machines-linux-extensions-features.md)に関するページを参照してください。

<!---HONumber=AcomDC_0928_2016-->