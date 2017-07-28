---
title: "Azure Data Lake Analytics のクォータ制限 | Microsoft Docs"
description: "Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。"
services: data-lake-analytics
keywords: Azure Data Lake Analytics
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: b68138f04eea1a21731118803d14698320e00be0
ms.contentlocale: ja-jp
ms.lasthandoff: 07/01/2017


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Azure Data Lake Analytics のクォータ制限
Azure Data Lake Analytics (ADLA) アカウントのクォータ制限を調整する方法と引き上げる方法について説明します。 これらの制限について把握すれば、U-SQL ジョブの動作の理解に役立つ可能性があります。 これらの制限はいずれも固定ではなく、Microsoft への連絡を通じていつでも上限を引き上げることができます。

## <a name="azure-subscriptions-limits"></a>Azure サブスクリプションの制限

**サブスクリプションあたりの ADLA アカウント数:** 5。 これは、サブスクリプションごとに作成できる ADLA アカウントの最大数です。 6 番目の ADLA アカウントを作成しようとすると、エラー "You have reached the maximum number of Data Lake Analytics accounts allowed (5) in region under subscription name (リージョンの <サブスクリプション名> で許可される Data Lake Analytics アカウントの最大数 (5) に達しました)" が表示されます。 このエラーは、サブスクリプションで使用している ADLA アカウントを削除するか、サポート チケットを開いて Microsoft に連絡することで、簡単に修正できます。

## <a name="adla-account-limits"></a>ADLA アカウントの制限

**アカウントあたりの Analytics ユニット (AU) の最大数:** 250。 

これは、アカウントで同時に実行できる AU の最大数です。 すべてのジョブで実行中の AU の合計数がこの数を超えることはできません。 この値を超えると、新しいジョブは自動的にキューに登録されます。 次に例を示します。

  * 250 個の AU を使用する 1 つのジョブが実行されているときに 2 つ目のジョブを送信した場合、そのジョブは 1 つ目のジョブが完了するまでジョブ キューに登録されます。
  * それぞれ 50 個の AU を使用する 5 つのジョブが既に実行されているときに 20 個の AU を使用する 6 番目のジョブを送信した場合、このジョブはジョブ キューで待機状態となり、20 個の AU が使用可能になった時点で実行が開始されます。


**アカウントあたりの同時実行 U-SQL ジョブの最大数:** 20。 

これは、アカウントで同時に実行できるジョブの最大数です。 この値を超えると、新しいジョブは自動的にキューに登録されます。

## <a name="adjust-the-adla-quota-limits-per-account"></a>アカウントあたりの ADLA クォータ制限の調整

1. [Azure Portal](https://portal.azure.com) にサインオンします。
2. 作成済みの ADLA アカウントを選択します。
3. **[プロパティ]** をクリックします。
4. ニーズに合わせて **[並行処理]** と **[最大同時ジョブ数]** を調整します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="to-increase-the-max-quota-limits"></a>最大クォータ制限を増やすには

1. Azure Portal でサポート要求を開きます。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. [問題の種類] で **[クォータ]** を選択します。
3. **サブスクリプション**を選択します ("試用" サブスクリプションではないことを確認してください)。
4. [クォータの種類] で **[Data Lake Analytics]** を選択します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. [問題] ブレードで、希望の上限と、この追加容量が必要な**詳しい理由**を入力します。

    ![Azure Data Lake Analytics ポータルのブレード](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. 自分の連絡先情報を確認し、サポート要求を作成します。

Microsoft で要求を確認し、お客様のビジネス ニーズにできるだけ早く対応できるよう努めます。

## <a name="next-steps"></a>次のステップ
* [Microsoft Azure Data Lake Analytics の概要](data-lake-analytics-overview.md)
* [Azure PowerShell を使用する Azure Data Lake Analytics の管理](data-lake-analytics-manage-use-powershell.md)
* [Azure Portal を使用して Azure Data Lake Analytics ジョブの監視とトラブルシューティングを行う](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

