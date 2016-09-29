<properties
	pageTitle="別のプランへの Azure サブスクリプションの切り替え | Microsoft Azure"
	description="サブスクリプション管理ポータルを使用して Azure サブスクリプションを変更し、別のプランに切り替える方法について説明します。"
	services=""
	documentationCenter=""
	authors="genlin"
	manager="msmbaldwin"
	editor=""
	tags="billing,top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="genli"/>

# 別のプランへの Azure サブスクリプションの切り替え

## 知っておくべきこと

サービスのダウンタイムなしで、サブスクリプションをより有利なプランに切り替えることが[できる場合があります](#where-is-the-button)。現在、[アカウント センター](https://account.windowsazure.com/Subscriptions)でサポートされているパスを以下に示します。各リンクをクリックして要件を確認してください。

| From | To |
|-------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [開発テスト用の従量課金制プラン](https://azure.microsoft.com/offers/ms-azr-0023p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [12 か月の前払い](https://azure.microsoft.com/offers/ms-azr-0026p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |
| [従量課金制](https://azure.microsoft.com/offers/ms-azr-0003p/) | [Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |
> [AZURE.NOTE] 無料試用版を使用している場合は、[従量課金制にアップグレードする](billing-buy-sign-up-azure-subscription.md#upgrade-azure-free-trial-to-pay-as-you-go)方法を確認してください。

> サブスクリプションにサインアップしたのが最近の場合、プランを変更するには、最初の請求期間が完了するまで待つ必要があります。

> 他のプラン変更については、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。
	
## サブスクリプションを別のプランに切り替えるためのガイド

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
2.	変更するサブスクリプションを選択します。
3.	**[別のプランに切り替える]** をクリックします。[このボタンが表示されていない場合](#where-is-the-button)は、その理由を明らかにしてください。

	![siwtchbutton](./media/billing-how-to-switch-azure-offer/switchbutton.png)
	
4.	ご使用のサブスクリプションで切り替えることができるすべてのプランが表示されます。この一覧の内容はアカウントが関連付けられているメンバーシップと、現在のプランに基づいて決まります。詳細については、各プランの下の **[詳細情報]** をクリックしてください。目的のプランをクリックして、次の手順に進みます。

	![selectoffer](./media/billing-how-to-switch-azure-offer/selectoffer.png)
5.	切り替え先のプランによっては、切り替えによって生じる影響について注意が表示される場合があります。先に進む前に、その内容をよく確認し、記載されている指示に従ってください。

	![thingstonote](./media/billing-how-to-switch-azure-offer/thingstonote.png)
6.	サブスクリプションの名前を変更できます。既定では、サブスクリプションは新しいプラン名に設定されます。**[プランの切り替え]** をクリックしてプロセスを完了します。

	![confirmpage](./media/billing-how-to-switch-azure-offer/confirmpage.png)
7.	成功です。 これでサブスクリプションは新しいプランに切り替わりました。

## よく寄せられる質問 (FAQ)

### どのようなプラン変更がサポートされていますか?

「[切り替え可能なプランの選択肢](#what-you-need-to-know)」のリストを参照してください。

### ボタンはどこにありますか?

次の場合、**[別のプランに切り替える]** は表示されません。

- 最初の請求期間中である場合。プランを変更するには、最初の請求期間が完了するまで待つ必要があります。

- プラン変更の対象ではない場合。[切り替え可能なプラン](#what-you-need-to-know)の一覧で、希望するプラン変更がサポートされていることを確認してください。

### プランを切り替えることで、サービスに何か影響がありますか?

サービスのダウンタイムは発生しません。ただし、切り替え先のプランによっては、制限がある場合があります。たとえば、一部のプランでは運用環境における使用が禁止されています。したがって、該当する場合は、運用環境のリソースを別のサブスクリプションに移動させる必要があります。

### プランの切り替えで、サービス管理者は何か影響を受けますか? 

サブスクリプションに関連付けられているユーザーへの影響はありません。

### プランの切り替えに伴い、どのような課金関連の変更が予想されますか?

切り替えを行った日に、すべての未処理料金の請求書が生成されます。その後、サブスクリプションは新しいプランの価格条件に従って課金されます。サブスクリプションの請求日はプランを変更した日付に変更されます。プラン変更前の使用状況と課金データは保存されないので、プランの切り替え前にこれらのデータをダウンロードしておくことをお勧めします。

> [AZURE.NOTE] 請求関連の制約により、サブスクリプションの作成後、最初の請求サイクル内にプランを変更することはできません。

### このしくみを使用して、Web ダイレクトから [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) または [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) プランに移行できますか?

このしくみを使用して可能なのは、Web ダイレクト プランに対する切り替えのみです。既存のサブスクリプションを EA に移行するには、登録管理者に依頼してアカウントを EA に追加してもらう必要があります。次に招待メールが送信されてきます。指示に従って招待を受け入れると、サブスクリプションは Enterprise Agreement に基づいて自動的に移行されます。現在、既存の Web ダイレクト サブスクリプションを CSP に移行するオプションはありません。

## 次のステップ

- サブスクリプションの[管理者ロールを管理する](billing-add-change-azure-subscription-administrator.md)方法を確認する

- [使用状況データと請求書をダウンロード](billing-download-azure-invoice-daily-usage-date.md)して使用状況を追跡する

> [AZURE.NOTE] まだ他に質問がある場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

<!---HONumber=AcomDC_0914_2016-->