---
title: "Azure AD SSPR とパスワード ライトバック | Microsoft Docs"
description: "Azure AD と Azure AD Connect を使って、オンプレミスのディレクトリにパスワードを書き戻します"
services: active-directory
keywords: "Active Directory パスワード管理, パスワード管理, Azure AD セルフサービスによるパスワードのリセット"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 12/06/2017
ms.author: joflore
ms.custom: it-pro
ms.openlocfilehash: 6dfc3246b210b382665eeef2d638945c91d5b62f
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/11/2017
---
# <a name="password-writeback-overview"></a>パスワード ライトバックの概要

パスワード ライトバックを使うと、オンプレミスの Active Directory にパスワードを書き戻すように Azure Active Directory (Azure AD) を構成できます。 パスワード ライトバックにより、複雑なオンプレミスのセルフサービスによるパスワードのリセット (SSPR) ソリューションを設定して管理する必要がなくなります。ユーザーはどこにいても、便利なクラウドベースの方法で自分のオンプレミスのパスワードをリセットできます。 パスワード ライトバックは、[Azure Active Directory Connect](./connect/active-directory-aadconnect.md) のコンポーネントです。Premium の [Azure Active Directory のエディション](active-directory-whatis.md)の現在のサブスクライバーは、この機能を有効にして使うことができます。

パスワード ライトバックには、次の機能が用意されています。

* **ゼロ遅延フィードバックの提供**: パスワード ライトバックは同期操作です。 ユーザーのパスワードがポリシーに合わなかった場合や、何らかの理由でリセットまたは変更できなかった場合は、すぐにユーザーに通知します。
* **Active Directory フェデレーション サービス (AD FS) または他のフェデレーション技術を使っているユーザーのパスワード リセットのサポート**: パスワード ライトバックを使うと、フェデレーション ユーザー アカウントと Azure AD テナントが同期している限り、クラウドからオンプレミスの Active Directory パスワードを管理できます。
* **[パスワード ハッシュ同期](./connect/active-directory-aadconnectsync-implement-password-synchronization.md)を使っているユーザーのパスワード リセットのサポート**: パスワード リセット サービスは、同期されているユーザー アカウントでパスワード ハッシュ同期が有効になっていることを検出すると、このアカウントのオンプレミスのパスワードとクラウドのパスワードの両方を同時にリセットします。
* **アクセス パネルと Office 365 からのパスワード変更のサポート**: フェデレーション ユーザーかパスワード同期されたユーザーが有効期限切れ、または有効期限切れでないパスワードを変更すると、これらのパスワードはローカル Active Directory 環境に書き戻されます。
* **管理者が Azure Portal でパスワードをリセットするときのパスワード ライトバックのサポート**: 管理者が [Azure Portal](https://portal.azure.com) でユーザーのパスワードをリセットするときに、そのユーザーがフェデレーションまたはパスワード同期されている場合は、ローカル Active Directory で管理者が選択するパスワードも設定されます。 現在、この機能は Office 管理ポータルではサポートされていません。
* **オンプレミスの Active Directory パスワード ポリシーの強制**: ユーザーが自分のパスワードをリセットするとき、パスワードがオンプレミスの Active Directory ポリシーに準拠していることを確認してから、そのディレクトリにコミットします。 この確認には、履歴、複雑さ、年齢、パスワード フィルター、ローカル Active Directory で定義された他のパスワード制限のチェックが含まれます。
* **受信ファイアウォール規則は不要**: パスワード ライトバックは、基盤の通信チャネルとして Azure Service Bus リレーを使います。 この機能を動作させるために、ファイアウォールで受信ポートを開く必要はありません。
* **オンプレミスの Active Directory の保護されたグループ内に存在するユーザー アカウントはサポート対象外**: 保護されたグループについて詳しくは、「[Protected accounts and groups in Active Directory](https://technet.microsoft.com/library/dn535499.aspx)」(Active Directory の保護されたアカウントとグループ) をご覧ください。

## <a name="how-password-writeback-works"></a>パスワード ライトバックのしくみ

フェデレーション ユーザーまたはパスワード ハッシュ同期されたユーザーがクラウドで自分のパスワードをリセットまたは変更する場合は、次の処理が行われます。

1. ユーザーのパスワードの種類が確認されます。 パスワードがオンプレミスで管理されていることが確認された場合、次のことが行われます。
   * ライトバック サービスが稼働しているかどうかをチェックします。 稼働している場合は、ユーザーが処理を続行できるようにします。
   * ライトバック サービスが稼働していない場合は、パスワードを今すぐリセットすることはできないことをユーザーに伝えます。
2. 次に、ユーザーが適切な認証ゲートを通過すると、**[パスワードのリセット]** ページが表示されます。
3. ユーザーは新しいパスワードを選択して確認します。
4. ユーザーが **[送信]** を選ぶと、プレーンテキスト パスワードがライトバックのセットアップ プロセス時に作成された対称キーを使って暗号化されます。
5. パスワードは暗号化された後、ペイロードに含められ、HTTPS チャネル経由でテナント固有の Service Bus Relay (ライトバックのセットアップ中にも設定される) に送信されます。 このリレーは、オンプレミスのインストールのみを認識するランダムに生成されたパスワードによって保護されます。
6. メッセージが Service Bus に到達した後、パスワード リセット エンドポイントが自動的にアクティブになり、保留中のリセット要求があるかどうかが確認されます。
7. サービスは、クラウドのアンカー属性を使ってユーザーを探します。 この検索が成功するには:

    * Active Directory コネクタ スペースにユーザー オブジェクトが存在している必要があります。
    * ユーザー オブジェクトが対応するメタバース (MV) オブジェクトにリンクされている必要があります。
    * ユーザー オブジェクトが対応する Azure Active Directory コネクタ オブジェクトにリンクされている必要があります。
    * Active Directory コネクタ オブジェクトから MC へのリンク上に、同期ルール `Microsoft.InfromADUserAccountEnabled.xxx` が存在する必要があります。 <br> <br>
    クラウドからの呼び出しがあると、同期エンジンは **cloudAnchor** 属性を使って Azure Active Directory コネクタ スペース オブジェクトを検索します。 その後、リンクをたどって MV オブジェクトに戻り、さらにリンクをたどって Active Directory オブジェクトに戻ります。 同じユーザーに対して複数の Active Directory オブジェクト (マルチ フォレスト) がある可能性があるため、同期エンジンは `Microsoft.InfromADUserAccountEnabled.xxx` のリンクに基づいて正しいユーザー アカウントを選びます。

    > [!Note]
    > このロジックの結果、パスワード ライトバックが動作するには、Azure AD Connect がプライマリ ドメイン コントローラー (PDC) エミュレーターと通信できる必要があります。 これを手動で有効にする必要がある場合は、PDC エミュレーターに Azure AD Connect を接続できます。 Active Directory 同期コネクタの **[プロパティ]** を右クリックし、**[configure directory partitions]\(ディレクトリ パーティションの構成\)** を選びます。 そこで、**[domain controller connection settings]\(ドメイン コントローラーの接続の設定\)** セクションを探して、**[only use preferred domain controllers]\(優先ドメイン コントローラーのみを使用する\)** チェック ボックスをオンにします。 優先ドメイン コントローラーが PDC エミュレーターではない場合、Azure AD Connect はパスワード ライトバックのために PDC へのアクセスを試行します。

8. ユーザー アカウントが見つかると、適切な Active Directory フォレスト内で直接パスワードのリセットを試みます。
9. パスワードの設定操作に成功すると、ユーザーにパスワードが変更されたことが通知されます。
    > [!NOTE]
    > ユーザーのパスワードがパスワード同期を使って Azure AD に同期される場合、オンプレミスのパスワード ポリシーが、クラウドのパスワード ポリシーと比べて厳密ではない可能性があります。 その場合、オンプレミスのポリシーはどのようなものであれ適用され、代わりにパスワード ハッシュ同期を使ってそのパスワードのハッシュが同期されます。 このポリシーにより、パスワード同期やフェデレーションによるシングル サインオンを提供していたとしても、クラウドでオンプレミスのポリシーが確実に適用されます。

10. パスワードの設定操作に失敗した場合は、エラーが返され、ユーザーは操作をもう一度試します。 次の理由により、操作が失敗することがあります。
    * サービスがダウンしていた。
    * 選択したパスワードが組織のポリシーを満たしていなかった。
    * ローカル Active Directory でユーザーが見つからなかった。

    多くの場合、特定のメッセージが表示され、問題解決の手段がユーザーに通知されます。

## <a name="configure-password-writeback"></a>パスワード ライトバックを構成する

パスワード ライトバックを使用する場合、マイクロソフトでは [Azure AD Connect](./connect/active-directory-aadconnect-get-started-express.md) の自動更新機能を使用することをお勧めしています。

DirSync と Azure AD Sync は、パスワード ライトバックを有効にする方法としてサポートされなくなりました。 切り替えについて詳しくは、「[DirSync と Azure AD Sync からのアップグレード](connect/active-directory-aadconnect-dirsync-deprecated.md)」をご覧ください。

以下の手順は、環境で[簡単](./connect/active-directory-aadconnect-get-started-express.md)設定または[カスタム](./connect/active-directory-aadconnect-get-started-custom.md)設定を使って、Azure AD Connect を既に構成してあることが前提になっています。

1. パスワード ライトバックを構成し、有効化するには、Azure AD Connect サーバーにサインインして **Azure AD Connect** 構成ウィザードを開始します。
2. **[ようこそ]** ページで **[構成]** を選びます。
3. **[追加のタスク]** ページで **[同期オプションのカスタマイズ]** を選んで、**[次へ]** を選びます。
4. **[Azure AD に接続]** ページで全体管理者の資格情報を入力し、**[次へ]** を選びます。
5. **[Connect ディレクトリ]** ページおよび **[ドメイン/OU のフィルタリング]** ページで、**[次へ]** を選びます。
6. **[オプション機能]** ページで **[パスワード ライトバック]** の横にあるチェック ボックスをオンにし、**[次へ]** を選びます。
   ![Azure AD Connect でパスワード ライトバックを有効にする][Writeback]
7. **[構成の準備完了]** ページで **[構成]** を選び、処理が完了するまで待ちます。
8. 構成の完了が表示されたら、**[終了]** を選びます。

パスワード ライトバックに関連する一般的なトラブルシューティングのタスクについては、トラブルシューティング記事の「[パスワード ライトバックのトラブルシューティング](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback)」セクションをご覧ください。

## <a name="active-directory-permissions"></a>Active Directory のアクセス許可

SSPR の範囲内に入るには、Azure AD Connect ユーティリティで指定されているアカウントに次の項目が設定されている必要があります。

* **パスワードのリセット** 
* **[パスワードの変更]** 
* `lockoutTime` に対する**書き込みアクセス許可**  
* `pwdLastSet` に対する**書き込みアクセス許可**
* 次のいずれかに対する**拡張権限**:
   * そのフォレスト内の*各ドメイン*のルート オブジェクト
   * SSPR の範囲内にしたいユーザー組織単位 (OU)

説明されているアカウントがどのアカウントのことか明らかでない場合は、Azure Active Directory Connect の構成 UI を開き、**[現在の構成を表示する]** オプションを選びます。 アクセス許可を追加する必要があるアカウントが、**[同期されたディレクトリ]** の下に表示されます。

これらのアクセス許可を設定した場合、各フォレストの MA サービス アカウントが、そのフォレスト内のユーザー アカウントの代わりに、パスワードを管理できるようになります。 

> [!IMPORTANT]
> これらのアクセス許可を割り当てないと、ライトバックが正常に構成されているように思われる場合でも、クラウドからオンプレミスのパスワードを管理しようとするとエラーが発生します。
>

> [!NOTE]
> ディレクトリ内のすべてのオブジェクトにこれらのアクセス許可をレプリケートするには、最大 1 時間かそれ以上かかることがあります。
>

パスワード ライトバックを行うための適切なアクセス許可を設定するには、以下の手順を完了します。

1. 適切なドメインの管理アクセス許可を持つアカウントで [Active Directory ユーザーとコンピューター] を開きます。
2. **[表示]** メニューで、**[高度な機能]** がオンになっていることを確認します。
3. 左側のパネルで、ドメインのルートを表すオブジェクトを右クリックし、**[プロパティ]** > **[セキュリティ]** > **[詳細設定]** の順に選びます。
4. **[アクセス許可]** タブで **[追加]** を選びます。
5. (Azure AD Connect のセットアップから) アクセス許可を適用するアカウントを選びます。
6. **[適用対象]** ドロップダウン ボックスの一覧で、**[Descendent user]\(下位ユーザー\)** オブジェクトを選びます。
7. **[アクセス許可]** で次のチェック ボックスをオンにします。
    * **パスワードのリセット**
    * **[パスワードの変更]**
    * **Write lockoutTime**
    * **Write pwdLastSet**
8. **[適用]/[OK]** を選んで変更を適用し、開いているすべてのダイアログ ボックスを終了します。

## <a name="licensing-requirements-for-password-writeback"></a>パスワード ライトバックに必要なライセンス

ライセンスについて詳しくは、「[パスワード ライトバックで必要なライセンス](active-directory-passwords-licensing.md#licenses-required-for-password-writeback)」または次のサイトをご覧ください。

* [Azure Active Directory の価格](https://azure.microsoft.com/pricing/details/active-directory/)
* [Enterprise Mobility + Security](https://www.microsoft.com/cloud-platform/enterprise-mobility-security)
* [Microsoft 365 Enterprise](https://www.microsoft.com/secure-productive-enterprise/default.aspx)

### <a name="on-premises-authentication-modes-that-are-supported-for-password-writeback"></a>パスワード ライトバックでサポートされるオンプレミスの認証モード

パスワード ライトバックは、次のユーザー パスワードの種類をサポートします。

* **クラウド専用ユーザー**: オンプレミスのパスワードがないため、この状況ではパスワード ライトバックは適用されません。
* **パスワード同期ユーザー**: パスワード ライトバックがサポートされます。
* **フェデレーション ユーザー**: パスワード ライトバックがサポートされます。
* **パススルー認証ユーザー**: パスワード ライトバックがサポートされます。

### <a name="user-and-admin-operations-that-are-supported-for-password-writeback"></a>パスワード ライトバックでサポートされるユーザーと管理者の操作

パスワードは次の状況で書き戻されます。

* **サポートされるエンドユーザーの操作**
  * エンド ユーザーによる自発的なパスワード変更
  * エンドユーザーによる強制的なパスワード変更 (パスワードの期限切れなど)
  * エンドユーザーにより、[パスワード リセット ポータル](https://passwordreset.microsoftonline.com)から実行されたセルフサービスによるパスワードのリセット
* **サポートされる管理者の操作**
  * 管理者による自発的なパスワード変更
  * 管理者による強制的なパスワード変更 (パスワードの期限切れなど)
  * 管理者により[パスワード リセット ポータル](https://passwordreset.microsoftonline.com)から実行された管理者によるセルフサービスによるパスワードのリセット
  * [Azure Portal](https://portal.azure.com) から管理者が開始したエンドユーザーのパスワードのリセット

### <a name="user-and-admin-operations-that-are-not-supported-for-password-writeback"></a>パスワード ライトバックでサポートされないユーザーと管理者の操作

パスワードの書き戻しは、次の状況では*実行されません*。

* **サポートされないエンドユーザーの操作**
  * PowerShell バージョン 1、バージョン 2、または Azure AD Graph API を使った、エンドユーザーによるパスワードのリセット
* **サポートされない管理者の操作**
  * [Office 管理ポータル](https://portal.office.com)から管理者が開始したエンドユーザーのパスワードのリセット
  * PowerShell バージョン 1、バージョン 2、または Azure AD Graph API から管理者が開始したエンド ユーザーのパスワードのリセット

マイクロソフトでは、これらの制限を取り除くための作業を続けていますが、共有できる予定はまだありません。

## <a name="password-writeback-security-model"></a>パスワード ライトバックのセキュリティ モデル

パスワード ライトバックは、安全性の高いサービスです。 ユーザーの情報を確実に保護するために、次の 4 層のセキュリティ モデルを実現しています。

* **テナント固有の Service Bus Relay**
  * サービスを設定すると、Microsoft でもアクセスできない、ランダムに生成された強力なパスワードで保護されたテナント固有の Service Bus リレーが設定されます。
* **ロックダウンされ、暗号強度の高いパスワード暗号化キー**
  * Service Bus リレーが作成されると、強力な非対称キー ペアが作成され、ネットワーク経由でパスワードが渡されるときに暗号化に使用されます。 このキーは、クラウド内の会社のシークレット ストアのみに存在し、厳重にロックダウンされ、ディレクトリ内の他のパスワードと同様に監査されます。
* **業界標準のトランスポート層セキュリティ (TLS)**
  1. クラウド内でパスワードのリセットや変更操作が行われる場合は、プレーン テキスト パスワードが取得され、公開キーを使用して暗号化されます。
  2. これを HTTPS メッセージに配置して、Microsoft の SSL 証明書を使って暗号化されたチャネルを介して Service Bus リレーに送信されます。
  3. Service Bus にメッセージが到着すると、オンプレミスのエージェントが起動され、以前に生成された強力なパスワードを使用して Service Bus に認証します。
  4. オンプレミスのエージェントは、暗号化されたメッセージを取得し、マイクロソフトが生成した秘密キーを使用して復号化します。
  5. オンプレミスのエージェントは、AD DS SetPassword API を使用して、パスワードの設定を試行します。 この手順に従うと、クラウドで Active Directory のオンプレミスのパスワード ポリシー (複雑さ、年齢、履歴、フィルターなど) を適用できます。
* **メッセージの有効期限ポリシー** 
  * オンプレミスのサービスがダウンしているためメッセージが Service Bus に残っている場合はタイムアウトになり、数分後に削除されます。 タイムアウトとメッセージの削除により、セキュリティがさらに強化されます。

### <a name="password-writeback-encryption-details"></a>パスワード ライトバックの暗号化の詳細

ユーザーがパスワードのリセットを送信した後、リセット要求はオンプレミスの環境に届く前に、いくつかの暗号化ステップを通過します。 これらの暗号化ステップにより、サービスの最大限の信頼性とセキュリティが保証されます。 暗号化ステップの説明を次に示します。

* **ステップ 1: 2048 ビット RSA キーによるパスワードの暗号化**: ユーザーが、オンプレミスに書き戻すパスワードを送信すると、送信されたパスワードそのものが 2048 ビット RSA キーを使って暗号化されます。
* **ステップ 2: AES-GCM によるパッケージ レベルの暗号化**: AES-GCM を使って、パッケージ全体 (パスワードと必要なメタデータ) が暗号化されます。 この暗号化により、ServiceBus チャネルに直接アクセスできる人物による内容の表示または改ざんを防止します。
* **ステップ 3: すべての通信が TLS/SSL 経由で行われる**: ServiceBus でのすべての通信は、SSL/TLS チャネルで実行されます。 この暗号化により、権限がないサード パーティに対してコンテンツが保護されます。
* **6 か月ごとの自動キー ロールオーバー**: 6 か月ごと、または Azure AD Connect でパスワード ライトバックが無効にされてから再度有効にされるたび。 自動的にすべてのキーがロールオーバーされて、サービスの最大限のセキュリティと安全性が確保されます。

### <a name="password-writeback-bandwidth-usage"></a>パスワード ライトバックの帯域幅の使用

パスワード ライトバックは帯域幅の低いサービスで、次の状況でのみ要求をオンプレミスのエージェントに戻します。

* Azure AD Connect を通じて機能が有効または無効にされると、2 つのメッセージが送信されます。
* サービスのハートビートとして 5 分おきに 1 回 1 つのメッセージが、サービスを実行している間中、送信されます。
* 新しいパスワードが送信されるたびに 2 つのメッセージが送信されます。
    * 1 番目のメッセージは操作の実行を要求します。
    * 2 番目のメッセージには操作の結果が含まれ、次の状況で送信されます。
        * ユーザーのセルフサービスのパスワードのリセット時に新しいパスワードが送信された場合
        * ユーザーのパスワード変更操作時に新しいパスワードが送信された場合
        * 管理者によるユーザー パスワードのリセット時に新しいパスワードが送信された場合 (Azure 管理ポータルからのみ)

#### <a name="message-size-and-bandwidth-considerations"></a>メッセージ サイズと帯域幅に関する考慮事項

前に説明した各メッセージのサイズは、通常 1 KB 未満です。 負荷が大きい場合でも、パスワード ライトバック サービス自体で 1 秒間に数キロビットの帯域幅しか消費されないことを意味します。 各メッセージは、パスワードの更新操作で必要になった場合にのみリアルタイムで送信されるため、またメッセージのサイズが非常に小さいため、ライトバック機能の帯域幅は非常に小さく、測定可能な影響を及ぼすには至りません。

## <a name="next-steps"></a>次のステップ

* [SSPR のロールアウトを適切に完了する方法。](active-directory-passwords-best-practices.md)
* [パスワードのリセットと変更。](active-directory-passwords-update-your-own-password.md)
* [セルフサービスによるパスワード リセットの登録。](active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](active-directory-passwords-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](active-directory-passwords-data.md)
* [ユーザーが使用できる認証方法。](active-directory-passwords-how-it-works.md#authentication-methods)
* [SSPR のポリシー オプション。](active-directory-passwords-policy.md)
* [SSPR でアクティビティをレポートする方法。](active-directory-passwords-reporting.md)
* [SSPR のすべてのオプションとその意味。](active-directory-passwords-how-it-works.md)
* [エラーが発生していると思われる場合のSSPR のトラブルシューティング方法。](active-directory-passwords-troubleshoot.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)

[Writeback]: ./media/active-directory-passwords-writeback/enablepasswordwriteback.png "Azure AD Connect でパスワード ライトバックを有効にする"
