<properties 
	pageTitle="Azure Resource Manager でのリソースのリンク | Microsoft Azure" 
	description="Azure Resource Manager で、異なるリソース グループ内の関連リソース間のリンクを作成します。" 
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
	ms.topic="article" 
	ms.date="05/16/2016" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーでのリソースのリンク

デプロイ中に、別のリソースに依存するものとしてリソースをマークできますが、そのライフサイクルはデプロイ中に終了します。デプロイ後、依存リソースの間に特定のリレーションシップはありません。Resource Manager では、リソース リンクという機能により、リソース間の永続的なリレーションシップを確立できます。

リソース リンクにより、複数のリソース グループにまたがるリレーションシップが記録できます。たとえば、一般的には、独自のライフ サイクルを持つデータベースが 1 つのリソース グループに存在し、別のライフサイクルを持つアプリが別のリソース グループに存在します。そのアプリはデータベースに接続するため、アプリとデータベース間のリンクをマークすることができます。

リンクされるリソースは、すべて同じサブスクリプションに属する必要があります。各リソースは、そのリソース以外の 50 のリソースにリンクできます。関連リソースを照会する唯一の方法は、REST API を使用することです。リンクされたリソースを削除または移動する場合、リンクの所有者は、残りのリンクをクリーンアップする必要があります。他のリソースにリンクしたリソースを削除しても、警告は表示**されません**。

## テンプレートでのリンク

テンプレート内のリンクを定義するには、リソース プロバイダーの名前空間を結合するリソースの種類とソース リソースの種類を **/providers/links** を使用して含めます。この名前には、ソース リソースの名前が含まれている必要があります。ターゲット リソースのリソース ID を指定します。次の例では、Web サイトとストレージ アカウント間のリンクを確立しています。

    {
      "type": "Microsoft.Web/sites/providers/links",
      "apiVersion": "2015-01-01",
      "name": "[concat(variables('siteName'),'/Microsoft.Resources/SiteToStorage')]",
      "dependsOn": [ "[variables('siteName')]" ],
      "properties": {
        "targetId": "[resourceId('Microsoft.Storage/storageAccounts','storagecontoso')]",
        "notes": "This web site uses the storage account to store user information."
      }
    }


テンプレートの形式の詳細については、「[リソース リンク テンプレート スキーマ](resource-manager-template-links.md)」を参照してください。

## REST API を使用したリンク

デプロイ済みのリソース間のリンクを定義するには、次のように実行します。

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

{subscription-id} を、サブスクリプション ID に置き換えます。{resource-group}、{provider-namespace}、{resource-type}、{resource-name} を、リンクの最初のリソースを識別する値に置き換えます。{link-name} を、作成するリンクの名前に置き換えます。api-version には 2015-01-01 を使用します。

この要求に、リンクの 2 番目のリソースを定義するオブジェクトを含めます。

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

properties 要素には、2 番目のリソースの識別子を含めます。

次を実行して、サブスクリプションのリンクを照会できます。

    https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Resources/links?api-version={api-version}

リンクに関する情報の取得方法など、その他の例については、[リンクされたリソース](https://msdn.microsoft.com/library/azure/mt238499.aspx)に関するページを参照してください。

## 次のステップ

- タグで、リソースを整理することもできます。リソースにタグを付ける方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」を参照してください。
- テンプレートを作成してデプロイするリソースを定義する方法については、「[テンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->