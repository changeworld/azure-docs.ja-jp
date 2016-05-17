<properties 
	pageTitle="Azure Resource Manager テンプレートをエクスポートする | Microsoft Azure" 
	description="Azure Resource Manager を使用して、既存のリソース グループからテンプレートをエクスポートします。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/10/2016" 
	ms.author="tomfitz"/>

# 既存のリソースから Azure Resource Manager テンプレートをエクスポートする

Azure Resource Manager テンプレートの構築方法を理解することは困難な場合もありますが、Resource Manager ではサブスクリプション内の既存のリソースのテンプレートをエクスポートすることができるため、理解が容易になります。この生成されたテンプレートを使用すると、テンプレートの構文を学習したり、必要に応じてソリューションの再デプロイを自動化したりすることができます。

このチュートリアルでは、ポータルでストレージ アカウントを作成し、そのストレージ アカウントのテンプレートをエクスポートします。その後、仮想ネットワークを追加してリソース グループを変更し、現在の状態を表す新しいテンプレートをエクスポートします。このトピックでは、単純なインフラストラクチャを扱っていますが、より複雑なソリューションのテンプレートのエクスポートにも、同様の手順を使用できます。

## ストレージ アカウントの作成

1. [Azure ポータル](https://portal.azure.com)で、**[新規]**、**[データ + ストレージ]**、**[ストレージ アカウント]** の順に選択します。

      ![create storage](./media/resource-manager-export-template/create-storage.png)

2. ストレージ アカウントを作成し、名前を **storage**、自分のイニシャル、日付の組み合わせにします。ストレージ アカウント名は、Azure 全体で一意である必要があります。名前が既に使用されている場合は、別の名前にしてください。リソース グループには、**ExportGroup** を使用します。他のプロパティには既定値をそのまま使用します。**[作成]** を選択します。

      ![provide values for storage](./media/resource-manager-export-template/provide-storage-values.png)

デプロイが完了すると、サブスクリプションにストレージ アカウントが含まれた状態になります。

## デプロイ用のテンプレートのエクスポート
   
1. 新しいリソース グループのリソース グループ ブレードに移動します。一覧に直前のデプロイの結果が表示されています。そのリンクを選択します。

      ![resource group blade](./media/resource-manager-export-template/resource-group-blade.png)
   
2. グループのデプロイの履歴が表示されます。この場合は、1 つのデプロイだけが一覧に表示されているでしょう。このデプロイを選択します。

     ![最終デプロイ](./media/resource-manager-export-template/last-deployment.png)

3. デプロイの概要が表示されます。概要には、デプロイの状態とその操作、およびパラメーターに指定した値が含まれています。デプロイに使用されたテンプレートを表示するには、**[テンプレートの表示]** を選択します。

     ![view deployment summary](./media/resource-manager-export-template/deployment-summary.png)

4. Resource Manager によって、5 個のファイルが取得されます。次に例を示します。

   1. ソリューションのインフラストラクチャを定義するテンプレート。ポータルでストレージ アカウントを作成したときに、Resource Manager はテンプレートを使用してそれをデプロイし、今後参照できるようにテンプレートを保存しました。

   2. デプロイ中に値を渡すために使用できるパラメーター ファイル。最初のデプロイ中に指定した値が含まれていますが、テンプレートを再デプロイするときに任意の値を変更することができます。

   3. テンプレートをデプロイするために使用することができる Azure PowerShell スクリプト ファイル。

   4. テンプレートをデプロイするために使用することができる Azure CLI スクリプト ファイル。
   
   5. テンプレートをデプロイするために使用することができる .NET クラス。

     ファイルは、ブレードのリンクを通じて使用できます。既定では、テンプレートが選択されています。
     
       ![view template](./media/resource-manager-export-template/view-template.png)
     
     特にテンプレートに気を付けてください。テンプレートは次のようになっているはずです。
   
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "name": {
              "type": "String"
            },
            "accountType": {
              "type": "String"
            },
            "location": {
              "type": "String"
            },
            "encryptionEnabled": {
              "defaultValue": false,
              "type": "Bool"
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "sku": {
                "name": "[parameters('accountType')]"
              },
              "kind": "Storage",
              "name": "[parameters('name')]",
              "apiVersion": "2016-01-01",
              "location": "[parameters('location')]",
              "properties": {
                "encryption": {
                  "services": {
                    "blob": {
                      "enabled": "[parameters('encryptionEnabled')]"
                    }
                  },
                  "keySource": "Microsoft.Storage"
                }
              }
            } 
          ]
        }
   
     ストレージ アカウント名、種類、場所、および暗号化が有効になっているかどうか (既定値は **false**) についてのパラメーターが定義されていることに注意してください。**resources** セクション内に、デプロイするストレージ アカウントの定義が表示されます。
     
デプロイ時に評価される式が、角かっこで囲まれています。上で示されている角かっこで囲まれている式は、デプロイ時にパラメーター値を取得するために使用されます。使用できる式は他にも多くあり、このトピックの後の方にも、その他の式の例が出てきます。完全な一覧については、「[Azure Resource Manager テンプレートの関数](resource-group-template-functions.md)」を参照してください。
   
テンプレートの構造の詳細については、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

## 仮想ネットワークの追加

前のセクションでダウンロードしたテンプレートは、元のデプロイのインフラストラクチャを表していますが、デプロイ後の変更には対応できません。この問題をわかりやすく示すために、ポータルで仮想ネットワークを追加して、リソース グループを変更してみましょう。

1. リソース グループ ブレードで **[追加]** を選択し、使用可能なリソースの中から **[仮想ネットワーク]** を選択します。
   
2. 仮想ネットワークの名前を **VNET** にし、その他のプロパティには既定値を使用します。**[作成]** を選択します。

      ![set alert](./media/resource-manager-export-template/create-vnet.png)
   
3. 仮想ネットワークがリソース グループに正常にデプロイされた後で、デプロイの履歴を見直してください。今度は 2 つのデプロイが表示されます。最新のデプロイを選択します。

      ![deployment history](./media/resource-manager-export-template/deployment-history.png)
   
4. そのデプロイのテンプレートを確認します。仮想ネットワークを追加するために行った変更だけが定義されていることに注意してください。

一般的には、デプロイにいくつものテンプレートを使用するのではなく、ソリューションのすべてのインフラストラクチャを 1 回の操作でデプロイするテンプレートを使用することをお勧めします。


## リソース グループのテンプレートをエクスポートする

各デプロイには、リソース グループに対して行った変更のみが表示されますが、いつでもテンプレートをエクスポートしてリソース グループ全体の属性を表示できます。

1. リソース グループのテンプレートを表示するには、**[テンプレートのエクスポート]** を選択します。

      ![リソース グループのエクスポート](./media/resource-manager-export-template/export-resource-group.png)

2. ソリューションを再デプロイするために使用できる 5 個のファイルが再び表示されますが、今度のテンプレートは少し異なります。このテンプレートには、パラメーターが 2 つだけあります (ストレージ アカウント名用が 1 つと、仮想ネットワーク名用が 1 つ)。

        "parameters": {
          "virtualNetworks_VNET_name": {
            "defaultValue": "VNET",
            "type": "String"
          },
          "storageAccounts_storagetf05092016_name": {
            "defaultValue": "storagetf05092016",
            "type": "String"
          }
        },
        
     Resource Manager は、デプロイ時に使用されたテンプレートを取得しませんでした。代わりに、リソースの現在の構成に基づいて、新しいテンプレートを生成しました。Resource Manager は、どの値がパラメーターとして渡されるかを判断できないため、リソース グループ内の値に基づいて、ほとんどの値をハードコーディングします。たとえば、ストレージ アカウントの場所とレプリケーションの値が、次のように設定されます。
     
        "location": "northeurope",
        "tags": {},
        "properties": {
            "accountType": "Standard_RAGRS"
        },

3. ローカルに作業できるように、テンプレートをダウンロードします。

      ![download template](./media/resource-manager-export-template/download-template.png)

4. ダウンロードした .zip ファイルを検索し、内容を展開します。このダウンロードしたテンプレートは、インフラストラクチャを再デプロイするために使用できます。

## 次のステップ

ご利用ありがとうございます。 ポータルで作成したリソースからテンプレートをエクスポートする方法について説明しました。

- このチュートリアルのパート 2 では、ダウンロードしたテンプレートにパラメーターを追加してカスタマイズし、スクリプトで再デプロイします。[エクスポートしたテンプレートのカスタマイズと再デプロイ](resource-manager-customize-template.md)に関するページを参照してください。
- PowerShell を使用してテンプレートをエクスポートする方法を確認するには、「[Azure Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。
- Azure CLI を使用してテンプレートをエクスポートする方法を確認するには、「[Azure Resource Manager での Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。 

<!---HONumber=AcomDC_0511_2016-->