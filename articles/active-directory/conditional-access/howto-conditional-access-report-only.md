---
title: レポートのみモードで条件付きアクセス ポリシーを構成する - Azure Active Directory
description: レポートのみモードを使用した条件付きアクセスでの導入の支援
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63aaa0103715a928cdd5332de738a473b329f2e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186085"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>レポートのみモードで条件付きアクセス ポリシーを構成する (プレビュー)

レポートのみモードで条件付きアクセス ポリシーを構成するには:

1. **Azure portal** に、条件付きアクセス管理者、セキュリティ管理者、またはグローバル管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]** の順に移動します。
1. **[新しいポリシー]** を選択します。
1. 必要に応じて、ポリシー条件と求められる制御の許可を構成します。
1. **[ポリシーの有効化]** の下で、トグルを **[レポートのみ]** モードに設定します。
1. **[保存]** を選びます。

> [!TIP]
> 既存のポリシーの **[ポリシーの有効化]** の状態を **[オン]** から **[レポートのみ]** に編集できますが、そうすることでポリシーの適用は無効になります。 

レポートのみを閲覧するには、Azure AD サインイン ログを確認します。

特定のサインインに対するレポートのみポリシーの結果を閲覧するには:

1. **Azure portal** に、レポート閲覧者、セキュリティ閲覧者、セキュリティ管理者、またはグローバル管理者としてサインインします。
1. **[Azure Active Directory]**  >  **[サインイン]** の順に移動します。
1. 1 つのサインインを選択するか、フィルターを追加して結果を絞り込みます。
1. **[詳細]** ドロワーで、 **[レポート専用 (プレビュー)]** タブを選択して、サインイン中に評価されたポリシーを表示します。

> [!NOTE]
> サインイン ログをダウンロードする場合は、条件付きアクセスの レポート専用の結果データを含めるための JSON 形式を選択します。

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure Monitor と Azure AD の統合をセットアップする

分析情報への条件付きアクセスの新しいブックを使用して、条件付きアクセスの総計上での影響を閲覧するには、Azure Monitor と Azure AD を統合してサインイン ログをエクスポートする必要があります。 2 つの手順を実行して、この統合を設定します。 

1. [Azure Monitor サブスクリプションにサインアップして、ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)。
1. [Azure AD から Azure Monitor へサインイン ログをエクスポートします](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)。

Azure Monitor の価格に関する詳細については、[Azure Monitor の価格に関するページ](https://azure.microsoft.com/pricing/details/monitor/)上で確認できます。 コストの見積もり、日次上限の設定、データ保有期間のカスタマイズに関する資料については、「[Azure Monitor ログで使用量とコストを管理する](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)」の記事を参照してください。

## <a name="view-conditional-access-insights-workbook"></a>分析情報への条件付きアクセス ブックを表示する

Azure AD ログと Azure Monitor を統合したら、分析情報への条件付きアクセスの新しいブックを使用して、条件付きアクセス ポリシーの影響を監視できます。

1. セキュリティ管理者またはグローバル管理者として、**Azure portal** にサインインします。
1. **[Azure Active Directory]**  >  **[ブック]** の順に移動します。
1. **[Conditional Access Insights (Preview)]\(分析情報への条件付きアクセス (プレビュー)\)** を選択します。
1. **[条件付きアクセス ポリシー]** ドロップダウンから、1 つまたは複数のポリシーを選択します。 既定では、有効になっているすべてのポリシーが選択されています。
1. 時間の範囲を選択します (時間の範囲が、利用可能なデータセットを超過した場合、レポートには利用可能なすべてのデータが表示されます)。 **[条件付きアクセス ポリシー]** と **[時間の範囲]** パラメータ―を設定したら、レポートが読み込まれます。
   1. 必要に応じて、個々の **[ユーザー]** または **[アプリ]** を検索して、レポートの範囲を絞り込みます。
1. ユーザー数またはサインイン回数別で、時間の範囲内でのデータの閲覧を選択します。
1. **[データ ビュー]** に応じて、 **[Impact Summary]\(影響の概要\)** には選択されたパラメーターの範囲にあるユーザー数またはサインイン数が、[合計] の数、 **[成功]** 、 **[失敗]** **[ユーザー アクションが必要です]** **[適用されません]** ごとにグループされて表示されます。 特定の結果の種類のサインインを確認するには、タイルを選択します。 
   1. ブックのパラメーターを変更した場合は、将来使用するためにコピーを保存することを選択できます。 レポートの最上部にある [保存] アイコンを選択し、保存先の名前と場所を指定します。
1. 下にスクロールすると、条件ごとにサインインの内訳が表示されます。
1. レポートの最下部にある **[サインインの詳細]** を表示して、上記の選択によってフィルター処理された個々のサインイン イベントを調査します。

> [!TIP] 
> 必要に応じて、特定のクエリをドリルダウンするか、またはサインインの詳細をエクスポートします。 クエリの右側にあるボタンを選択して、Log Analytics 上でクエリを開きます。 [エクスポート] を選択して、CSV または Power BI にエクスポートします。

## <a name="common-problems-and-solutions"></a>一般的な問題と解決策

### <a name="why-are-the-queries-in-the-workbook-failing"></a>ブック内のクエリがエラーになるのはなぜですか?

お客様は、誤ったワークスペースや複数のワークスペースがブックに関連付けられている場合に、クエリがエラーになることがあるとお気付きになりました。 この問題を解決するには、ブックの最上部にある **[編集]** をクリックして、設定の歯車をクリックします。 ブックに関連付けられていないワークスペースを選択して削除します。 各ブックに関連付けられているワークスペースは 1 つだけになります。

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>[条件付きアクセス ポリシー] ドロップダウンのパラメーターに自分のポリシーが含まれていないのはなぜですか?

[条件付きアクセスポリシー] ドロップダウンは、4 時間以内の最新のサインインを照会して設定されます。 過去 4 時間以内にテナントにサインインがない場合は、ドロップダウンが空になることがあります。 サインインの頻度が低い小規模のテナントなど、この遅延が永続的な問題である場合、管理者は [条件付きアクセスポリシー] ドロップダウンのクエリを編集して、クエリの時間を 4 時間より長い時間に延長できます。

## <a name="next-steps"></a>次のステップ

[Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)

Azure AD ブックに関する詳細については、[Azure Active Directory レポートに Azure Monitor ブックを使用する方法](../reports-monitoring/howto-use-azure-monitor-workbooks.md)に関する記事を参照してください。
