---
title: Azure Storage のコストを計画および管理する
description: Azure portal でコスト分析を使用して、Azure Storage のコストを計画および管理する方法を説明します。
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: normesta
ms.subservice: common
ms.custom: subject-cost-optimization
ms.openlocfilehash: aa0b789b31f50c8b1ccf5450700874a02ad4664c
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2020
ms.locfileid: "78304362"
---
# <a name="plan-and-manage-costs-for-azure-storage"></a>Azure Storage のコストを計画および管理する

この記事では、Azure Storage のコストを計画および管理する方法について説明します。 リソースを追加する前に、まず、ストレージ コストの計画に役立つ Azure 料金計算ツールを使用します。 Azure Storage の使用を開始したら、コスト管理機能を使用して、予算を設定し、コストを監視します。 また、予想コストを確認し、支出の傾向を監視して、対処した方がよい領域を識別することもできます。

Azure Storage のコストは、Azure の請求に記載された月額料金の一部でしかないことを理解してください。 この記事では、Azure Storage のコストを計画し、管理する方法について説明しますが、サードパーティのサービスを含め、Azure サブスクリプションで使用されるすべての Azure サービスとリソースに対して課金されます。 Azure Storage のコスト管理に慣れたら、同様の方法を適用して、サブスクリプションで使用されているすべての Azure サービスのコストを管理できます。

## <a name="prerequisites"></a>前提条件

コスト分析では、さまざまな種類の Azure アカウントがサポートされています。 サポートされているアカウントの種類の完全な一覧については、「[Understand Cost Management data (Cost Management データの概要)](../../cost-management-billing/costs/understand-cost-mgt-data.md)」を参照してください。 コスト データを表示するには、少なくとも Azure アカウントの読み取りアクセス許可が必要です。 Azure Cost Management データに対するアクセス権の割り当てについては、[データへのアクセス許可の割り当て](../../cost-management-billing/costs/assign-access-acm-data.md)に関するページを参照してください。

## <a name="estimate-costs-before-creating-an-azure-storage-account"></a>Azure Storage アカウントを作成する前にコストを見積もる

Azure Storage アカウントを作成し、データの転送を開始する前にコストを見積もるには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/) を使用します。

1. [[Azure 料金計算ツール]](https://azure.microsoft.com/pricing/calculator/) ページで、 **[ストレージ アカウント]** タイルを選択します。

2. ページを下方向にスクロールして、見積りの **[ストレージ アカウント]** セクションを見つけます。

3. ドロップダウン リストからオプションを選択します。 

   これらのドロップダウン リストの値を変更すると、コストの見積り額が変化します。 その見積り額は、上部の隅と見積りの下部に表示されます。 
    
   ![[コスト分析] ペインを使用してコストを監視する](media/storage-plan-manage-costs/price-calculator-storage-type.png)

   **[種類]** ドロップダウン リストの値を変更すると、このワークシートに表示されている他のオプションも変更されます。 **[詳細]** セクションのリンクを使用して、各オプションの意味とこれらのオプションがストレージ関連の操作の価格にどのように影響するかを確認します。 

4. 残りのオプションを変更して、それらが見積もりに与える影響を確認します。

## <a name="use-budgets-and-cost-alerts"></a>予算とコストのアラートを使用する

[予算](../../cost-management-billing/costs/tutorial-acm-create-budgets.md)を作成してコストを管理し、異常な支出や超過出費のリスクについて関係者に自動的に通知するアラートを作成できます。 アラートは、予算とコストのしきい値と比較した支出に基づきます。 予算とアラートは、Azure サブスクリプションとリソース グループに対して作成されるため、全体的なコスト監視戦略の一環として役立ちます。 ただし、これらはより高いレベルでコストを追跡するように設計されているため、Azure Storage のコストなど、個々の Azure サービスのコストを管理する機能が限定されていることがあります。

## <a name="monitor-costs"></a>コストを監視する

Azure Storage で Azure リソースを使用する場合、コストが発生します。 リソース使用のユニット コストは、期間 (秒、分、時間、日数) やユニット使用量 (バイト、メガバイトなど) によって異なります。Azure Storage の使用を開始するとただちにコストが発生します。 コストは、Azure portal の [[コスト分析]](../../cost-management-billing/costs/quick-acm-cost-analysis.md) ペインで確認できます。

コスト分析を使用すると、さまざまな期間について、Azure Storage のコストをグラフや表で表示できます。 たとえば、日単位、現在の月、以前の月、年単位などがあります。 予算や予想コストを基準としてコストを表示することもできます。 長期にわたるより長い期間のビューに切り替えると、支出の傾向を特定し、超過出費が発生した可能性のある時期を確認するのに役立ちます。 予算を作成したら、それを超えた場合を簡単に確認できます。

Azure Storage のコストをコスト分析で表示するには、次の操作を行います。

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. **[コストの管理と請求]** ウィンドウを開き、メニューから **[Cost management] (コスト管理)** を選択してから、 **[コスト分析]** を選択します。 その後、特定のサブスクリプションのスコープを **[スコープ]** ドロップダウンから変更できます。

   ![[コスト分析] ペインを使用してコストを監視する](./media/storage-plan-manage-costs/cost-analysis-pane.png)

4. Azure Storage のコストのみを表示するには、 **[フィルターの追加]** 、 **[サービス名]** の順に選択します。 次に、リストから **[ストレージ]** を選択します。 

   Azure Storage のみのコストを表示する例を次に示します。

   ![[コスト分析] ペインを使用してストレージ コストを監視する](./media/storage-plan-manage-costs/cost-analysis-pane-storage.png)

前の例では、サービスの現在のコストが表示されます。 また、Azure リージョン (場所) 別およびリソース グループ別のコストも表示されます。  

## <a name="next-steps"></a>次のステップ

[コスト分析](../../cost-management-billing/costs/quick-acm-cost-analysis.md)を使用してコストを管理する方法について詳細に説明します。

Azure Storage での課金のしくみの詳細については、以下の記事を参照してください。

- [Azure Storage の価格の概要](https://azure.microsoft.com/pricing/details/storage/)
- [予約容量を使用して BLOB ストレージのコストを最適化する](../blobs/storage-blob-reserved-capacity.md)
