---
title: "ポリシーの割り当てを作成し、Azure 環境内の非対応リソースを特定する | Microsoft Docs"
description: "この記事では、ポリシー定義を作成して、非対応リソースを特定する手順について説明します。"
services: azure-policy
keywords: 
author: Jim-Parker
ms.author: jimpark
ms.date: 10/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 2e0962ae02dd8132d878792634abc1f63b2c29a1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>ポリシーの割り当てを作成し、Azure 環境内の非対応リソースを特定する
Azure のコンプライアンスを理解する第一歩は、自分の現在のリソースの状態を把握することです。 このクイックスタートでは、ポリシー割り当てを作成して、SQL Server バージョン 12.0 を使用していないリソースを特定するプロセスについて順を追って説明します。 このプロセスを終了すると、異なるバージョン、つまり "*非準拠*" サーバーを適切に特定できるようになります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="opt-in-to-azure-policy"></a>Azure Policy を選択する

Azure Policy は現在、制限付きのプレビュー段階であるため、登録してアクセスを要求する必要があります。

1. Azure Policy (https://aka.ms/getpolicy) にアクセスし、左側のウィンドウにある **[サインアップ]** を選択します。

   ![ポリシーを検索する](media/assign-policy-definition/sign-up.png)

2. **[サブスクリプション]** の一覧から、使用するサブスクリプションを選択して Azure Policy を選択します。 次に、**[登録]** を選択します。

   ![Azure Policy の使用を選択する](media/assign-policy-definition/preview-opt-in.png)

   ニーズに基づいて、登録要求が承認されるまでに数日かかる場合があります。 要求が承認されると、サービスの利用を開始できることが電子メールで通知されます。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、"*SQL Server バージョン 12.0 が必要*" 定義を割り当てます。 

1. Azure Portal ページの左側のウィンドウで **[割り当て]** を選択します。
2. **[割り当て]** ウィンドウの上部で **[ポリシーの割り当て]** を選択します。

   ![ポリシー定義を割り当てる](media/assign-policy-definition/select-assign-policy.png)

3. **[ポリシーの割り当て]** ページで、**[ポリシー]** フィールドの横にある![ポリシー定義ボタン](media/assign-policy-definition/definitions-button.png)をクリックします。

   ![使用できるポリシー定義を開く](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy には、使用できる組み込みのポリシー定義があらかじめ含まれています。 次のような組み込みのポリシー定義が表示されます。

   - タグとその値を強制
   - タグとその値を適用
   - SQL Server バージョン 12.0 が必要

4. ポリシー定義から "*SQL Server バージョン 12.0 が必要*" 定義を見つけます。 そのポリシーをクリックし、**[選択]** をクリックします。

   ![適切なポリシー定義を見つける](media/assign-policy-definition/select-available-definition.png)

5. ポリシー割り当ての表示**名**を入力します。 この例では、"*SQL Server バージョン 12.0 が必要*" を名前として使用してみましょう。 必要に応じて、**説明**を追加することもできます。 この説明には、このポリシー割り当てで、この環境内に作成されるすべての SQL Server をバージョン 12.0 にする方法について詳しく入力します。
6. このポリシーを確実に既存のリソースに適用するには、価格レベルを **Standard** に変更します。

   Azure Policy 内には、*Free* と *Standard* という 2 つの価格レベルがあります。 Free レベルでは、今後のリソースにのみポリシーを強制することができます。Standard では、既存のリソースにも強制して、コンプライアンスの状態に対する理解を深めることができます。 制限付きプレビュー段階なので、価格モデルはまだリリースされていないません。したがって、*[Standard]* を選択しても、請求が発生することはありません。 価格の詳細については、[Azure Policy の価格](https://acom-milestone-ignite.azurewebsites.net/pricing/details/azure-policy/)に関するページをご覧ください。

7. ポリシーを適用する **[スコープ]** を選択します。  スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。
8. 以前 Azure Policy を選択したときに登録したサブスクリプション (またはリソース グループ) を選択します。 この例では、**Azure Analytics Capacity Dev** サブスクリプションを使用していますが、使用できるオプションはさまざまです。

   ![適切なポリシー定義を見つける](media/assign-policy-definition/assign-policy.png)

9. **[割り当て]** を選択します。

これで、非対応リソースを特定し、環境のコンプライアンスの状態を確認する準備ができました。

## <a name="identify-non-compliant-resources"></a>非対応リソースを特定する

左側のウィンドウで **[コンプライアンス]** を選択し、作成したポリシー割り当てを検索します。

![ポリシーのコンプライアンス](media/assign-policy-definition/policy-compliance.png)

この新しい割り当てに対応していない既存のリソースがある場合、**[Non-compliant resources]\(非対応リソース\)** タブに表示されます。

既存のリソースにわたって条件が評価され、一部が該当した場合、そのリソースは、ポリシー非対応としてマークされます。 次の表は、条件の評価結果に対して現時点で実行できるアクションと、リソースのコンプライアンスの状態を示しています。

|リソース  |ポリシーの条件の評価結果  |ポリシーでのアクション   |コンプライアンスの状態  |
|-----------|---------|---------|---------|
|Exists     |True     |DENY     |非対応 |
|Exists     |False    |DENY     |対応     |
|Exists     |True     |追加   |非対応 |
|Exists     |False    |追加   |対応     |
|Exists     |True     |Audit    |非対応 |
|Exists     |False    |Audit    |非対応 |

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このコレクションの他のガイドは、このクイックスタートに基づいています。 引き続きチュートリアルの作業を行う場合は、このクイックスタートで作成したリソースをクリーンアップしないでください。 これ以上作業を行わない場合は、次の手順に従って、このクイック スタートで作成したすべてのリソースを Azure Portal で削除してください。
1. 左側のウィンドウの **[割り当て]** を選択します。
2. 作成した割り当てを検索します。

   ![割り当てを削除する](media/assign-policy-definition/delete-assignment.png)

3.  **[割り当ての削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、ポリシー定義をスコープに割り当てて、そのスコープのすべてのリソースが準拠しているかどうか、また、どのリソースが準拠していないかを確認しました。

ポリシーの割り当てについてさらに詳しく学び、**今後**作成されるリソースが準拠していることを確認するには、次に進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./create-manage-policy.md)

