---
title: データのラベル付けベンダー企業
titleSuffix: Azure Machine Learning
description: データのラベル付けベンダー会社を使用して、データのラベル付けプロジェクトのデータにラベルを付ける
services: machine-learning
ms.service: machine-learning
ms.subservice: mldata
ms.reviewer: sgilley
author: kvijaykannan
ms.author: vkann
ms.date: 10/21/2021
ms.topic: how-to
ms.openlocfilehash: 449a6de24b9b02d64e1bda66f24afaa683868a56
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558556"
---
# <a name="work-with-a-data-labeling-vendor-company"></a>データのラベル付けベンダー会社と連携する

データのラベル付けのサポートを受けるために、データのラベル付けベンダー会社と契約する方法について説明します。 これらの会社の詳細、および提供されているラベル付けサービスの詳細については、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend) のリスト ページを参照してください。


## <a name="workflow-summary"></a>ワークフローの概要

データのラベル付けプロジェクトを作成する前に、以下を行います。

1. ラベル付けサービス プロバイダーを選択します。  Azure Marketplace でプロバイダーを検索するには、以下を行います。
    1. [これらのベンダーのラベル付け会社の掲載情報](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)を確認します。
    1. ベンダーのラベル付け会社が要件を満たしている場合は、Azure Marketplace の **[連絡してください]** オプションを選択します。 Azure Marketplace は、お問い合わせをベンダーのラベル付け会社に転送します。 選択する会社の最終決定を行う前に、複数のベンダーのラベル付け会社に問い合わせることができます。

1. ラベル付けのサービス プロバイダーと連絡を取り、契約を締結します。

ベンダーのラベル付け会社との契約が成立したら、以下を行います。

1. [Azure Machine Learning スタジオ](https://ml.azure.com)でラベル付けプロジェクトを作成します。 プロジェクトの作成の詳細については、[画像のラベル付けプロジェクト](how-to-create-image-labeling-projects.md)または[テキストのラベル付けプロジェクト](how-to-create-text-labeling-projects.md)の作成方法に関するページを参照してください。
1. Azure Marketplace に掲載されているデータのラベル付けプロバイダー以外を使用することもできます。  ただし、Azure Marketplace のプロバイダーを使用する場合は、以下を行うようにしてください。
    1. 従業員のステップで **[Use a vendor labeling company from Azure Marketplace]\(Azure Marketplace に掲載されているベンダー ラベル付け企業を使用する\)** を選択します。
    1. ドロップダウンで、適切なデータのラベル付け会社を選択します。

    > [!NOTE]
    > ラベル付けプロジェクトを作成した後に、ラベル付けベンダー会社の名前を変更することはできません。

1. プロバイダーを Azure Marketplace で見つけた場合も、その他の場所で見つけた場合も、Azure ロールベースのアクセス制御 (RBAC) を使用して、ラベル付けベンダー会社へのアクセス (`labeler` ロール、`techlead` ロール) を有効にします。 これらのロールにより、会社からリソースにアクセスしてデータに注釈を付けることができます。

## <a name="select-a-company"></a><a name="review"></a> 会社の選定

Microsoft は、お客様のニーズを満たすことができる知識と経験を持つラベル付けサービス プロバイダーを特定しています。 ラベル付けサービス プロバイダーについて理解し、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend) の掲載ページからお客様のプロジェクトのニーズや要件を考慮して、プロバイダーを選ぶことができます。

> [!IMPORTANT]
> これらの会社と、提供されているラベル付けサービスの詳細については、Azure Marketplace の掲載ページを参照してください。 Azure Marketplace でサービスを提供しているラベル付け会社を利用するかどうかを決定することは、お客様の責任であり、ラベル付け会社が持つ経験、サービス、スタッフ、条件などが、お客様のプロジェクトの要件を満たすかどうかを独自に判断する必要があります。 お客様は、Azure Marketplace を通じてサービスを提供しているラベル付け会社に、Azure Marketplace の **[連絡してください]** オプションを使って連絡することができます。連絡した企業からは、3 営業日以内に連絡が届きます。 お客様は、ラベリング会社と直接契約を行い、支払いも直接行います。

Microsoft は、Azure Marketplace に掲載されているラベル付けサービス プロバイダー候補のリストを定期的に見直しているため、時期を問わず、リストにプロバイダーが追加されたり、削除されたりすることがあります。  

* プロバイダーが削除されても、既存のプロジェクトや、会社によるプロジェクトへのアクセスには影響しません。
* Azure Marketplace に掲載されなくなったプロバイダーを使用する場合は、新しいプロジェクトの **[Use a vendor labeling company from Azure Marketplace]\(Azure Marketplace に掲載されているベンダー ラベル付け企業を使用する\)** オプションは選択しないでください。
* 削除されたプロバイダーは、Azure Marketplace に掲載されなくなります。
* 削除されたプロバイダーに、Azure Marketplace 経由で連絡を取ることはできなくなります。

さまざまなラベル付けプロジェクトのニーズに合わせて、複数のベンダーのラベル付け会社に依頼することができます。 プロジェクトは、それぞれ 1 社のベンダーのラベル付け会社にリンクされます。

以下は、Azure Machine Learning のデータのラベル付けサービスを使用してデータのラベル付けの支援を行っているベンダーのラベル付け会社です。 [ベンダー会社の掲載一覧](https://azuremarketplace.microsoft.com/marketplace/consulting-services?page=1&search=AzureMLVend)を参照してください。

* [iSoftStone](https://azuremarketplace.microsoft.com/marketplace/consulting-services/isoftstoneinc1614950352893.20210527) 

* [Quadrant Resource](https://azuremarketplace.microsoft.com/marketplace/consulting-services/quadrantresourcellc1587325810226.quadrant_resource_data_labeling)

## <a name="enter-into-a-contract"></a>契約を結ぶ 

依頼するラベル付け会社を選択したら、契約条件を定めた上で、その会社と直接契約を締結する必要があります。 Microsoft は本契約の当事者ではなく、適用される条件の決定や交渉には一切関与しません。 本契約に基づいて支払われる金額は、このラベル付け会社に直接支払われます。

ラベル付けプロジェクトで支援付き機械学習ラベル付けを有効にした場合、Microsoft はこのサービスに関連して消費されたコンピューティング リソースの料金をお客様に別途請求します。 Azure Machine Learning の使用に関連するその他の料金 (Azure Machine Learning ワークスペースで使用されるデータの格納など) は、Microsoft との契約条件に準拠します。

## <a name="enable-access"></a>アクセスを有効にする

ベンダーのラベル付け会社がプロジェクトにアクセスできるようにするには、ワークスペース レベルでの [Azure ロールベースのアクセス制御 (RBAC)](how-to-assign-roles.md#manage-workspace-access) によるアクセスを有効にします。  異なるラベル付けプロジェクトごとに複数のベンダーのラベル付け会社を使用する予定の場合は、会社ごとに別々のワークスペースを作成することをお勧めします。

> [!IMPORTANT]
> ラベル付け会社とお客様によるやり取りのすべての側面 (範囲、品質、スケジュール、価格に関する問題を含むがこれに限定されない) については、Microsoft ではなくお客様が責任を負うものとします。

## <a name="next-steps"></a>次のステップ

* [画像ラベル付けプロジェクトを作成してラベルをエクスポートする](how-to-create-image-labeling-projects.md)
* [テキスト ラベル付けプロジェクトを作成してラベルをエクスポートする (プレビュー)](how-to-create-text-labeling-projects.md)