---
title: "ポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する | Microsoft Docs"
description: "この記事では、ポリシー定義を作成して、準拠していないリソースを特定する手順について説明します。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 12/06/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: b28e442a075e38a4fbe7b0d9d46f2c9d23e7c6fb
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment"></a>ポリシーの割り当てを作成し、Azure 環境内の準拠していないリソースを特定する
Azure のコンプライアンスを理解する第一歩は、自分の現在のリソースの状態を把握することです。 このクイックスタートでは、ポリシー割り当てを作成して、管理ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、管理ディスクを使用していない、つまり "*非準拠*" の仮想マシンを適切に特定できるようになります。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイックスタートでは、ポリシー割り当てを作成し、"*管理ディスクのない仮想マシンを監査*" ポリシー定義を割り当てます。

1. Azure Portal ページの左側のウィンドウで **[割り当て]** を選択します。
2. **[割り当て]** ウィンドウの上部で **[ポリシーの割り当て]** を選択します。

   ![ポリシー定義を割り当てる](media/assign-policy-definition/select-assign-policy.png)

3. **[Assign Policy]\(ポリシーの割り当て\)** ページで、**[ポリシー]** フィールドの横にある![ポリシー定義ボタン](media/assign-policy-definition/definitions-button.png)をクリックして、使用できる定義の一覧を開きます。

   ![使用できるポリシー定義を開く](media/assign-policy-definition/open-policy-definitions.png)

   Azure Policy には、使用できる組み込みのポリシー定義があらかじめ含まれています。 次のような組み込みのポリシー定義が表示されます。

   - タグとその値を強制
   - タグとその値を適用
   - SQL Server バージョン 12.0 が必要

4. ポリシー定義を検索して、"*管理ディスクを使用しない VM を監査*" 定義を見つけます。 そのポリシーをクリックし、**[割り当て]** をクリックします。

   ![適切なポリシー定義を見つける](media/assign-policy-definition/select-available-definition.png)

5. ポリシー割り当ての表示**名**を入力します。 ここでは、"*管理ディスクを使用しない VM を監査*" を使用しましょう。 必要に応じて、**説明**を追加することもできます。 この説明には、この環境内に作成された管理ディスクを使用しないすべての仮想マシンを、このポリシー割り当てによって特定する方法を詳しく入力します。
6. このポリシーを確実に既存のリソースに適用するには、価格レベルを **Standard** に変更します。

   Azure Policy 内には、*Free* と *Standard* という 2 つの価格レベルがあります。 Free レベルでは、今後のリソースにのみポリシーを強制することができます。Standard では、既存のリソースにも強制して、コンプライアンスの状態に対する理解を深めることができます。 制限付きプレビュー段階なので、価格モデルはまだリリースされていないません。したがって、*[Standard]* を選択しても、請求が発生することはありません。 価格の詳細については、[Azure Policy の価格](https://azure.microsoft.com/pricing/details/azure-policy/)に関するページをご覧ください。

7. ポリシーを適用する **[スコープ]** を選択します。  スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。 サブスクリプションからリソース グループまで、適用対象は多岐にわたります。
8. 以前に登録したサブスクリプション (またはリソース グループ) を選びます。 この例では、**Azure Analytics Capacity Dev** サブスクリプションを使用していますが、使用できるオプションはさまざまです。

   ![適切なポリシー定義を見つける](media/assign-policy-definition/assign-policy.png)

9. **[割り当て]** を選択します。

以上の手順で、準拠していないリソースを特定し、環境のコンプライアンスの状態を理解できるようになりました。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

左側のウィンドウで **[コンプライアンス]** を選択し、作成したポリシー割り当てを検索します。

![ポリシーのコンプライアンス](media/assign-policy-definition/policy-compliance.png)

この新しい割り当てに準拠していない既存のリソースがある場合、**[Non-compliant resources]\(準拠していないリソース\)** タブに表示されます。

既存のリソースにわたって条件が評価され、一部が該当した場合、そのリソースは、ポリシーに準拠していないとしてマークされます。 次の表は、条件の評価結果に対して現時点で実行できるアクションと、リソースのコンプライアンスの状態を示しています。

|リソース  |ポリシーの条件の評価結果  |ポリシーでのアクション   |コンプライアンスの状態  |
|-----------|---------|---------|---------|
|Exists     |True     |DENY     |準拠していない |
|Exists     |False    |DENY     |対応     |
|Exists     |True     |追加   |準拠していない |
|Exists     |False    |追加   |対応     |
|Exists     |True     |Audit    |準拠していない |
|Exists     |False    |Audit    |準拠していない |

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
