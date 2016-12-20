---
title: "MFA のセキュリティのベスト プラクティス | Microsoft Docs"
description: "このドキュメントでは、Azure アカウントで Azure MFA を使用する場合のベスト プラクティスについて説明します。"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 3be7d968-96bb-4320-8701-869fd04a2595
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2471a8daa91113c1865507239361d093f8695e30


---
# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Azure AD アカウントで Azure Multi-Factor Authentication を使用するためのセキュリティのベスト プラクティス
認証プロセスの強化に関して、ほとんどの組織には 2 段階認証を選択することをお勧めします。 Azure Multi-Factor Authentication (MFA) を使用すると、企業はセキュリティやコンプライアンスの要件を満たすと同時に、ユーザーに簡単なサインイン エクスペリエンスを提供することができます。 この記事では、Azure MFA の導入を計画する際に考慮すべきベスト プラクティスを紹介します。

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>クラウドの Azure Multi-Factor Authentication のベスト プラクティス
すべてのユーザーに 2 段階認証を提供し、Azure MFA が提供する拡張機能を利用するには、すべてのユーザーに対して Azure MFA を有効にする必要があります。  これは、次のいずれを実装することで実現されます。

* Azure AD Premium または Enterprise Mobility Suite
* 多要素認証プロバイダー

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Azure AD Premium/Enterprise Mobility Suite
![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

Azure AD Premium または Enterprise Mobility Suite を使用してクラウドに Azure MFA を導入するための最初の手順は、ユーザーにライセンスを割り当てることです。  Azure Multi-Factor Authentication はこれらのスイートに含まれるため、組織は、2 段階認証機能をすべてのユーザーに展開するためにそれ以外の操作を行う必要はありません。 ユーザーにライセンスを割り当てるだけで、MFA をオンにすることができます。

Multi-Factor Authentication を設定する場合は、次の点を考慮してください。

* 多要素認証プロバイダーを作成する必要はありません。  Azure AD Premium と Enterprise Mobility Suite には、どちらも Azure Multi-Factor Authentication が付属しています。  
* Azure AD Connect は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。 オンプレミスの Active Directory インスタンスと同期されない Azure AD ディレクトリを使用している場合、Azure AD Connect は必要ありません。

### <a name="multi-factor-auth-provider"></a>多要素認証プロバイダー
![多要素認証プロバイダー](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Azure AD Premium またはEnterprise Mobility Suite がない場合、クラウドに Azure MFA を導入するための最初の手順は、MFA 認証プロバイダーを作成することです。 MFA は、Azure Active Directory を使用しているグローバル管理者が既定で使用できますが、組織に MFA をデプロイするときは、2 段階認証機能をすべてのユーザーに展開する必要があります。 これを行うには、多要素認証プロバイダーが必要です。
認証プロバイダーを選択する場合は、ディレクトリを選択して次の点を考慮する必要があります。

* Multi-Factor Auth Provider の作成に、Azure AD ディレクトリは必要ありません。 ただし、次に示す高度な機能を利用するには、Azure AD ディレクトリに Multi-Factor Auth プロバイダーを関連付ける必要があります。  
  * すべてのユーザーを 2 段階認証の対象にする  
  * 管理ポータル、カスタムの案内応答、レポートなどの追加機能をグローバル管理者に提供する
* DirSync または AAD Sync は、オンプレミスの Active Directory 環境を Azure AD ディレクトリと同期している場合にのみ必要です。 Active Directory のオンプレミスのインスタンスと同期されない Azure AD ディレクトリを使用する場合、DirSync または AAD Sync は必要ありません。
* ビジネスに適した使用モデル (認証ごとまたは有効化されたユーザーごと) を選択してください。 使用モデルはいったん選択すると変更できません。
  * 認証ごと: 検証ごとに課金されます。 特定のユーザーではなく、特定のアプリにアクセスするすべてのユーザーに対して 2 段階認証を実行する場合は、このモデルを使用します。
  * 有効化されたユーザーごと: Azure MFA を有効にしたユーザーごとに課金されます。 Azure AD Premium または Enterprise Mobility Suite ライセンスを所有しているユーザーと所有していないユーザーが混在している場合は、このモデルを使用します。

### <a name="user-account"></a>ユーザー アカウント
ユーザーに対して Azure MFA を有効にすると、ユーザー アカウントは、3 つの主要な状態 (無効、有効、強制) のいずれかになります。
次のガイドラインを使用して、デプロイに最適なオプションを使用していることを確認してください。

* ユーザーの状態が**無効**に設定されている場合、そのユーザーは 2 段階認証を実行していません。 これが既定の状態です。
* ユーザーの状態が**有効**に設定されている場合、そのユーザーの 2 段階認証は有効ですが、登録プロセスが完了していないことを意味します。 次回サインインするときにプロセスを完了するよう求められます。 この設定は、ブラウザー以外のアプリには影響しません。 すべてのアプリは、登録プロセスが完了するまで機能し続けます。
* ユーザーの状態が**強制**に設定されている場合、そのユーザーが登録を完了しているかどうかは不明です。 登録プロセスが完了していれば、ユーザーは 2 段階認証を実行しています。 登録プロセスが完了していない場合は、次回サインインするときに登録プロセスを完了するよう求められます。 この設定は、ブラウザー以外のアプリに影響します。 アプリのパスワードを作成して使用するまで、これらのアプリは機能しません。

「 [クラウドでの Azure Multi-Factor Authentication の概要](multi-factor-authentication-get-started-cloud.md) 」で入手できるユーザー通知テンプレートを使用して、MFA の導入に関する電子メールをユーザーに送信します。

### <a name="supportability"></a>サポート
ユーザーの大半は、パスワードのみを使用した認証に慣れているため、企業はこのプロセスに関してすべてのユーザーに認識してもらうことが重要です。 このような認識により、ユーザーが MFA 関連の重要でない問題についてヘルプ デスクに問い合わせる可能性は低くなります。
ただし、一部のシナリオでは MFA を一時的に無効にすることが必要になる場合があります。 次のガイドラインに従って、これらのシナリオへの対処方法を理解してください。

* モバイル アプリや携帯電話が通知や電話呼び出しを受信せず、これが理由でユーザーがサインインできないシナリオに対処するためのトレーニングをテクニカル サポート担当者が受講済みであることを確認します。 テクニカル サポート担当者は、ワンタイム バイパス オプションを有効にし、2 段階認証を "バイパス" することによってユーザーを 1 回だけ認証できるようにすることができます。 バイパスは一時的なものであり、指定された秒数が経過すると無効になります。
* 必要に応じて、Azure MFA で信頼できる IP 機能を利用できます。 この機能を使用すると、管理者常駐型テナントまたはフェデレーション テナントの管理者は、会社のローカル イントラネットからサインインするユーザーの 2 段階認証をバイパスできます。 この機能は、Azure AD Premium、Enterprise Mobility Suite、または Azure Multi-Factor Authentication ライセンスを所有している Azure AD テナントで使用できます。

## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>オンプレミスの Azure Multi-Factor Authentication のベスト プラクティス
企業が独自のインフラストラクチャを利用して MFA を有効にすることを決定した場合は、Azure Multi-Factor Authentication Server をオンプレミスでデプロイする必要があります。 MFA Server のコンポーネントを次の図に示します。

![多要素認証プロバイダー](./media/multi-factor-authentication-security-best-practices/server.png)
\*既定ではインストールされていません \**既定ではインストールされていますが、有効になっていません

Azure Multi-Factor Authentication Server を使用すると、Azure AD アカウントがアクセスするクラウドのリソースとオンプレミスのリソースをセキュリティで保護することができます。 ただし、これを実行できるのは、フェデレーションを使用した場合のみです。  つまり、AD FS を用意し、それを Azure AD テナントとフェデレーションしておく必要があります。
Multi-Factor Authentication Server を設定する場合は、次の点を考慮してください。

* Active Directory フェデレーション サービスを使用して Azure AD リソースをセキュリティで保護している場合、認証の第 1 段階は AD FS を使用してオンプレミスで実行され、 第 2 段階は、要求を受け入れることによりオンプレミスで実行されます。
* AD FS フェデレーション サーバーへの Azure Multi-Factor Authentication Server のインストールは要件ではありません。 ただし、AD FS を実行中の Windows Server 2012 R2 には、AD FS 用の Multi-Factor Authentication アダプターをインストールしておく必要があります。 サポートされているバージョンであれば、サーバーを別のコンピューターにインストールすることができ、AD FS アダプターを AD FS フェデレーション サーバーに別個にインストールすることができます。 アダプターを別個にインストールする手順については、以下の手順を参照してください。
* Multi-Factor Authentication AD FS アダプターのインストール ウィザードは、Active Directory 内に PhoneFactor Admins というセキュリティ グループを作成した後、このグループに AD FS サービス アカウントを追加します。 PhoneFactor Admins グループが実際に作成されていることと、AD FS サービス アカウントがこのグループのメンバーであることを、ドメイン コントローラーで確認することをお勧めします。 必要に応じて、AD FS サービス アカウントをドメイン コント ローラーの PhoneFactor Admins グループに手動で追加します。

### <a name="user-portal"></a>ユーザー ポータル
このポータルはインターネット インフォメーション サーバー (IIS) Web サイトで動作します。ここでは、セルフ サービス機能を使用できるほか、ユーザー管理機能一式が用意されています。 このコンポーネントを構成するには、次のガイドラインに従ってください。

* IIS 6 以降が必要です。
* ASP.NET v2.0.507207 をインストールして登録する必要があります。
* このサーバーは境界ネットワークにデプロイできます。

### <a name="app-passwords"></a>アプリ パスワード
組織が SSO を使用して Azure AD とフェデレーションされているときに Azure MFA を使用する場合は、アプリ パスワードを使用する際に、次の点に注意してください (これらの注意点は、フェデレーション (SSO) が使用されている場合にのみ適用されます)。

* アプリ パスワードは Azure AD によって検証されます。したがってフェデレーションをバイパスします。 フェデレーションは、アプリ パスワードを設定するときにのみ使用されます。
* フェデレーション (SSO) ユーザーの場合、パスワードは組織 ID の中に保存されます。 ユーザーが退職した場合、その情報は、DirSync を使用してリアルタイムで組織 ID に送信される必要があります。 アカウントの無効化/削除を同期させるには最大 3 時間かかる可能性があり、Azure AD 内のアプリ パスワードの無効化/削除が遅れることがあります。
* オンプレミスのクライアント アクセス制御の設定は、アプリ パスワードでは受け入れられません。
* アプリ パスワードに対するオンプレミス以外の認証ログ/監査機能はありません。
* Microsoft Lync 2013 クライアントには、より多くのエンドユーザー教育が必要です。
* ある種の高度なアーキテクチャ設計では、2 段階認証をクライアントで使用するときに、認証場所によっては、組織のユーザー名とパスワードをアプリ パスワードと組み合わせて使用する必要があります。 オンプレミスのインフラストラクチャに対して認証するクライアントの場合は、組織のユーザー名とパスワードを使用します。 Azure AD に対して認証するクライアントはアプリケーション パスワードを使用します。
* 既定では、ユーザーはアプリ パスワードを作成できません。 企業がそれを要求する場合、またはシナリオによってアプリ パスワードの作成をユーザーに許可する必要がある場合は、**[ブラウザーではないアプリケーションへのサインイン用にアプリケーション パスワードの作成を許可する]** オプションが選択されていることを確認します。

## <a name="additional-considerations"></a>追加の考慮事項
オンプレミスでデプロイされる各コンポーネントの追加の考慮事項とベスト プラクティスについては、次の一覧を参照してください。

| メソッド | Description |
|:--- |:--- |
| [Active Directory フェデレーション サービス](multi-factor-authentication-get-started-adfs.md) |AD FS による Azure Multi-Factor Authentication の設定に関する情報です。 |
| [RADIUS 認証](multi-factor-authentication-get-started-server-radius.md) |RADUIS による Azure MFA Server のセットアップと構成に関する情報です。 |
| [IIS 認証](multi-factor-authentication-get-started-server-iis.md) |IIS を使用した Azure MFA Server のセットアップと構成に関する情報です。 |
| [Windows 認証](multi-factor-authentication-get-started-server-windows.md) |Windows 認証による Azure MFA Server のセットアップと構成に関する情報です。 |
| [LDAP 認証](multi-factor-authentication-get-started-server-ldap.md) |LDAP 認証による Azure MFA Server のセットアップと構成に関する情報です。 |
| [RADIUS を使用したリモート デスクトップ ゲートウェイと Multi-Factor Authentication Server](multi-factor-authentication-get-started-server-rdg.md) |RADIUS を使用したリモート デスクトップ ゲートウェイによる Azure MFA Server のセットアップと構成に関する情報です。 |
| [Windows Server Active Directory との同期](multi-factor-authentication-get-started-server-dirint.md) |Active Directory と Azure MFA Server 間の同期のセットアップと構成に関する情報です。 |
| [Azure Multi-Factor Authentication Server モバイル アプリ Web サービスのデプロイ](multi-factor-authentication-get-started-server-webservice.md) |Azure MFA Server Web サービスのセットアップと構成に関する情報です。 |
| [Azure Multi-Factor Authentication を使用した高度な VPN の構成](multi-factor-authentication-advanced-vpn-configurations.md) |Cisco ASA、Citrix Netscaler、Juniper/Pulse Secure などの VPN アプライアンスの LDAP または RADIUS を使用した構成に関する情報です。 |

## <a name="additional-resources"></a>その他のリソース
この記事では Azure MFA のベスト プラクティスに重点を置いて説明しましたが、このほかにも、MFA のデプロイを計画する際に利用できるリソースがあります。 このプロセスで役立つ重要な記事を次に示します。

* [Azure Multi-Factor Authentication のレポート](multi-factor-authentication-manage-reports.md)
* [Azure Multi-Factor Authentication のセットアップ エクスペリエンス](multi-factor-authentication-end-user-first-time.md)
* [Azure Multi-Factor Authentication についてよく寄せられる質問 (FAQ)](multi-factor-authentication-faq.md)




<!--HONumber=Nov16_HO3-->


