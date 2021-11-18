---
title: Microsoft Sentinel でのマルチステージ攻撃の検出 (Fusion) ルールを構成する
description: Microsoft Sentinel での Fusion テクノロジに基づく攻撃の検出ルールを作成して構成します。
services: sentinel
documentationcenter: na
author: yelevin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2a684bf84438bc743d8ce30e62faa539310df024
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716165"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-microsoft-sentinel"></a>Microsoft Sentinel でのマルチステージ攻撃の検出 (Fusion) ルールを構成する

> [!IMPORTANT]
> 新しいバージョンの Fusion 分析ルールは現在 **プレビュー版** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel は、拡張可能な機械学習アルゴリズムに基づく関連付けエンジンである Fusion を使用して、キル チェーンのさまざまな段階で観察される異常な動作と疑わしいアクティビティの組み合わせを特定することによって、マルチステージ攻撃を自動的に検出します。 これらの検出を基に、Microsoft Sentinel では、Microsoft Sentinel 以外では検出が困難であろうインシデントが生成されます。 このインシデントは、2 つ以上のアラートまたはアクティビティで構成されています。 設計上、このようなインシデントでは、ボリュームが低、忠実度が高、重大度が高になります。

この検出テクノロジはご利用の環境によってカスタマイズされるため、[誤検知](false-positives.md)率を減らすだけでなく、情報が制限されているか、不足している攻撃も検出できます。

## <a name="configure-fusion-rules"></a>Fusion ルールの構成

この検出は、Microsoft Sentinel では既定で有効になっています。 その状態を確認または変更するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインして **Microsoft Seninel** に入ります。

1. Microsoft Sentinel のナビゲーション メニューから **[分析]** を選択します。

1. **[アクティブなルール]** タブを選択し、ルールの種類が **Fusion** であるものだけを表示するようリストをフィルターにかけ、 **[名称]** 列で **[高度なマルチステージ攻撃の検出]** を見つけます。 **[状態]** 列を見て、この検出が有効になっているか、無効になっているか確認します。

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Fusion 分析ルールのスクリーンショット。" lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. 状態を変更するには、この項目を選択し、 **[高度なマルチステージ攻撃の検出]** プレビュー ペインで **[編集]** を選択します。

1. **[分析ルール ウィザード]** の **[全般]** タブで、状態 (有効/無効) をメモするか、必要に応じて変更します。

    状態を変更したが、それ以上変更する必要がない場合は、 **[確認と更新]** タブを選択し、 **[保存]** を選択します。

    Fusion 検出ルールをさらに構成するには、 **[次へ: Fusion の構成]** を選択します。

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Fusion ルールの構成のスクリーンショット。" lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Fusion 検出用のソース シグナルを構成する**: 最良の結果を得るには、リストされているすべてのソース シグナルに加え、すべての重要度レベルを含めることをお勧めします。 既定では、これらはすべて既に含まれていますが、次の方法で変更することもできます。

    > [!NOTE]
    > 特定のソース シグナルまたはアラートの重要度レベルを除外すると、そのソースからのシグナル、またはその重要度レベルに一致するアラートに依存する Fusion 検出はトリガーされません。 
    
    - 異常、さまざまなプロバイダーからのアラート、未加工のログなど、**Fusion 検出からのシグナルを除外します**。
     
        *ユース ケース:* ノイズの多いアラートを生成することがわかっている特定のシグナル ソースをテストする場合は、Fusion 検出のためにその特定のシグナル ソースからのシグナルを一時的にオフにすることができます。

    - **プロバイダーごとにアラートの重要度を構成する**: 仕様では、Fusion ML モデルは、複数のデータ ソースからのキルチェーン全体の異常なシグナルに基づいて、信頼度の低いシグナルを重要度の高い 1 つのインシデントに関連付けます。 Fusion に含まれるアラートは、一般的に重要度が低い (中、低、情報) ですが、関連する重要度の高いアラートは、場合によっては含めることができます。
     
        *ユース ケース:* 重要度の高いアラートをトリアージおよび調査するための個別のプロセスがあり、これらのアラートを Fusion に含めない場合は、重要度の高いアラートを Fusion 検出から除外するようにソース シグナルを構成できます。 



    - **Fusion 検出から特定の検出パターンを除外します**。 特定の Fusion 検出は、環境に適用できない場合や、擬陽性が発生しやすい場合があります。 特定の Fusion 検出パターンを除外する場合は、次の手順に従います。

        1. 除外する種類の Fusion インシデントを見つけて開きます。

        1. **[説明]** セクションで、 **[詳細を表示]** を選択します。

        1. **[この特定の検出パターンを除外する]** で、 **[除外リンク]** を選択します。これにより、分析ルール ウィザードの **[Fus​​ion の構成]** タブにリダイレクトされます。

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Fusion インシデントのスクリーンショット。除外リンクを選択します。":::

        **[Fus​​ion の構成]** タブに、検出パターンが表示されます。これは Fusion インシデントのアラートと異常を組み合わせたもので、検出パターンが追加された時刻と共に除外リストに追加されています。

        除外された検出パターンは、その検出パターンにあるごみ箱アイコンを選択すると、いつでも削除できます。

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="除外された検出パターンの一覧のスクリーンショット。":::

        除外された検出パターンに一致するインシデントは引き続きトリガーされますが、それらは **アクティブなインシデント キューには表示されません**。 これには、次の値が自動的に設定されます。

        - **[状態]** : "終了"
        
        - **[終了の分類]** : "不明"

        - **[コメント]** : "自動終了、除外された Fusion 検出パターン"

        - **タグ**: "ExcludedFusionDetectionPattern" - このタグに対してクエリを実行し、この検出パターンに一致するすべてのインシデントを表示できます。

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="自動終了、除外される Fusion インシデントのスクリーンショット。":::



> [!NOTE]
> 現在、Microsoft Sentinel では 30 日間の履歴データを使用して機械学習システムをトレーニングしています。 このデータは、機械学習パイプラインを通過するときに、Microsoft のキーを使用して常に暗号化されます。 ただし、Microsoft Sentinel ワークスペースで CMK を有効にした場合、トレーニング データは[カスタマー マネージド キー (CMK)](customer-managed-keys.md) を使用して暗号化されません。 Fusion をオプトアウトするには、 **[Microsoft Sentinel]** \> **[構成]** \> **[分析]\> [アクティブな規則] の順にクリックし、** **[Advanced Multistage Attack Detection]\(高度なマルチステージ攻撃の検出\)** 規則を右クリックし、 **[無効にする]** を選択します。

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Fusion 検出のスケジュールされた分析のルールを構成する

> [!IMPORTANT]
>
> - 分析ルール アラートを使用した Fusion による検出は、現在 **プレビュー版** です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

**Fusion** では、[スケジュールされた分析のルール](detect-threats-custom.md)により生成されるアラートを使用して、シナリオベースのマルチステージ攻撃や新たな脅威を検出できます。 Microsoft Sentinel の Fusion の機能を最大限に活用できるよう、次の手順でこれらのルールを構成、有効化することをお勧めします。

1. 新たな脅威の場合、Fusion では、キル チェーン (戦術) とエンティティ マッピング情報を含む、[組み込み](detect-threats-built-in.md#scheduled)とセキュリティ アナリストによって作成された[すべてのスケジュールされた分析ルールによって生成されたアラートを使用できます](detect-threats-custom.md)。 Fusion で分析ルールの出力を使用して、新たな脅威を検出できるようにするには、次のようにします。

    - これらのスケジュール ルールの **エンティティ マッピング** を確認します。 [エンティティ マッピング構成に関するセクション](map-data-fields-to-entities.md)を参考にして、クエリの結果から Microsoft Sentinel で認識しているエンティティに、パラメーターをマッピングします。 Fusion は、エンティティ (*ユーザー アカウント*、*IP アドレス* など) に基づいてアラートの関連付けを行うので、その ML アルゴリズムでは、エンティティ情報がなければアラートの一致を検出できません。

    - 分析ルールの詳細で **タクティクス** を確認します。 Fusion ML アルゴリズムでは、[MITRE ATT&CK](https://attack.mitre.org/) タクティクス情報を使用してマルチステージ攻撃を検出します。分析ルールにラベルとして付けたタクティクスは、生成されるインシデントに表示されます。 受け取る警告にタクティクスの情報がない場合、Fusion の計算に影響が出ることがあります。

1. Fusion では、次の **スケジュールされた分析のルールのテンプレート** に基づくルールを使用してシナリオベースの脅威を検出することもできます。これは **[分析]** ブレードの **[ルールのテンプレート]** タブにあります。 これらの検出を有効にするには、テンプレート ギャラリーでルール名を選択し、プレビュー ペインで **[ルールの作成]** をクリックします。

    - [Cisco - firewall block but success logon to Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml) (Cisco - ファイアウォールによるブロック Azure AD へのログインに成功)
    - [Fortinet - Beacon pattern detected](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml) (Fortinet - ビーコン パターン検出)
    - [IP with multiple failed Azure AD logins successfully logs in to Palo Alto VPN](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml) (Azure AD へのログインに何回も失敗した IP が Palo Alto VPN へのログインに成功)
    - [Multiple Password Reset by user](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml) (ユーザーがパスワードを何回もリセット)
    - [Rare application consent](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml) (通常見られないアプリケーションへの同意)
    - [SharePointFileOperation via previously unseen IPs](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml) (これまで見られなかった IP による SharePointFileOperation)
    - [Suspicious Resource deployment](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml) (リソースの不審なデプロイ)

    > [!NOTE]
    > Fusion で使用するスケジュールされた分析のルールでは、ML アルゴリズムは、テンプレートにある KQL クエリに対してあいまい一致を適用しません。 テンプレート名を変更しても、Fusion による検出に影響はありません。

## <a name="next-steps"></a>次のステップ

[Microsoft Sentinel での Fusion による検出](fusion.md)を参照してください。

数多くの [Fusion のシナリオベースの検出](fusion-scenario-reference.md)を参照してください。

高度なマルチステージ攻撃の検出に関する詳細を学習したので、自分のデータや潜在的な脅威を視覚化する方法を学習することができる以下のクイックスタートにも関心を持たれるかもしれません。[Microsoft Sentinel の使用を開始する](get-visibility.md)。

作成されたインシデントを調査する準備ができたら、次のチュートリアルをご覧ください。[Microsoft Sentinel を使用してインシデントを調査する](investigate-cases.md)。
