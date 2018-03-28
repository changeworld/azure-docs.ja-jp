---
title: Azure Data Lake Analytics のクォータ制限
description: Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: ''
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.topic: article
ms.workload: big-data
ms.date: 03/15/2018
ms.author: omidm
ms.openlocfilehash: 22774511720173915207da80a6ca33d5dbc83e19
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics のクォータ制限

Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。 これらの制限について把握すれば、U-SQL ジョブの動作の理解に役立つ可能性があります。 すべてのクォータ制限は固定ではないため、Azure サポートに連絡して上限を引き上げることができます。

## <a name="azure-subscriptions-limits"></a>Azure サブスクリプションの制限

**サブスクリプションあたりの ADLA アカウントの最大数:** 5

これは、各リージョンでサブスクリプションごとに作成できる ADLA アカウントの最大数です。 6 番目の ADLA アカウントを作成しようとすると、エラー "サブスクリプション name のリージョンで許容される Data Lake Analytics アカウントの最大数 (5) に達しました。" が表示されます。 この状況では、適切であれば別のリージョンを選択するか、同じリージョンの使用されてない ADLA アカウントを削除できます。クォータの引き上げをリクエストする[サポート チケット](#increase-maximum-quota-limits)を開いて Azure サポートに連絡することもできます。

## <a name="adla-account-limits"></a>ADLA アカウントの制限

**アカウントあたりの Analytics ユニット (AU) の最大数:** 250

これは、アカウントで同時に実行できる AU の最大数です。 すべてのジョブで実行されている AU の総数がこの制限を超えている場合は、新しいジョブが自動的にキューに挿入されます。 例: 

* 250 個の AU を使用する 1 つのジョブのみ実行されているときに 2 つ目のジョブを送信した場合、そのジョブは 1 つ目のジョブが完了するまでジョブ キューで待機します。
* 実行しているジョブが既に 5 つあり、それぞれが 50 AU を使用している場合に、20 個の AU 必要とする 6 番目のジョブを送信すると、そのジョブは、20 個の AU が使用可能になるまでジョブ キューで待機します。

**アカウントあたりの同時実行 U-SQL ジョブの最大数:** 20

これは、アカウントで同時に実行できるジョブの最大数です。 この値を超えると、新しいジョブは自動的にキューイングされます。

## <a name="adjust-adla-quota-limits-per-account"></a>アカウントあたりの ADLA クォータ制限を調整する

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. 既存の ADLA アカウントを選びます。
3. **[プロパティ]**をクリックします。
4. ニーズに合わせて **[並行処理]** と **[最大同時ジョブ数]** を調整します。

    ![Azure Data Lake Analytics ポータル ページ](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>最大クォータ制限を増やす

1. Azure Portal でサポート要求を開きます。

    ![Azure Data Lake Analytics ポータル ページ](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics ポータル ページ](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. [問題の種類] で **[クォータ]** を選択します。
3. **サブスクリプション**を選択します ("試用" サブスクリプションではないことをご確認ください)。
4. [クォータの種類] で **[Data Lake Analytics]** を選択します。

    ![Azure Data Lake Analytics ポータル ページ](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. [問題] ページで、希望の上限と、この追加容量が必要な理由の**詳細**を入力します。

    ![Azure Data Lake Analytics ポータル ページ](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 自分の連絡先情報を確認し、サポート要求を作成します。

Microsoft で要求を確認し、お客様のビジネス ニーズにできるだけ早く対応できるよう努めます。

## <a name="next-steps"></a>次の手順

* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
* [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
