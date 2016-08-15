<properties
	pageTitle="Azure サブスクリプションが無効になった場合にどうすればよいか | Microsoft Azure"
	description="Azure サブスクリプションが無効になった理由と解決策を説明する"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/27/2016"
	ms.author="genli"/>

# Azure サブスクリプションが無効になった場合にどうすればよいか

Azure サブスクリプションが無効になった場合、この記事の手順を使って再び有効にできます。Azure サブスクリプションは、次の理由で無効になることがあります。

## 使用制限に達している

誤ってプランの規定量を超過したリソースの利用に対する課金を防ぐために、[使用制限](https://azure.microsoft.com/pricing/spending-limits/)機能が用意されています。プランに含まれる月間使用量に達した場合、その請求月のそれ以降の期間、サービスは無効化されます。使用制限を削除してサービスの無効化を回避できます。また、Azure アカウントの課金状況を監視して管理できるように、[Microsoft Azure サブスクリプションの課金アラートを設定](billing-set-up-alerts.md)できます。

使用制限を削除する方法を次に示します。

1. [Azure アカウント センター](https://account.windowsazure.com/Home/Index)にログインします。

2. **[サブスクリプション]** をクリックします。

3. サブスクリプションを選択します。

4. "*サブスクリプションの使用制限に達したので、課金されないように無効にされました*" というメッセージが表示されたら、それをクリックします。
	
	それ以外の場合は、**[サブスクリプションの状態]** 領域の **[使用制限の削除]** をクリックします。
	
5. 必要に応じて次のオプションのいずれかを選択します。

|オプション|効果|
|------|------|
|使用制限を無期限に削除|次の請求期間の開始時に使用制限を自動的に有効にすることなく、使用制限を削除します。|
|現在の請求期間の使用制限を削除|使用制限を削除し、次の請求期間の開始時に使用制限を自動的に有効に戻します。|

>[AZURE.NOTE] 無料試用版サブスクリプションを使用している場合、使用制限を削除すると、ご利用のサブスクリプションが、無料試用の最後に従量課金制に変換されます。

## 請求の支払い期限が過ぎている

未払いの残高を全額支払います。支払いを行うには、「[Azure サブスクリプションに支払期限を過ぎた未払い額があるという通知を受信する理由](billing-azure-subscription-past-due-balance.md#what-can-you-do-to-resolve-the-issue)」を参照してください。

## 請求がクレジット カードの制限を超えている

この問題を解決するには、次の方法のいずれかを使用してください。

- [別のクレジット カードに切り替えます](billing-how-to-change-credit-card.md)。
- 企業の場合、[請求書による支払いに切り替える](https://azure.microsoft.com/pricing/invoicing/)ことができます。

## サブスクリプションを再度有効にする方法

取り消されたサブスクリプションを再度有効にするには、サポート チケットを作成する必要があります。[Azure ポータル](https://portal.azure.com/)にサインインし、**[ヘルプとサポート]**、**[新しいサポート要求]** の順に選択してください。

![helpandsupportbutton](./media/billing-how-to-create-billing-support-ticket/helpandsupport.png)

ポータルにアクセスできない場合は、Azure サポート Web サイトか Azure アカウント センターからサポート要求を送信してください。

 * [Azure サポート Web サイト](https://azure.microsoft.com/support/options/)からサポート チケットを送信するには、**[サポートの要求]** をクリックします。
 * [Azure アカウント センター](https://account.windowsazure.com/Subscriptions)からサポート チケットを送信するには、サブスクリプションを選択し、**[Microsoft サポートに問い合わせる]** をクリックします。

詳細については、「[Azure の請求とサブスクリプションの問題についてサポート チケットを作成する方法](billing-how-to-create-billing-support-ticket.md)」を参照してください。

<!---HONumber=AcomDC_0803_2016-->