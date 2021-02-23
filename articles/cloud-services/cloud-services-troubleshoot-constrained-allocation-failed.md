---
title: Azure にクラウド サービスをデプロイするときの ConstrainedAllocationFailed のトラブルシューティング | Microsoft Docs
description: この記事では、Azure にクラウド サービスをデプロイするときの ConstrainedAllocationFailed 例外を解決する方法について説明します。
services: cloud-services
author: mibufo
ms.author: v-mibufo
ms.service: cloud-services
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: de344bbcd89158676bacf2a8aa1743d282700b9d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520822"
---
# <a name="troubleshoot-constrainedallocationfailed-when-deploying-a-cloud-service-to-azure"></a>Azure にクラウド サービスをデプロイするときの ConstrainedAllocationFailed のトラブルシューティング

この記事では、制約のために Azure Cloud Services をデプロイできない割り当てエラーのトラブルシューティングを行います。

次のことを行う場合に Microsoft Azure が割り当てられます。

- Cloud Services インスタンスのアップグレード

- 新しい Web または worker ロール インスタンスの追加

- クラウド サービスへのインスタンスのデプロイ

これらの操作を行っているときには、Azure サブスクリプションの制限に達していなくても、エラーが発生する場合があります。

> [!TIP]
> この情報は、サービスのデプロイを計画する場合にも役立ちます。

## <a name="symptom"></a>症状

Azure portal で、ご利用のクラウド サービスに移動し、サイドバーの *[操作ログ (クラシック)]* を選択してログを表示します。

そのクラウド サービスのログを調べると、次の例外が表示されます。

|例外の種類  |エラー メッセージ  |
|---------|---------|
|ConstrainedAllocationFailed     |Azure 操作 '`{Operation ID}`' がコード Compute.ConstrainedAllocationFailed で失敗しました。 詳細:割り当てに失敗しました。要求の制約条件を満たしていません。 要求されている新しいサービスのデプロイがアフィニティ グループにバインドされているか、仮想ネットワークを対象としています。または、このホストされるサービスに既存のデプロイがあります。 ここに挙げた条件に該当する場合には、新しいデプロイが特定の Azure リソースに制限されます。 後でもう一度やり直すか、VM サイズまたはロール インスタンスの数を減らしてみてください。 可能な場合には、上に挙げた制約条件を削除することや、別のリージョンにデプロイすることもできます。|

## <a name="cause"></a>原因

デプロイ先のリージョンまたはクラスターに容量の問題があります。 これは、選択したリソース SKU が、指定した場所で利用できない場合に発生します。

> [!NOTE]
> クラウド サービスの最初のノードをデプロイすると、リソース プールにそれが "*ピン留め*" されます。 リソース プールは、単一のクラスターとすることも、クラスター グループとすることもできます。
>
> 時間が経過すると、このリソース プール内のリソースが完全に使用されてしまう可能性があります。 ピン留めされたリソース プールで利用できるリソースが不足している場合に、クラウド サービスから追加リソースの割り当て要求が出されると、その要求によって[割り当てエラー](cloud-services-allocation-failures.md)が発生します。

## <a name="solution"></a>解決策

このシナリオでは、ご利用のクラウド サービスのデプロイ先として別のリージョンまたは SKU を選択する必要があります。 クラウド サービスをデプロイまたはアップグレードする前に、リージョンまたは可用性ゾーンで使用可能な SKU を特定できます。 以下に示す [Azure CLI](#list-skus-in-region-using-azure-cli)、[PowerShell](#list-skus-in-region-using-powershell)、または [REST API](#list-skus-in-region-using-rest-api) のプロセスに従ってください。

### <a name="list-skus-in-region-using-azure-cli"></a>Azure CLI を使用してリージョン内の SKU を一覧表示する

[az vm list-skus](https://docs.microsoft.com/cli/azure/vm.html#az_vm_list_skus) コマンドを使用できます。

- 出力をフィルター処理で使用中の場所に絞り込むには、`--location` パラメーターを使用します。
- 名前の一部で検索するには、`--size` パラメーターを使用します。
- 詳細については、「[SKU 利用不可エラーを解決する](../azure-resource-manager/templates/error-sku-not-available.md#solution-2---azure-cli)」のガイドを参照してください。

    **例:**

    ```azurecli
    az vm list-skus --location southcentralus --size Standard_F --output table
    ```

    **結果の例:** ![使用可能な SKU を表示する 'az vm list-skus --location southcentralus --size Standard_F --output table' コマンドを Azure CLI で実行した出力。](./media/cloud-services-troubleshoot-constrained-allocation-failed/cloud-services-troubleshoot-constrained-allocation-failed-1.png)

#### <a name="list-skus-in-region-using-powershell"></a>PowerShell を使用してリージョン内の SKU を一覧表示する

[Get-AzComputeResourceSku](https://docs.microsoft.com/powershell/module/az.compute/get-azcomputeresourcesku) コマンドを使用できます。

- 結果を場所でフィルター処理します。
- このコマンドには、PowerShell の最新バージョンが必要です。
- 詳細については、「[SKU 利用不可エラーを解決する](../azure-resource-manager/templates/error-sku-not-available.md#solution-1---powershell)」のガイドを参照してください。

**例:**

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations -icontains "centralus"}
```

**その他の便利なコマンド:**

サイズ (Standard_DS14_v2) を含む場所をフィルターで除外します。

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("Standard_DS14_v2")}
```

サイズ (V3) を含むすべての場所をフィルターで除外します。

```azurepowershell
Get-AzComputeResourceSku | where {$_.Locations.Contains("centralus") -and $_.ResourceType.Contains("virtualMachines") -and $_.Name.Contains("v3")} | fc
```

#### <a name="list-skus-in-region-using-rest-api"></a>REST API を使用してリージョン内の SKU を一覧表示する

「[リソース SKU - 一覧](https://docs.microsoft.com/rest/api/compute/resourceskus/list)」の操作を使用できます。 次の形式で利用可能な SKU とリージョンを返します。

```json
{
  "value": [
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A0",
      "tier": "Standard",
      "size": "A0",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    {
      "resourceType": "virtualMachines",
      "name": "Standard_A1",
      "tier": "Standard",
      "size": "A1",
      "locations": [
        "eastus"
      ],
      "restrictions": []
    },
    <Rest_of_your_file_is_located_here...>
  ]
}
    
```

## <a name="next-steps"></a>次のステップ

その他の割り当てエラーの解決策について確認し、それらを導く方法について十分に理解します。

> [!div class="nextstepaction"]
> [割り当てエラー (クラウド サービス)](cloud-services-allocation-failures.md)

この記事で Azure の問題に対処できない場合は、[MSDN および Stack Overflow](https://azure.microsoft.com/support/forums/) の Azure 関連フォーラムを参照してください。 問題をこれらのフォーラムまたは [Twitter の @AzureSupport](https://twitter.com/AzureSupport) に投稿できます。 Azure サポート要求を送信することもできます。 サポート要求を送信するには、[[Azure サポート]](https://azure.microsoft.com/support/options/) ページで *[サポートを受ける]* を選択します。
