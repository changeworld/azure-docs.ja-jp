---
title: Advisor のクイック修正による修復のレコメンデーション
description: Advisor でクイック修正を使って一括修復を実行する
ms.topic: article
ms.date: 03/13/2020
ms.author: sagupt
ms.openlocfilehash: 3c3ac27f04aa516fbef1bfff30e3392659b58919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503927"
---
# <a name="quick-fix-remediation-for-advisor"></a>Advisor のクイック修正
**クイック修正** を使用すると、複数のリソースに対するレコメンデーションの修復をすばやく簡単に実行できます。 リソースの一括修復を行う機能が用意され、リソースの大規模修復によってサブスクリプションをより迅速に最適化できます。
この機能は、Azure portal で、特定のレコメンデーションについてのみ使用できます。


## <a name="steps-to-use-quick-fix"></a>"クイック修正" を使用する手順

1. **クイック修正**ラベルが付いているレコメンデーションの一覧で、レコメンデーションをクリックします。

   ![Advisor のクイック修正](./media/quick-fix-1.png)
   
   "*画像内の価格は例示の目的でのみ使用されています*"

2. レコメンデーションの詳細ページに、このレコメンデーションに対するリソースの一覧が表示されます。 レコメンデーションに対して修復を行うすべてのリソースを選択します。

   ![Advisor のクイック修正](./media/quick-fix-2.png)
   
   "*画像内の価格は例示の目的でのみ使用されています*"

3. リソースを選択したら、 **[クイック修正]** をクリックして一括修復します。

   > [!NOTE]
   > 一覧表示されるリソースの一部は、変更するための適切なアクセス許可を持っていないために無効な場合があります。
   
   > [!NOTE]
   > Advisor に記載されているメリット以外の影響が他にある場合は、エクスペリエンスで通知され、これは情報に基づいた修復に関する意思決定を行ううえで役立ちます。
   
4. 修復完了の通知が届きます。 修復されていないリソースがある場合はエラーが表示されます。また、リソース リスト ビューに、選択されているモードのリソースが表示されます。  


## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。
* [Azure Advisor の概要](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor の優れた運用の推奨事項](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
