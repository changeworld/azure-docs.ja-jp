---
title: "Azure のサインアップに関する問題のトラブルシューティング | Microsoft Docs"
description: "Azure のサインアップに関する一般的な問題をトラブルシューティングする方法について説明します。"
services: 
documentationcenter: 
author: JiangChen79
manager: adpick
editor: 
tags: billing,top-support-issue
ms.assetid: a0907da1-cb2d-41d1-a97f-43841fabe355
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: cjiang
translationtype: Human Translation
ms.sourcegitcommit: f058194302949d1f5ba7d86e9a693ba229bdc9a2
ms.openlocfilehash: b913fd0d944568e422ffcf86791910a746367add


---
# <a name="i-cant-sign-up-for-azure"></a>Azure にサインアップできません
Azure にサインアップできない場合は、問題をトラブルシューティングするために確認できるいくつかの事柄があります。

## <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>[カードによる本人確認] セクションで進行状況バーが停止する

Azure のサインアップでは、途中に [カードによる本人確認] という名前のセクションがあります。 進行状況バーが停止した場合:

![サインアップ中に停止した [カードによる本人確認] セクションのスクリーンショット](./media/billing-troubleshoot-azure-sign-up-issues/identity-verification-hangs.PNG)

この問題は、ブラウザーでサード パーティの Cookie がブロックされたときに発生します。

### <a name="suggestion"></a>推奨事項

1. ブラウザーの設定で、サード パーティの Cookie を許可します。
  * Edge の場合は、[設定]、[詳細設定を表示]、[Cookie] の順に選択し、[Cookie をブロックしない] を選びます。
  * Chrome の場合は、[設定]、[詳細設定を表示]、[プライバシー]、[コンテンツの設定] の順に選択し、[サードバーティの Cookie とサイト データをブロックする] をオフにします。
2. Azure のサインアップ ページを更新し、問題が解決したかどうかを確認します。
3. 更新しても問題が解決しなかった場合は、ブラウザーを終了して再起動した後、やり直してください。

## <a name="no-text-messages-or-calls-during-sign-up-account-verification"></a>サインアップ アカウントの検証中に、テキスト メッセージの送信がなく、電話もかかってこない
* テキスト コードが配信されるまでに、最大で&4; 分かかる場合があります。
* SMS を受信できる電話番号であることを確認します。
* ドロップダウン メニューで、国コードの内容を含む電話番号を再確認してください。
* [テキスト メッセージを受信] を使用する場合はご自分の電話でテキスト メッセージ (SMS) を受信できることを、[電話で確認コードを受け取る] を選択した場合は通話呼び出しを受信できることを確認します。
* テキスト メッセージを受信したら、テキスト ボックスにそのコードを入力し、検証ボタンをクリックして続行します。

### <a name="suggestions"></a>検索候補
* ご自分の電話でテキスト メッセージ (SMS) を受信しない場合は、代わりの確認方法である [電話で確認コードを受け取る] を使用してください。
* SMS と [電話で確認コードを受け取る] のどちらの方法を使用しても電話での確認手順に失敗する場合は、別の電話番号を使用してください。
* 電話確認処理に VoIP 電話番号を使用することはできません。

## <a name="credit-card-declined-or-not-accepted"></a>クレジット カードが拒否されます
サインアップ時に使う支払方法がサポートされていることを確認します。 詳しくは、「[Azure へのサインアップ時にデビット カードまたはクレジット カードが拒否される](billing-credit-card-fails-during-azure-sign-up.md)」もご覧ください。

## <a name="free-trial-is-not-available"></a>"無料試用版は使用できません"
過去に Azure サブスクリプションを使用していませんか。 Azure 使用条件契約書では、Azure を初めて使用するユーザーのみが Azure 無料試用版をアクティブ化できるという制限を設けています。 他の種類の Azure サブスクリプションを持っている場合も、無料試用版をアクティブ化することはできません。

### <a name="suggestion"></a>推奨事項
* [従量課金のサブスクリプション](https://azure.microsoft.com/offers/ms-azr-0003p/)へのサインアップを検討してください。

## <a name="cant-activate-azure-benefit-plan-like-msdn-bizspark-bizsparkplus-or-mpn"></a>MSDN、BizSpark、BizSparkPlus、MPN などの Azure 特典プランをアクティブ化できません
正しい資格情報を使っていることを確認し、プランを選択する資格がある場合は、特典プログラム チャネルを調べます。

* MSDN
  * [MSDN アカウント ページ](https://msdn.microsoft.com/subscriptions/manage/default.aspx)で資格の状態を確認します。
  * 状態を確認できない場合は、 [MSDN サブスクリプション カスタマー サービス センター](https://msdn.microsoft.com/subscriptions/contactus.aspx)
* MPN
  * [MPN ポータル](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx) にサインインし、資格の状態を確認します。 該当する [クラウド プラットフォーム コンピテンシー](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)をお持ちの場合、追加特典の資格を与えられることがあります。
  * 状態を確認できない場合は、 [MPN サポート](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx)にお問い合わせください。
* BizSpark
  * [BizSpark ポータル](https://www.microsoft.com/bizspark/default.aspx#start-two) にサインインし、BizSpark と BizSpark Plus の資格の状態を確認します。
  * 状態を確認できない場合は、[BizSpark チームにお問い合わせ](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)ください

## <a name="cant-activate-new-azure-in-open-subscription"></a>新しい Azure イン オープン プラン サブスクリプションをアクティブ化できません
新しい Azure イン オープン プラン サブスクリプションを作成するには、少なくとも&1; つの Azure イン オープン プラン トークンが関連付けられている有効な OSA キーを持っている必要があります。

### <a name="suggestion"></a>推奨事項
OSA キーを持っていない場合は、[Microsoft Pinpoint](http://pinpoint.microsoft.com/)に記載されている Microsoft パートナーにお問い合わせください。

## <a name="need-help-contact-support"></a>お困りの際は、 サポートにお問い合せください。
お困りの際は、問題を迅速に解決するために、[サポートにお問い合わせ](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)ください。



<!--HONumber=Feb17_HO2-->


