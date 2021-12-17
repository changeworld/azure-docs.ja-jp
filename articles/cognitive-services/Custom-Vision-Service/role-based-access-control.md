---
title: Azure ロールベースのアクセス制御 - Custom Vision
titleSuffix: Azure Cognitive Services
description: この記事では、Custom Vision プロジェクトに対して Azure ロールベースのアクセス制御を構成する方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 09/11/2020
ms.author: pafarley
ms.openlocfilehash: e2f59dc309181308f409b3533d58d3b6b4b1fdd8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361892"
---
# <a name="azure-role-based-access-control"></a>Azure ロールベースのアクセス制御

Custom Vision では、Azure リソースへの個々のアクセスを管理するための認可システムである、Azure のロールベースのアクセス制御 (Azure RBAC) がサポートされています。 Azure RBAC を使用すると、チーム メンバーによって異なる Custom Vision プロジェクトに対するアクセス許可レベルを割り当てることができます。 Azure RBAC の詳細については、「[Azure RBAC のドキュメント](../../role-based-access-control/index.yml)」を参照してください。

## <a name="add-role-assignment-to-custom-vision-resource"></a>Custom Vision リソースへのロールの割り当ての追加

Azure RBAC は、Custom Vision リソースに割り当てることができます。 Azure リソースへのアクセス権を付与するには、ロールの割り当てを追加します。
1. [Azure portal](https://ms.portal.azure.com/) で **[すべてのサービス]** を選択します。 
1. 次に、 **[Cognitive Services]** を選択し、特定の Custom Vision トレーニング リソースに移動します。
   > [!NOTE]
   > また、リソース グループ、サブスクリプション、または管理グループ全体に対して Azure RBAC を設定することもできます。 これを行うには、目的のスコープ レベルを選択し、目的の項目に移動します (たとえば、 **[リソース グループ]** を選択し、目的のリソース グループをクリックします)。
1. 左側のナビゲーション ウィンドウで **[アクセス制御 (IAM)]** を選択します。
1. **[追加]**  ->  **[ロール割り当ての追加]** の順に選択します。
1. 次の画面の **[ロール]** タブで、追加するロールを選択します。
1. **[メンバー]** タブで、ユーザー、グループ、サービス プリンシパル、またはマネージド ID を選択します。
1. **[確認と 割り当て]** タブで、 **[確認と割り当て]** を選択して ロールを割り当てます。

数分以内に、選択したスコープで選択したロールがターゲットに割り当てられます。 これらの手順については、「[Azure portal を使用して Azure ロールを割り当てる](../../role-based-access-control/role-assignments-portal.md)」を参照してください。

## <a name="custom-vision-role-types"></a>Custom Vision ロールの種類

次の表を使用して、Custom Vision リソースのアクセス ニーズを判断してください。

|Role  |アクセス許可  |
|---------|---------|
|`Cognitive Services Custom Vision Contributor`     | プロジェクトの作成、編集、または削除を含む、プロジェクトへのフル アクセス。        |
|`Cognitive Services Custom Vision Trainer`     | プロジェクトの作成または削除を除くフル アクセス。 トレーナーは、プロジェクトの表示と編集、モデルのトレーニング、公開、非公開、エクスポートを行うことができます。        |
|`Cognitive Services Custom Vision Labeler`     | トレーニング画像のアップロード、編集、または削除、タグの作成、追加、削除、または削除を行うことができます。 ラベラーはプロジェクトを表示できますが、トレーニング画像とタグ以外は更新できません。         |
|`Cognitive Services Custom Vision Deployment`     | モデルの公開、非公開、またはエクスポートができます。 デプロイ担当者はプロジェクトを表示できますが、プロジェクト、トレーニング画像、タグを更新することはできません。        |
|`Cognitive Services Custom Vision Reader`     | プロジェクトを表示できます。 閲覧者は変更を加えることはできません。        |