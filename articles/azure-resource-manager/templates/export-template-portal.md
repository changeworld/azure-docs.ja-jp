---
title: Azure portal でテンプレートをエクスポートする
description: Azure portal を使用して、サブスクリプション内のリソースから Azure Resource Manager テンプレートをエクスポートします。
ms.topic: conceptual
ms.date: 09/01/2021
ms.openlocfilehash: c6987f95f2ccb953977244d8ff70b2f925f35f2e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436250"
---
# <a name="use-azure-portal-to-export-a-template"></a>Azure portal を使用してテンプレートをエクスポートする

[!INCLUDE [Export template intro](../../../includes/resource-manager-export-template-intro.md)]

この記事では、**ポータル** を使用してテンプレートをエクスポートする方法について説明します。 その他のオプションについては、以下を参照してください。

* [Azure CLI でテンプレートをエクスポートする](export-template-cli.md)
* [Azure PowerShell でテンプレートをエクスポートする](export-template-powershell.md)
* [リソース グループからの REST API エクスポート](/rest/api/resources/resourcegroups/exporttemplate)および[デプロイ履歴からの REST API エクスポート](/rest/api/resources/deployments/export-template)

[!INCLUDE [Export template choose option](../../../includes/resource-manager-export-template-choose-option.md)]

[!INCLUDE [Export template limitations](../../../includes/resource-manager-export-template-limitations.md)]

## <a name="export-template-from-a-resource-group"></a>リソース グループからテンプレートをエクスポートする

リソース グループから 1 つまたは複数のリソースをエクスポートするには:

1. エクスポートするリソースを含むリソース グループを選択します。

1. 該当するチェックボックスを選択して、1 つまたは複数のリソースを選択します。  すべてを選択するには、 **[名前]** の左側にあるチェックボックスをオンにします。 **[テンプレートのエクスポート]** メニュー項目を有効にするには、少なくとも 1 つのリソースを設定している必要があります。

   ![すべてのリソースをエクスポートする](./media/export-template-portal/select-all-resources.png)

    スクリーンショットでは、ストレージ アカウントのみが選択されています。
1. **[テンプレートのエクスポート]** を選択します。

1. エクスポートされたテンプレートが表示され、ダウンロードおよびデプロイできるようになります。

   ![テンプレートの表示](./media/export-template-portal/show-template.png)

   **[パラメーターを含める]** は、既定で選択されています。  選択すると、テンプレートの生成時にすべてのテンプレート パラメーターが含められます。 独自のパラメーターを作成する場合は、パラメーターを含めないようにこのチェックボックスを切り替えます。

## <a name="export-template-from-a-resource"></a>リソースからテンプレートをエクスポートする

1 つのリソースをエクスポートするには:

1. エクスポートするリソースを含むリソース グループを選択します。

1. エクスポートするリソースを選択して、リソースを選択します。

1. そのリソースについて、左側のウィンドウで **[テンプレートのエクスポート]** を選択します。

   ![リソースのエクスポート](./media/export-template-portal/export-single-resource.png)

1. エクスポートされたテンプレートが表示され、ダウンロードおよびデプロイできるようになります。 このテンプレートには、1 つのリソースのみが含まれています。 **[パラメーターを含める]** は、既定で選択されています。  選択すると、テンプレートの生成時にすべてのテンプレート パラメーターが含められます。 独自のパラメーターを作成する場合は、パラメーターを含めないようにこのチェックボックスを切り替えます。

## <a name="download-template-before-deployment"></a>デプロイ前にテンプレートをダウンロードする

ポータルには、テンプレートをデプロイする前にダウンロードするオプションがあります。 このオプションは、PowerShell や Azure CLI では使用できません。

1. デプロイする Azure サービスを選択します。

1. 新しいサービスの値を入力します。

1. 検証に合格したら、デプロイを開始する前に、 **[Automation のテンプレートをダウンロードする]** を選択します。

   ![テンプレートのダウンロード](./media/export-template-portal/download-before-deployment.png)

1. テンプレートが表示され、ダウンロードおよびデプロイできるようになります。

## <a name="export-template-after-deployment"></a>デプロイ後にテンプレートをエクスポートする

既存のリソースをデプロイするために使用されたテンプレートをエクスポートできます。 取得するテンプレートは、デプロイに使用されたものとまったく同じです。

1. エクスポートするリソース グループを選択します。

1. **[デプロイ]** の下のリンクを選択します。

   ![デプロイ履歴の選択](./media/export-template-portal/select-deployment-history.png)

1. デプロイ履歴からいずれかのデプロイを選択します。

   ![デプロイの選択](./media/export-template-portal/select-details.png)

1. **[テンプレート]** を選択します。 このデプロイに使用されているテンプレートが表示され、ダウンロードできるようになります。

   ![テンプレートの選択](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>次のステップ

- [Azure CLI](export-template-cli.md)、[Azure PowerShell](export-template-powershell.md)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用してテンプレートをエクスポートする方法を学びます。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./syntax.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](../index.yml)のページをご覧ください。