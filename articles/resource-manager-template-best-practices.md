---
title: Resource Manager テンプレートのベスト プラクティス | Microsoft Docs
description: Azure Resource Manager テンプレートを単純化するためのガイドラインです。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn

ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/15/2016
ms.author: tomfitz

---
# Azure Resource Manager テンプレートを作成するためのベスト プラクティス
以下のガイドラインは、信頼性が高く使いやすい Resource Manager テンプレートを作成するのに役立ちます。これらのガイドラインはあくまで推奨事項であり、絶対的な要件ではありません。シナリオに応じて、ガイドラインを変更する必要のある場合があります。

## リソース名
一般に、使用するリソース名には次の 3 種類があります。

1. 一意である必要があるリソース名
2. 一意でなくてもよいが、コンテキストを特定しやすい名前にする必要があるリソース名
3. 一般的なものでよいリソース名

命名規則の策定については、「[インフラストラクチャの名前付けガイドライン](virtual-machines/virtual-machines-windows-infrastructure-naming-guidelines.md)」を参照してください。リソース名の制限事項については、「[Azure リソースの推奨される名前付け規則](./guidance/guidance-naming-conventions.md)」を参照してください。

### 一意のリソース名
データ アクセス エンドポイントを持つリソースの種類に対しては、一意のリソース名を付ける必要があります。一意の名前にする必要があるリソースの種類には次のようなものがあります。

* ストレージ アカウント
* Web サイト
* SQL Server
* Key Vault
* Redis Cache
* Batch アカウント
* Traffic Manager
* 検索サービス
* HDInsight クラスター

また、ストレージ アカウント名は、小文字で 24 文字以下にする必要があり、ハイフンを含めることはできません。

デプロイ時にこうしたリソース名のパラメーターを指定して一意の名前を考える代わりに、[uniqueString()](resource-group-template-functions.md#uniquestring) 関数を使用して名前を生成する変数を作成できます。また、多くの場合、リソースの種類を名前から判別しやくするために、**uniqueString** の結果にプレフィックスまたはポストフィックスを追加することもお勧めします。たとえば、次の変数ではストレージ アカウントの一意の名前を生成できます。

    "variables": {
        "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
    }

uniqueString のプレフィックスが付いたストレージ アカウントは、同じラックにクラスター化されません。

### 特定しやすいリソース名
名前を付ける必要があるものの一意性は必要ないリソースの種類については、コンテキストとリソースの種類がわかる名前を付けるだけで十分です。リソース名のリストで見つけやすい説明的な名前を付けることをお勧めします。デプロイごとにリソース名を変える必要がある場合は、次のように名前のパラメーターを使用します。

    "parameters": {
        "vmName": { 
            "type": "string",
            "defaultValue": "demoLinuxVM",
            "metadata": {
                "description": "The name of the VM to create."
            }
        }
    }

デプロイ時に名前を渡す必要がない場合は、次のように変数を使用します。

    "variables": {
        "vmName": "demoLinuxVM"
    }

または、次のようにハードコーディングした値を使用します。

    {
      "type": "Microsoft.Compute/virtualMachines",
      "name": "demoLinuxVM",
      ...
    }

### 一般的なリソース名
別のリソースから頻繁にアクセスされるリソースの種類に対しては、テンプレートでハードコーディングされた一般名を使用できます。たとえば、SQL Server のファイアウォール ルールの名前はカスタマイズできないようにした方が良い場合があります。

    {
        "type": "firewallrules",
        "name": "AllowAllWindowsAzureIps",
        ...
    }

## パラメーター
1. パラメーターはできる限り少なくしてください。可能であれば変数またはリテラルを使用してください。パラメータを指定するのは次のものに対してのみにしてください。
   
   * 環境ごとに変える必要がある設定 (SKU、サイズ、容量など)
   * 特定しやすくするために付けるリソース名
   * 他のタスクを実行するために頻繁に使用する値 (管理者ユーザー名など)
   * シークレット情報 (パスワードなど)
   * リソースの種類のインスタンスを複数作成するときに使用する多数の値または値の配列
2. パラメーター名には、**キャメルケース**を使用してください。
3. すべてのパラメーターに対してメタデータで説明を提供してください。
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
4. パラメーターの既定値を定義してください (パスワードおよび SSH キーは除く)。
   
        "parameters": {
            "storageAccountType": {
                "type": "string",
                "defaultValue": "Standard_GRS",
                "metadata": {
                    "description": "The type of the new storage account created to store the VM disks"
                }
            }
        }
5. すべてのパスワードおよびシークレット情報には **securestring** を使用してください。
   
        "parameters": {
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        }
6. 可能であれば、**場所**の指定にパラメーターは使用しないでください。代わりに、リソース グループの location プロパティを使用します。すべてのリソースで **resourceGroup().location** 式を使用すると、テンプレート内のリソースがリソース グループと同じ場所にデプロイされます。
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              ...
          }
        ]
   
     リソースの種類がサポートされる場所に限りがある場合は、テンプレートで有効な場所を直接していすることを検討してください。location パラメーターを使用する必要がある場合は、同じ場所に配置される可能性があるリソースでできる限りそのパラメーターを共有してください。このようにすることで、ユーザーがリソースの種類ごとに場所を指定する必要性が最小限に抑えられます。
7. リソースの種類の API バージョンにはパラメーターや変数を使用しないでください。リソースのプロパティおよび値は、バージョン番号ごとに異なる可能性があります。パラメーターまたは変数に API バージョンがセットすると、コード エディターの Intellisense が適切なスキーマを決定できなくなります。代わりに、テンプレートの API バージョンをハードコーディングしてください。

## 変数
1. テンプレート内で複数回使用する必要のある値には、変数を使用してください。1 回しか使用しない値は、ハードコーディングすることでテンプレートが読みやすくなります。
2. variables セクションでは [reference](resource-group-template-functions.md#reference) 関数は使用できません。reference 関数ではリソースのランタイム状態から値を取得しますが、テンプレートの初回分析時には変数が解決されます。**reference** 関数が必要な値は、テンプレートの **resources** セクションまたは **outputs** セクションに直接作成してください。
3. 「[リソース名](#resource-names)」で説明しているように、一意である必要のあるリソース名には変数を含めてください。
4. 変数は複合オブジェクトにまとめてください。複合オブジェクトの値は、**variable.subentry** 形式で参照できます。変数をグループ化すると、関連する変数を整理し、テンプレートを読みやすくできます。
   
        "variables": {
            "storage": {
                "name": "[concat(uniqueString(resourceGroup().id),'storage')]",
                "type": "Standard_LRS"
            }
        },
        "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storage').name]",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "sku": {
                  "name": "[variables('storage').type]"
              },
              ...
          }
        ]
   
   > [!NOTE]
   > 複合オブジェクトに、複合オブジェクトの値を参照する式を含めることはできません。この場合は、別の変数を定義してください。
   > 
   > 
   
     変数としての複合オブジェクトの使用に関するより高度な例については、「[Azure リソース マネージャーのテンプレートでの状態の共有](best-practices-resource-manager-state.md)」を参照してください。

## リソース
1. 各リソースには、他の共同作業者にリソースの用途がわかるように **comments** を指定してください。
   
        "resources": [
          {
              "name": "[variables('storageAccountName')]",
              "type": "Microsoft.Storage/storageAccounts",
              "apiVersion": "2016-01-01",
              "location": "[resourceGroup().location]",
              "comments": "This storage account is used to store the VM disks",
              ...
          }
        ]
2. リソースの詳細情報を追加できるタグを使用して、メタデータをリソースに追加してください。たとえば、リソースに請求の詳細に関するメタデータを追加できます。詳細については、[タグを使用した Azure リソースの整理](resource-group-using-tags.md)に関するページを参照してください。
3. テンプレートで**パブリック エンドポイント** (BLOB Storage のパブリック エンドポイントなど) を使用する場合、名前空間は**ハードコーディングしないでください**。名前空間を動的に取得するには、**reference** 関数を使用します。このようにすることで、テンプレートのエンドポイントを手作業で変更することなく、別のパブリック名前空間環境にテンプレートをデプロイできます。apiVersion のバージョンは、テンプレートの storageAccount で使用するものと同じにいてください。
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     ストレージ アカウントを同じテンプレートにデプロイする場合、リソースの参照でプロバイダーの名前空間を指定する必要はありません。簡略化された構文は次のようになります。
   
        "osDisk": {
            "name": "osdisk",
            "vhd": {
                "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
            }
        }
   
     テンプレート内の他の値をパブリック名前空間を使用して構成している場合、同じ reference 関数を反映するようにこれらの値を変更してください。たとえば、仮想マシン diagnosticsProfile の storageUri プロパティは次のようにします。
   
        "diagnosticsProfile": {
            "bootDiagnostics": {
                "enabled": "true",
                "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
            }
        }
   
     また、別のリソース グループにある既存のストレージ アカウントを**参照**することもできます。

        "osDisk": {
            "name": "osdisk", 
            "vhd": {
                "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
            }
        }

1. 仮想マシンに publicIPAddresses を割り当てるのは、アプリケーションで必要な場合のみにしてください。デバッグや管理のために接続する場合は、inboundNatRules、virtualNetworkGateways、ジャンプボックスのいずれかを使用してください。
   
     仮想マシンへの接続の詳細については、以下の記事を参照してください。
   
   * [Running VMs for an N-tier architecture on Azure](./guidance/guidance-compute-3-tier-vm.md) (Azure の N 層アーキテクチャで仮想マシンを実行する)
   * [Azure Resource Manager の仮想マシンの WinRM アクセスを設定する](virtual-machines/virtual-machines-windows-winrm.md)
   * [Azure ポータルを使用して VM への外部アクセスを許可する](virtual-machines/virtual-machines-windows-nsg-quickstart-portal.md)
   * [PowerShell を使用して VM への外部アクセスを許可する](virtual-machines/virtual-machines-windows-nsg-quickstart-powershell.md)
   * [ポートおよびエンドポイントの開放](virtual-machines/virtual-machines-linux-nsg-quickstart.md)
2. publicIPAddresses の **domainNameLabel** プロパティは一意である必要があります。3 文字以上 63 文字以下で、正規表現 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` で指定された規則に従う必要があります。uniqueString 関数では 13 文字の文字列が生成されるため、次の例では、dnsPrefixString プレフィックス文字列が 50 文字以下であり、前述の規則に準拠していることを確認済みであると想定しています。
   
        "parameters": {
            "dnsPrefixString": {
                "type": "string",
                "maxLength": 50,
                "metadata": {
                    "description": "DNS Label for the Public IP. Must be lowercase. It should match with the following regular expression: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$ or it will raise an error."
                }
            }
        },
        "variables": {
            "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
        }
3. **customScriptExtension** にパスワードを追加する場合は、protectedSettings で **commandToExecute** プロパティを使用してください。
   
        "properties": {
            "publisher": "Microsoft.OSTCExtensions",
            "type": "CustomScriptForLinux",
            "settings": {
                "fileUris": [
                    "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
                ]
            },
            "protectedSettings": {
                "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
            }
        }
   
   > [!NOTE]
   > 仮想マシン/拡張機能にパラメーターとして渡されるシークレット情報を暗号化するために、関連する拡張機能の protectedSettings プロパティを使用する必要があります。
   > 
   > 

## 出力
テンプレートで新しい **publicIPAddresses** を作成する場合は、デプロイ後に IP アドレスおよび完全修飾ドメインの詳細を取得しやすくするために、これらの詳細を提供する **output** セクションをテンプレートに記載してください。リソースを参照する場合は、リソースの作成で使用した API バージョンを使用してください。

```
"outputs": {
    "fqdn": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName')), '2016-07-01').ipAddress]",
        "type": "string"
    }
}
```

## 単一テンプレートまたは入れ子になったテンプレート
ソリューションをデプロイする場合、単一テンプレートと、複数のテンプレートが入れ子になったメイン テンプレートのいずれかを使用できます。高度なシナリオでは、入れ子になったテンプレートが一般的です。入れ子にテンプレートには次のようなメリットがあります。

1. ソリューションを対象コンポーネントに分解できる
2. 別のメイン テンプレートで入れ子になったテンプレートを再利用できる

テンプレートのデザインを複数の入れ子になったテンプレートに分解する場合は、以下のガイドラインによりデザインを標準化できます。これらのガイドラインは、[Azure Resource Manager テンプレートのデザイン パターン](best-practices-resource-manager-design-templates.md)に関する記事に基づいています。デザインは、以下のテンプレートで構成することをお勧めします。

* **メイン テンプレート** (azuredeploy.json): 入力パラメーター用に使用します。
* **共有リソース テンプレート**: その他すべてのリソースで使用する共有リソース (例: 仮想ネットワーク、可用性セット) をデプロイします。dependsOn 式を使用して、このテンプレートが他のテンプレートよりも先にデプロイされるようにします。
* **オプション リソース テンプレート**: パラメーターに基づいてリソースを条件付きでデプロイします (例: ジャンプボックス)。
* **メンバー リソース テンプレート**: アプリケーション層内の各インスタンス タイプには、独自の構成があります。層内ではさまざまなインスタンス タイプを定義できます (例: 最初のインスタンスで新しいクラスターを作成し、別のインスタンスは既存のクラスターに追加する)。各インスタンス タイプには、独自のデプロイ テンプレートがあります。
* **スクリプト**: 各インスタンス タイプでは、広範囲に再利用できるスクリプトを適用できます (例: 追加ディスクの初期化やフォーマット)。特定のカスタマイズ用に作成されたカスタム スクリプトは、インスタンス タイプごとに異なります。

![入れ子になったテンプレート](./media/resource-manager-template-best-practices/nestedTemplateDesign.png)

詳細については、「[Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

## 入れ子になったテンプレートへの条件付きリンク
テンプレートの URI の一部になるパラメーターを使用すると、入れ子になったテンプレートを条件付きでリンクできます。

    "parameters": {
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },
    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/Contoso/Templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                }
            }
        }
    ]

## テンプレートの形式
1. JSON 検証ツールを使用してテンプレートを渡して、デプロイ時のエラーの原因となる余分なコンマ、かっこ、角かっこを削除することをお勧めします。[JSONlint](http://jsonlint.com/)、または使い慣れた編集環境のリンター パッケージを使用してください (Visual Studio Code、Atom、Sublime Text、Visual Studio など)。
2. また、読みやすくするために JSON のフォーマットを整えることもお勧めします。ローカル エディターの JSON フォーマッター パッケージを使用できます。Visual Studio では、**Ctrl+K、Ctrl+D** でドキュメントのフォーマットを整えることができます。VS Code では、**Alt+Shift+F** です。ローカル エディターでドキュメントを整形できない場合は、[オンライン フォーマッター](https://www.bing.com/search?q=json+formatter)を使用してください。

## 次のステップ
1. 仮想マシン用のソリューションの構築に関するガイダンスについては、「[Running a Windows VM on Azure](./guidance/guidance-compute-single-vm.md)」 (Azure での Windows VM の実行) および「[Azure での Linux VM の実行](./guidance/guidance-compute-single-vm-linux.md)」を参照してください。
2. ストレージ アカウントのセットアップのガイダンスについては、「[Microsoft Azure Storage のパフォーマンスとスケーラビリティに対するチェック リスト](storage/storage-performance-checklist.md)」を参照してください。
3. 仮想ネットワークについては、「[ネットワーク インフラストラクチャのガイドライン](virtual-machines/virtual-machines-windows-infrastructure-networking-guidelines.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->