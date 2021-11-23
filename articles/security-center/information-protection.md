---
title: データの機密性に応じた、セキュリティ アクションの優先順位付け - Microsoft Defender for Cloud
description: Microsoft Defender for Cloud で Azure Purview のデータ秘密度分類を使用する
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 7f82750d9b02bba6e843390562ae2eee060cef47
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477868"
---
# <a name="prioritize-security-actions-by-data-sensitivity"></a>データの機密性に応じた、セキュリティ アクションの優先順位付け

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Purview](../purview/overview.md) (Microsoft のデータ ガバナンス サービス) は、*データの機密性* に関して豊富な分析情報を提供します。 Purview には、自動データ検出、機密データ分類、エンド ツー エンドのデータ系列機能が備わっているため、組織がハイブリッドおよびマルチクラウド環境でデータを管理および制御するのに役立ちます。

Azure Purview を使用する Microsoft Defender for Cloud のお客様は、アラートと推奨事項に含まれる追加の重要なメタデータ層 (機密の可能性があるデータの内包についての情報) を活用できます。 この知識は、トリアージの問題を解決するのに役立ち、セキュリティの専門家は機密データに対する脅威に集中できます。

このページでは、Defender for Cloud 内での Purview のデータ秘密度分類ラベルの統合について説明します。

## <a name="availability"></a>可用性
|側面|詳細|
|----|:----|
|リリース状態:|プレビュー。<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|価格:|データ秘密度分類を作成してスキャンを実行するには、Azure Purview アカウントが必要です。 Defender for Cloud ユーザーは、無料でスキャン結果を表示し、出力を使用できます|
|必要なロールとアクセス許可:|**セキュリティ管理者** と **セキュリティ共同作成者**|
|クラウド:|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用クラウド<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet (**部分的**: SQL サーバーのアラートと脆弱性評価のサブセット。 Behavioral Threat Protection は利用できません。)|
|||


## <a name="the-triage-problem-and-defender-for-clouds-solution"></a>トリアージの問題と Defender for Cloud のソリューション
セキュリティ チームは、受け取った問題をどのようにトリアージするかという課題に定期的に直面します。 

Defender for Cloud には、推奨事項とセキュリティ アラートに優先順位を付けるのに役立つ 2 つのメカニズムが含まれています。

- 推奨事項の場合、**セキュリティ コントロール** が提供されており、各推奨事項がセキュリティ体制全体に対してどの程度重要か理解するのに役立ちます。 Defender for Cloud には、各コントロールの **セキュリティ スコア** 値が含まれており、セキュリティ作業に優先順位を付けるのに役立ちます。 詳しくは、「[セキュリティ コントロールとその推奨事項](secure-score-security-controls.md#security-controls-and-their-recommendations)」を参照してください。

- アラートの場合、各アラートに **重大度ラベル** が割り当てられており、各アラートに注意を向ける優先順位を付けるのに役立ちます。 詳しくは、「[アラートはどのように分類されますか](alerts-overview.md#how-are-alerts-classified)」を参照してください。

ただし、可能であれば、組織の **データ** に対するリスクに、セキュリティ チームの取り組みを集中させることが望ましいです。 2 つの推奨事項がセキュリティ スコアに等しい影響を与えるが、一方の推奨事項が機密データを含むリソースに関連する場合、優先順位を決定するときに、その知識を考慮するのが理想的です。

Azure Purview のデータ秘密度分類とデータ秘密度ラベルでは、この知識が提供されます。

## <a name="discover-resources-with-sensitive-data"></a>機密データを含むリソースを検出する
Defender for Cloud では、検出された機密データに関する重要な情報を提供し、必要なときにその情報を確認できるようにするために、Purview からの情報が複数の場所に表示されます。

> [!TIP]
> リソースが複数の Purview アカウントによってスキャンされた場合、Defender for Cloud に表示される情報は最新のスキャンに関連します。


### <a name="alerts-and-recommendations-pages"></a>アラートと推奨事項のページ
推奨事項を確認するときや、アラートを調べるとき、機密の可能性があるデータの内包についての情報がページに表示されます。

この重要な追加のメタデータ層は、トリアージの問題を解決するのに役立ち、セキュリティ チームは機密データに対する脅威に集中できます。



### <a name="inventory-filters"></a>インベントリ フィルター
[資産インベントリ ページ](asset-inventory.md)には、強力なフィルターのコレクションがあります。これにより、任意のシナリオに関連する条件に従って、未処理のアラートと推奨事項があるリソースをグループ化できます。 これらのフィルターには、**データ秘密度分類** と **データ秘密度ラベル** が含まれます。 これらのフィルターを使用して、Purview が機密データを検出したリソースのセキュリティ体制を評価します。

:::image type="content" source="./media/information-protection/information-protection-inventory-filters.png" alt-text="Microsoft Defender for Cloud の資産インベントリ ページ内の情報保護フィルターのスクリーンショット。" lightbox="./media/information-protection/information-protection-inventory-filters.png":::

### <a name="resource-health"></a>リソース正常性 
アラート、推奨事項、またはインベントリ ページから 1 つのリソースを選択すると、リソース中心のビューを含む詳細な正常性ページが表示され、そのリソースに関連する重要なセキュリティ情報が示されます。 

[リソース正常性] ページでは、1 つのリソースの全体的な正常性のスナップショット ビューが提供されます。 リソースに関する詳細情報と、そのリソースに適用されるすべての推奨事項を確認できます。 また、Microsoft Defender のプランのいずれかを使用する場合、その特定リソースに関する未処理のセキュリティ アラートも確認できます。

特定リソースの正常性を確認すると、このページに Purview の情報が表示されます。この情報を使用して、リソースのスキャンに使用された Purview アカウントと共に、このリソースでどのデータが検出されたか判別できます。

:::image type="content" source="./media/information-protection/information-protection-resource-health.png" alt-text="Azure Purview からの情報保護ラベルと分類が表示されている Defender for Cloud のリソース正常性ページのスクリーンショット。" lightbox="./media/information-protection/information-protection-resource-health.png":::

### <a name="overview-tile"></a>概要タイル
Defender for Cloud の [概要ダッシュボード](overview-page.md)内の専用 **情報保護** タイルには、Azure Purview の対象範囲が表示されます。 また、検出された最も機密性の高いデータを含むリソースの種類も表示されます。

グラフにより、分類されたリソースの種類別の推奨事項とアラートの数が示されます。 このタイルには、追加のリソースをスキャンするための Azure Purview へのリンクも含まれます。 タイルを選択すると、分類されたリソースが Defender for Cloud の資産インベントリ ページに表示されます。

:::image type="content" source="./media/information-protection/overview-dashboard-information-protection.png" alt-text="Microsoft Defender for Cloud の概要ダッシュボード内の情報保護タイルのスクリーンショット。" lightbox="./media/information-protection/overview-dashboard-information-protection.png":::


## <a name="next-steps"></a>次のステップ

関連情報については、以下をご覧ください。

- [Azure Purview とは](../purview/overview.md)
- [Purview のサポートされるデータ ソースとファイルの種類](../purview/sources-and-scans.md)および[サポートされるデータ ストア](../purview/purview-connector-overview.md)
- [Azure Purview のデプロイのベスト プラクティス](../purview/deployment-best-practices.md)
- [Azure Purview でデータにラベルを付ける方法](../purview/how-to-automatically-label-your-content.md)
