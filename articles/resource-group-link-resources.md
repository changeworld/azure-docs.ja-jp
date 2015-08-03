<properties 
	pageTitle="Azure リソース マネージャーでのリソースのリンク" 
	description="Azure リソース マネージャーで、異なるリソース グループ内のリソース間のリンクを作成します。" 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Azure リソース マネージャーでのリソースのリンク

デプロイ後に、リソース間のリレーションシップまたはリンクを照会する場合があります。依存関係はデプロイに情報を提供しますが、そのライフサイクルはデプロイ時に終了します。デプロイが完了すると、依存リソースの間に特定のリレーションシップはありません。

代わりに、Azure リソース マネージャーでは、リソース リンクという新機能により、リソース間のリレーションシップを確立し、照会できます。リソースのリンク元となるリソースや、リソースのリンク先となるリソースを確認できます。

リソース リンクのスコープには、サブスクリプション、リソース グループまたは特定のリソースを使用できます。つまり、リソース リンクにより、複数のリソース グループにまたがるリレーションシップが記録できます。複数のテンプレートと複数のリソース グループにソリューションを分解するときに、これらのリソース リンクを識別するメカニズムがあると役立ちます。たとえば、一般的には、独自のライフ サイクルを持つデータベースが 1 つのリソース グループに存在し、別のライフサイクルを持つアプリが別のリソース グループに存在します。このアプリはデータベースに接続するので、異なるリソース グループ内のリソース間にリンクがあります。

リンクされるリソースは、すべて同じサブスクリプションに属する必要があります。各リソースは、そのリソース以外の 50 のリソースにリンクできます。リンクされたリソースを削除または移動する場合、リンクの所有者は、残りのリンクをクリーンアップする必要があります。

## テンプレートでのリンク

次の例では、Web サイト、通知ハブ、および SQL データベースへの一方向のリレーションシップのセットを設定した "Microsoft.AppService/apiapps" という種類のリソースを作成するテンプレートを示しています。

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

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

リンクに関する情報の取得方法など、その他の例については、[リンクされたリソース](https://msdn.microsoft.com/library/azure/mt238499.aspx)に関するページを参照してください。

## 次のステップ

- タグで、リソースを整理することもできます。リソースにタグを付ける方法については、「[タグを使用した Azure リソースの整理](resource-group-using-tags.md)」を参照してください。
- テンプレートを作成してデプロイするリソースを定義する方法については、「[テンプレートの作成](resource-group-authoring-templates.md)」を参照してください。

<!---HONumber=July15_HO4-->