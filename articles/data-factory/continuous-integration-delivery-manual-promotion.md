---
title: Resource Manager テンプレートの手動レベル上げ
description: Azure Data Factory 内で継続的インテグレーションおよびデリバリーを使用して Resource Manager テンプレートを複数の環境に手動でレベル上げする方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485ace0d04375a406c8013d60d45635a596964a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319517"
---
# <a name="manually-promote-a-resource-manager-template-to-each-environment"></a>Resource Manager テンプレートを各環境に手動でレベル上げする

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

次の手順を使用して、Azure Data Factory 内での継続的インテグレーションおよびデリバリー用に Resource Manager テンプレートを各環境にレベル上げします。

## <a name="steps-to-manually-promote-a-template"></a>テンプレートを手動でレベル上げする手順

1. データ ファクトリの **[管理]** ハブに移動し、[ソース管理] セクションの **[ARM テンプレート]** を選択します。 **[ARM テンプレート]** セクションで、 **[ARM テンプレートのエクスポート]** を選択して、開発環境にデータ ファクトリ用の Resource Manager テンプレートをエクスポートします。

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image-1.png" alt-text="Resource Manager テンプレートのエクスポート":::

1. テストおよび運用データ ファクトリで、 **[Import ARM template]\(ARM テンプレートのインポート\)** を選択します。 このアクションによって Azure Portal に移動して、エクスポートされたテンプレートをインポートできます。 **[Build your own template in the editor]\(エディターで独自のテンプレートを作成する\)** を選択して、Resource Manager テンプレート エディターを開きます。

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-build-your-own-template.png" alt-text="独自のテンプレートの作成"::: 

1. **[ファイルの読み込み]** を選択し、生成された Resource Manager テンプレートを選択します。 これは、手順 1 でエクスポートした .zip ファイルに格納されている **arm_template.json** ファイルです。

   :::image type="content" source="media/continuous-integration-delivery/custom-deployment-edit-template.png" alt-text="テンプレートの編集":::

1. 設定セクションで、リンクされたサービスの資格情報など、デプロイに必要な構成値を入力します。 完了したら、 **[確認と作成]** を選択して Resource Manager テンプレートをデプロイします。

   :::image type="content" source="media/continuous-integration-delivery/continuous-integration-image5.png" alt-text="設定セクション":::

## <a name="next-steps"></a>次のステップ

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [リンクされた Resource Manager テンプレート](continuous-integration-delivery-linked-templates.md)
- [修正プログラム運用環境の適用](continuous-integration-delivery-hotfix-environment.md)
- [サンプルのデプロイ前とデプロイ後のスクリプト](continuous-integration-delivery-sample-script.md)