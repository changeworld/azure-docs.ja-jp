<properties
	pageTitle="別のプランへの Azure サブスクリプションの切り替え | Microsoft Azure"
	description="サブスクリプション管理ポータルから Azure サブスクリプションを変更し、別の Azure プランに切り替える方法について説明します。"
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
	ms.date="08/24/2016"
	ms.author="genli"/>

# 別のプランへの Azure サブスクリプションの切り替え

既存のサブスクリプションをサービスのダウンタイムなしで、より有利なプランに切り替えることができます。次のような場合、プランの切り替えの検討できます。

-	Azure に対する支出が非常に高いことに気づいた場合に、年額コミットメント プランに切り替えて料金の割引を受ける資格がある。Microsoft Azure リソースの消費を把握する方法の詳細については、[こちら](billing-usage-rate-card-overview.md)をクリックしてください。
-	従量課金制サブスクリプションへのサインアップが終了した後で、MSDN や BizSpark などのメンバーシップ特典を利用したい。

> [AZURE.NOTE] この記事についてさらにヘルプが必要な場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

## サブスクリプションを別のプランに切り替えるためのガイド

> [AZURE.VIDEO switch-to-a-different-azure-offer]

1.	[Azure アカウント センター](https://account.windowsazure.com/Subscriptions)にサインインします。
2.	変更するサブスクリプションを選択します。
3.	**[別のプランに切り替える]** をクリックします。

	![siwtchbutton](.\media\billing-how-to-switch-azure-offer\switchbutton.png)
4.	ご使用のサブスクリプションで切り替えることができるすべてのプランが表示されます。この一覧の内容はアカウントが関連付けられているメンバーシップと、現在のプランに基づいて決まります。詳細については、各プランの下の “詳細情報” リンクをクリックしてください。目的のプランを選択して、次の手順に進みます。

	![selectoffer](.\media\billing-how-to-switch-azure-offer\selectoffer.png)
5.	切り替え先のプランによっては、切り替えによって生じる影響について注意が表示される場合があります。先に進む前に、その注意内容をよく確認し、記載されている指示に従ってください。

	![thingstonote](.\media\billing-how-to-switch-azure-offer\thingstonote.png)
6.	サブスクリプションの名前を変更するオプションが用意されています。既定では、サブスクリプションは新しいプラン名に設定されます。[プランの切り替え] をクリックしてプロセスを完了します。

	![confirmpage](.\media\billing-how-to-switch-azure-offer\confirmpage.png)
7.	成功です。 これでサブスクリプションは新しいプランに切り替わりました。

## よく寄せられる質問 (FAQ)

**どのようなプラン変更がサポートされていますか?**

「[切り替え可能なプランの選択肢](#available-offers-you-can-switch-to)」のリストを参照してください。

**プランを切り替えることで、サービスに何か影響がありますか?**

サービスのダウンタイムは発生しません。ただし、切り替え先のプランによっては、制限がある場合があります。たとえば、一部のプランでは運用環境における使用が禁止されています。したがって、該当する場合は、運用環境のリソースを別のサブスクリプションに移動させる必要があります。

**プランの切り替えで、サービス管理者は何か影響を受けますか?**

サブスクリプションに関連付けられているユーザーへの影響はありません。

**プランの切り替えに伴い、どのような課金関連の変更が予想されますか?**

切り替えを行った日に、すべての未処理料金の請求書が生成されます。その後、サブスクリプションは新しいプランの価格条件に従って課金されます。サブスクリプションの請求日はプランを変更した日付に変更されます。プラン変更前の使用状況と課金データは保存されないので、プランの切り替え前にこれらのデータをダウンロードしておくことをお勧めします。

**注:** 課金に関連する制約条件により、サブスクリプションの作成後、最初の課金サイクル内でプランを変更することはできません。

**このしくみを使用して、Web ダイレクトから [Cloud Solution Provider](https://partner.microsoft.com/Solutions/cloud-reseller-overview) (CSP) または [Enterprise Agreement](https://azure.microsoft.com/pricing/enterprise-agreement/) (EA) プランに移行できますか?**

このしくみを使用して可能なのは、Web ダイレクト プランに対する切り替えのみです。既存のサブスクリプションを EA に移行するには、登録管理者に依頼してアカウントを EA に追加してもらう必要があります。次に招待メールが送信されてきます。指示に従って招待を受け入れると、サブスクリプションは Enterprise Agreement に基づいて自動的に移行されます。現在、既存の Web ダイレクト サブスクリプションを CSP に移行するオプションはありません。

## 切り替え可能なプランの選択肢

現在、[アカウント センター](https://account.windowsazure.com/Subscriptions)でサポートされているパスを以下に示します。この記事は、オプションを追加した場合、更新されます。

-	[従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)から [12 か月分の前払いプラン](https://azure.microsoft.com/offers/ms-azr-0026p/)への変更
-	[従量課金](https://azure.microsoft.com/offers/ms-azr-0003p/)から [Visual Studio サブスクライバー向けの月単位の Azure クレジット プラン](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)のいずれかへの変更

その他のプランの変更については、[サポートにお問い合わせ](http://go.microsoft.com/fwlink/?LinkID=619338)ください。

> [AZURE.NOTE] まだ他に質問がある場合は、問題を迅速に解決できるよう[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。

<!---HONumber=AcomDC_0824_2016-->