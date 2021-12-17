---
title: リンクされた Resource Manager テンプレートの使用
description: Azure Data Factory パイプライン内で継続的インテグレーションと継続的デリバリーを使用して、リンクされた Resource Manager テンプレートを使用する方法について説明します。
ms.service: data-factory
ms.subservice: ci-cd
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 09/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d2df62471818bbca069802f3c60993ce79bb50dd
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129219322"
---
# <a name="linked-resource-manager-templates-with-cicd"></a>リンクされた Resource Manager テンプレート (CI/CD を使用)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ご自身のデータ ファクトリに対して継続的インテグレーションと継続的デリバリー (CI/CD) を設定している場合、ファクトリが拡大したときに、Azure Resource Manager テンプレートの制限を超えることがあります。 たとえば、1 つの制限として、Resource Manager テンプレート内のリソースの最大数があります。 ファクトリ用の完全な Resource Manager テンプレートを生成しながら、大規模なファクトリに対応するために、Data Factory では、リンクされた Resource Manager テンプレートが生成されるようになりました。 この機能を使用すると、ファクトリのペイロード全体が複数のファイルに分割されるので、制限によって制約されることはありません。

## <a name="finding-the-linked-templates"></a>リンクされたテンプレートの検索

Git が構成されている場合は、リンクされたテンプレートが生成され、完全な Resource Manager テンプレートと共に、 adf_publish ブランチの linkedTemplates という名前の新しいフォルダーに保存されます。

:::image type="content" source="media/continuous-integration-delivery/linked-resource-manager-templates.png" alt-text="リンクされた Azure Resource Manager テンプレートのフォルダー":::

通常、リンクされた Resource Manager テンプレートは、マスター テンプレートと、マスターにリンクされた一連の子テンプレートで構成されます。 親テンプレートは ArmTemplate_master.json という名前になり、子テンプレートには、ArmTemplate_0. json、ArmTemplate_1.json といったパターンで名前が付けられます。 

## <a name="using-linked-templates"></a>リンクされたテンプレートの使用
完全な Resource Manager テンプレートではなく、リンクされたテンプレートを使用するには、ArmTemplateForFactory.json (完全な Resource Manager テンプレート) ではなく ArmTemplate_master.json をポイントするように CI/CD タスクを更新します。 Resource Manager では、リンクされたテンプレートをストレージ アカウントにアップロードして、デプロイ時に Azure によってアクセスできるようにする必要もあります。 詳細については、[リンクされた Resource Manager テンプレートの VSTS を使用したデプロイ](/archive/blogs/najib/deploying-linked-arm-templates-with-vsts)に関する記事を参照してください。

Data Factory のスクリプトは、必ず CI/CD パイプラインのデプロイ タスクの前後に追加してください。

Git が構成されていない場合は、 **[ARM テンプレート]** 一覧の **[ARM テンプレートのエクスポート]** を使用して、リンクされたテンプレートにアクセスできます。

リソースをデプロイするときには、デプロイが増分更新と完全更新のどちらであるかを指定する必要があります。 これら 2 つのモードの違いは、テンプレートにないリソース グループの既存のリソースを Resource Manager が処理する方法にあります。 「[デプロイ モード](../azure-resource-manager/templates/deployment-modes.md)」を確認してください。

## <a name="next-steps"></a>次のステップ

- [継続的インテグレーションとデリバリーの概要](continuous-integration-delivery.md)
- [Azure Pipelines リリースを使用して継続的インテグレーションを自動化する](continuous-integration-delivery-automate-azure-pipelines.md)
- [各環境用の Resource Manager テンプレートを手動でプロモートする](continuous-integration-delivery-manual-promotion.md)
- [Resource Manager テンプレートでカスタム パラメーターを使用する](continuous-integration-delivery-resource-manager-custom-parameters.md)
- [運用環境への修正プログラムの適用](continuous-integration-delivery-hotfix-environment.md)
- [サンプルのデプロイ前とデプロイ後のスクリプト](continuous-integration-delivery-sample-script.md)