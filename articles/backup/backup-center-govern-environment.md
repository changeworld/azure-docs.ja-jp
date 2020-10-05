---
title: バックアップ センターを使用してバックアップ資産を管理する
description: バックアップ センターを使用して、バックアップの観点からすべてのリソースが準拠していることを確認するために Azure 環境を管理する方法について説明します。
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90993445"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>バックアップ センターを使用してバックアップ資産を管理する

バックアップ センターは、バックアップの観点からすべてのリソースが準拠していることを確認するために Azure 環境を管理するのに役立ちます。 バックアップ センターのガバナンス機能のいくつかを次に示します。

* バックアップ用の Azure ポリシーの表示と割り当て

* バックアップ用に構成されていないすべてのデータソースの表示。

## <a name="supported-scenarios"></a>サポートされるシナリオ

* サポートされるシナリオとサポートされないシナリオの詳細な一覧については、[サポート マトリックス](backup-center-support-matrix.md)を参照してください。

## <a name="azure-policies-for-backup"></a>バックアップ用の Azure ポリシー

バックアップに使用できるすべての [Azure ポリシー](https://docs.microsoft.com/azure/governance/policy/overview)を表示するには、 **[バックアップ用の Azure ポリシー]** メニュー項目を選択します。 これにより、サブスクリプションとリソース グループへの割り当てに使用できる、組み込みとカスタムのすべての[バックアップ用の Azure ポリシー定義](policy-reference.md)が表示されます。

いずれかの定義を選択すると、スコープに[ポリシーを割り当てる](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy)ことができます。

![Azure ポリシー定義を選択する](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>保護可能なデータソース

**[保護可能なデータソース]** メニュー項目を選択すると、バックアップ用に構成されていないすべてのデータソースを表示できます。 この一覧は、データソースのサブスクリプション、リソース グループ、場所、種類、タグでフィルター処理できます。 バックアップする必要のあるデータソースを識別したら、対応するグリッド項目を右クリックし、 **[バックアップ]** を選択してそのリソースのバックアップを構成できます。

![[保護可能なデータソース] メニュー](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>次のステップ

* [バックアップを監視および操作する](backup-center-monitor-operate.md)
* [バックアップ センターを使用してアクションを実行する](backup-center-actions.md)
* [バックアップに関する分析情報を取得する](backup-center-obtain-insights.md)
