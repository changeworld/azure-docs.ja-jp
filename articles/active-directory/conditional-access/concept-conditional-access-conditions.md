---
title: 条件付きアクセス ポリシーの条件 - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーの条件とは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9fe24e4a2b25b1ef3f0da2b1a5e1c0f29251df1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192080"
---
# <a name="conditional-access-conditions"></a>条件付きアクセス:条件

管理者は条件付きアクセスポリシー内で、リスク、デバイス プラットフォーム、場所などの条件からのシグナルを利用して、ポリシーの決定を強化できます。 

![条件付きアクセス ポリシーを定義し、条件を指定する](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

複数の条件を組み合わせて、きめ細かで具体的な条件付きアクセス ポリシーを作成することができます。

たとえば、機密性の高いアプリケーションにアクセスするときに、管理者は、多要素認証などの他のコントロールに加えて、Identity Protection や場所からのサインイン リスク情報を、アクセスの決定要因の 1 つとして含めることができます。

## <a name="sign-in-risk"></a>サインイン リスク

[Identity Protection](../identity-protection/overview-identity-protection.md) にアクセスできるお客様の場合、条件付きアクセス ポリシーの一部としてサインイン リスクを評価できます。 サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。 サインイン リスクの詳細については、「[リスクとは](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)」と「[方法:リスク ポリシーを構成して有効にする](../identity-protection/howto-identity-protection-configure-risk-policies.md)」を参照してください。

## <a name="device-platforms"></a>デバイス プラットフォーム

デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます。 Azure AD では、デバイスによって提供される、ユーザー エージェント文字列などの情報を使用してプラットフォームを識別します。 ユーザー エージェント文字列は変更できるため、この情報は未検証です。 デバイス プラットフォームは、Microsoft Intune デバイス コンプライアンス ポリシーと連携して使用するか、ブロック ステートメントの一部として使用する必要があります。 既定では、すべてのデバイス プラットフォームに適用されます。

Azure AD 条件付きアクセスは、次のデバイス プラットフォームをサポートします。

- Android
- iOS
- Windows Phone
- Windows
- macOS

**他のクライアント**条件を使用してレガシ認証をブロックする場合は、デバイスのプラットフォーム条件も設定できます。

## <a name="locations"></a>場所

組織は場所を条件として構成するときに、場所を含めるか除外するかを選択できます。 これらの名前付きの場所には、パブリック IPv4 ネットワーク情報、国、リージョンだけでなく、特定の国やリージョンにマップされていない不明な領域が含まれる場合もあります。 信頼できる場所としてマークできるのは IP 範囲のみです。

**任意の場所**を含める場合、このオプションには、構成された名前付きの場所ではなく、インターネット上の任意の IP アドレスが含まれます。 **任意の場所**を選択するときには、管理者は、**信頼されているすべての場所**または**選択した場所**を除外することを選択できます。

たとえば、組織によっては、物理的な本社のように、信頼できる場所のネットワークにユーザーが接続されているときは多要素認証を要求しないことを選択する可能性もあります。 管理者は、すべての場所を含むが、本社ネットワーク用に選択された場所を除外するポリシーを作成することも可能です

## <a name="client-apps-preview"></a>クライアント アプリ (プレビュー)

条件付きアクセス ポリシーは、既定では、ブラウザー ベースのアプリケーションと、最新の認証プロトコルを利用するアプリケーションに適用されます。 これらのアプリケーションに加えて、管理者は、Exchange ActiveSync クライアントと、レガシ プロトコルを利用する他のクライアントを含めることを選択できます。

- Browser
   - これには、SAML、WS-Federation、OpenID Connect、OAuth 機密クライアントとして登録されているサービスなどのプロトコルを使用する Web ベースのアプリケーションが含まれます。
- モバイル アプリとデスクトップ クライアント
   - 先進認証クライアント
      - このオプションには、Office デスクトップや Phone アプリケーションなどのアプリケーションが含まれます。
   - Exchange ActiveSync クライアント
      - ポリシーによって Exchange ActiveSync の使用がブロックされると、影響を受けるユーザーには 1 通の検疫電子メールが送信されます。 この電子メールには、ブロックされた理由に関する情報が記載され、可能な場合は修復の手順が含められます。
   - その他のクライアント
      - このオプションには、最新の認証をサポートしていない IMAP、MAPI、POP、SMTP、レガシ Office アプリケーションなどの、基本/レガシ認証プロトコルを使用するクライアントが含まれます。

これらの条件がよく使用されるのは、マネージド デバイスを要求する場合、レガシ認証をブロックする場合、Web アプリケーションをブロックするがモバイル アプリやデスクトップ アプリは許可する場合です。

### <a name="supported-browsers"></a>サポートされているブラウザー

この設定は、すべてのブラウザーで動作します。 ただし、デバイス ポリシーを満たすため、準拠デバイスの要件と同様に、次のオペレーティング システムとブラウザーがサポートされています。

| OS | ブラウザー |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome |
| Windows 8 / 8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge、Intune Managed Browser、Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome、Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>ブラウザーに証明書のプロンプトが表示される理由

Windows 7、iOS、Android、および macOS では、Azure AD によって、デバイスが Azure AD に登録されるときにプロビジョニングされたクライアント証明書を使用してデバイスが識別されます。  ユーザーは、ブラウザーで最初にサインインするときに、証明書の選択を求められます。 ユーザーは、ブラウザーを使用する前に、この証明書を選択する必要があります。

#### <a name="chrome-support"></a>Chrome のサポート

**Windows 10 Creators Update (バージョン 1703)** 以降で Chrome をサポートするには、[Windows 10 Accounts 拡張機能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)をインストールしてください。 条件付きアクセス ポリシーでデバイス固有の詳細が必要な場合は、この拡張機能が必要です。

Chrome ブラウザーにこの拡張機能を自動的に展開するには、次のレジストリ キーを作成します。

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | 1 |
| Type | REG_SZ (文字列) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

**Windows 8.1 および 7** で Chrome をサポートするには、次のレジストリ キーを作成してください。

|    |    |
| --- | --- |
| Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | 1 |
| Type | REG_SZ (文字列) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

これらのブラウザーはデバイス認証をサポートしており、デバイスを識別してポリシーで検証することができます。 ブラウザーがプライベート モードで実行している場合、デバイス チェックは失敗します。

### <a name="supported-mobile-applications-and-desktop-clients"></a>サポートされているモバイル アプリケーションとデスクトップ クライアント

組織は、クライアント アプリとして **[モバイル アプリとデスクトップ クライアント]** を選択できます。

この設定は、以下のモバイル アプリおよびデスクトップ クライアントからのアクセス試行に影響を与えます。

| クライアント アプリ | 対象サービス | プラットフォーム |
| --- | --- | --- |
| Dynamics CRM アプリ | Dynamics CRM | Windows 10、Windows 8.1、iOS、Android |
| メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)| Office 365 Exchange Online | Windows 10 |
| アプリ用の MFA と場所のポリシー。 デバイス ベースのポリシーはサポートされていません。| 任意のマイ アプリ アプリ サービス | Android および iOS |
| Microsoft Teams Services - このコントロールは Microsoft Teams とそのすべてのクライアント アプリ (Windows デスクトップ、iOS、Android、WP、および Web クライアント) をサポートするすべてのサービスを制御する | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android、および macOS |
| Office 2016 アプリ、Office 2013 (最新の認証を使用)、[OneDrive 同期クライアント](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1、Windows 7 |
| Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、[OneDrive 同期クライアント](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word、Excel、PowerPoint、OneNote のみ)。 | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10、macOS |
| Office モバイル アプリ | Office 365 SharePoint Online | Android、iOS |
| Office Yammer アプリ | Office 365 Yammer | Windows 10、iOS、Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用) | Office 365 Exchange Online | Windows 8.1、Windows 7 |
| Outlook Mobile アプリ | Office 365 Exchange Online | Android、iOS |
| Power BI アプリ | Power BI サービス | Windows 10、Windows 8.1、Windows 7、Android、iOS |
| Skype for Business | Office 365 Exchange Online| Android、IOS |
| Visual Studio Team Services アプリ | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS、Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync クライアント

- 組織が Exchange ActiveSync クライアントを選択できるのは、ユーザーまたはグループにポリシーを割り当てるときにだけです。 **[すべてのユーザー]** 、 **[すべてのゲストと外部ユーザー]** 、または **[ディレクトリ ロール]** を選択すると、すべてのユーザーがブロックされます。
- Exchange ActiveSync クライアントに割り当てられるポリシーを作成する場合は、**Office 365 Exchange Online** が、そのポリシーに割り当てられる唯一のクラウド アプリケーションである必要があります。 
- 組織は、 **[デバイス プラットフォーム]** の条件を使用して、このポリシーの範囲を特定のプラットフォームに限定することができます。

ポリシーに割り当てられたアクセス制御で、 **[承認済みクライアント アプリを必須にする]** が使用されている場合、ユーザーは Outlook モバイル クライアントをインストールして使用するように指示されます。 **多要素認証**が必須の場合、基本認証では多要素認証がサポートされていないため、影響を受けるユーザーはブロックされます。

詳細については、次の記事を参照してください。

- [条件付きアクセスを使用してレガシ認証をブロックする](block-legacy-authentication.md)ことに関する記事
- [条件付きアクセスを使用して承認済みクライアント アプリを必須にする](app-based-conditional-access.md)ことに関する記事

### <a name="other-clients"></a>その他のクライアント

**[その他のクライアント]** を選択することで、基本認証とメール プロトコル (IMAP、MAPI、POP、SMTP など) を使用するアプリや、先進認証を使用しない以前の Office アプリに影響を及ぼすポリシーを指定できます。

## <a name="device-state-preview"></a>デバイス状態 (プレビュー)

デバイス状態の条件を使用して、組織の条件付きアクセス ポリシーから、ハイブリッド Azure AD 参加済みデバイスや、Microsoft Intune コンプライアンス ポリシーに準拠しているとマークが付けられているデバイスを除外することができます。

例: *Microsoft Azure の管理*クラウド アプリにアクセスする*すべてのユーザー*について、*アクセス制御*と**ブロック**のために、**すべてのデバイスの状態**を含め、**ハイブリッド Azure AD 参加済みのデバイス**と**デバイスは準拠としてマーク済み**を除外する。 
   - この例では、ハイブリッド Azure AD 参加済みデバイスや準拠としてマーク済みのデバイスからは、Microsoft Azure の管理へのアクセスのみを許可するポリシーが作成されます。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:許可](concept-conditional-access-grant.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
