---
title: Publish-WebApplicationVM | Microsoft Docs
description: 仮想マシンに Web アプリケーションをデプロイする方法を学習します。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。
services: visual-studio-online
author: ghogen
manager: douge
assetId: de4cec95-f73f-44d9-babd-9f47f2633cdb
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 11/11/2016
ms.author: ghogen
ms.openlocfilehash: c2dc6057eeb4eba1306309785e13192674bc43c6
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42144338"
---
# <a name="publish-webapplicationvm-windows-powershell-script"></a>Publish-WebApplicationVM (Windows PowerShell スクリプト)
仮想マシンに Web アプリケーションをデプロイします。 このスクリプトは、必要なリソースが Azure サブスクリプションに存在しない場合にそれらを作成します。

```
Publish-WebApplicationVM
–Configuration <configuration>
-SubscriptionName <subscriptionName>
-WebDeployPackage <packageName>
-VMPassword @{Name = "name"; Password = "password")
-DatabaseServerPassword @{Name = "name"; Password = "password"}
-SendHostMessagesToOutput
-Verbose
```

### <a name="configuration"></a>構成
デプロイの詳細が記述されている JSON 構成ファイルへのパス。

| エイリアス | なし |
| --- | --- |
| 必須 |true |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="subscriptionname"></a>SubscriptionName
仮想マシンを作成する Azure サブスクリプションの名前。

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |サブスクリプション ファイルで最初のサブスクリプションを使用する |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="webdeploypackage"></a>WebDeployPackage
仮想マシンに発行する Web デプロイ パッケージへのパス。 Visual Studio で Web の発行ウィザードを使用して、このパッケージを作成できます。 詳細については、「 [方法: Visual Studio で Web デプロイ パッケージを作成する](https://msdn.microsoft.com/library/dd465323.aspx)」をご覧ください。

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="allowuntrusted"></a>AllowUntrusted
True の場合は、信頼されたルート証明機関によって署名されていない証明書の使用が許可されます。

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |false |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="vmpassword"></a>VMPassword
仮想マシンアカウントの資格情報。 例: -VMPassword @{Name = "admin"; Password = "password"}

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="databaseserverpassword"></a>DatabaseServerPassword
Azure での SQL Database の資格情報。 例: -DatabaseServerPassword @{Name = "admin"; Password = "password"}

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |なし |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

### <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput
true の場合、スクリプトからのメッセージは出力ストリームに出力されます。

| エイリアス | なし |
| --- | --- |
| 必須 |false |
| 位置 |named |
| 既定値 |false |
| パイプライン入力を許可する |false |
| ワイルドカード文字を許可する |false |

## <a name="remarks"></a>解説
スクリプトを使用して開発とテストの環境を作成する方法の詳細については、「 [Windows PowerShell スクリプトを使用した開発環境およびテスト環境の発行](vs-azure-tools-publishing-using-powershell-scripts.md)」をご覧ください。

JSON 構成ファイルではデプロイ対象の詳細が指定されます。 これには、仮想マシンの名前、アフィニティ グループ、VHD イメージ、およびサイズなど、プロジェクトを作成したときに指定した情報が含まれています。 また、仮想マシン上のエンドポイント、プロビジョニングするデータベース (該当する場合)、Web デプロイメント パラメーターも含まれています。 次のコードは JSON 構成ファイルの例を示しています。

```
{
    "environmentSettings": {
        "cloudService": {
            "name": "myvmname",
            "affinityGroup": "",
            "location": "West US",
            "virtualNetwork": "",
            "subnet": "",
            "availabilitySet": "",
            "virtualMachine": {
                "name": "myvmname",
                "vhdImage": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201404.01-en.us-127GB.vhd",
                "size": "Small",
                "user": "vmuser1",
                "password": "",
                "enableWebDeployExtension": true,
                "endpoints": [
                    {
                        "name": "Http",
                        "protocol": "TCP",
                        "publicPort": "80",
                        "privatePort": "80"
                    },
                    {
                        "name": "Https",
                        "protocol": "TCP",
                        "publicPort": "443",
                        "privatePort": "443"
                    },
                    {
                        "name": "WebDeploy",
                        "protocol": "TCP",
                        "publicPort": "8172",
                        "privatePort": "8172"
                    },
                    {
                        "name": "Remote Desktop",
                        "protocol": "TCP",
                        "publicPort": "3389",
                        "privatePort": "3389"
                    },
                    {
                        "name": "Powershell",
                        "protocol": "TCP",
                        "publicPort": "5986",
                        "privatePort": "5986"
                    }
                ]
            }
        },
        "databases": [
            {
                "connectionStringName": "",
                "databaseName": "",
                "serverName": "",
                "user": "",
                "password": ""
            }
        ],
        "webDeployParameters": {
            "iisWebApplicationName": "Default Web Site"
        }
    }
}
```

プロビジョニング対象が変更されるように JSON 構成ファイルを編集できます。 仮想マシンとクラウド サービスは必須ですが、データベースのセクションは省略可能です。

