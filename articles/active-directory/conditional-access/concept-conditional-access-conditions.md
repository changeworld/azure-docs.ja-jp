---
title: 条件付きアクセス ポリシーの条件 - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーの条件とは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, sandeo-MSFT
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89dab74b476345e08a5b995ad04d7d512d0e3a28
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012645"
---
# <a name="conditional-access-conditions"></a>条件付きアクセス:条件

管理者は条件付きアクセスポリシー内で、リスク、デバイス プラットフォーム、場所などの条件からのシグナルを利用して、ポリシーの決定を強化できます。 

[![条件付きアクセス ポリシーを定義し、条件を指定する](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

複数の条件を組み合わせて、きめ細かで具体的な条件付きアクセス ポリシーを作成することができます。

たとえば、機密性の高いアプリケーションにアクセスする場合、管理者は、多要素認証などの他の制御に加えて、Identity Protection と場所からのサインイン リスク情報をアクセスの決定に組み込む可能性があります。

## <a name="sign-in-risk"></a>サインイン リスク

[Identity Protection](../identity-protection/overview-identity-protection.md) にアクセスできるお客様の場合、条件付きアクセス ポリシーの一部としてサインイン リスクを評価できます。 サインイン リスクは、特定の認証要求が ID 所有者によって承認されていない可能性があることを表します。 サインイン リスクの詳細については、「[リスクとは](../identity-protection/concept-identity-protection-risks.md#sign-in-risk)」と「[方法:リスク ポリシーを構成して有効にする](../identity-protection/howto-identity-protection-configure-risk-policies.md)」を参照してください。

## <a name="user-risk"></a>ユーザー リスク 

[Identity Protection](../identity-protection/overview-identity-protection.md) にアクセスできるお客様の場合、条件付きアクセス ポリシーの一部としてユーザー リスクを評価できます。 ユーザー リスクは、特定の ID またはアカウントが侵害されているおそれがあることを表します。 ユーザー リスクの詳細については、「[リスクとは](../identity-protection/concept-identity-protection-risks.md#user-linked-detections)」および「[方法: リスク ポリシーを構成して有効にする](../identity-protection/howto-identity-protection-configure-risk-policies.md)」を参照してください。

## <a name="device-platforms"></a>デバイス プラットフォーム

デバイス プラットフォームは、デバイスで実行されているオペレーティング システムによって分類されます。 Azure AD では、デバイスによって提供される、ユーザー エージェント文字列などの情報を使用してプラットフォームを識別します。 ユーザー エージェント文字列は変更できるため、この情報は未検証です。 デバイス プラットフォームは、Microsoft Intune デバイス コンプライアンス ポリシーと連携して使用するか、ブロック ステートメントの一部として使用する必要があります。 既定では、すべてのデバイス プラットフォームに適用されます。

Azure AD 条件付きアクセスは、次のデバイス プラットフォームをサポートします。

- Android
- iOS
- Windows Phone
- Windows
- macOS

**他のクライアント** 条件を使用してレガシ認証をブロックする場合は、デバイスのプラットフォーム条件も設定できます。

> [!IMPORTANT]
> Microsoft はサポートされていないデバイス プラットフォームに対して条件付きアクセス ポリシーを設定することをお勧めします。 たとえば、Linux やその他のサポートされていないクライアントから会社のリソースへのアクセスをブロックする場合は、すべてのデバイスが含まれ、サポートされているデバイス プラットフォームが除外されるデバイス プラットフォームの条件を使用し、アクセスをブロックする制御セットが付与されるポリシーを構成する必要があります。

## <a name="locations"></a>場所

組織は場所を条件として構成するときに、場所を含めるか除外するかを選択できます。 これらの名前付きの場所には、パブリック IPv4 ネットワーク情報、国、リージョンだけでなく、特定の国やリージョンにマップされていない不明な領域が含まれる場合もあります。 信頼できる場所としてマークできるのは IP 範囲のみです。

**任意の場所** を含める場合、このオプションには、構成された名前付きの場所ではなく、インターネット上の任意の IP アドレスが含まれます。 **任意の場所** を選択するときには、管理者は、**信頼されているすべての場所** または **選択した場所** を除外することを選択できます。

たとえば、組織によっては、ユーザーが物理的な本社などの信頼できる場所にあるネットワークに接続されている場合に、多要素認証を必要としない場合があります。 管理者は、本社ネットワーク用に選択された場所を除いて、すべての場所を含むポリシーを作成することも可能です。

場所に関する詳細については、「[Azure Active Directory 条件付きアクセスの場所の条件の概要](location-condition.md)」の記事を参照してください。

## <a name="client-apps"></a>クライアント アプリ

既定では、新しく作成されたすべての条件付きアクセス ポリシーは、クライアント アプリの条件が構成されていない場合でも、すべてのクライアント アプリの種類に適用されます。 

> [!NOTE]
> クライアント アプリの条件の動作は、2020 年 8 月に更新されました。 既存の条件付きアクセス ポリシーがある場合、それらは変更されません。 ただし、既存のポリシーをクリックすると、構成の切り替えが削除されており、ポリシーが適用されるクライアント アプリが選択されます。

> [!IMPORTANT]
> レガシ認証クライアントからのサインインでは MFA はサポートされず、デバイスの状態情報は Azure AD に渡されないため、条件付きアクセス許可制御によってブロックされます (MFA または準拠デバイスを求められるなど)。 レガシ認証を使用する必要があるアカウントがある場合は、それらのアカウントをポリシーから除外するか、先進認証クライアントにのみ適用するようにポリシーを構成する必要があります。

**[構成]** は切り替え可能で、 **[はい]** に設定されている場合は、選択されている項目に適用され、 **[いいえ]** に設定されている場合は、レガシ認証クライアントと先進認証クライアントを含むすべてのクライアント アプリに適用されます。 この切り替えは、2020 年 8 月より前に作成されたポリシーには表示されません。

- 先進認証クライアント
   - Browser
      - これには、SAML、WS-Federation、OpenID Connect、OAuth 機密クライアントとして登録されているサービスなどのプロトコルを使用する Web ベースのアプリケーションが含まれます。
   - モバイル アプリとデスクトップ クライアント
      -  このオプションには、Office デスクトップや Phone アプリケーションなどのアプリケーションが含まれます。
- レガシ認証クライアント
   - Exchange ActiveSync クライアント
      - この選択には Exchange ActiveSync (EAS) プロトコルのすべての使用が含まれます。
      - ポリシーによって Exchange ActiveSync の使用がブロックされると、影響を受けるユーザーには 1 通の検疫電子メールが送信されます。 この電子メールには、ブロックされた理由に関する情報が記載され、可能な場合は修復の手順が含められます。
      - 管理者は、条件付きアクセス Microsoft Graph API を使用して、サポートされているプラットフォーム (iOS、Android、Windows など) にのみポリシーを適用できます。
   - その他のクライアント
      - このオプションには、最新の認証をサポートしていない基本またはレガシ認証プロトコルを使用するクライアントが含まれます。
         - 認証済み SMTP - 電子メール メッセージを送信するために POP および IMAP クライアントで使用されます。
         - 自動検出 - Exchange Online でメールボックスを検索して接続するために Outlook および EAS のクライアントで使用されます。
         - Exchange Online PowerShell - リモート PowerShell を使用して Exchange Online に接続するために使用されます。 Exchange Online PowerShell の基本認証をブロックする場合は、Exchange Online PowerShell モジュールを使用して接続する必要があります。 手順については、「多要素認証[をConnect PowerShell Exchange Onlineする方法」を参照してください](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)。
         - Exchange Web サービス (EWS) - Outlook、Outlook for Mac、およびサードパーティ製アプリによって使用されるプログラミング インターフェイスです。
         - IMAP4 - IMAP 電子メール クライアントで使用されます。
         - MAPI over HTTP (MAPI/HTTP) - Outlook 2010 以降で使用されます。
         - オフライン アドレス帳 (OAB) - Outlook によってダウンロードおよび使用されるアドレス一覧コレクションのコピーです。
         - Outlook Anywhere (RPC over HTTP) - Outlook 2016 以前で使用されます。
         - Outlook サービス - Windows 10 用のメール/カレンダー アプリで使用されます。
         - POP3 - POP 電子メール クライアントで使用されます。
         - レポート Web サービス - Exchange Online でレポート データを取得するために使用されます。

これらの条件がよく使用されるのは、マネージド デバイスを要求する場合、レガシ認証をブロックする場合、Web アプリケーションをブロックするがモバイル アプリやデスクトップ アプリは許可する場合です。

### <a name="supported-browsers"></a>サポートされているブラウザー

この設定は、すべてのブラウザーで動作します。 ただし、デバイス ポリシーを満たすため、準拠デバイスの要件と同様に、次のオペレーティング システムとブラウザーがサポートされています。

| OS | ブラウザー |
| :-- | :-- |
| Windows 10 | Microsoft Edge、Internet Explorer、Chrome、[Firefox 91+](https://support.mozilla.org/kb/windows-sso) |
| Windows 8 / 8.1 | Internet Explorer、Chrome |
| Windows 7 | Internet Explorer、Chrome |
| iOS | Microsoft Edge、Intune Managed Browser、Safari |
| Android | Microsoft Edge、Intune Managed Browser、Chrome |
| Windows Phone | Microsoft Edge、Internet Explorer |
| Windows Server 2019 | Microsoft Edge、Internet Explorer、Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Microsoft Edge、Chrome、Safari |

これらのブラウザーはデバイス認証をサポートしており、デバイスを識別してポリシーで検証することができます。 ブラウザーがプライベート モードで実行されている場合、または Cookie が無効になっている場合、デバイスのチェックは失敗します。

> [!NOTE]
> Edge 85+ の場合、デバイス ID を適切に渡すには、ユーザーがブラウザーにサインインする必要があります。 そうしない場合、アカウントの拡張機能のない Chrome のように動作します。 Hybrid Azure AD Join シナリオでは、このサインインが自動的に行われないことがあります。 Safari はデバイスベースの条件付きアクセスでサポートされていますが、**承認済みクライアント アプリを必須にする** または **アプリの保護ポリシーを必須にする** の条件を満たすことができません。 Microsoft Edge のような管理対象ブラウザーは、承認済みクライアント アプリとアプリ保護ポリシーの要件を満たしています。

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>ブラウザーに証明書のプロンプトが表示される理由

Windows 7、iOS、Android、および macOS では、Azure AD によって、デバイスが Azure AD に登録されるときにプロビジョニングされたクライアント証明書を使用してデバイスが識別されます。  ユーザーは、ブラウザーで最初にサインインするときに、証明書の選択を求められます。 ユーザーは、ブラウザーを使用する前に、この証明書を選択する必要があります。

#### <a name="chrome-support"></a>Chrome のサポート

**Windows 10 Creators Update (バージョン 1703)** 以降で Chrome をサポートするには、[Windows 10 Accounts 拡張機能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)をインストールしてください。 条件付きアクセス ポリシーでデバイス固有の詳細が必要な場合は、この拡張機能が必要です。

Chrome ブラウザーにこの拡張機能を自動的に展開するには、次のレジストリ キーを作成します。

- パス HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist
- 名前 1
- 型 REG_SZ (文字列)
- データ ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx

**Windows 8.1 および 7** で Chrome をサポートするには、次のレジストリ キーを作成してください。

- パス HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls
- 名前 1
- 型 REG_SZ (文字列)
- データ {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}

### <a name="supported-mobile-applications-and-desktop-clients"></a>サポートされているモバイル アプリケーションとデスクトップ クライアント

組織は、クライアント アプリとして **[モバイル アプリとデスクトップ クライアント]** を選択できます。

この設定は、以下のモバイル アプリおよびデスクトップ クライアントからのアクセス試行に影響を与えます。

| クライアント アプリ | 対象サービス | プラットフォーム |
| --- | --- | --- |
| Dynamics CRM アプリ | Dynamics CRM | Windows 10、Windows 8.1、iOS、Android |
| メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)| Exchange Online | Windows 10 |
| アプリ用の MFA と場所のポリシー。 デバイス ベースのポリシーはサポートされていません。| 任意のマイ アプリ アプリ サービス | Android および iOS |
| Microsoft Teams Services - このクライアント アプリは Microsoft Teams とそのすべてのクライアント アプリ (Windows デスクトップ、iOS、Android、WP、および Web クライアント) をサポートするすべてのサービスを制御する | Microsoft Teams | Windows 10、Windows 8.1、Windows 7、iOS、Android、および macOS |
| Office 2016 アプリ、Office 2013 (最新の認証を使用)、[OneDrive 同期クライアント](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1、Windows 7 |
| Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、[OneDrive 同期クライアント](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016 (Word、Excel、PowerPoint、OneNote のみ)。 | SharePoint | macOS |
| Office 2019| SharePoint | Windows 10、macOS |
| Office モバイル アプリ | SharePoint | Android、iOS |
| Office Yammer アプリ | Yammer | Windows 10、iOS、Android |
| Outlook 2019 | SharePoint | Windows 10、macOS |
| Outlook 2016 (Office for macOS) | Exchange Online | macOS |
| Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用) | Exchange Online | Windows 8.1、Windows 7 |
| Outlook Mobile アプリ | Exchange Online | Android、iOS |
| Power BI アプリ | Power BI サービス | Windows 10、Windows 8.1、Windows 7、Android、iOS |
| Skype for Business | Exchange Online| Android、iOS |
| Visual Studio Team Services アプリ | Visual Studio Team Services | Windows 10、Windows 8.1、Windows 7、iOS、Android |

### <a name="exchange-activesync-clients"></a>Exchange ActiveSync クライアント

- 組織が Exchange ActiveSync クライアントを選択できるのは、ユーザーまたはグループにポリシーを割り当てるときにだけです。 **[すべてのユーザー]** 、 **[すべてのゲストと外部ユーザー]** 、または **[ディレクトリ ロール]** を選択すると、すべてのユーザーがポリシーの対象となります。
- Exchange ActiveSync クライアントに割り当てられるポリシーを作成する場合は、**Exchange Online** が、そのポリシーに割り当てられる唯一のクラウド アプリケーションである必要があります。 
- 組織は、 **[デバイス プラットフォーム]** の条件を使用して、このポリシーの範囲を特定のプラットフォームに限定することができます。

ポリシーに割り当てられたアクセス制御で、 **[承認済みクライアント アプリを必須にする]** が使用されている場合、ユーザーは Outlook モバイル クライアントをインストールして使用するように指示されます。 **多要素認証**、**使用条件**、または **カスタム コントロール** が必要な場合、基本認証ではこれらのコントロールがサポートされないため、影響を受けるユーザーはブロックされます。

詳細については、次の記事を参照してください。

- [条件付きアクセスを使用してレガシ認証をブロックする](block-legacy-authentication.md)ことに関する記事
- [条件付きアクセスを使用して承認済みクライアント アプリを必須にする](app-based-conditional-access.md)ことに関する記事

### <a name="other-clients"></a>その他のクライアント

**[その他のクライアント]** を選択することで、基本認証とメール プロトコル (IMAP、MAPI、POP、SMTP など) を使用するアプリや、先進認証を使用しない以前の Office アプリに影響を及ぼすポリシーを指定できます。

## <a name="device-state-preview"></a>デバイス状態 (プレビュー)
> [!CAUTION]
> **このプレビュー機能は非推奨とされます。** お客様は、以前にデバイス **の** 状態 (プレビュー) 条件を使用して実現されたシナリオを満たすために、条件付きアクセスでデバイスの条件に対してフィルターを使用する必要があります。

デバイス状態の条件を使用して、組織の条件付きアクセス ポリシーから、ハイブリッド Azure AD 参加済みデバイスや、Microsoft Intune コンプライアンス ポリシーに準拠しているとマークが付けられているデバイスを除外することができます。

例: *Microsoft Azure の管理* クラウド アプリにアクセスする *すべてのユーザー* について、*アクセス制御* と **ブロック** のために、**すべてのデバイスの状態** を含め、**ハイブリッド Azure AD 参加済みのデバイス** と **デバイスは準拠としてマーク済み** を除外する。 
   - この例では、ハイブリッド Azure AD 参加済みデバイスまたは準拠としてマーク済みのデバイスのいずれかから、Microsoft Azure の管理へのアクセスのみを許可するポリシーが作成されます。

上記のシナリオでは、次の規則device.trustType -ne "ServerAD" -または device.isCompliant -ne Trueとアクセス制御、ブロックを使用して、デバイス条件のフィルターを除く *Microsoft Azure Management* クラウド アプリにアクセスするすべてのユーザーを使用して構成できます。
- この例では、ハイブリッド Azure AD 参加済みデバイスまたは準拠としてマーク済みのデバイスのいずれかから、Microsoft Azure の管理へのアクセスのみを許可するポリシーが作成されます。

> [!IMPORTANT]
> デバイス状態とデバイスのフィルターは、条件付きアクセス ポリシーで一緒に使用することはできません。 デバイスのフィルターを使用する方が、`trustType` および `isCompliant` プロパティを介して、対象とするデバイス状態情報のサポートも含め、より詳細に対象設定できます。

## <a name="filter-for-devices"></a>デバイスのフィルター

条件付きアクセスには、デバイスのフィルターと呼ばれる新しい省略可能な条件があります。 条件としてデバイスのフィルターを構成する場合、組織は、デバイスのプロパティに対するルール式を使用して、フィルターに基づいてデバイスを含めるか除外することができます。 デバイスのフィルターのルール式は、ルール ビルダーまたはルール構文を使用して作成できます。 このエクスペリエンスは、グループの動的なメンバーシップの規則に使用されるものと似ています。 詳細については、「条件付きアクセス: デバイスのフィルター処理 [(プレビュー)」を参照してください](concept-condition-filters-for-devices.md)。

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:許可](concept-conditional-access-grant.md)

- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
