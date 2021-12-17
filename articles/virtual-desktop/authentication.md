---
title: Azure Virtual Desktop 認証 - Azure
description: Azure Virtual Desktop の認証方法。
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/16/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: c987ad26d28a6c96d187c420b32bd3103391c62d
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2021
ms.locfileid: "122228762"
---
# <a name="supported-authentication-methods"></a>サポートされている認証方法

この記事では、Azure Virtual Desktop で使用できる認証の種類の概要について説明します。

## <a name="identities"></a>Identities

Azure Virtual Desktop では、選択した構成に応じて、さまざまな種類の ID がサポートされます。 このセクションでは、構成ごとに使用できる ID について説明します。

### <a name="on-premise-identity"></a>オンプレミス ID

Azure Virtual Desktop にアクセスするには、Azure Active Directory (Azure AD) 経由でユーザーを検出できる必要があるため、Active Directory Domain Services (AD DS) にのみ存在するユーザー ID はサポートされません。 これには、Active Directory フェデレーション サービス (AD FS) を使用したスタンドアロンの Active Directory デプロイ が含まれます。

### <a name="hybrid-identity"></a>ハイブリッド ID

Azure Virtual Desktop では、AD FS を使用してフェデレーションされるものを含めて、Azure AD を通じた[ハイブリッド ID](../active-directory/hybrid/whatis-hybrid-identity.md) がサポートされています。 これらのユーザー ID は AD DS で管理し、[Azure AD Connect](../active-directory/hybrid/whatis-azure-ad-connect.md) を使用して Azure AD に同期できます。 また、Azure AD を使用してこれらの ID を管理し、これらを [Azure AD Directory Services (Azure AD DS)](../active-directory-domain-services/overview.md) に同期できます。

ハイブリッド ID を使用して Azure Virtual Desktop にアクセスする場合、Active Directory (AD) および Azure AD のユーザーのユーザー プリンシパル名 (UPN) またはセキュリティ識別子 (SID) が一致しない場合があります。 たとえば、AD アカウント user@contoso.local は Azure AD の user@contoso.com に対応することがあります。 Azure Virtual Desktop では、AD と Azure AD アカウントの両方の UPN または SID が一致する場合にのみ、この種類の構成がサポートされます。 SID とは、AD ではユーザー オブジェクト プロパティ "ObjectSID"、Azure AD では "OnPremisesSecurityIdentifier" のことです。

### <a name="cloud-only-identity"></a>クラウド専用 ID

Azure Virtual Desktop では、[Azure AD 参加済み VM](deploy-azure-ad-joined-vm.md) を使用しているとき、クラウド専用 ID がサポートされます。

### <a name="external-identity"></a>外部 ID

Azure Virtual Desktop では、[外部 ID](../active-directory/external-identities/index.yml) は現在サポートされていません。

## <a name="service-authentication"></a>サービス認証

Azure Virtual Desktop リソースにアクセスするには、まず Azure AD アカウントにサインインしてサービスの認証を受ける必要があります。 認証は、ワークスペースをサブスクライブしてリソースを取得する場合、またはアプリまたはデスクトップに接続するたび発生します。 Azure AD とフェデレーションされていれば、[サードパーティの ID プロバイダー](../active-directory/devices/azureadjoin-plan.md#federated-environment)を使用できます。

### <a name="multifactor-authentication"></a>多要素認証

[Azure Virtual Desktop での多要素認証の設定](set-up-mfa.md)に関する手順に従って、デプロイで多要素認証 (MFA) を有効にする方法について学習してください。 この記事では、ユーザーに資格情報の入力を求める頻度を構成する方法も示します。 Azure AD 参加済み VM をデプロイするときは、[Azure AD 参加済み VM のための MFA の有効化](deploy-azure-ad-joined-vm.md#enabling-mfa-for-azure-ad-joined-vms)の構成ガイドに従ってください。

### <a name="smart-card-authentication"></a>スマート カード認証

スマート カードを使用して Azure AD への認証を行うには、まず[ユーザー証明書認証用に AD FS を構成する](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)必要があります。

## <a name="session-host-authentication"></a>セッション ホスト認証

[シングル サインオン](#single-sign-on-sso)をまだ有効にしていない場合、または資格情報をローカルに保存していない場合は、セッション ホストに対する認証も必要になります。 Azure Virtual Desktop クライアントが現在サポートしているセッション ホストのサインイン方法を次に示します。

- Windows デスクトップクライアント
    - ユーザー名とパスワード
    - スマート カード
    - [Windows Hello for Business 証明書信頼](/windows/security/identity-protection/hello-for-business/hello-hybrid-cert-trust)
    - [Windows Hello for Business 証明書によるキー信頼](/windows/security/identity-protection/hello-for-business/hello-deployment-rdp-certs)
- Microsoft Store クライアント
    - ユーザー名とパスワード
- Web クライアント
    - ユーザー名とパスワード
- Android
    - ユーザー名とパスワード
- iOS
    - ユーザー名とパスワード
- macOS
    - ユーザー名とパスワード

Azure Virtual Desktop では、セッション ホスト認証に NT LAN Manager (NTLM) と Kerberos の両方がサポートされています。 スマートカードと Windows Hello for Business では、サインインに Kerberos のみが使用できます。 クライアントで Kerberos を使用するには、ドメイン コントローラーで実行されているキー配布センター (KDC) サービスから Kerberos セキュリティ チケットを取得する必要があります。 チケットを取得するには、クライアントからドメイン コントローラーへ向かう直接のネットワーク通信経路が必要になります。 企業ネットワーク内で直接接続するか、VPN 接続を使用するか、[KDC プロキシ サーバー](key-distribution-center-proxy.md)を設定することで、通信経路を確立できます。

### <a name="single-sign-on-sso"></a>シングル サインオン (SSO)

Azure Virtual Desktop では、Windows および Web クライアント用の [Active Directory フェデレーション サービス (AD FS) を使用した SSO](configure-adfs-sso.md) がサポートされています。 SSO を使用すると、セッション ホスト認証をスキップできます。

それ以外の場合、セッション ホストの認証情報を要求されないようにする唯一の方法は、クライアントに保存することです。 他のユーザーがリソースにアクセスできないようにするため、これはセキュリティで保護されたデバイスを使用している場合にのみ行うことをお勧めします。

## <a name="in-session-authentication"></a>セッション内認証

リモート アプリまたはデスクトップに接続すると、セッション内で認証を求めるメッセージが表示される場合があります。 このセクションでは、このシナリオでユーザー名とパスワード以外の資格情報を使用する方法について説明します。

### <a name="smart-cards"></a>スマート カード

セッションでスマート カードを使用するには、セッション ホストにスマート カード ドライバーがインストールされ、[スマート カード リダイレクト](configure-device-redirections.md#smart-card-redirection)が有効になっている必要があります。 [クライアント比較チャート](/windows-server/remote/remote-desktop-services/clients/remote-desktop-app-compare#other-redirection-devices-etc)を参照して、クライアントでスマート カード リダイレクトがサポートされるか確認してください。

### <a name="fido2-and-windows-hello-for-business"></a>FIDO2 および Windows Hello for Business

現在、Azure Virtual Desktop では、FIDO2 または Windows Hello for Business を使用したセッション内認証はサポートされていません。

## <a name="next-steps"></a>次のステップ

- デプロイをセキュリティで保護するための他の方法については、 「[セキュリティの運用方法](security-guide.md)」を確認してください。
- Azure AD 参加済み VM への接続で問題が発生していますか? [Azure AD 参加済み VM への接続のトラブルシューティング](troubleshoot-azure-ad-connections.md)。
- 企業ネットワークの外部からスマート カードを使用する必要がありますか? [KDC プロキシ サーバー](key-distribution-center-proxy.md)を設定する方法を参照してください。
