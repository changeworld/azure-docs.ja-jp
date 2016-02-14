<properties
	pageTitle="Azure Marketplace の料金について | Microsoft Azure"
	description="Marketplace 注文に関連した料金を確認する方法について説明します。"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="billing"
	/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/03/2016"
	ms.author="cjiang"/>

# Azure Marketplace の料金について
この記事では、Marketplace 注文に関連した料金を確認する方法の概要を説明します。課金内容を確認する際のいくつかの注意点として、さまざまな価格の種類 (基本のみ、使用量のみ、基本 + 使用量) があります。Marketplace で行う注文ごとに月単位の請求サイクルが発生することに注意してください。これらのトピックについては、以下で詳しく説明します。

## Marketplace の課金内容を確認する
[Azure アカウント ポータル](https://account.windowsazure.com/subscriptions/)にログインすると、現在の課金情報と過去の課金情報を確認したりダウンロードしたりすることができます。

課金内容を表示またはダウンロードするには、次の手順を実行します。

1. 組織 ID を使用して Azure アカウント ポータルにサインインします。
2. **[Marketplace]** をクリックします。表示される一覧は、Azure アカウントで行われたすべての Marketplace 注文を含む一覧です。注文は親の Azure サブスクリプションの名前と ID でグループ化されていることに気付きます。![Marketplace orders](./media/billing-understand-your-azure-marketplace-charges/marketplace-orders.png)
3. 表示された注文のいずれかをクリックすると、その注文の現在の請求サイクルの概要が表示されます。さまざまな価格モデルの説明については、「[Marketplace FAQ](https://azure.microsoft.com/marketplace/faq/)」を参照してください。![Order summary](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

この概要ビューには、各注文の次の情報が表示されます。- 価格 - 請求サイクル - 購入日 - 関連付けられた親サブスクリプション - 請求通貨

この情報の表示方法にはいくつか変更が加えられたことに注意してください。以前は、注文の価格が税込みで表示されていました。この点が更新され、お住まいの国に応じて、表示価格を外税方式または内税方式にできるようになりました。また、価格の位置は、右側のナビゲーションから中央に移動されました。このような料金が関連付けられている Azure サブスクリプションを見つけやすくするために、Azure サブスクリプションの名前と ID も **[支払い方法の変更]** オプションと共に右側のナビゲーションに追加されました。

## 支払い方法と注文管理を更新する
概要ページにはユーザー アクションが表示されており、これを使用すると、支払いモデルと注文管理の機能を更新できます。

> [AZURE.NOTE] 組織 ID を使用して個人情報を変更する場合は、サポート付きのチケットを記録する必要があります。

支払い方法を更新するには、ページの右側にある **[支払い方法の変更]** リンクをクリックします。![Order summary](./media/billing-understand-your-azure-marketplace-charges/order-summary.png)

このリンクをクリックすると別のポータルが表示されます。ここで、希望する支払い方法に変更できます。

支払い方法を変更するには、次の手順に従います。

1. **[支払い方法の変更]** をクリックします。![サブスクリプション](./media/billing-understand-your-azure-marketplace-charges/subscriptions.jpg)
2. 変更後の支払い方法を選択します。**[支払い方法]** オプションを使用すると、クレジット カードを選択できます。**[支払い方法を追加する]** オプションを使用すると、新しいクレジット カードを追加できます。![Change payment method](./media/billing-understand-your-azure-marketplace-charges/change-payment-method.jpg)

## 注文管理
概要ページでは、**[注文の管理]** リンクが表示されます。このリンクをクリックすると、ポータルが表示されます。ここでは、すべての Marketplace 注文の表示、サービス正常性の確認、サポートの要求を行うことができます。![ポータル](./media/billing-understand-your-azure-marketplace-charges/portal.jpg)

サポートを要求するには、**[ヘルプとサポート]** タイルをクリックします。これにより、次の **[ヘルプとサポート]** ページが表示されます。このページでは、次の操作を実行できます。- 新しいサポート要求を開く。 - 既存のサポート要求を管理する。 - リソースの正常性を管理する。

![サポートの要求](./media/billing-understand-your-azure-marketplace-charges/request-support.jpg)

## 請求履歴
概要ページの新機能として、仮想マシンなど、使用量ベースのプランを表示できます。請求サイクルに基づき、現在の期間と終了した期間両方の使用量をダウンロードできるようになりました。使用量を表示するには、表示する期間ごとに、**[使用量のダウンロード]** をクリックします。

実際の料金の確認に関心がある場合は、**[料金の表示]** をクリックします。これにより、別のポータルにリダイレクトされます。ここでは、すべての料金を税込みで表示できます。組織 ID を使用している場合、**[料金の表示]** ボタンは使用できないため、サポート チケットを送信して料金の概要を求める必要があります。![Billing history](./media/billing-understand-your-azure-marketplace-charges/billing-history.png)

実際の料金を表示するには、次の手順に従います。

1. **[請求履歴]** ページで **[料金の表示]** をクリックします。新しいタブが開いて、[注文履歴](https://account.microsoft.com/billing/orders#/)が表示されます。課金サービス ポータルで未処理の利用状況データにアクセスして取得する場合は、ページの下部にあるサポート リンクをクリックしてサポートに問い合わせる必要があることに注意してください。![Order history](./media/billing-understand-your-azure-marketplace-charges/order-history.jpg)
2. 確認する注文を特定して **[詳細]** をクリックし、料金の内訳を表示します。この内訳には、小計、税、合計料金が含まれます。![Order details](./media/billing-understand-your-azure-marketplace-charges/order-details.jpg)

<!---HONumber=AcomDC_0204_2016-->