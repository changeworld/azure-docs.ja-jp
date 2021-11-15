---
title: Azure portal でのアカウント管理者のタスク
description: Azure portal で支払い操作を実行する方法について説明します
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: 7fb94b8e714c62a92b0fe9411af97f69e0a50423
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130229562"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Azure portal でのアカウント管理者のタスク

この記事では、Azure portal で次のタスクを実行する方法について説明します。
- サブスクリプションの支払い方法を管理する
- サブスクリプションの使用制限を削除する
- Azure イン オープン プラン サブスクリプションにクレジットを追加する

これらのすべてのタスクを実行するには、アカウント管理者である必要があります。

## <a name="accounts-portal-is-retiring"></a>アカウント ポータルは廃止される

アカウント ポータルは廃止され、ユーザーは 2021 年 12 月 31 日までに Azure portal にリダイレクトされます。 アカウント ポータルでサポートされている機能は、Azure portal に移行されます。 この記事では、Azure portal で最も一般的な操作のいくつかを実行する方法について説明します。


## <a name="navigate-to-your-subscriptions-payment-methods"></a>サブスクリプションの支払い方法に移動する

1. Azure Portal にアカウント管理者としてサインインします。

1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、クレジット カードを追加するサブスクリプションを選択します。

   ![サブスクリプションを選択できる [コストの管理と請求] ページを示すスクリーンショット。](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. **[支払い方法]** を選択します。

    ![支払い方法を追加できる [支払い方法] ページを示すスクリーンショット。](./media/account-admin-tasks/subscription-payment-methods-blade.png)

ここでは、新しいクレジット カードの追加、アクティブな支払い方法の変更、クレジット カードの詳細の編集、およびクレジット カードの削除を行えます。

### <a name="change-active-payment-method"></a>アクティブな支払い方法を変更する

アクティブな支払い方法を変更するには、新しいクレジット カードを追加するか、既に保存されているものを選択します。 アクティブな支払い方法を新しいクレジット カードに変更するには:

1. 左上隅にある [+] を選択して、クレジット カードを追加します。

    ![[+] 記号を示すスクリーンショット](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. 右側のフォームにクレジット カードの詳細を入力します。

    ![クレジット カードの追加フォームを示すスクリーンショット。](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. このカードをアクティブな支払い方法にするには、フォームの上にある **[これをアクティブな支払い方法にする]** の横のチェック ボックスをオンにします。 選択したサブスクリプションと同じカードを使用しているすべてのサブスクリプションで、このカードがアクティブな支払い方法になります。

    ![カードをアクティブな支払い方法にするためのチェック ボックスを示すスクリーンショット。](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. **[次へ]** を選択します。

アクティブな支払い方法を、既に保存されているクレジットカードに変更するには:

1. アクティブな支払い方法にするカードの横にあるチェック ボックスを選択します。

    ![クレジット カードの横のオンになっているボックスを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. コマンド バーで、 **[アクティブに設定]** をクリックします。

    ![[アクティブに設定] ボタンを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>クレジット カードの詳細を編集する

有効期限日や住所などのクレジット カードの詳細を編集するには、編集するクレジット カードをクリックします。 クレジット カードのフォームが右側に表示されます。

![選択されたクレジット カードを示すスクリーンショット](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

クレジット カードの詳細を更新し、 **[保存]** をクリックします。

### <a name="remove-a-credit-card-from-the-account"></a>クレジット カードをアカウントから削除する

1. 削除するカードの横にあるチェック ボックスをオンにします。

    ![クレジット カードの横のオンになっているボックスを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. コマンド バーの **[削除]** をクリックします。

    ![[削除] ボタンを示すスクリーンショット](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

クレジット カードがいずれかの Microsoft サブスクリプションのアクティブな支払い方法である場合は、Azure アカウントから削除できません。 このクレジット カードにリンクされているすべてのサブスクリプションのアクティブな支払い方法を変更し、再度お試しください。

### <a name="switch-to-invoice-payment"></a>請求書支払いに切り替える

請求書による支払い (小切手または電信送金) の資格がある場合は、Azure portal でサブスクリプションを請求書支払い (小切手または電信送金) に切り替えることができます。

1. コマンド バーで **[Pay by invoice]\(請求書による支払い\)** を選択します。

    ![[請求書による支払い] が選択されている [支払い方法] ページを示すスクリーンショット。](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. 請求書による支払い方法用に、住所を入力します。
1. **[次へ]** をクリックします。

請求書による支払いの承認を得るには、[請求書による支払い方法](pay-by-invoice.md)に関するページをご覧ください。

### <a name="edit-invoice-payment-address"></a>請求書支払いの住所を編集する

請求書支払い方法の住所を編集するには、サブスクリプションの支払い方法の一覧で **[請求書]** をクリックします。 住所のフォームが右側に表示されます。

## <a name="remove-spending-limit"></a>支出制限を削除する

Azure の使用制限により、クレジット額を超過する支出が防止されます。 Azure サブスクリプションに関連付けられた有効な支払方法がある限り、使用制限はいつでも削除できます。 複数月にわたるクレジットを含むサブスクリプション タイプ (Visual Studio Enterprise、Visual Studio Professional など) の場合、翌請求期間の開始時に使用制限を再度有効にできます。

使用制限は、コミットメント プランや従量課金制価格を使用したサブスクリプションでは利用できません。

1. Azure Portal にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、Visual Studio Enterprise サブスクリプションを選択します。

   ![[Visual Studio Enterprise サブスクリプション] を選択できる [個人用サブスクリプション] 領域を示すスクリーンショット。](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Visual Studio サブスクリプションの一部がここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 これらのサブスクリプションについては、ディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. サブスクリプションの概要で、オレンジ色のバナーをクリックして使用制限を削除します。

    ![使用制限の削除バナーを示すスクリーンショット](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. 使用制限を永久に削除するか、現在の請求期間に対してのみ削除するかを選択します。

   ![[使用制限の削除] ブレードを示すスクリーンショット](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. **[支払い方法の選択]** をクリックして、サブスクリプションの支払い方法を選択します。 これが、サブスクリプションのアクティブな支払い方法になります。

1. **[完了]** をクリックします。

## <a name="add-credits-to-azure-in-open-subscription"></a>Azure イン オープン プラン サブスクリプションにクレジットを追加する

Azure イン オープン プランのライセンス サブスクリプションがある場合は、プロダクト キーを使用するか、クレジット カードでクレジットを購入することによって、Azure portal でサブスクリプションにクレジットを追加できます。

1. Azure Portal にアカウント管理者としてサインインします。
1. "**コスト管理 + 請求**" を検索します。

    ![[コストの管理と請求] の検索を示すスクリーンショット ](./media/account-admin-tasks/search-bar.png)

1. **[個人用サブスクリプション]** 一覧で、Azure イン オープン プラン サブスクリプションを選択します。

    ![[Azure イン オープン プラン サブスクリプション] を選択できる [個人用サブスクリプション] 領域を示すスクリーンショット。](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > サブスクリプションがここに表示されない場合、原因として、ある時点でサブスクリプション ディレクトリを変更したことが考えられます。 サブスクリプションのディレクトリを元のディレクトリ (最初にサインアップしたディレクトリ) に切り替える必要があります。 その後、手順 2 を繰り返します。

1. **[クレジットの履歴]** を選択します。

    ![クレジットの履歴を示すスクリーンショット](./media/account-admin-tasks/aio-credit-history-blade.png)

1. 左上隅にある [+] を選択して、さらにカードを追加します。

    ![[クレジットの追加] ボタンを示すスクリーンショット](./media/account-admin-tasks/aio-credit-history-plus.png)

1. ドロップダウンで支払い方法の種類を選択します。 プロダクト キーを追加するか、クレジット カードを使用してクレジットを購入できます。

    ![[クレジットの追加] ブレードの [支払方法] ドロップダウンを示すスクリーンショット](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. プロダクト キーを選択する場合:
    - プロダクト キーを入力します
    - **[検証]** をクリックします

1. クレジット カードを選択する場合:
    - **[支払い方法の選択]** をクリックして、クレジット カードを追加するか既存のものを選択します。
    - 追加するクレジットの金額を指定します。

1. **[適用]** をクリックします

## <a name="usage-details-files-comparison"></a>使用状況詳細ファイルの比較

次の情報を使用すると、アカウント ポータルのファイルの v1 および v2 バージョンで使用可能なフィールドと、Azure portal の使用状況詳細ファイルの最新バージョンとの間の対応がわかります。

| V1 | V2 | Azure portal |
| --- | --- | --- |
| 追加情報 | 追加情報 | AdditionalInfo: |
| 通貨 | 通貨 | BillingCurrency |
| 請求期間 | 請求期間 | BillingPeriodEndDate |
| 請求期間 | 請求期間 | BillingPeriodStartDate |
| サービス | 使用サービス | ConsumedService |
| 値 | 値 | コスト |
| 使用日 | 使用日 | Date |
| 名前 | 測定カテゴリ | MeterCategory |
| ResourceGuid | Meter Id | MeterId |
| リージョン | 測定リージョン | MeterRegion |
| リソース | 測定名 | MeterName  |
| Type | 測定サブカテゴリ | MeterSubcategory |
| 使用量 | Consumed Quantity | 数量 |
| コンポーネント | リソース グループ | ResourceGroup |
|   | インスタンス ID | ResourceId |
| サブ リージョン | リソースの場所 | ResourceLocation |
| サービス情報 1 | サービス情報 1 | ServiceInfo1 |
| サービス情報 2 | サービス情報 2 | ServiceInfo2 |
| サブスクリプション ID | サブスクリプション ID | SubscriptionId |
| サブスクリプション名 | サブスクリプション名 | SubscriptionName |
|   | Tags | Tags |
| ユニット | ユニット | UnitOfMeasure |
| | 料金 | UnitPrice |

最新の使用状況詳細ファイルで使用できるフィールドについて詳しくは、「[Azure の利用状況と請求金額ファイル内の用語について](../understand/understand-usage.md)」をご覧ください。

次のフィールドは、アカウント ポータルのファイルの v1 および v2 バージョンからのものです。 これらは、最新の使用状況詳細ファイルでは利用できなくなりました。

| V1 | V2 |
| --- | --- |
| 注文 ID | 注文 ID |
| 説明 | 説明 |
| 請求日 (契約応当日) | 請求日 (契約応当日) |
| プラン名 | プラン名 |
| サービス名 | サービス名 |
| サブスクリプションの状態 | サブスクリプションの状態 |
| サブスクリプションの追加の状態 | サブスクリプションの追加の状態 |
| プロビジョニング状態 | プロビジョニング状態 |
| SKU | SKU |
| Included | 含まれる量 |
| Billable | 超過量 |
| コミットメント以内 | コミットメント以内 |
| コミットメント レート | コミットメント レート |
| 超過料金 | 超過料金 |
| コンポーネント |  |

## <a name="troubleshooting"></a>トラブルシューティング
バーチャル カードやプリペイド カードはサポートされません。 有効なクレジット カードを追加または更新するときにエラーが発生する場合は、ブラウザーをプライベート モードで開いてみてください。

## <a name="next-steps"></a>次のステップ
- [想定外の料金の分析](../understand/analyze-unexpected-charges.md)に関する詳細情報
