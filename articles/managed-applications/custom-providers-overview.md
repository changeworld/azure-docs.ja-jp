---
title: Azure Custom Providers プレビューの概要
description: Azure Resource Manager を使用してカスタム リソース プロバイダーを作成するための概念について説明します。
author: MSEvanhi
ms.service: managed-applications
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: evanhi
ms.openlocfilehash: bbfb10f612690af0f4fd3683e0f58986a21048d8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157919"
---
# <a name="azure-custom-providers-preview-overview"></a>Azure Custom Providers プレビューの概要

Azure Custom Providers を使用すると、Azure を拡張してお使いのサービスと連携することができます。 カスタマイズされたリソースの種類とアクションを含む、独自のリソース プロバイダーを作成します。 カスタム プロバイダーは Azure Resource Manager と統合されています。 テンプレートのデプロイやロールベースのアクセス制御などの Resource Manager の機能を使用して、サービスのデプロイとセキュリティ保護を行うことができます。

この記事では、カスタム プロバイダーとその機能の概要について説明します。 次の図は、カスタム プロバイダーに定義されているリソースとアクションのワークフローを示しています。

![カスタム プロバイダーの概要](./media/custom-providers-overview/overview.png)

> [!IMPORTANT]
> 現在、Custom Providers はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="define-your-custom-provider"></a>カスタム プロバイダーを定義する

まず、お使いのカスタム プロバイダーについて Azure Resource Manager に知らせることから始めます。 **Microsoft.CustomProviders/resourceProviders** のリソースの種類を使用するカスタム プロバイダー リソースを Azure にデプロイします。 そのリソースで、お使いのサービスのリソースとアクションを定義します。

たとえば、サービスに **users** という名前のリソースの種類が必要な場合は、そのリソースの種類をカスタム プロバイダー定義に含めます。 リソースの種類ごとに、そのリソースの種類の REST 操作 (PUT、GET、DELETE) を提供するエンドポイントを指定します。 エンドポイントは任意の環境でホストできます。また、そのリソースの種類に対する操作をサービスが処理する方法に関するロジックが含まれています。

リソース プロバイダーのカスタム アクションを定義することもできます。 アクションは POST 操作を表します。 起動、停止、再起動などの操作にアクションを使用します。 要求を処理するエンドポイントを指定します。

次の例は、アクションとリソースの種類を使用してカスタム プロバイダーを定義する方法を示しています。

```json
{
  "apiVersion": "2018-09-01-preview",
  "type": "Microsoft.CustomProviders/resourceProviders",
  "name": "[parameters('funcName')]",
  "location": "[parameters('location')]",
  "properties": {
    "actions": [
      {
        "name": "ping",
        "routingType": "Proxy",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ],
    "resourceTypes": [
      {
        "name": "users",
        "routingType": "Proxy,Cache",
        "endpoint": "[concat('https://', parameters('funcName'), '.azurewebsites.net/api/{requestPath}')]"
      }
    ]
  }
},
```

**routingType** の有効な値は `Proxy` と `Cache` です。 Proxy は、リソースの種類またはアクションに対する要求がエンドポイントによって処理されることを意味します。 cache 設定は、アクションではなく、リソースの種類に対してのみサポートされています。 cache を指定するには、proxy も指定する必要があります。 Cache は、読み取り操作を最適化するためにエンドポイントからの応答が格納されることを意味します。 cache 設定を使用すると、他の Resource Manager サービスと一貫性があり、準拠している API をより簡単に実装できます。

## <a name="deploy-your-resource-types"></a>リソースの種類をデプロイする

カスタム プロバイダーを定義したら、カスタマイズしたリソースの種類をデプロイできます。 次の例は、カスタム プロバイダーのリソースの種類をデプロイするテンプレートに含める JSON を示しています。 このリソースの種類は、他の Azure リソースと同じテンプレートにデプロイできます。

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.CustomProviders/resourceProviders/users",
    "name": "[concat(parameters('rpname'), '/santa')]",
    "location": "[parameters('location')]",
    "properties": {
        "FullName": "Santa Claus",
        "Location": "NorthPole"
    }
}
```

## <a name="manage-access"></a>アクセスを管理する

リソース プロバイダーへのアクセスを管理するには、Azure の[ロールベースのアクセス制御](../role-based-access-control/overview.md)を使用します。 所有者、共同作成者、閲覧者などの[組み込みロール](../role-based-access-control/built-in-roles.md)をユーザーに割り当てることができます。 または、リソース プロバイダーの操作に固有の[カスタム ロール](../role-based-access-control/custom-roles.md)を定義できます。

## <a name="next-steps"></a>次の手順

この記事では、カスタム プロバイダーについて学習しました。 次の記事にアクセスしてカスタム プロバイダーを作成しましょう。

> [!div class="nextstepaction"]
> [チュートリアル:カスタム プロバイダーの作成とカスタム リソースのデプロイ](create-custom-provider.md)
