---
title: Azure Active Directory の条件付きアクセス設定に関するリファレンス | Microsoft Docs
description: Azure Active Directory の条件付きアクセス ポリシーでサポートされる設定の概要を説明します。
services: active-directory.
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 0055968e4333f5ac7653a37fcd728b040024ee55
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629849"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Azure Active Directory の条件付きアクセス設定に関するリファレンス

[Azure Active Directory (Azure AD) の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)を利用し、許可されたユーザーがリソースにアクセスする方法を制御できます。   

この記事では、条件付きアクセス ポリシーの次の構成オプションに対するサポート情報が提供されます。 

- クラウド アプリケーションの割り当て

- デバイス プラットフォームの条件 

- クライアント アプリケーションの条件

- 承認されたクライアント アプリケーションの要件


これらが探している情報でない場合には、この記事の最後にコメントを入力してください。

## <a name="cloud-apps-assignments"></a>クラウド アプリの割り当て

条件付きアクセス ポリシーを使用して、ユーザーが[クラウド アプリ](conditions.md#cloud-apps)にアクセスする方法を制御します。 条件付きアクセス ポリシーを構成する際に、クラウド アプリを少なくとも 1 つ選択する必要があります。 

![ポリシーに対してクラウド アプリを選択する](./media/technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft クラウド アプリケーション

Microsoft から、次のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure Information Protection - [詳細](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (OneDrive for Business および Project Online を含む)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>他のアプリケーション 

Microsoft クラウド アプリに加えて、次の種類のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。

- Azure AD に接続しているアプリケーション

- フェデレーションが事前に統合されているサービスとしてのソフトウェア (SaaS) アプリケーション

- パスワード シングル サインオン (SSO) を使うアプリケーション

- 基幹業務アプリケーション

- Azure AD アプリケーション プロキシを使うアプリケーション


## <a name="device-platform-condition"></a>デバイス プラットフォームの条件

条件付きアクセス ポリシーでは、デバイス プラットフォームの条件を構成し、ポリシーをクライアント上のオペレーティング システムに関連付けることができます。 Azure AD 条件付きアクセスは、次のデバイス プラットフォームをサポートします。

- Android

- iOS

- Windows Phone

- Windows

- macOS


![クライアント OS にアクセス ポリシーを関連付ける](./media/technical-reference/41.png)





## <a name="client-apps-condition"></a>クライアント アプリの条件 

条件付きアクセス ポリシー内で、[クライアント アプリ](conditions.md#client-apps)の条件を構成して、アクセス試行を開始したクライアント アプリにポリシーを関連付けることができます。 クライアント アプリの条件を設定し、次の種類のクライアント アプリからアクセス試行が行われたときに、アクセスを許可またはブロックします。

- [ブラウザー] ボタンを
- モバイル アプリとデスクトップ アプリ

![クライアント アプリのアクセスの制御](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>サポートされているブラウザー 

条件付きアクセス ポリシー内で、クライアント アプリとして **[ブラウザー]** を選択できます。

![サポートされているブラウザーのアクセスの制御](./media/technical-reference/05.png)

この設定は、すべてのブラウザーで動作します。 ただし、デバイス ポリシーを満たすため、準拠デバイスの要件と同様に、次のオペレーティング システムとブラウザーがサポートされています。


| OS                     | ブラウザー                            | サポート     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer、Microsoft Edge、Chrome     | ![○][1] |
| Windows 8 / 8.1        | Internet Explorer、Chrome           | ![○][1] |
| Windows 7              | Internet Explorer、Chrome           | ![○][1] |
| iOS                    | Safari、Intune Managed Browser      | ![○][1] |
| Android                | Chrome、Intune Managed Browser      | ![○][1] |
| Windows Phone          | Internet Explorer、Microsoft Edge             | ![○][1] |
| Windows Server 2016    | Internet Explorer、Microsoft Edge             | ![○][1] |
| Windows Server 2016    | Chrome                              | 近日対応予定 |
| Windows Server 2012 R2 | Internet Explorer、Chrome           | ![○][1] |
| Windows Server 2008 R2 | Internet Explorer、Chrome           | ![○][1] |
| macOS                  | Chrome、Safari                      | ![○][1] |



#### <a name="chrome-support"></a>Chrome のサポート

**Windows 10 Creators Update (バージョン 1703)** 以降で Chrome をサポートするには、[この拡張機能](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)をインストールしてください。

Chrome ブラウザーにこの拡張機能を自動的に展開するには、次のレジストリ キーを作成します。

|    |    |
|--- | ---|
|Path | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
|Name | 1 |
|type | REG_SZ (文字列) |
|データ | ppnbnpeolgkicgegkbkbjmhlideopiji; https://clients2.google.com/service/update2/crx

**Windows 8.1 および 7** で Chrome をサポートするには、次のレジストリ キーを作成してください。

|    |    |
|--- | ---|
|Path | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
|Name | 1 |
|type | REG_SZ (文字列) |
|データ | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}|

これらのブラウザーはデバイス認証をサポートしており、デバイスを識別してポリシーで検証することができます。 ブラウザーがプライベート モードで実行している場合、デバイス チェックは失敗します。 


### <a name="supported-mobile-applications-and-desktop-clients"></a>サポートされているモバイル アプリケーションとデスクトップ クライアント

条件付きアクセス ポリシー内で、クライアント アプリとして **[モバイル アプリとデスクトップ クライアント]** を選択できます。


![サポートされているモバイル アプリまたはデスクトップ クライアントのアクセスの制御](./media/technical-reference/06.png)


この設定は、以下のモバイル アプリおよびデスクトップ クライアントからのアクセス試行に影響を与えます。 


|クライアント アプリ|対象サービス|プラットフォーム|
|---|---|---|
|Azure リモート アプリ|Azure Remote App サービス|Windows 10、Windows 8.1、Windows 7、iOS、Android、Mac OS X|
|Dynamics CRM アプリ|Dynamics CRM|Windows 10、Windows 8.1、iOS、Android|
|メール/カレンダー/People アプリ、Outlook 2016、Outlook 2013 (先進認証を使用)|Office 365 Exchange Online|Windows 10|
|アプリ用の MFA と場所のポリシー。 デバイス ベースのポリシーはサポートされていません。 |任意のマイ アプリ アプリ サービス|Android および iOS|
|Microsoft Teams Services - このコントロールは Microsoft Teams とそのすべてのクライアント アプリ (Windows デスクトップ、iOS、Android、WP、および Web クライアント) をサポートするすべてのサービスを制御する|Microsoft Teams|Windows 10、Windows 8.1、Windows 7、iOS、Android、および macOS |
|Office 2016 アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照)|Office 365 SharePoint Online|Windows 8.1、Windows 7|
|Office 2016 アプリ、ユニバーサル Office アプリ、Office 2013 (最新の認証を使用)、OneDrive 同期クライアント ([メモ](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)参照)。Office Groups および SharePoint アプリについては将来サポート予定|Office 365 SharePoint Online|Windows 10|
|Office 2016 for macOS (Word、Excel、PowerPoint、OneNote のみ)。 OneDrive for Business は将来サポート予定|Office 365 SharePoint Online|Mac OS X|
|Office モバイル アプリ|Office 365 SharePoint Online|Android、iOS|
|Office Yammer アプリ|Office 365 Yammer|Windows 10、iOS、Android|
|Outlook 2016 (Office for macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016、Outlook 2013 (先進認証を使用)、Skype for Business (先進認証を使用)|Office 365 Exchange Online|Windows 8.1、Windows 7|
|Outlook Mobile アプリ|Office 365 Exchange Online|Android、iOS|
|PowerBI アプリ|PowerBI サービス|Windows 10、Windows 8.1、Windows 7、Android、iOS|
|Skype for Business|Office 365 Exchange Online|Android、IOS |
|Visual Studio Team Services アプリ|Visual Studio Team Services|Windows 10、Windows 8.1、Windows 7、iOS、Android|



## <a name="approved-client-app-requirement"></a>承認されたクライアント アプリケーションの要件 

条件付きアクセス ポリシー内で、選択したクラウド アプリに対しては承認されたクライアント アプリからアクセス試行されるように要求することができます。 

![承認されたクライアント アプリのアクセスの制御](./media/technical-reference/21.png)

この設定は、以下のクライアント アプリに適用されます。


- Microsoft Intune Managed Browser
- Microsoft PowerBI
- Microsoft Invoicing
- Microsoft Launcher
- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype for Business
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**解説**

- 承認されたクライアント アプリは、Intune モバイル アプリケーション管理機能をサポートしています。

- **[承認されたクライアント アプリが必要です]** 要件:

    - [デバイス プラットフォームの条件](#device-platforms-condition)については iOS と Android のみをサポートしています。


## <a name="next-steps"></a>次の手順

- 条件付きアクセスの概要については、「[Azure Active Directory の条件付きアクセス](../active-directory-conditional-access-azure-portal.md)」をご覧ください。
- 環境に適用する条件付きアクセス ポリシーを構成する準備ができたら、[Azure Active Directory の条件付きアクセスの推奨プラクティス](best-practices.md)に関するページをご覧ください。



<!--Image references-->
[1]: ./media/technical-reference/01.png


