---
title: Azure MFA Server から Azure Multi-Factor Authentication に移行する - Azure Active Directory
description: オンプレミスの Azure MFA Server から Azure Multi-Factor Authentication への移行に関するステップ バイ ステップのガイダンス
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/22/2021
ms.author: BaSelden
author: BarbaraSelden
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4415e7ce86d2beb9e2903f23d0b6fa9ac7d3ec04
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597835"
---
# <a name="migrate-from-azure-mfa-server-to-azure-multi-factor-authentication"></a>Azure MFA Server から Azure Multi-Factor Authentication に移行する

多要素認証 (MFA) は、インフラストラクチャと資産を不正ユーザーからセキュリティ保護するのに重要です。 Azure MFA Server は新しいデプロイには使用できません。非推奨になります。 MFA Server を使用しているお客様は、クラウドベースの Azure Active Directory (Azure AD) 多要素認証の使用に移行してください。 このドキュメントでは、次のようなハイブリッド環境があることを想定しています。

- MFA に MFA Server を使用している。
- Active Directory フェデレーション サービス (AD FS) または別の ID プロバイダー フェデレーション製品によるフェデレーションを Azure AD で使用している。
  - この記事の対象は AD FS ですが、他の ID プロバイダーにも同様の手順が適用されます。
- MFA Server が AD FS と統合されている。 
- 認証に AD FS を使用しているアプリケーションがある場合がある。

目標に応じて、移行について複数の最終状態が考えられます。

| <br> | 目標: MFA Server のみの使用を停止する | 目標: MFA Server の使用を停止し、Azure AD Authentication に移行する | 目標: MFA Server と AD FS の使用を停止する |
|------|------------------------------------|-------------------------------------------------------------------|-----------------------------------------|
|MFA プロバイダー | MFA プロバイダーを MFA Server から Azure AD MFA に変更します。 | MFA プロバイダーを MFA Server から Azure AD MFA に変更します。 |   MFA プロバイダーを MFA Server から Azure AD MFA に変更します。 |
|ユーザー認証  |Azure AD Authentication にフェデレーションを引き続き使用します。 | パスワード ハッシュ同期 (優先) またはパススルー認証 **および** シームレス シングル サインオンを使用して、Azure AD に移行します。| パスワード ハッシュ同期 (優先) またはパススルー認証 **および** シームレス シングル サインオンを使用して、Azure AD に移行します。 |
|アプリケーション認証 | アプリケーション に AD FS 認証を引き続き使用します。 | アプリケーション に AD FS 認証を引き続き使用します。 | Azure MFA に移行する前に、アプリを Azure AD に移行します。 |

可能な場合は、MFA とユーザー認証の両方を Azure に移行します。 ステップ バイ ステップのガイダンスについては、[Azure AD MFA と Azure AD 認証への移行](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)に関するページを参照してください。 

ユーザー認証を移行できない場合は、[フェデレーションを使用した Azure AD MFA への移行](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)に関するステップ バイ ステップのガイダンスを参照してください。

## <a name="prerequisites"></a>前提条件

- AD FS 環境 (MFA を移行する前にすべてのアプリを Azure AD に移行していない場合は必須)
  - AD FS for Windows Server 2019、ファーム動作レベル (FBL) 4 にアップグレードします。 これにより、ユーザー切り替えがシームレスになるように、グループ メンバーシップに基づいて認証プロバイダーを選択できます。 Windows Server 2016 FBL 3 では、AD FS での移行が可能ですが、ユーザーについては同様にシームレスになりません。 移行中は、移行が完了するまで、認証プロバイダー (MFA Server または Azure MFA) を選択するように求めるメッセージがユーザーに表示されます。 
- アクセス許可
  - Azure AD MFA の AD FS ファームを構成するための Active Directory のエンタープライズ管理者ロール
  - Azure AD PowerShell を使用して Azure AD の構成を実行するための Azure AD のグローバル管理者ロール


## <a name="considerations-for-all-migration-paths"></a>すべての移行パスに関する考慮事項

MFA Server から Azure AD MFA に移行するには、登録されている MFA 電話番号を移行するだけでは不十分です。 Microsoft の MFA Server は多くのシステムと統合できます。Azure AD MFA と統合するための最適な方法を理解するには、これらのシステムで MFA Server がどのように使用されているかを評価する必要があります。 

### <a name="migrating-mfa-user-information"></a>MFA ユーザー情報を移行する

ユーザーを一括で移行する場合の一般的な考え方には、リージョン、部署、または管理者などのロールによるユーザーの移行が含まれます。 どの方法を選択する場合でも、テストおよびパイロット グループから開始して、ユーザーを繰り返し移行し、ロールバック計画を設定する必要があります。 

ユーザーの登録済み MFA 電話番号とハードウェア トークンを移行できますが、Microsoft Authenticator アプリの設定など、デバイスの登録を移行することはできません。 ユーザーは、Authenticator アプリで新しいアカウントを登録して追加し、古いアカウントを削除する必要があります。 

MFA Server にリンクされている古いアカウントと新しく追加したアカウントをユーザーが区別できるようにするには、MFA Server 上のモバイル アプリのアカウント名が、2 つのアカウントを区別できる名前になっている必要があります。 たとえば、MFA Server の [モバイル アプリ] の下に表示されるアカウント名が OnPrem MFA Server に変更されているとします。 Authenticator アプリのアカウント名は、次にユーザーにプッシュ通知されると変更されます。 

電話番号を移行すると、古い番号が移行される可能性があり、パスワードレス モードの Microsoft Authenticator サインインなど、安全な方法を設定する代わりに、ユーザーが電話ベースの MFA を使用し続ける可能性があります。 したがって、選択した移行パスに関係なく、[統合されたセキュリティ情報](howto-registration-mfa-sspr-combined.md)をすべてのユーザーに登録してもらうことをお勧めします。


#### <a name="migrating-hardware-security-keys"></a>ハードウェア セキュリティ キーを移行する

Azure AD では、OATH ハードウェア トークンをサポートしています。 MFA Server から Azure AD MFA にトークンを移行するには、一般に "シード ファイル" と呼ばれる [CSV ファイルを使用して、トークンを Azure AD にアップロードする必要があります](concept-authentication-oath-tokens.md#oath-hardware-tokens-preview)。 シード ファイルには、シークレット キーとトークンのシリアル番号、およびトークンを Azure AD にアップロードするために必要なその他の必要な情報が含まれます。 

シークレット キーが含まれるシード ファイルがなくなると、MFA Server からシークレット キーをエクスポートできません。 シークレット キーにアクセスできなくなった場合のサポートについては、ハードウェア ベンダーにお問い合せください。

MFA Server Web サービス SDK を使用すると、特定のユーザーに割り当てられた OATH トークンのシリアル番号をエクスポートできます。 IT 管理者は、この情報とシード ファイルを使用して、トークンを Azure AD にインポートし、シリアル番号に基づいて、指定されたユーザーに OATH トークンを割り当てることができます。 デバイスから OTP 情報を指定して登録を完了するように、インポート時にユーザーに通知する必要があります。 MFA Server の Multi-Factor Authentication Server ヘルプ ファイルの [GetUserInfo] > [userSettings] > [OathTokenSerialNumber] のトピックを参照してください。 


### <a name="additional-migrations"></a>追加の移行

MFA Server から Azure MFA への移行を決定すると、他の移行も可能になります。 追加の移行を完了できるかどうかは、特に次のような多くの要因に左右されます。

- Azure AD Authentication をユーザーに使用する意図がある
- アプリケーションを Azure AD 移行する意図がある

MFA Server はアプリケーションとユーザー認証の両方と深く統合されているため、MFA 移行の一環として、これらの機能の両方を Azure に移行し、最終的に AD FS の使用を停止することを検討することもできます。 

推奨事項: 

- 堅牢なセキュリティとガバナンスが可能になるため、Azure AD を認証に使用する
- 可能であれば、アプリケーションを Azure AD に移行する

組織に最適なユーザー認証方法を選択するには、「[Azure Active Directory ハイブリッド ID ソリューションの適切な認証方法を選択する](../hybrid/choose-ad-authn.md)」を参照してください。 パスワード ハッシュ同期 (PHS) を使用することをお勧めします。

### <a name="passwordless-authentication"></a>パスワードレスの認証

2 つ目の要素として Microsoft Authenticator を使用するユーザーの登録の一環として、パスワードレスの電話でのサインインを有効にすることをお勧めします。 FIDO や Windows Hello for Business などの他のパスワードレスの方法を含む詳細については、「[Azure Active Directory でパスワードレス認証のデプロイを計画する](howto-authentication-passwordless-deployment.md#plan-for-and-deploy-the-microsoft-authenticator-app)」を参照してください。

### <a name="microsoft-identity-manager-self-service-password-reset"></a>Microsoft Identity Manager セルフサービス パスワード リセット 

Microsoft Identity Manager (MIM) SSPR では、MFA Server を使用して、パスワード リセット フローの一部として SMS ワンタイム パスコードを呼び出すことができます。 Azure MFA を使用するように MIM を構成することはできません。 SSPR サービスを Azure AD SSPR に移行することを評価することをお勧めします。

ユーザーが Azure MFA に登録する機会を利用し、統合された登録エクスペリエンスを使用して Azure AD SSPR に登録できます。


### <a name="radius-clients-and-azure-ad-mfa"></a>RADIUS クライアントと Azure AD MFA

MFA Server では、プロトコルをサポートするアプリケーションとネットワーク デバイスで MFA を呼び出すことができるように、RADIUS をサポートしています。 RADIUS と MFA Server を使用している場合は、クライアント アプリケーションを最新のプロトコル (SAML、Open ID Connect、Azure AD 上の OAuth など) に移行することをお勧めします。 アプリケーションを更新できない場合は、Azure MFA 拡張機能を持つネットワーク ポリシー サーバー (NPS) をデプロイできます。 ネットワーク ポリシー サーバー (NPS) 拡張機能は、RADIUS ベースのアプリケーションと Azure AD MFA の間のアダプターとして機能し、認証の 2 番目の要素が提供されます。 これにより、RADIUS クライアントを Azure MFA に移行し、MFA Server の使用を停止できます。

#### <a name="important-considerations"></a>重要な考慮事項

RADIUS クライアントに NPS を使用する場合は制限があるため、RADIUS クライアントを評価して、最新の認証プロトコルにアップグレードできるかどうかを判断することをお勧めします。 サポートされている製品のバージョンとそれらの機能については、サービス プロバイダーに確認してください。 

- NPS 拡張機能では、Azure AD 条件付きアクセス ポリシー使用しません。 RADIUS の使用を継続し、NPS 拡張機能を使用する場合、NPS に送信されるすべての認証要求でユーザーが MFA を実行する必要があります。
- ユーザーは、NPS 拡張機能を使用する前に、Azure AD MFA に登録する必要があります。 そうしないと、拡張機能はユーザーの認証に失敗し、ヘルプ デスクの呼び出しが生成される場合があります。
- NPS 拡張機能で MFA が呼び出されると、MFA 要求がユーザーの既定の MFA メソッドに送信されます。 
  - サインインはサードパーティのアプリケーションで行うため、視覚的な通知 (MFA が必要であり、要求がデバイスに送信された旨を通知) がユーザーに表示される可能性は低くなります。
  - MFA の要件を満たすには、MFA の要件の確認中に、ユーザーが既定の認証方法にアクセスできる必要があります。 別の方法を選択することはできません。 既定の認証方法は、テナント認証方法と MFA ポリシーで無効になっている場合でも使用されます。
  - ユーザーは、[セキュリティ情報] ページで既定の MFA メソッドを変更できます (aka.ms/mysecurityinfo)。
- RADIUS クライアントで使用可能な MFA メソッドは、RADIUS アクセス要求を送信するクライアント システムによって制御されます。
  - パスワードを入力した後にユーザー入力が必要になる MFA メソッドは、RADIUS を使用したアクセス チャレンジ応答をサポートするシステムでのみ使用できます。 入力方式には、OTP、ハードウェア OATH トークン、または Microsoft Authenticator アプリケーションなどがあります。
  - 一部のシステムでは、使用可能な MFA メソッドが Microsoft Authenticator プッシュ通知と電話での通話に制限される場合があります。


>[!NOTE]
>RADIUS クライアントと NPS システムの間で使用されるパスワード暗号化アルゴリズムと、クライアントが使用できる入力方式によって、使用できる認証方法が決まります。 詳細については、[ユーザーが使用できる認証方法の決定](howto-mfa-nps-extension.md)に関するページを参照してください。 

一般的な RADIUS クライアントの統合には、[リモート デスクトップ ゲートウェイ](howto-mfa-nps-extension-rdg.md)や [VPN サーバー](howto-mfa-nps-extension-vpn.md)などのアプリケーションが含まれます。 その他の統合を次に示します。

- Citrix Gateway
  - [Citrix Gateway](https://docs.citrix.com/en-us/citrix-gateway) では、RADIUS と NPS の両方の拡張機能の統合と、SAML 統合がサポートされています。
- Cisco VPN
  - Cisco VPN では、[SSO の RADIUS 認証と SAML 認証](../saas-apps/cisco-anyconnect.md)の両方がサポートされています。
  - RADIUS 認証から SAML に移行すると、NPS 拡張機能をデプロイすることなく、Cisco VPN を統合できます。
- すべての VPN
  - 可能であれば、VPN を SAML アプリとしてフェデレーションすることをお勧めします。 これにより、条件付きアクセスを使用できるようになります。 詳細については、[Azure AD アプリ ギャラリーに統合されている VPN ベンダーの一覧](../manage-apps/secure-hybrid-access.md#secure-hybrid-access-through-azure-ad-partner-integrations)を参照してください。


### <a name="resources-for-deploying-nps"></a>NPS のデプロイに関するリソース

- [新しい NPS インフラストラクチャを追加する](/windows-server/networking/technologies/nps/nps-top)
- [NPS デプロイのベスト プラクティス](https://www.youtube.com/watch?v=qV9wddunpCY)
- [Azure MFA NPS 拡張機能の正常性チェック スクリプト](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)
- [既存の NPS インフラストラクチャを Azure AD MFA と統合する](howto-mfa-nps-extension-vpn.md)

## <a name="next-steps"></a>次の手順

- [フェデレーションを使用して Azure AD MFA に移行する](how-to-migrate-mfa-server-to-azure-mfa-with-federation.md)
- [Azure AD MFA と Azure AD ユーザー認証に移行する](how-to-migrate-mfa-server-to-azure-mfa-user-authentication.md)


