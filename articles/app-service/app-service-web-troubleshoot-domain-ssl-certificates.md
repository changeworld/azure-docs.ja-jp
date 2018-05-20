---
title: Azure Web アプリでのドメインと SSL 証明書に関する問題のトラブルシューティング | Microsoft Docs
description: Azure Web アプリでのドメインと SSL 証明書に関する問題のトラブルシューティング
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2018
ms.author: genli
ms.openlocfilehash: ba21475b771f1688c0a3f2f34c8d961fba5cd182
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2018
---
# <a name="troubleshoot-domain-and-ssl-certificate-problems-in-azure-web-apps"></a>Azure Web アプリでのドメインと SSL 証明書に関する問題のトラブルシューティング

この記事では、Azure Web アプリのためにドメインまたは SSL 証明書を構成するときに発生する可能性がある、一般的な問題の一覧を示します。 これらの問題の考えられる原因と解決策についても説明します。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

## <a name="certificate-problems"></a>証明書に関する問題

### <a name="unable-to-add-bind-ssl-certificate-to-a-web-app"></a>Web アプリに SSL 証明書のバインドを追加できない 

### <a name="symptom"></a>症状

SSL バインディングを追加するときに、次のエラー メッセージが表示されます。

**SSL バインディングを追加できませんでした。既存の VIP の証明書は設定できません。別の VIP が既にその証明書を使用しています。**

### <a name="cause"></a>原因

この問題は、複数の Web アプリにわたって、同じ IP アドレスへの IP ベースの SSL バインディングが複数ある場合に発生することがあります。 たとえば、Web アプリ A に、古い証明書を持つ IP ベースの SSL があり、 Web アプリ B に、同じ IP アドレスの新しい証明書を持つ IP ベースの SSL がある場合です。 新しい証明書を持つ Web アプリの SSL バインディングを更新すると、別のアプリに対して同じ IP アドレスが使われているため、このエラーで更新が失敗します。 

### <a name="solution"></a>解決策 

この問題を解決するには、次のいずれかの方法を使用します。

- Web アプリで、古い証明書を使用している IP ベースの SSL バインディングを削除します。 
- 新しい証明書を使用する、新しい IP ベースの SSL バインディングを作成します。

### <a name="unable-to-delete-a-certificate"></a>証明書を削除できない 

### <a name="symptom"></a>症状

証明書を削除しようとすると、次のエラー メッセージが表示されます。

**証明書は、SSL バインディングで使用されているため、削除できません。証明書を削除できるようにするには、先に SSL バインディングを削除する必要があります。**

### <a name="cause"></a>原因

この問題は、証明書が別の Web アプリで使用されている場合に発生する可能性があります。

### <a name="solution"></a>解決策

Web アプリからその証明書の SSL バインディングを削除します。 その後で証明書の削除を試みます。 それでも証明書を削除できない場合は、インターネット ブラウザーのキャッシュをクリアし、新しいブラウザー ウィンドウで再度 Azure Portal を開きます。 そして次に、証明書の削除を試みます。

### <a name="unable-to-purchase-an-app-service-certificate"></a>App Service 証明書を購入できない 

### <a name="symptom"></a>症状
Azure Portal から [App Service 証明書](./web-sites-purchase-ssl-web-site.md)を購入できません。

### <a name="cause-and-solution"></a>原因と解決策
この問題は、次のいずれかの理由で発生することがあります。

- App Service プランが "Free" または "Shared" である。 これらの価格レベルでは SSL はサポートされていません。 

    **解決策**: Web アプリの App Service プランを "Standard" にアップグレードします。

- サブスクリプションに有効なクレジット カードが登録されていない。

    **解決策**: サブスクリプションに有効なクレジット カードを追加します。 

- サブスクリプション プランで、Microsoft Student などの App Service 証明書の購入がサポートされていない。  

    **解決策**: サブスクリプションをアップグレードします。 

- そのサブスクリプションは、サブスクリプションで許可されている購入の上限に達しています。

    **解決策**: サブスクリプションの種類が従量課金および EA の場合、App Service 証明書の購入上限は 10 件です。 その他のサブスクリプションの種類の場合、上限は 3 件です。 この上限を増やすには、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)へお問い合わせください。
- App Service 証明書が不正であるとマークされて、 次のエラー メッセージが表示される。"証明書に不正が疑われる旨のフラグが設定されました。 The request is currently under review. 証明書が 24 時間以内に使用可能にならなかった場合には、...”

    **解決策**: 証明書が不正であるとマークされて 24 時間以内に解決されなかった場合は、以下の手順に従ってください。

    1. [Azure Portal](https://portal.azure.com) にログインします。
    2. **[App Service 証明書]** に移動して、証明書を選択します。
    3. **[証明書の構成]** > **[手順 2: 確認]** > **[ドメインの検証]** と選択します。 これで、問題を解決するために、Azure の証明書プロバイダーに電子メールの通知が送信されます。

## <a name="domain-problems"></a>ドメインに関する問題

### <a name="purchased-ssl-certificate-for-wrong-domain"></a>正しくないドメインの SSL 証明書を購入した

### <a name="symptom"></a>症状

誤ったドメインの App Service 証明書を購入し、正しいドメインを使用するように証明書を更新できない。

### <a name="solution"></a>解決策

- その証明書を削除してから、新しい証明書を購入します。
- 誤ったドメインを使用している現在の証明書が “Issued” 状態にある場合は、課金の対象もその証明書になります。 App Service 証明書は払い戻しできませんが、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)に連絡し、他のオプションがあるかどうかを確認できます。 

### <a name="app-service-certificate-was-renewed-but-still-shows-the-old-certificate"></a>App Service 証明書は更新されたが、まだ古い証明書が表示される 

### <a name="symptom"></a>症状

App Service 証明書が更新されましたが、その App Service 証明書を使用する Web アプリが、まだ古い証明書を使用しています。 また、HTTPS プロトコルが必要であるという警告が表示されます。

### <a name="cause"></a>原因 
Web アプリ サービスは 8 時間ごとにバックグラウンド ジョブを実行し、変更があった場合は証明書リソースを同期します。 そのため、証明書の交換や更新を行うときに、アプリケーションが今までどおり古い証明書を取得していて、新しく更新された証明書を取得していないことがあります。 これは、証明書リソースを同期するジョブがまだ実行されていないためです。 
 
### <a name="solution"></a>解決策

証明書の同期を強制することができます。

1. [Azure Portal](https://portal.azure.com) にログインします。 **[App Service 証明書]** を選択し、次に目的の証明書を選択します。
2. **[キー更新と同期]** をクリックし、**[同期]** をクリックします。これが完了するまでしばらく時間がかかります。 
3. 同期が完了すると、"最新の証明書ですべてのリソースが正常に更新されました" という通知が表示されます。

### <a name="domain-verification-is-not-working"></a>ドメインの確認が機能していない 

### <a name="symptom"></a>症状 
App Service 証明書では、証明書を使う準備ができる前に、ドメインの確認を行う必要があります。 **[確認]** をクリックするとプロセスが失敗します。

### <a name="solution"></a>解決策
TXT レコードを追加して、手動でドメインを確認します。
 
1.  使用中のドメイン名をホストしているドメイン ネーム サービス (DNS) プロバイダーに移動します。
2.  Azure Portal に表示されるドメイン トークンの値を使用しているドメインの TXT レコードを追加します。 

DNS 伝達が実行されるのを数分待ってから、**[最新の情報に更新]** ボタンをクリックして確認をトリガーします。 

手動で確認する別の方法は、"HTML Web ページによる方法" です。この方法を使用すると、証明機関は、証明書の発行対象となるドメインの所有権を確認できます。

1.  {Domain Verification Token}.html という名前の HTML ファイルを作成します。 
2.  このファイルの内容には、ドメイン確認トークンの値を指定します。
3.  ドメインをホストしている Web サーバーのルートに、このファイルをアップロードします
4.  **[最新の情報に更新]** をクリックして証明書の状態を確認します。 確認が完了するまで数分かかる場合があります。

たとえば、"1234abcd" というドメイン確認トークンを使用して azure.com の標準証明書を購入しようとしている場合、http://azure.com/1234abcd.html に対して Web 要求を行うと、1234abcd が返されます。 

> [!IMPORTANT]
> 証明書の注文では、ドメイン確認操作を完了するまでの日数は 15 日のみです。 15 日を過ぎると証明書は証明機関に拒否されて、その証明書の課金は行われません。 この状況では、この証明書を削除し、もう一度やり直します。
>
> 

### <a name="unable-to-purchase-a-domain"></a>ドメインを購入できない

### <a name="symptom"></a>症状
Azure Portal 内の Web アプリまたは App Service ドメインからドメインを購入できません。

### <a name="cause-and-solution"></a>原因と解決策

この問題は、次のいずれかの理由で発生します。

- Azure サブスクリプションにクレジット カードが未登録、またはクレジット カードが無効。

    **解決策**: 未登録の場合はサブスクリプションに有効なクレジット カードを追加します。

- サブスクリプションの所有者でない場合は、ドメインを購入する権限がない可能性があります。

    **解決策**: アカウントに[所有者ロールを追加](../billing/billing-add-change-azure-subscription-administrator.md)するか、サブスクリプションの管理者に連絡し、ドメインを購入する権限を取得します。
- サブスクリプションで、ドメイン購入の上限に達しました。 現在の上限は 20 です。

    **解決策**: 上限の増加を依頼するには、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)へお問い合わせください。
- ご利用の Azure サブスクリプションの種類は、App Service ドメインの購入をサポートしていません。

    **解決策**: Azure サブスクリプションを、従量課金制サブスクリプションなどの他のサブスクリプションの種類にアップグレードします。

### <a name="unable-to-add-a-hostname-to-web-app"></a>Web アプリにホスト名を追加できない 

### <a name="symptom"></a>症状

ホスト名を追加するときに、ドメインの確認と検証のプロセスが失敗します。

### <a name="cause"></a>原因 

この問題は、次のいずれかの理由で発生します。

- ホスト名を追加するための権限がありません。

    **解決策**: サブスクリプションの管理者に問い合わせて、ホスト名を追加する権限があることを確認します。
- ドメインの所有権を確認できませんでした。

    **解決策**: CNAME または A レコードが正しく構成されているかどうかを確認します。 Web アプリにカスタム ドメインをマップするには、CNAME または A レコードのいずれかを作成します。 ルート ドメインを使用する場合は、A レコードおよび TXT レコードを使用する必要があります。

    |レコード タイプ|Host|参照先|
    |------|------|-----|
    |A|@|Web アプリの IP アドレス|
    |TXT|@|<app-name>.azurewebsites.net|
    |CNAME|www|<app-name>.azurewebsites.net|

### <a name="dns-cannot-be-resolved"></a>DNS を解決できない

### <a name="symptom"></a>症状

"DNS レコードが見つかりませんでした" というエラー メッセージが表示されます。

### <a name="cause"></a>原因
この問題は、次のいずれかの理由で発生します。

- Time to Live (TTL) 期間が有効期限切れになりませんでした。 ドメインの DNS 構成を調べて TTL 値を確認し、その期間が有効期限切れになるまで待機します。
- DNS 構成が正しくありません。

### <a name="solution"></a>解決策
- この問題が自動的に解決されるのを 48 時間待ちます。
- DNS 構成で TTL 設定を変更できる場合は、値を 5 分に変更し、この問題が解決されるかどうかを確認します。
- [WhatsmyDNS.net](https://www.whatsmydns.net/) を使用して、ドメインが Web アプリの IP アドレスを指し示していることを確認します。 そうでない場合は、A レコードを、Web アプリの正しい IP アドレスに向けて構成します。

### <a name="restore-a-deleted-domain"></a>削除したドメインを復元する 

### <a name="symptom"></a>症状
ドメインが Azure Portal に表示されなくなっています。

### <a name="cause"></a>原因 
サブスクリプションの所有者によってドメインが意図せず削除された可能性があります。

### <a name="solution"></a>解決策
ドメインが削除されたのが 7 日前までの場合、ドメインの削除プロセスはまだ開始されていません。 この場合、Azure Portal で、同じサブスクリプションを使用してもう一度同じドメインを購入できます (必ず、検索ボックスに正確なドメイン名を入力してください)。 このドメインに対して再度の請求は行われません。 ドメインが削除されたのが 7 日より前の場合は、ドメイン復元の助けを得るため、[Azure サポート](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)にお問い合わせください。

### <a name="custom-domain-returns-404-or-site-inaccessible"></a>カスタム ドメインが 404 または "サイトにアクセスできません" を返す 

### <a name="symptom"></a>症状

カスタム ドメイン名を使用してサイトを参照すると、次のエラー メッセージが表示されます。

**Error 404-Web app not found. (エラー 404-Web アプリが見つかりません。)**


### <a name="cause-and-solution"></a>原因と解決策

**原因 1** 

構成したカスタム ドメインに CNAME または A レコードがありません。 

**原因 1 の解決策**

- A レコードを追加した場合は、必ず TXT レコードも追加するようにします。 詳細については、[レコードの作成](./app-service-web-tutorial-custom-domain.md#create-the-a-record)についてのページを参照してください。
- Web アプリのためにルート ドメインを使用する必要がない場合は、A レコードではなく CNAME レコードを使用することをお勧めします。
- 同じドメインのために CNAME と A レコードの両方を使用しないでください。 これによって競合が発生し、ドメインが解決されない可能性があります。 

**原因 2** 

インターネット ブラウザーが、ドメインの古い IP アドレスをまだキャッシュしている可能性があります。 

**原因 2 の解決策**

ブラウザーをクリアします。 Windows デバイスの場合は、`ipconfig /flushdns` コマンドを実行できます。 [WhatsmyDNS.net](https://www.whatsmydns.net/) を使用して、ドメインが Web アプリの IP アドレスを指し示していることを確認します。 

### <a name="unable-to-add-subdomain"></a>サブドメインを追加できない 

### <a name="symptom"></a>症状

Web アプリに新しいホスト名を追加し、サブドメインを割り当てることができません。

### <a name="solution"></a>解決策

- サブスクリプションの管理者に問い合わせて、Web アプリにホスト名を追加する権限があることを確認します。
- さらに多くのサブドメインが必要な場合は、ドメイン ホスティングを Azure DNS に変更することをお勧めします。 Azure DNS を使用すると、Web アプリケーションに 500 のホスト名を追加できます。 詳細については、[サブドメインの追加](https://blogs.msdn.microsoft.com/waws/2014/10/01/mapping-a-custom-subdomain-to-an-azure-website/)に関するページを参照してください。











 


















