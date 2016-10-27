<properties
    pageTitle="Azure MFA の概要 | Microsoft Azure"
    description="Multi-Factor Authentication とは、MFA を使用する理由、Multi-factor Authentication クライアントに関する詳細、使用可能なさまざまな認証方法とバージョン。 "
    keywords="MFA の概要, mfaの概要, mfa とは"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>


# <a name="what-is-azure-multi-factor-authentication?"></a>Azure Multi-Factor Authentication とは
2 段階認証は、複数の確認方法を要求することで、ユーザーのサインインとトランザクションにさらなる重要なセキュリティ レイヤーを追加する認証方法です。 これらは、次の確認方法のうち 2 つ以上を要求することで機能します。

- ユーザーが知っているもの (通常はパスワード)
- ユーザーが持っているもの (携帯電話など、簡単には複製できない信頼できるデバイス)
- ユーザー自身 (生体認証)

<center>![ユーザー名とパスワード](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![証明書](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート フォン](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![スマート カード](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![仮想スマート カード](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![ユーザー名とパスワード](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) は、Microsoft の 2 段階認証ソリューションです。 Azure MFA では、シンプルなサインイン プロセスを好むユーザーのニーズに応えながら、データやアプリケーションへのアクセスを効果的に保護することができます。 電話、テキスト メッセージ、モバイル アプリによる確認など、一連の照合方法を通じて確実な認証を行うことができます。

>[AZURE.VIDEO multi-factor-authentication-overview]

## <a name="why-use-azure-multi-factor-authentication?"></a>Azure Multi-Factor Authentication を使う理由

インターネットの接続人口は今やかつてない水準にまで増加しています。 スマート フォン、タブレット、ノート PC、デスクトップ PC など、インターネットへの接続手段は多様化し、常時接続していることが当たり前の時代となりました。 人々は、どこにいても、自分のアカウントやアプリケーションにアクセスすることができます。そのことが生産性とサービスの質の向上につながっているといえるでしょう。

Azure Multi-Factor Authentication は、認証手段の多層化によってユーザーを絶えず保護することができる、使いやすくスケーラブルで信頼性の高いソリューションです。

![使いやすい](./media/multi-factor-authentication/simple.png)| ![拡張性](./media/multi-factor-authentication/scalable.png)| ![常時保護](./media/multi-factor-authentication/protected.png)|![信頼性](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**使いやすい**|**拡張性**|**常時保護**|**信頼性**

- **使いやすい** -Azure Multi-Factor Authentication は、簡単にセットアップして使用することができます。 ユーザーは、Azure Multi-Factor Authentication に付属している追加の保護を使用して自分のデバイスを管理できます。 多くの場合、セットアップは数回のクリックで簡単に行うことができます。
- **拡張性** - Azure Multi-Factor Authentication はクラウドの強みを活かし、オンプレミスの AD やカスタム アプリと連携します。 その保護の範囲は、ハイボリュームのミッション クリティカルなシナリオにまで広げることができます。
- **常時保護** - Azure Multi-Factor Authentication は、きわめて高い業界標準に基づく強力な認証手段です。
- **信頼性** - Azure Multi-Factor Authentication には、99.9% の可用性が確保されています。 確認要求を受信することも処理することもできなくなったときに、2 段階認証のサービスは利用不可と見なされます。

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication のしくみ

2 段階認証のセキュリティは、レイヤー アプローチによってもたらされます。 攻撃者にとって、複数の確認方法を侵害することはかなり困難です。 攻撃者がパスワードを知ったとしても、信頼できるデバイスを手に入れなければ役に立ちません。 デバイスを紛失した場合、そのデバイスを見つけた人は、パスワードを知らなければデバイスを使用できません。

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## <a name="methods-available-for-multi-factor-authentication"></a>Multi-Factor Authentication で使用可能な方法
ユーザーがサインインしようとすると、追加の確認要求がユーザーに対して行われます。 この 2 番目の確認のために使用できる方法の一覧を次に示します。

確認方法 | Description
------------- | ------------- |
音声通話 | ユーザーの電話が呼び出され、サインインを確認するよう求めるメッセージが再生されます。 確認プロセスを完了するには、# キーを押します。 このオプションは構成可能であり、指定するコードを変更することができます。
テキスト メッセージ | 6 桁のコードを含むテキスト メッセージがユーザーのスマートフォンに送信されます。 確認プロセスを完了するには、このコードを入力します。
モバイル アプリの通知 | ユーザーのスマート フォンに、モバイル アプリで **[確認]** を選択して確認を完了するよう求める確認要求が送信されます。 これは、アプリの通知がプライマリの確認方法である場合に発生します。 サインインしようとしていないときにこの通知を受信した場合、ユーザーはそれを不正アクセスとして報告できます。
モバイル アプリの確認コード | ユーザーのデバイス上のモバイル アプリで、確認コードが生成されます。 これは、プライマリの確認方法として確認コードを選択した場合に発生します。

Azure Multi-Factor Authentication では、モバイル アプリの確認方法にスマート フォン用のサードパーティ認証アプリを使用していますが、 [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071)、[Android](http://go.microsoft.com/fwlink/?Linkid=825072)、[iOS](http://go.microsoft.com/fwlink/?Linkid=825073) で利用できる Microsoft Authenticator アプリの使用をお勧めします。

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の使用可能なバージョン
Azure Multi-Factor Authentication は、次の 3 種類のバージョンが使用可能です。 

バージョン | Description
------------- | ------------- |
Office 365 の多要素認証 | このバージョンは、Office 365 アプリケーション専用に動作し、Office 365 ポータルから管理されます。 これにより、管理者は 2 段階認証を使用して Office 365 リソースを保護できるようになります。 このバージョンには、Office 365 サブスクリプションが付属しています。
Azure 管理者用の多要素認証 | すべての Azure 管理者には、Office 365 向けと同じ 2 段階認証機能が追加費用なしでご利用いただけます。 Azure サブスクリプションのすべての管理者アカウントは、この機能を有効にすることでさらなる保護を実現できるようになりました。 したがって、VM または Web サイトの作成、ストレージの管理、その他の Azure サービスの使用のために Azure Portal にアクセスする必要がある管理者は、各自の管理者アカウントに MFA を追加することができます。
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication は、豊富な機能を備えています。 [Azure クラシック ポータル](http://manage.windowsazure.com)を介した追加の構成オプション、高度なレポート、および一連のオンプレミスおよびクラウド アプリケーションのサポートを提供します。 Azure Multi-Factor Authentication は、Azure Active Directory Premium と Enterprise Mobility Suite の一部として提供され、クラウドまたはオンプレミスのいずれかにデプロイできます。

## <a name="feature-comparison-of-versions"></a>バージョンごとの機能の比較
次の表に、さまざまなバージョンの Azure Multi-Factor Authentication で使用できる機能の一覧を示します。

>[AZURE.NOTE] この比較表では、各サブスクリプションの一部の機能について説明します。 Azure AD Premium または Enterprise Mobility Suite を使用する場合は、[MFA をクラウドで使用するか、オンプレミスで使用するか](multi-factor-authentication-get-started.md)によって、一部の機能を使用できないことがあります。

機能 | Office 365 の多要素認証 (Office 365 SKU に付属)|Azure 管理者用の多要素認証 (Azure サブスクリプションに付属) | Azure Multi-Factor Authentication (Azure AD Premium および Enterprise Mobility Suite に付属)
------------- | :-------------: |:-------------: |:-------------: |
管理者は MFA によってアカウントを保護することができます。| ● | ● (Azure 管理者アカウントのみ使用可能)|●
モバイル アプリを 2 番目の要素にする|● | ● | ●
音声通話を 2 番目の要素にする|● | ● | ●
SMS を 2 番目の要素にする|● | ● | ●
MFA をサポートしていないクライアントのアプリ パスワード|● | ● | ●
認証方法の管理制御| ● | ● | ●
PIN モード| | | ●
不正アクセスのアラート| | | ●
MFA レポート| | | ●
ワンタイム バイパス| | | ●
音声通話のカスタムあいさつ文| | | ●
音声通話の発信元 ID のカスタマイズ| | | ●
イベントの確認| | | ●
信頼できる IP| | | ●
信頼済みデバイスの MFA の記憶 |● | ● | ●
MFA の SDK | | | ● Multi-Factor Authentication プロバイダーと完全な Azure サブスクリプションが必要
MFA サーバーによる、オンプレミス アプリケーション用の MFA| | | ●

## <a name="how-to-get-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication の入手方法

Azure Multi-Factor Authentication で提供されるすべての機能を利用するには、次のような方法があります。

1.  Azure Multi-Factor Authentication のライセンスを購入し、ユーザーに割り当てる。
2.  Azure Multi-Factor Authentication がバンドルされた Azure Active Directory Premium、Enterprise Mobility Suite、Enterprise Cloud Suite などのライセンスを購入し、ユーザーに割り当てる。
3.  Azure サブスクリプション内で Azure Multi-Factor Authentication プロバイダーを作成する。 Azure Multi-Factor Authentication プロバイダーには次の 2 つの使用モデルがあり、どちらも Azure サブスクリプションを通じて請求されます。  
    - **ユーザーごと**。 定期的な認証が必要な一定数の従業員に対して、2 段階認証を有効にしたい企業向けです。  
    - **認証ごと**。 頻繁に認証する必要がない大規模グループの外部ユーザーに対して、2 段階認証を有効にしたい企業向けです。  

Azure Multi-Factor Authentication では、クラウドとサーバーの両方で検証方法を選択できます。 つまり、ユーザーが使用できる方法を選択できます。 クラウド バージョンの多要素認証では、この機能は現在パブリック プレビュー段階にあります。 詳細については、「[選択可能な検証方法](multi-factor-authentication-whats-next.md#selectable-verification-methods)」をご覧ください。

料金の詳細については、「[Azure MFA の料金](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

Azure Multi-Factor Authentication の使用を開始するには、まず、 [クラウドでの MFA か、オンプレミスの MFA かを選択](multi-factor-authentication-get-started.md)



<!--HONumber=Oct16_HO2-->


