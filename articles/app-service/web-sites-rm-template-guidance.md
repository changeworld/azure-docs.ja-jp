---
title: テンプレートを使った Azure Web アプリのデプロイに関するガイダンス | Microsoft Docs
description: Web アプリをデプロイするための Azure Resource Manager テンプレートの作成に関する推奨事項です。
services: app-service
documentationcenter: app-service
author: tfitzmac
manager: timlt
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: tomfitz
ms.openlocfilehash: dc816bb6e95d2800d79124dfac60b55e88eaa500
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="guidance-on-deploying-web-apps-by-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用した Web アプリのデプロイに関するガイダンス

この記事では、Azure App Service ソリューションをデプロイするための Azure Resource Manager テンプレートの作成に関する推奨事項を示します。 これらの推奨事項は、一般的な問題を回避するために役立ちます。

## <a name="define-dependencies"></a>依存関係を定義する

Web アプリの依存関係を定義するには、Web アプリ内のリソースがどのように連係しているかを理解する必要があります。 依存関係を間違った順序で指定すると、デプロイ エラーが発生するか、デプロイを停止させる競合状態が発生する場合があります。

> [!WARNING]
> テンプレートに MSDeploy サイト拡張機能を含める場合は、すべての構成リソースを、MSDeploy リソースに依存するように設定する必要があります。 構成を変更すると、サイトが非同期で再起動されます。 構成リソースを MSDeploy に依存させることで、サイトの再起動前に MSDeploy を確実に終了させることができます。 これらの依存関係がない場合は、MSDeploy のデプロイ プロセス中にサイトが再起動されることがあります。 テンプレートの例については、「[WordPress Template with Web Deploy Dependency (Web 配置依存関係を持つ WordPress テンプレート)](https://github.com/davidebbo/AzureWebsitesSamples/blob/master/ARMTemplates/WordpressTemplateWebDeployDependency.json)」を参照してください。

次の図は、さまざまな App Service リソースの依存関係の順序を示しています。

![Web アプリの依存関係](media/web-sites-rm-template-guidance/web-dependencies.png)

次の順序でリソースをデプロイします。

**レベル 1**
* App Service プラン。
* データベースやストレージ アカウントなどの他の関連リソース。

**レベル 2**
* Web アプリ - App Service プランに依存。
* サーバー ファームを対象とする Azure Application Insights インスタンス - App Service プランに依存。

**レベル 3**
* ソース管理 - Web アプリに依存。
* MSDeploy サイト拡張機能 - Web アプリに依存。
* サーバー ファームを対象とする Application Insights インスタンス - Web アプリに依存。

**レベル 4**
* App Service 証明書 - ソース管理または MSDeploy に依存 (いずれかが存在する場合)。 存在しない場合は Web アプリに依存。
* 構成設定 (接続文字列、Web 構成値、アプリ設定) - ソース管理または MSDeploy に依存 (いずれかが存在する場合)。 存在しない場合は Web アプリに依存。

**レベル 5**
* ホスト名のバインディング - 証明書に依存 (存在する場合)。 存在しない場合は上位レベルのリソースに依存。
* サイト拡張機能 - 構成設定に依存 (存在する場合)。 存在しない場合は上位レベルのリソースに依存。

通常、ソリューションにはこれらのリソースとレベルの一部しか含まれていません。 ないレベルについては、下位のリソースを次に高いレベルにマップします。

次の例は、テンプレートの一部を示しています。 接続文字列の構成値は、MSDeploy 拡張機能に応じて異なります。 MSDeploy 拡張機能は、Web アプリおよびデータベースに依存します。

```json
{
    "name": "[parameters('name')]",
    "type": "Microsoft.Web/sites",
    "resources": [
      {
          "name": "MSDeploy",
          "type": "Extensions",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'))]",
            "[concat('SuccessBricks.ClearDB/databases/', parameters('databaseName'))]"
          ],
          ...
      },
      {
          "name": "connectionstrings",
          "type": "config",
          "dependsOn": [
            "[concat('Microsoft.Web/Sites/', parameters('name'), '/Extensions/MSDeploy')]"
          ],
          ...
      }
    ]
}
```

## <a name="find-information-about-msdeploy-errors"></a>MSDeploy エラーについての情報を見つける

Resource Manager テンプレートで MSDeploy が使用されている場合は、デプロイ エラー メッセージを理解しにくいことがあります。 デプロイの失敗後に詳細情報を取得するには、次の手順を実行します。

1. サイトの [Kudu コンソール](https://github.com/projectkudu/kudu/wiki/Kudu-console)に移動します。
2. D:\home\LogFiles\SiteExtensions\MSDeploy のフォルダーを参照します。
3. appManagerStatus.xml および appManagerLog.xml ファイルを探します。 最初のファイルには、状態が記録されます。 2 番目のファイルには、エラーに関する情報が記録されます。 エラーが明確でない場合は、フォーラムに支援を求めるときに、そのファイルを含めることができます。

## <a name="choose-a-unique-web-app-name"></a>一意の Web アプリ名の選択

Web アプリの名前は、グローバルに一意である必要があります。 一意になると思われる命名規則を使用するか、[uniqueString 関数](../azure-resource-manager/resource-group-template-functions-string.md#uniquestring)を使用して一意の名前の生成を支援することができます。

```json
{
  "apiVersion": "2016-08-01",
  "name": "[concat(parameters('siteNamePrefix'), uniqueString(resourceGroup().id))]",
  "type": "Microsoft.Web/sites",
  ...
}
```

## <a name="next-steps"></a>次の手順

* テンプレートを使用して Web アプリをデプロイするチュートリアルについては、「[Azure でマイクロサービスを予測どおりにデプロイする](app-service-deploy-complex-application-predictably.md)」を参照してください。