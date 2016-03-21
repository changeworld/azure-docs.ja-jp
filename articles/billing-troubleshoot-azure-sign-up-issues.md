<properties
	pageTitle="Azure サインアップに関する問題のトラブルシューティング | Microsoft Azure"
	description="Azure にサインアップする際に発生の可能性のある問題の一般的な原因を探る"
	services="billing"
	documentationCenter=""
	authors="jiangchen79"
	manager="felixwu"
	editor=""
	tags="top-support-issue"/>

<tags
	ms.service="billing"
	ms.workload="na"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="rgarodia"/>

# Azure サインアップに関する問題のトラブルシューティング
この記事は、Azure にサインアップする際に発生の可能性がある問題について、その一般的な原因を探るのに役立ちます。

## アカウント プロファイルの情報は正確で完全ですか?
プロファイルの詳細は課金明細書の生成に使用されるため、サインアップに成功するには、自分の情報を正確に、さらに完全に入力することが重要となります。不正確または不完全なフィールドの例として、- 名前または姓が入力されていない、- 電話番号が無効である、- 住所が無効である、- 標準以外の文字が使用されている、などが挙げられます。

ご提案 - 入力する情報は必ず課金明細書と一致させてください。

## サインアップのアカウント検証段階でご自分の電話番号が SMS または通話呼び出しを受信できることを確認済みですか?
- ドロップダウン メニューで、国コードの内容を含む電話番号を再確認してください。
- [テキスト メッセージを受信] を使用する場合はご自分の電話でテキスト メッセージ (SMS) を受信できることを、[電話で確認コードを受け取る] を選択した場合は通話呼び出しを受信できることを確認します。
- 携帯電話を使用する場合は、携帯電話の接続状態が良好であることを確認します。
- [テキスト メッセージを受信] を選択した場合は、最大で 4 分待つと、メッセージング システムからお客様のテキスト コードが送信されてきます。
- テキスト メッセージを受信したらすぐに、テキスト ボックスにそのコードを入力し、検証ボタンをクリックして続行します。

ご提案: - 電話にテキスト メッセージ (SMS) が送信されてこない場合は、もう 1 つの検証方法である [電話で確認コードを受け取る] を使用してください。- SMS および [電話で確認コードを受け取る] の両方の方法が電話検証段階で失敗する場合は別の電話番号を使用してください。電話検証プロセスで VOIP 電話番号を使用することはできません。

**注:** 後で希望の電話番号を変更するには、[プロファイル情報](https://account.windowsazure.com/Profile)を更新します。

## サインアップ時に使用する支払方法が Azure のアクティブ化または支払いでサポートされていることを確認していますか?
- バーチャルおよびプリペイドのクレジット/デビット カードは使用できません。
- 使用できるクレジット/デビット カード会社は、アカウントの国によって異なります。

ご提案: - バーチャルまたはプリペイドのデビット/クレジット カードは使用しないでください。- 支払いまたはアクティブ化に失敗した場合は、別のサポートされている有効なクレジット カードを使用してみてください。- クレジット カードがアクティブであること、セキュリティ ポリシーを理由に銀行がオンライン トランザクションをブロックしていないことを確認してください。

## MSDN、BizSpark、BizSparkPlus、または MPN などの Azure 特典プランをアクティブ化しようとしていますか?
プランを選択する資格がある場合は、特典プログラム チャネルを調べます。- MSDN: - [MSDN アカウント ページ](https://msdn.microsoft.com/subscriptions/manage/default.aspx)で資格の状態を確認します。- 資格の状態を確認できない場合は、[MSDN サブスクリプション カスタマー サービス センター](https://msdn.microsoft.com/subscriptions/contactus.aspx)にお問い合わせください。- MPN: - [MPN ポータル](https://mspartner.microsoft.com/en/us/Pages/Locale.aspx)にサインインして、資格の状態を確認します。適切な[クラウド プラットフォーム コンピテンシー](https://mspartner.microsoft.com/en/us/pages/membership/cloud-platform-competency.aspx)を有している場合は、追加の特典を受ける資格がある場合があります。- 自分の資格の状態を確認できない場合は、[MPN サポート](https://mspartner.microsoft.com/en/us/Pages/Support/Premium/contact-support.aspx) にお問い合わせください。- Bizpark: - [BizSpark ポータル](https://www.microsoft.com/bizspark/default.aspx#start-two)にサインインして BizSpark および BizSpark Plus の資格の状態を確認します。自分の資格の状態を確認できない場合は、[BizSpark サポート チーム](mailto:bizspark@microsoft.com?subject=BizSpark%20Support&body=Thank%20you%20for%20contacting%20BizSpark.%20Please%20provide%20as%20much%20of%20the%20following%20information%20as%20possible,%20as%20it%20will%20help%20expedite%20our%20response%20to%20you.%0aContact%20name:%0aStartup%20name:%0aMicrosoft%20Account/Live%20ID:%0aSpecific%20description%20of%20issue%20experienced%20or%20question:%0a%0aThank%20you,%0a%0aThe%20BizSpark%20Team)に電子メールを送信して BizSpark サポートにお問い合わせください。

ご提案: - サインアップ操作中に新しい特典サブスクリプションをアクティブ化しようとするとエラーが発生するという場合は、[Azure サブスクリプション ページ](http://account.windowsazure.com/Subscriptions)でサブスクリプションのセットアップが完了しているかどうかを確認してください。サブスクリプションがアクティブ状態であると表示されるまで数分かかる場合があります。サブスクリプションがアクティブになると、電子メールが届きます。4 分間以上経過してもサブスクリプションが保留中のままである場合は、[Azure サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。

## 新しい Azure イン オープン プラン サブスクリプションをアクティブ化しようとしていますか?
- 新しい Azure イン オープン プラン サブスクリプションをアクティブ化するには、少なくとも 1 つの Azure イン オープン トークンが関連付けられている有効な OSA キーを持っている必要があります。

ご提案: - OSA キーを持っていない場合は、[Microsoft Pinpoint](http://pinpoint.microsoft.com/) に一覧されている Microsoft パートナーのいずれかにお問い合わせください。

## 過去に Azure サブスクリプションを使用した経験があるお客様で、今回、Azure 無料試用版をアクティブ化しようとしていますか?
Azure 使用条件契約書では、以前に使用経験のないユーザーに限り Azure 無料試用版を 1 回だけアクティブ化できるという制限を設けています。他の種類の Azure サブスクリプションを持っていた場合でも、無料試用版をアクティブ化することはできません。

ご提案: - 過去に Azure サブスクリプションを既にアクティブ化しているために無料試用版をアクティブ化できないという場合は、従量課金サブスクリプションの購入をご検討ください。特典を受ける資格をお持ちの可能性があります。Azure 無料試用版プランではなく、[Microsoft Azure プランの詳細に関するページ](https://azure.microsoft.com/support/legal/offer-details/)を参照してください。福利厚生制度には特定の前提条件が必要となりますので注意してください。

## まだサインアップできませんか?
サインアップに関する問題がまだ解決しない場合、または発生している問題がこの記事で扱われていない場合は、[Azure サポートにお問い合わせ](http://go.microsoft.com/fwlink/?linkid=544831&clcid=0x409)ください。

<!---HONumber=AcomDC_0128_2016-->