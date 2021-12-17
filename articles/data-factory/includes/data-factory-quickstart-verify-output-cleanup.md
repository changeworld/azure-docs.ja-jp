---
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
author: linda33wj
ms.author: jingwang
ms.openlocfilehash: 18ff33287be7f5763a23cdf33c6e023501645299
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "126056631"
---
## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

このパイプラインは、adftutorial BLOB コンテナーに対して output フォルダーを自動的に作成します。 そのうえで、input フォルダーから output フォルダーに emp.txt ファイルをコピーします。 

1. Azure portal の **adftutorial** コンテナー ページで、 **[最新の情報に更新]** を選択して出力フォルダーを表示します。 
    
    :::image type="content" source="media/data-factory-quickstart-verify-output-cleanup/output-refresh.png" alt-text="コンテナー ページのスクリーンショット。ここでページを最新の情報に更新することができます。":::

2. フォルダー一覧で、 **[output]** を選択します。 

3. **emp.txt** が output フォルダーにコピーされていることを確認します。 

    :::image type="content" source="media/data-factory-quickstart-verify-output-cleanup/output-file.png" alt-text="output フォルダーの内容を示すスクリーンショット。":::

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このクイックスタートで作成したリソースは、2 とおりの方法でクリーンアップすることができます。 [Azure リソース グループ](../../azure-resource-manager/management/overview.md)を削除した場合、そのリソース グループに含まれたすべてのリソースが対象となります。 他のリソースをそのまま維持する場合は、このチュートリアルで作成したデータ ファクトリだけを削除してください。

リソース グループを削除すると、その中のデータ ファクトリも含めて、すべてのリソースが削除されます。 次のコマンドを実行して、リソース グループ全体を削除します。 

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

> [!Note]
> リソース グループの削除には、しばらく時間がかかることがあります。 処理が完了するまでお待ちください。

リソース グループ全体ではなく、データ ファクトリだけを削除する場合は、次のコマンドを実行します。 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```