---
title: テンプレートを使用して VM にタグを付ける方法
description: テンプレートを使用して仮想マシンにタグを付ける方法について説明します。
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.author: cynthn
author: cynthn
ms.date: 10/26/2018
ms.openlocfilehash: 92be32279b812d4a8ff3baad3e3ccc881fe20049
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695327"
---
# <a name="tagging-a-vm-using-a-template"></a>テンプレートを使用して VM にタグを付ける

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

この記事では、Resource Manager テンプレートを使用して Azure の VM にタグを付ける方法について説明します。 タグはユーザー定義のキーと値ペアです。リソースまたはリソース グループに直接設定できます。 現在、Azure では、1 つのリソースまたはリソース グループにつき最大 50 個のタグがサポートされます。 タグは、リソースの作成時に付けたり、既存のリソースに追加したりすることができます。

[このテンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags) では、Compute (仮想マシン)、Storage (ストレージ アカウント)、および Network (パブリック IP アドレス、Virtual Network、およびネットワーク インターフェイス) というリソースにタグを設定します。 このテンプレートは Windows VM のものですが、Linux VM 用に調整できます。

[テンプレート リンク](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/vm-tags)から **[Deploy to Azure (Azure へのデプロイ)]** ボタンをクリックします。 [Azure Portal](https://portal.azure.com/) が開きます。ポータルで、このテンプレートをデプロイできます。

![タグを使用した単純なデプロイ](./media/tag/deploy-to-azure-tags.png)

このテンプレートには、*Department*、*Application*、および *Created By* というタグが含まれています。 別のタグ名を使いたい場合は、テンプレートにタグを追加したり、タグを編集したりすることができます。

![テンプレートの Azure タグ](./media/tag/azure-tags-in-a-template.png)

上の例のように、タグはキーと値のペアとして定義され、コロン (:) で区切られています。 タグは次の形式で定義する必要があります。

```config
"tags": {
    "Key1" : "Value1",
    "Key2" : "Value2"
}
```

選択したタグの編集を完了したら、テンプレート ファイルを保存します。

次に、 **[パラメーターの編集]** セクションで、タグの値を入力します。

![Azure ポータルでタグを編集する](./media/tag/edit-tags-in-azure-portal.png)

**[作成]** をクリックして、タグの値が指定されたこのテンプレートをデプロイします。

### <a name="next-steps"></a>次のステップ

- Azure リソースへのタグ付けについて詳しくは、「[Azure Resource Manager の概要](../azure-resource-manager/management/overview.md)」と「[タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)」をご覧ください。
- Azure リソースの使用を管理するときにタグを役立てる方法については、「[Azure の課金内容の確認」](../cost-management-billing/understand/review-individual-bill.md)を参照してください。
