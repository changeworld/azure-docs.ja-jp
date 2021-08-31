---
title: Azure Policy を使用して VM に対して Azure Site Recovery を有効にする
description: Azure Site Recovery を使用して VM を保護するポリシー サポートを有効にする方法について説明します。
author: rishjai-msft
ms.author: rishjai
ms.topic: how-to
ms.date: 07/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 1c936df9ffb467d732e0c07651e7c6fb31f28b5c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739046"
---
# <a name="using-policy-with-azure-site-recovery-public-preview"></a>Azure Site Recovery でのポリシーの使用 (パブリック プレビュー)

この記事では、Azure Policy を使用して、リソースに対して [Azure Site Recovery](./site-recovery-overview.md) を設定する方法について説明します。 [Azure Policy](../governance/policy/overview.md) は、特定のビジネス ルールを Azure リソースに適用し、そのリソースのコンプライアンスを評価するのに役立ちます。

## <a name="disaster-recovery-with-azure-policy"></a>Azure Policy を使用したディザスター リカバリー
Site Recovery は、計画的または計画外のゾーンまたはリージョンの障害が発生した場合にアプリケーションの稼働状態を維持するのに役立ちます。 Azure portal を介して複数のコンピューターで Site Recovery を大規模に有効にすることは困難な場合があります。 そこで、ポータルを介して特定のリソース グループ (ポリシーの "_スコープ_") で Site Recovery を一括して有効にする方法があります。

Azure Policy がこの問題を解決します。 リソース グループに対してディザスター リカバリー ポリシーを作成すると、そのリソース グループに追加されたすべての新しい仮想マシンで Site Recovery が自動的に有効になります。 さらに、リソース グループに既に存在するすべての仮想マシンに対して、"_修復_" と呼ばれるプロセスを通じて Site Recovery を有効にすることができます (詳細は以下のとおりです)。

>[!NOTE]
>このポリシーの "_スコープ_" は、リソース グループ レベルにする必要があります。

## <a name="prerequisites"></a>前提条件

- ポリシーを割り当てる方法を[こちら](../governance/policy/assign-policy-portal.md)で参照してください。
- Azure から Azure へのディザスター リカバリーのアーキテクチャの詳細について[こちら](./azure-to-azure-architecture.md)を参照してください。
- Azure Site Recovery ポリシー サポートについてサポート マトリックスを確認してください。

**シナリオ** | **サポートに関する声明**
--- | ---
Managed Disks | サポートされています
非管理対象ディスク  | サポートされていません
複数ディスク | サポートされています
可用性セット | サポートされています
可用性ゾーン | サポートされています
Azure Disk Encryption (ADE) 対応 VM | サポートされていません
近接配置グループ (PPG) | サポートされています
カスタマー マネージド キー (CMK) 対応ディスク | サポートされていません
記憶域スペース ダイレクト (S2D) クラスター | サポートされていません
Azure Resource Manager デプロイ モデル | サポートされています
クラシック デプロイ モデル | サポートされていません
ゾーンからゾーンへの DR  | サポートされています
既定で Azure により適用される他のポリシー (ある場合) との相互運用性 | サポートされています

>[!NOTE]
>次の場合、Site Recovery は有効になりません。 ただし、リソース コンプライアンスでは "_非準拠_" として反映されます。
>1. サポートされていない VM がポリシーのスコープ内で作成された場合。
>1. VM が可用性セットと PPG の両方に含まれる場合。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する
このセクションでは、新しく作成されたすべてのリソースに対して Azure Site Recovery を有効にするポリシー割り当てを作成します。
1. **Azure portal** に移動して、 **[Azure Policy]** に進みます。
1. Azure Policy ページの左側にある **[割り当て]** を選択します。 割り当てとは、特定のスコープで実行するよう割り当てられたポリシーです。
   :::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assignments.png" alt-text="[ポリシーの概要] ページで [割り当て] ページを選択する操作のスクリーンショット。" border="false":::

1. **[ポリシー - 割り当て]** ページの上部で **[ポリシーの割り当て]** を選択します。
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-assign-policy.png" alt-text="[割り当て] ページで [ポリシーの割り当て] を選択する操作のスクリーンショット。" border="false":::

1. **[ポリシーの割り当て]** ページで、省略記号を選択して **[スコープ]** を設定し、続いてサブスクリプションを選択した後にリソース グループを選択します。 スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 次に、**[スコープ]** ページの下部にある **[選択]** ボタンを使用します。

1. **[ポリシー定義]** の横の省略記号を選択して、"_ポリシー定義ピッカー_" を起動します。 " _[レプリケーションを有効にして、仮想マシンでのディザスター リカバリーを構成する] を探し_"、このポリシーを選択します。
:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/select-policy-definition.png" alt-text="[基本] ページで &quot;ポリシー定義&quot; を選択したスクリーンショット。" border="true":::

1. **[割り当て名]** には選択したポリシー名が自動的に入力されますが、この名前は変更できます。 複数の Azure Site Recovery ポリシーを同じスコープに割り当てる予定の場合に役立つ可能性があります。

1. **[次へ]** を選択してポリシーの Azure Site Recovery プロパティを構成します。

## <a name="configure-target-settings-and-properties"></a>ターゲット設定とプロパティの構成
Azure Site Recovery を有効にするポリシーを作成しているところです。 ここで、ターゲット設定とプロパティを構成しましょう。
1. 現在、 _[ポリシーの割り当て]_ ワークフローの _[パラメーター]_ セクションを表示しています。これは、このようになります。:::image type="content" source="./media/azure-to-azure-how-to-enable-policy/specify-parameters.png" alt-text="[パラメーター] ページのパラメーター設定のスクリーンショット" border="true":::。
1. これらのパラメーターに適切な値を選択します。
    - **ソース リージョン**: ポリシーが適用される仮想マシンのソース リージョン。
    >[!NOTE]
    >ポリシーは、ポリシーのスコープ内でソース リージョンに属するすべての仮想マシンに適用されます。 ソース リージョンに存在しない仮想マシンは、"_リソースのコンプライアンス_" に含まれません。
    - **ターゲット リージョン**: ソース仮想マシン データがレプリケートされる場所。 Site Recovery は、レプリケート先にできるターゲット リージョンの一覧を提供します。 Recovery Services コンテナーと同じ場所にすることをお勧めします。
    - **ターゲット リソース グループ**: レプリケートされたすべての仮想マシンが属するリソース グループ。 既定では、Site Recovery により、ターゲット リージョン内に新しいリソース グループが作成されます。
    - **コンテナー リソース グループ**: Recovery Services コンテナーが存在するリソース グループ。
    - **Recovery Services コンテナー**: スコープ内のすべての VM が保護されるコンテナー。 ポリシーは、必要であればユーザーに代わって、新しいコンテナーを作成できます。
    - **復旧仮想ネットワーク**: 復旧仮想マシンに使用されるターゲット リージョンの既存の仮想ネットワークを選択します。 ポリシーは、必要であれば、新しい仮想ネットワークも作成できます。
    - **ターゲット可用性ゾーン**: 仮想マシンがフェールオーバーするターゲット リージョンの可用性ゾーンを入力します。
    >[!NOTE]
    >ゾーンからゾーンへのシナリオでは、ソース リージョンと同じターゲット リージョンを選択し、別の可用性ゾーンを _[ターゲット可用性ゾーン]_ で選択する必要があります。     
    >ゾーンからゾーンへの DR を設定している場合、リソース グループ内の一部の仮想マシンが既にターゲット可用性ゾーン内にあると、それらに対してはポリシーが適用されません。
    - **効果**: ポリシーの実行を有効または無効にします。 _DeployIfNotExists_ を選択すると、ポリシーは作成されるとすぐに有効になります。

1. **[次へ]** を選択して修復タスクについて決定します。

## <a name="remediation-and-other-properties"></a>修復とその他のプロパティ
1. Azure Site Recovery のターゲット プロパティが構成されました。 ただし、このポリシーは、ポリシーのスコープ内で新たに作成された仮想マシンに対してのみ有効になります。 ポリシーが割り当てられた後は、修復タスクを使用して既存のリソースに適用することができます。 ここで _[修復タスクの作成]_ チェックボックスをオンにして修復タスクを作成できます。

1. Azure Policy によって、スコープ内のリソースに対して Azure Site Recovery を有効にするための所有者アクセス許可を持つ[マネージド ID](../governance/policy/how-to/remediate-resources.md) が作成されます。

1. _[非コンプライアンス メッセージ]_ タブで、ポリシーに関するカスタムの非コンプライアンス メッセージを構成できます。

1. ページ下部にある [次へ] またはページの上部にある _[確認と作成]_ タブを選択して、割り当てウィザードの次のセグメントに移動します。

1. 選択したオプションを確認してから、ページ下部にある _[作成]_ を選択します。

## <a name="next-steps"></a>次の手順

テスト フェールオーバーの実行に関する[詳細を確認](site-recovery-test-failover-to-azure.md)する。
