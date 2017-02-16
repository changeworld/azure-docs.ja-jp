---
title: "タグを使用した Azure リソースの整理 | Microsoft Docs"
description: "タグを適用して、課金や管理のためにリソースを整理する方法を示します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 01/03/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 87973df7bb1b81c2d1cbdeb31e2fabb584f7e625
ms.openlocfilehash: 61e47f479b7cb05e1aca06e12ff5997769b2097d


---
# <a name="using-tags-to-organize-your-azure-resources"></a>タグを使用した Azure リソースの整理
リソース マネージャーを使用すると、タグを適用してリソースを理論的に整理できます。 タグは、定義したプロパティによりリソースを識別するキーと値のペアで構成されます。 同じカテゴリに属すリソースとしてマークするには、これらのリソースに同じタグを適用します。

特定のタグを持つリソースを表示した場合、すべてのリソース グループからのリソースが表示されます。 同じリソース グループ内のリソースに限定されないため、デプロイ関係にとらわれずにリソースを整理できます。 タグは、課金または管理の目的でリソースを整理する必要がある場合に役立ちます。

リソースまたはリソース グループに追加する各タグは、サブスクリプション全体の分類に自動的に追加されます。 タグの名前でサブスクリプションの分類を事前設定し、リソースとして使用する値を後でタグ付けすることもできます。

各リソースまたはリソース グループには、最大で 15 個のタグを含めることができます。 タグ名は 512 文字まで、タグ値は 256 文字までに制限されます。

> [!NOTE]
> タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。 仮想マシン、仮想ネットワーク、またはストレージをクラシック デプロイ モデル (クラシック ポータルなど) を使用して作成した場合、そのリソースにタグを適用することはできません。 タグ付けをサポートするには、Resource Manager を介してこれらのリソースを再デプロイします。 その他のすべてのリソースでは、タグ付けがサポートされています。
> 
> 

## <a name="templates"></a>テンプレート
デプロイ中にリソースにタグを付けるには、デプロイしているリソースに **タグ** 要素を追加し、タグの名前と値を指定します。 タグの名前と値は、サブスクリプションに事前に存在する必要はありません。 各リソースに対して、最大で 15 個のタグを指定できます。

次の例では、タグを使用したストレージ アカウントを示します。

```json
"resources": [
    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": "[concat('storage', uniqueString(resourceGroup().id))]",
        "location": "[resourceGroup().location]",
        "tags": {
            "dept": "Finance"
        },
        "properties": 
        {
            "accountType": "Standard_LRS"
        }
    }
]
```

現時点で、リソース マネージャーはタグの名前と値のオブジェクトの処理をサポートしません。 代わりに、タグの値のオブジェクトを渡しますが、次の例のようにタグ名を指定する必要があります。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagvalues": {
      "type": "object",
      "defaultValue": {
        "dept": "Finance",
        "project": "Test"
      }
    }
  },
  "resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "examplestorage",
    "tags": {
      "dept": "[parameters('tagvalues').dept]",
      "project": "[parameters('tagvalues').project]"
    },
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }]
}
```

## <a name="portal"></a>ポータル
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli"></a>Azure CLI
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API
ポータルと PowerShell のどちらも、バックグラウンドで [リソース マネージャーの REST AP](https://docs.microsoft.com/rest/api/resources/) を使用します。 別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。

## <a name="tags-and-billing"></a>タグと課金
サービスがサポートされていれば、タグを使用して課金データをグループ化できます。 たとえば、Azure Resource Manager でデプロイされた Virtual Machines を使用すると、仮想マシンの課金データを整理するためのタグを定義および適用できます。 異なる組織向けに複数の VM を実行している場合は、タグを使用すると、コスト センターごとに課金データをグループ化できます。  
また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。

タグに関する情報は、 [Azure Resource Usage API と RateCard API](../billing-usage-rate-card-overview.md) から、あるいはコンマ区切り値 (CSV) ファイルから取得できます。 使用状況ファイルは [Azure アカウント ポータル](https://account.windowsazure.com/)または [EA ポータル](https://ea.azure.com)からダウンロードします。 課金情報へのプログラムによるアクセスの詳細については、「 [Microsoft Azure リソースの消費を把握する](../billing-usage-rate-card-overview.md)」を参照してください。 REST API の操作については、「 [Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。

課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「 **Tags** 」列に表示されます。 詳細については、「[Microsoft Azure の課金内容の確認](../billing/billing-understand-your-bill.md)」を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>次のステップ
* カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。 定義するポリシーには、すべてのリソースが特定のタグに値が指定されていることが必要になる場合があります。 詳細については、「 [ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。
* リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「 [Azure リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。
* リソースのデプロイ時に Azure CLI を使用する方法の概要については、「 [Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」をご覧ください。
* ポータルの使用方法の概要については、「 [Azure ポータルを使用した Azure リソースの管理](resource-group-portal.md)  
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。




<!--HONumber=Jan17_HO1-->


