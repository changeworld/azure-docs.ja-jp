---
title: "詳細情報: Azure Active Directory のパスワード管理 | Microsoft Docs"
description: "パスワード ライトバックのしくみ、パスワード ライトバックのセキュリティ、パスワード リセット ポータルのしくみ、パスワードのリセットで使用されるデータなど、Azure AD のパスワード管理に関する高度なトピック。"
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: curtand
ms.assetid: d3be2912-76c8-40a0-9507-b7b1a7ce2f8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 0035aa17e661a52db371b533b547c88dcb0f0148
ms.openlocfilehash: 8a9e412776acf4e08658517b714d9644b172f523
ms.lasthandoff: 02/24/2017


---
# <a name="learn-more-about-password-management"></a>パスワード管理の詳細情報
> [!IMPORTANT]
> **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md)にお進みください。
>
>

既にパスワード管理をデプロイしている場合、またはデプロイする前に技術的な動作方法についてさらにしく知りたい場合は、サービスの背景技術の概念について、このセクションで概要を説明します。 次の内容を説明します。

* [**パスワード ライトバックの概要**](#password-writeback-overview)
  * [パスワード ライトバックのしくみ](#how-password-writeback-works)
  * [パスワード ライトバックでサポートされているシナリオ](#scenarios-supported-for-password-writeback)
  * [パスワード ライトバックのセキュリティ モデル](#password-writeback-security-model)
  * [パスワード ライトバックの帯域幅の使用](#password-writeback-bandwidth-usage)
* [**パスワード リセット ポータルのしくみ**](#how-does-the-password-reset-portal-work)
  * [パスワードのリセットで使用されるデータ](#what-data-is-used-by-password-reset)
  * [ユーザーのパスワード リセット データにアクセスする方法](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>パスワード ライトバックの概要
パスワード ライトバックは [Azure Active Directory Connect](connect/active-directory-aadconnect.md) コンポーネントです。Azure Active Directory Premium の現在のサブスクライバーによって有効にされ、使用されます。 詳細については、 [Azure Active Directory のエディション](active-directory-editions.md)を参照してください。

パスワード ライトバックを使用すると、オンプレミスの Active Directory にパスワードを書き戻すようにクラウド テナントを構成できます。  これにより、複雑なオンプレミスのセルフサービス パスワード リセット ソリューションを設定して管理する必要がなくなります。ユーザーはどこにいても、便利なクラウドベースの方法で自分のオンプレミスのパスワードをリセットできます。  次に、パスワード ライトバックの主な機能を示します。

* **ゼロ遅延フィードバック。**  パスワード ライトバックは同期操作です。  ユーザーのパスワードがポリシーに合わなかった場合や、何らかの理由でリセットまたは変更できなかった場合は、すぐにユーザーに通知します。
* **AD FS または他のフェデレーション技術を使ってユーザーのパスワードをリセットできます。**  パスワード ライトバック使うと、フェデレーション ユーザー アカウントと Azure AD テナントが同期している限り、クラウドからオンプレミスの AD パスワードを管理できます。
* **パスワード ハッシュ同期を使ってユーザーのパスワードをリセットできます。** パスワード リセット サービスは、同期されているユーザー アカウントでパスワード ハッシュ同期が有効になっていることを検出すると、このアカウントのオンプレミスのパスワードとクラウドのパスワードの両方を同時にリセットします。
* **アクセス パネルと Office 365 からのパスワードの変更をサポートします。**  フェデレーション ユーザーかパスワード同期されたユーザーが有効期限切れまたは有効期限切れでないパスワードを変更すると、これらのパスワードはローカル AD 環境に書き戻されます。
* **管理者が** [**Microsoft Azure 管理ポータル**](https://manage.windowsazure.com)でパスワードをリセットする場合のパスワードのライトバックをサポートします。  管理者が [Microsoft Azure 管理ポータル](https://manage.windowsazure.com)でユーザーのパスワードをリセットするときに、そのユーザーがフェデレーションまたはパスワード同期されている場合は、ローカル AD で管理者が選択するパスワードも設定されます。  現時点で、これは Office の管理ポータルではサポートされていません。
* **オンプレミスの AD パスワード ポリシーを強制します。**  ユーザーが自分のパスワードをリセットする場合、パスワードがオンプレミスの AD ポリシーに準拠していることを確認してから、そのディレクトリにコミットします。  このポリシーには、履歴、複雑さ、年齢、パスワード フィルター、ローカル AD で定義された他のパスワード制限が含まれます。
* **受信のファイアウォール規則を要求しません。**  パスワード ライトバックは、基礎となる通信チャネルとして Azure Service Bus Relay を使用します。つまり、この機能を使用するためにファイアウォールで受信ポートを開く必要はありません、
* **オンプレミスの Active Directory の保護グループ内に存在するユーザー アカウントではサポートされません。** 保護グループの詳細については、「 [Protected Accounts and Groups in Active Directory (Active Directory の保護アカウントとグループ)](https://technet.microsoft.com/library/dn535499.aspx)。

### <a name="how-password-writeback-works"></a>パスワード ライトバックのしくみ
パスワード ライトバックには次の&3; つの主要コンポーネントがあります。

* パスワード リセットのクラウド サービス (これは Azure AD のパスワード変更ページにも統合されています)
* テナント固有の Azure Service Bus Relay
* オンプレミスのパスワード リセット エンドポイント

これらは、以下の図で説明するように統合されています。

  ![][001]

フェデレーション ユーザーまたはパスワード ハッシュ同期されたユーザーがクラウドで自分のパスワードをリセットまたは変更する場合は、次の処理が行われます。

1. ユーザーのパスワードの種類が確認されます。  パスワードがオンプレミスで管理されていることが確認されると、ライトバック サービスが起動し、実行します。  オンプレミスで管理されている場合は続行しますが、管理されていない場合は、パスワードをリセットできないことがユーザーに通知されます。
2. 次に、ユーザーが適切な認証ゲートを通過すると、パスワードのリセット画面が表示されます。
3. ユーザーは新しいパスワードを選択して確認します。
4. 送信をクリックすると、プレーンテキスト パスワードがライトバックのセットアップ プロセス時に作成された対称キーを使用して暗号化されます。
5. パスワードは暗号化された後、ペイロードに含められ、HTTPS チャネル経由でテナント固有の Service Bus Relay (ライトバックのセットアップ中にも設定される) に送信されます。  このリレーは、オンプレミスのインストールのみを認識するランダムに生成されたパスワードによって保護されます。
6. メッセージが Service Bus に到達すると、パスワード リセット エンドポイントが自動的にアクティブになり、保留中のリセット要求があるかどうかを確認します。
7. サービスは、クラウドのアンカー属性を使用して該当するユーザーを探します。  この検索が正常に動作するには、ユーザー オブジェクトが AD コネクタ スペースに存在し、これが対応する MV オブジェクトにリンクし、さらにこれが対応する AAD コネクタ オブジェクトにリンクする必要があります。 最後に、同期によってこのユーザー アカウントを検索するには、AD コネクタ オブジェクトから MV へのリンクに `Microsoft.InfromADUserAccountEnabled.xxx` 同期ルールが含まれている必要があります。  これが必要なのは、クラウドから呼び出される場合に、同期エンジンは cloudAnchor 属性を使用して AAD コネクタ スペース オブジェクトを検索し、MV オブジェクトのリンクに戻り、次に AD オブジェクトのリンクに戻るためです。 同じユーザーに対して複数の AD オブジェクト (マルチ フォレスト) がある可能性があるため、同期エンジンは `Microsoft.InfromADUserAccountEnabled.xxx` のリンクに依存して正しいユーザー アカウントを選択します。
8. ユーザー アカウントが見つかると、適切な AD フォレスト内で直接パスワードのリセットを試みます。
9. パスワードの設定操作に成功すると、ユーザーにパスワードが変更されたことが通知され、そのまま続行されます。
10. パスワードの設定操作に失敗した場合は、エラーが返され、やり直す必要があります。  サービスがダウンした、選択したパスワードが組織のポリシーを満たしていない、ローカル AD でユーザーが見つからないなど、さまざまな原因で操作に失敗する可能性があります。  多くの場合、特定のメッセージが表示され、問題解決の手段がユーザーに通知されます。

### <a name="scenarios-supported-for-password-writeback"></a>パスワード ライトバックでサポートされているシナリオ
次の表では、同期機能のバージョンでサポートされているシナリオについて説明します。  通常、パスワード ライトバックを使用する場合は、最新バージョンの [Azure AD Connect](connect/active-directory-aadconnect.md#install-azure-ad-connect) をインストールすることを強くお勧めします。

  ![][002]

### <a name="password-writeback-security-model"></a>パスワード ライトバックのセキュリティ モデル
パスワード ライトバックは、安全性と信頼性の高いサービスです。  ユーザーの情報を確実に保護するために、以下で説明する 4 層のセキュリティ モデルを実現しています。

* **テナント固有の Service Bus Relay** – サービスを設定すると、マイクロソフトでもアクセスできない、ランダムに生成された強力なパスワードで保護されたテナント固有の Service Bus Relay が設定されます。
* **ロックダウンされ、暗号強度の高いパスワード暗号化キー** – Service Bus Relay が作成されると、強力な非対称キー ペアが作成され、ネットワーク経由でパスワードが渡されるときに暗号化に使用されます。  このキーは、クラウド内の会社のシークレット ストアのみに存在し、厳重にロックダウンされ、ディレクトリ内のパスワードと同様に監査されます。
* **業界標準の TLS** – クラウド内でパスワードのリセットや変更操作が行われる場合は、プレーン テキスト パスワードが取得され、公開キーを使用して暗号化されます。  次に、これを HTTPS メッセージにして、マイクロソフトの SSL 証明書を使用して暗号化されたチャネルを介して Service Bus Relay に送信されます。  このメッセージが Service Bus に到着すると、オンプレミスのエージェントがアクティブになり、前に生成された強力なパスワードを使用して Service Bus に認証され、暗号化されたメッセージを取得し、生成された秘密キーを使用して復号化され、AD DS SetPassword API を使用してパスワードの設定を試みます。  この手順に従うと、クラウドで AD オンプレミスのパスワード ポリシー (複雑さ、年齢、履歴、フィルターなど) を適用できます。
* **メッセージの有効期限ポリシー** - 最後に、何らかの理由でオンプレミス サービスが停止しているために Service Bus でメッセージが表示される場合は、セキュリティをさらに強化するために数分後にタイムアウトになり、削除されます。

### <a name="password-writeback-bandwidth-usage"></a>パスワード ライトバックの帯域幅の使用

パスワード ライトバックは、次の状況でのみ、要求をオンプレミスのエージェントに戻す、きわめて低い帯域幅のサービスです。

1. Azure AD Connect を通じて機能を有効または無効にしたときに&2; つのメッセージが送信されます。
2. サービスのハートビートとして 5 分おきに 1 回 1 つのメッセージが、サービスを実行している間中、送信されます。
3. 新しいパスワードが送信されるたびに&2; つのメッセージが送信されます。1 つは要求として操作を実行するメッセージです。後続のメッセージには、操作の結果が含まれます。 これらのメッセージは、次の状況で送信されます。
4. ユーザーのセルフサービスのパスワードのリセット時に新しいパスワードが送信された場合
5. ユーザーのパスワード変更操作時に新しいパスワードが送信された場合
6. 管理者によるユーザー パスワードのリセット時に新しいパスワードが送信された場合 (Azure 管理ポータルからのみ)

#### <a name="message-size-and-bandwidth-considerations"></a>メッセージ サイズと帯域幅に関する考慮事項

上記で説明したメッセージの各サイズは、通常 1 kb 未満です。つまり、負荷が大きい場合でも、パスワード ライトバック サービス自体で 1 秒間に最大でも数キロビットの帯域幅しか消費されないことを意味します。 各メッセージは、パスワードの更新操作で必要になった場合にのみリアルタイムで送信されるため、またメッセージのサイズが非常に小さいため、ライトバック機能の帯域幅は実質的に非常に小さく、実際に測定可能な影響を及ぼすには至りません。

## <a name="how-does-the-password-reset-portal-work"></a>パスワード リセット ポータルのしくみ
ユーザーがパスワード リセット ポータルに移動すると、そのユーザー アカウントは有効か、そのユーザーはどの組織に属しているか、そのユーザーのパスワードはどこに管理されているか、ユーザーは機能を使用するライセンスが付与されているかを判断するためのワークフローが開始されます。  パスワード リセット ページの背後にあるロジックの詳細については、次の手順をお読みください。

1. ユーザーは、"あなたのアカウントにアクセスできません" のリンクをクリックするか、直接 [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com)に移動します。
2. ユーザーは、ユーザー ID を入力し、CAPTCHA を渡します。
3. Azure AD は、次の手順を行うことで、ユーザーがこの機能を使用できるかどうかを確認します。
   * ユーザーがこの機能を有効にしていて、Azure AD ライセンスが割り当てられていることを確認します。
     * ユーザーがこの機能を有効にしていない、またはライセンスが割り当てられていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
   * ユーザーは、管理者ポリシーに従って、自分のアカウントに正しいチャレンジ データが定義されていることを確認します。
     * ポリシーが&1; つのチャレンジのみを要求する場合は、管理者ポリシーで有効になっている&1; つ以上のチャレンジに対して、ユーザーが適切なデータを定義していることが確認されます。
       * ユーザーが構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
     * ポリシーが&2; つのチャレンジを要求する場合は、管理者ポリシーで有効になっている&2; つ以上のチャレンジに対して、ユーザーが適切なデータを定義していることが確認されます。
       * ユーザーが構成されていない場合、そのユーザーは管理者に連絡してパスワードをリセットするように求められます。
   * ユーザーのパスワード (フェデレーションまたはパスワード ハッシュ同期された) がオンプレミスで管理されているかどうかを確認します。
     * ライトバックがデプロイされていて、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは自分のパスワードを認証してリセットできます。
     * ライトバックがデプロイされておらず、ユーザーのパスワードがオンプレミスで管理されている場合、ユーザーは管理者に連絡してパスワードをリセットするように求められます。
4. ユーザーが自分のパスワードを正常にリセットできると判断された場合は、リセット プロセスの説明がユーザーに示されます。

パスワード ライトバックのデプロイ方法については、「[概要: Azure AD でのパスワード管理](active-directory-passwords-getting-started.md)」をご覧ください。

### <a name="what-data-is-used-by-password-reset"></a>パスワードのリセットで使用されるデータ
次の表は、このデータがパスワードのリセット時に使用される場所と方法を示し、組織に適した認証オプションを決定する際に役立つように作られています。 この表では、このデータを検証しない入力パスからユーザーに代わってデータを指定する場合の形式の要件も示しています。

> [!NOTE]
> 会社電話は、現在ユーザーはディレクトリでこのプロパティを編集できないため、登録ポータルに表示されません。
>
>

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>連絡方法</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Azure Active Directory のデータ要素</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>使用される場所 / 設定可能な場所</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>形式の要件</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>会社電話</p>
            </td>
            <td>
              <p>PhoneNumber</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -PhoneNumber "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>設定可能な場所: </p>
              <p>PhoneNumber は、PowerShell、DirSync、Microsoft Azure 管理ポータル、Office の管理ポータルから設定可能です</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例: +1 1234567890)</p>
              <ul>
                <li class="unordered">
国コードを提供する必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
市外局番を指定する必要があります (該当する場合)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードの前に + 記号を指定する必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードと残りの番号の間にスペースを入れる必要があります<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
内線番号はサポートされません。内線番号が指定されている場合は、通話をディスパッチする前に電話番号から内線番号が削除されます。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>携帯電話</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>または</p>
              <p>MobilePhone</p>
              <p>(認証用電話はデータが存在する場合に使用されます。それ以外の場合は、携帯電話フィールドが使用されます)。</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -MobilePhone "+1 1234567890x1234"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル</p>
              <p>設定可能な場所:  </p>
              <p>AuthenticationPhone は、パスワード リセット登録ポータルまたは MFA 登録ポータルから設定可能です。</p>
              <p>MobilePhone は、PowerShell、DirSync、Microsoft Azure 管理ポータル、Office の管理ポータルから設定可能です</p>
            </td>
            <td>
              <p>+ccc xxxyyyzzzz (例: +1 1234567890)</p>
              <ul>
                <li class="unordered">
国コードを提供する必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
市外局番を指定する必要があります (該当する場合)。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードの前に + 記号を指定する必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
国コードと残りの番号の間にスペースを入れる必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
内線番号はサポートされません。内線番号が指定されている場合は、通話をディスパッチするときに無視されます。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>連絡用メール アドレス</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>または</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(認証用電子メールはデータが存在する場合に使用されます。それ以外の場合は、連絡用メール アドレス フィールドが使用されます)</p>
              <p>注: 連絡用メール アドレス フィールドは、ディレクトリ内の文字列の配列として指定されます。  この配列内の最初のエントリが使用されます。</p>
              <p>例: Set-MsolUser -UserPrincipalName JWarner@contoso.com -AlternateEmailAddresses "email@live.com"</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル</p>
              <p>設定可能な場所:  </p>
              <p>AuthenticationEmail は、パスワード リセット登録ポータルまたは MFA 登録ポータルから設定可能です。</p>
              <p>AlternateEmail は、PowerShell、Microsoft Azure 管理ポータル、Office の管理ポータルから設定可能です。</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a> または甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
電子メールは標準的な形式に従う必要があります。<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Unicode の電子メールがサポートされています。<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>セキュリティの質問と回答</p>
            </td>
            <td>
              <p>ディレクトリ内で直接変更することはできません。</p>
            </td>
            <td>
              <p>使用される場所: </p>
              <p>パスワード リセット ポータル</p>
              <p>登録ポータル </p>
              <p>設定可能な場所:  </p>
              <p>セキュリティの質問は、Microsoft Azure 管理ポータルからのみ設定可能です。</p>
              <p>特定のユーザーからのセキュリティに関する質問への回答は、登録ポータルからのみ設定可能です。</p>
            </td>
            <td>
              <p>セキュリティの質問は、最小 3 文字 ～ 最大 200 文字で指定します。</p>
              <p>回答は、最小 3 文字 ～ 最大 40 文字で指定します。</p>
            </td>
          </tr>
        </tbody></table>

### <a name="how-to-access-password-reset-data-for-your-users"></a>ユーザーのパスワード リセット データにアクセスする方法
#### <a name="data-settable-via-synchronization"></a>同期によって設定できるデータ
次のフィールドは、オンプレミスから同期することができます。

* 携帯電話
* 会社電話

#### <a name="data-accessible-with-azure-ad-powershell"></a>Azure AD PowerShell を使用してアクセスできるデータ
次のフィールドは、Azure AD PowerShell と Graph API を使用してアクセスすることができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話
* 認証用電話
* 認証用メール

#### <a name="data-settable-with-registration-ui-only"></a>登録 UI でのみ設定できるデータ
次のフィールドは、SSPR 登録 UI でのみアクセスできます (https://aka.ms/ssprsetup):

* セキュリティの質問と回答

#### <a name="what-happens-when-a-user-registers"></a>ユーザーの登録時に発生すること
ユーザーが登録するとき、登録ページには **常に** 次のフィールドが設定されます。

* 認証用電話
* 認証用メール
* セキュリティの質問と回答

**[携帯電話]** または **[連絡用メール アドレス]** に値が指定されている場合、ユーザーはそれらを使用してすぐにパスワードをリセットすることができます。これはユーザーがサービスに登録していない場合でも実行できます。  さらに、これらの値は、ユーザーが初めて登録するときに表示され、ユーザーは必要に応じてそれらを変更することができます。  ただし、ユーザーが正常に登録された後、これらの値は、それぞれ **[認証用電話]** フィールドと **[認証用メール]** フィールドの固定値になります。

これは、SSPR を使用できるように多数のユーザーをブロック解除すると同時に、ユーザーが登録プロセス中にこの情報を確認できるようにする便利な方法として利用できます。

#### <a name="setting-password-reset-data-with-powershell"></a>PowerShell を使用したパスワード リセット データの設定
次のフィールドの値は、Azure AD PowerShell を使用して設定することができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話

操作を開始する前に、 [Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。  インストールした後、次の手順に従って各フィールドを構成できます。

##### <a name="alternate-email"></a>連絡用メール アドレス
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

##### <a name="mobile-phone"></a>携帯電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

##### <a name="office-phone"></a>会社電話
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

#### <a name="reading-password-reset-data-with-powershell"></a>PowerShell を使用したパスワード リセット データの読み取り
次のフィールドの値は、Azure AD PowerShell を使用して読み取ることができます。

* 連絡用メール アドレス
* 携帯電話
* 会社電話
* 認証用電話
* 認証用メール

操作を開始する前に、 [Azure AD PowerShell モジュールをダウンロードしてインストールする](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)必要があります。  インストールした後、次の手順に従って各フィールドを構成できます。

##### <a name="alternate-email"></a>連絡用メール アドレス
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

##### <a name="mobile-phone"></a>携帯電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

##### <a name="office-phone"></a>会社電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

##### <a name="authentication-phone"></a>認証用電話
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

##### <a name="authentication-email"></a>認証用メール
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="next-steps"></a>次のステップ
Azure AD のパスワードのリセットに関するすべてのドキュメント ページへのリンクを以下に示します。

* **サインインに問題がありますか?** その場合は、[自分のパスワードを変更してリセットする方法をここから参照してください](active-directory-passwords-update-your-own-password.md)にお進みください。
* [**しくみ**](active-directory-passwords-how-it-works.md) - サービスの&6; つの異なるコンポーネントとそれぞれの機能について説明します。
* [**概要**](active-directory-passwords-getting-started.md) - ユーザーによるクラウドまたはオンプレミスのパスワードのリセットと変更を許可する方法について説明します。
* [**カスタマイズ**](active-directory-passwords-customize.md) - 組織のニーズに合わせてサービスの外観と動作をカスタマイズする方法について説明します。
* [**ベスト プラクティス**](active-directory-passwords-best-practices.md) - 組織内でのパスワードの迅速なデプロイと効果的な管理方法について説明します。
* [**洞察を得る**](active-directory-passwords-get-insights.md) - 統合レポート機能について説明します。
* [**FAQ**](active-directory-passwords-faq.md) -よく寄せられる質問の回答を得ます。
* [**トラブルシューティング**](active-directory-passwords-troubleshoot.md) - サービスに関する問題を迅速にトラブルシューティングする方法について説明します。

[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"

