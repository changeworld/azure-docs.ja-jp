---
title: 条件付きアクセス ポリシーのクラウド アプリ、アクション、認証コンテキスト - Azure Active Directory
description: Azure AD 条件付きアクセス ポリシーのクラウド アプリ、操作、認証コンテキストとは
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/15/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a0bd64f8e3377b78f276b1b3c4bdef6f43d8c76
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308050"
---
# <a name="conditional-access-cloud-apps-actions-and-authentication-context"></a>条件付きアクセス: クラウド アプリ、アクション、認証コンテキスト

クラウド アプリ、操作、認証コンテキストは、Azure AD 条件付きアクセス ポリシーの重要なシグナルです。 管理者は、条件付きアクセス ポリシーを使用して、特定のアプリケーション、アクション、認証コンテキストにコントロールを割り当てることができます。

- 管理者は、組み込みの Microsoft アプリケーションおよび [Azure AD 統合アプリケーション](../manage-apps/what-is-application-management.md) (ギャラリー、ギャラリー以外、[アプリケーション プロキシ](../app-proxy/what-is-application-proxy.md)経由で公開されたアプリケーションなど) を含むアプリケーションの一覧から選択できます。
- 管理者は、クラウド アプリケーションではなく、**セキュリティ情報の登録** や **デバイスの登録または参加** などの [ユーザー操作](#user-actions)に基づいてポリシーを定義することを選択できます。これにより、条件付きアクセスで、これらの操作に関する制御を適用できます。
- 管理者は、[認証コンテキスト](#authentication-context-preview)を使用して、アプリケーション内に追加のセキュリティ レイヤーを提供できます。 

![条件付きアクセス ポリシーを定義し、クラウド アプリを指定する](./media/concept-conditional-access-cloud-apps/conditional-access-cloud-apps-or-actions.png)

## <a name="microsoft-cloud-applications"></a>Microsoft クラウド アプリケーション

既存の Microsoft クラウド アプリケーションの多くは、選択できるアプリケーションの一覧に含まれています。 

管理者は、次の Microsoft のクラウド アプリに条件付きアクセス ポリシーを割り当てることができます。 Office 365 や Microsoft Azure Management などの一部のアプリには、複数の関連する子アプリやサービスが含まれています。 アプリは継続的に追加されるため、次の一覧はすべてを網羅したものではなく、変更されることがあります。

- [Office 365](#office-365)
- Azure Analysis Services
- Azure DevOps
- Azure Event Hubs
- Azure Service Bus
- [Azure SQL Database と Azure Synapse Analytics](../../azure-sql/database/conditional-access-configure.md)
- Common Data Service
- Microsoft Application Insights Analytics
- [Microsoft Azure Information Protection](/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- [Microsoft Azure Management](#microsoft-azure-management)
- Microsoft Azure Subscription Management
- Microsoft Defender for Cloud Apps
- Microsoft Commerce Tools Access Control Portal
- Microsoft Commerce Tools Authentication Service
- Microsoft フォーム
- Microsoft Intune
- [Microsoft Intune Enrollment](/intune/enrollment/multi-factor-authentication)
- Microsoft Planner
- Microsoft Power Apps
- Microsoft Power Automate
- Microsoft Search in Bing
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Exchange Online
- SharePoint
- Yammer
- Office Delve
- Office Sway
- Outlook Groups
- Power BI サービス
- Project Online
- Skype for Business Online
- 仮想プライベート ネットワーク (VPN)
- Windows Defender ATP

> [!IMPORTANT]
> 条件付きアクセスで使用できるアプリケーションは、オンボードと検証のプロセスを経ています。 この一覧にすべての Microsoft アプリが含まれているわけではありません。多くはバックエンド サービスであり、ポリシーを直接適用することは想定されていないためです。 欠けているアプリケーションを探している場合は、特定のアプリケーション チームに問い合わせるか、または [UserVoice](https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789) で要求を発行することができます。

### <a name="office-365"></a>Office 365

Microsoft 365 では、Exchange、SharePoint、Microsoft Teams などのクラウドベースの生産性およびコラボレーション サービスが提供されます。 Microsoft 365 クラウド サービスは、スムーズに共同作業を行うために緊密に統合されています。 Microsoft Teams などの一部のアプリは SharePoint や Exchange などの他のアプリに依存しているため、この統合により、ポリシーの作成時に混乱が生じる可能性があります。

Office 365 スイートにより、これらのサービスを一度にすべてターゲットにすることができます。 [サービスの依存関係](service-dependencies.md)に関する問題を回避するために、個々のクラウド アプリをターゲットにするのではなく、新しい Office 365 スイートを使用することをお勧めします。 

このアプリケーション グループをターゲットにすると、整合性のないポリシーや依存関係のために発生する可能性のある問題を回避するのに役立ちます。 たとえば、Exchange Online アプリは、メール、予定表、連絡先情報などの従来の Exchange Online データに関連付けられています。 関連するメタデータは、検索などのさまざまなリソースを通して公開される可能性があります。 すべてのメタデータが意図したとおりに確実に保護されるようにするために、管理者は Office 365 アプリにポリシーを割り当てる必要があります。

管理者は、必要に応じて特定のアプリをポリシーから除外する (つまり、Office 365 スイートをポリシーに含め、特定のアプリを除外する) ことができます。

Office 365 クライアント アプリには、次の主要なアプリケーションが含まれています。

   - Microsoft フォーム
   - Microsoft Stream
   - Microsoft To-Do
   - Microsoft Teams
   - Exchange Online
   - SharePoint Online
   - Microsoft 365 Search サービス
   - Yammer
   - Office Delve
   - Office Online
   - Office.com
   - OneDrive
   - Power Automate
   - Power Apps
   - Skype for Business Online
   - Sway

### <a name="microsoft-azure-management"></a>Microsoft Azure の管理

Microsoft Azure の管理アプリケーションには、複数のサービスが含まれています。 

   - Azure portal
   - Azure Resource Manager プロバイダー
   - クラシック デプロイ モデル API
   - Azure PowerShell
   - Azure CLI
   - Visual Studio サブスクリプション管理者ポータル
   - Azure DevOps
   - Azure Data Factory ポータル

> [!NOTE]
> Microsoft Azure Management アプリケーションは、Azure Resource Manager API を呼び出す Azure PowerShell に適用されます。 Microsoft Graph を呼び出す Azure AD PowerShell には適用されません。

### <a name="other-applications"></a>他のアプリケーション

管理者は、Azure AD に登録された任意のアプリケーションを条件付きアクセス ポリシーに追加できます。 これらのアプリケーションには次が含まれます。 

- [Azure AD アプリケーション プロキシ](../app-proxy/what-is-application-proxy.md)経由で公開されるアプリケーション
- [ギャラリーから追加されたアプリケーション](../manage-apps/add-application-portal.md)
- [ギャラリーにないカスタム アプリケーション](../manage-apps/view-applications-portal.md)
- [アプリ配信コントローラーとネットワーク経由で公開されるレガシ アプリケーション](../manage-apps/secure-hybrid-access.md)
- [パスワードに基づくシングル サインオン](../manage-apps/configure-password-single-sign-on-non-gallery-applications.md)を使用するアプリケーション

> [!NOTE]
> 条件付きアクセス ポリシーでは、サービスにアクセスするための要件が設定されるため、クライアント (パブリック/ネイティブ) アプリケーションにそれを適用することはできません。 つまり、ポリシーはクライアント (パブリック/ネイティブ) アプリケーションに直接設定されるのではなく、クライアントがサービスを呼び出すときに適用されます。 たとえば、SharePoint サービスで設定したポリシーは、SharePoint を呼び出すクライアントに適用されます。 Exchange で設定されたポリシーは、Outlook クライアントを使用して電子メールにアクセスしようとしたときに適用されます。 このため、クラウド アプリの選択で、クライアント (パブリック/ネイティブ) アプリケーションを選択できず、テナントに登録されているクライアント (パブリック/ネイティブ) アプリケーションのアプリケーション設定で、[条件付きアクセス] オプションを選択できません。 

## <a name="user-actions"></a>ユーザー操作

ユーザー操作とは、ユーザーが実行できるタスクです。 現在、条件付きアクセスでは 2 つのユーザー操作がサポートされています。 

- **セキュリティに関する情報の登録**: このユーザー操作により、統合された登録が有効になったユーザーが自分のセキュリティに関する情報を登録しようとすると、条件付きアクセス ポリシーが適用されます。 詳細情報については、「[統合されたセキュリティ情報の登録](../authentication/concept-registration-mfa-sspr-combined.md)」を参照してください。

- **デバイスの登録または参加**: このユーザー操作により、管理者は、ユーザーがデバイスを Azure AD に [登録](../devices/concept-azure-ad-register.md)するか、または [参加](../devices/concept-azure-ad-join.md)させたときに条件付きアクセス ポリシーを適用できます。 これにより、現在存在するテナント全体のポリシーではなく、デバイスを登録するか参加させるための多要素認証をきめ細かく構成できます。 このユーザー操作には、次の 3 つの重要な考慮事項があります。 
   - `Require multi-factor authentication` は、このユーザー操作で使用できる唯一のアクセス制御であり、それ以外はすべて無効になっています。 この制限により、Azure AD デバイス登録に依存している、あるいは Azure AD デバイス登録に適用されないアクセス制御との競合を防ぐことができます。 
   - `Client apps`、`Filters for devices`、`Device state` の各条件は、条件付きアクセス ポリシーの適用を Azure AD デバイス登録に依存しているため、このユーザー操作では使用できません。
   - このユーザー操作によって条件付きアクセスポリシーを有効にした場合、 **[Azure Active Directory]**  >  **[デバイス]**  >  **[デバイス設定]**  - `Devices to be Azure AD joined or Azure AD registered require Multi-Factor Authentication` を **[いいえ]** に設定する必要があります。 そうしないと、このユーザー操作での条件付きアクセス ポリシーが適切に適用されません。 このデバイス設定に関するより詳細な情報は、「[デバイス設定の構成](../devices/device-management-azure-portal.md#configure-device-settings)」に記載されています。 

## <a name="authentication-context-preview"></a>認証コンテキスト (プレビュー)

認証コンテキストを使用すると、アプリケーションのデータとアクションをさらにセキュリティで保護することができます。 これらのアプリケーションには、独自のカスタム アプリケーション、カスタム基幹業務 (LOB) アプリケーション、SharePoint のようなアプリケーション、Microsoft Defender for Cloud Apps によって保護されるアプリケーションが該当します。 

たとえば、組織は、ランチ メニューや秘密の BBQ ソース レシピなどのファイルを SharePoint サイト内に保持することができます。 すべてのユーザーがランチ メニュー サイトにアクセスできる場合がありますが、秘密の BBQ ソース レシピにアクセスできるユーザーは、管理対象デバイスからアクセスして、特定の利用規約に同意する必要があります。

### <a name="configure-authentication-contexts"></a>認証コンテキストの構成

認証コンテキストは、Azure portal の **[Azure Active Directory]**  >  **[セキュリティ]**  >  **[条件付きアクセス]**  >  **[認証コンテキスト]** の下で管理されます。

![Azure portal で認証コンテキストを管理する](./media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-get-started.png)

> [!WARNING]
> * プレビュー期間中は、認証コンテキスト定義を削除することはできません。 
> * プレビューでは、Azure portal の認証コンテキスト定義は合計 25 個に制限されています。

新しい認証コンテキスト定義を作成するには、Azure portal で **[新しい認証コンテキスト]** を選択します。 次の属性を構成します。

- **[表示名]** は、Azure AD と認証コンテキストを使用するアプリケーション間で認証コンテキストを識別するために使用される名前です。 必要な認証コンテキストの数を減らすために、"信頼されたデバイス" のようなリソース間で使用できる名前をお勧めします。 セットを小さくすると、リダイレクトの回数が制限され、エンドツーエンドのユーザー エクスペリエンスが向上します。
- **[説明]** には Azure AD 管理者が使用するポリシーと、リソースに認証コンテキストを適用するポリシーに関する詳細情報が記載されています。
- **[アプリに発行]** チェックボックスをオンにすると、認証コンテキストがアプリに対して公開され、割り当て可能になります。 オフにした場合、認証コンテキストはダウンストリーム リソースに使用できなくなります。 
- **[ID]** は読み取り専用で、トークンとアプリで要求固有の認証コンテキスト定義に使用されます。 トラブルシューティングおよび開発のユースケース用として、ここに一覧表示されています。 

#### <a name="add-to-conditional-access-policy"></a>条件付きアクセス ポリシーに追加する

管理者は、 **[割り当て]**  >  **[クラウド アプリまたはアクション]** で **[このポリシーが適用される対象を選択する]** メニューから **[認証コンテキスト]** を選択することによって、条件付きアクセス ポリシーで、公開されている認証コンテキストを選択できます。

:::image type="content" source="media/concept-conditional-access-cloud-apps/conditional-access-authentication-context-in-policy.png" alt-text="ポリシーへの条件付きアクセス認証コンテキストの追加":::

### <a name="tag-resources-with-authentication-contexts"></a>認証コンテキストを含むリソースをタグ付けする 

アプリケーションでの認証コンテキストの使用の詳細については、次の記事を参照してください。

- [SharePoint サイトを保護するための Microsoft Information Protection 秘密度ラベル](/microsoft-365/compliance/sensitivity-labels-teams-groups-sites#more-information-about-the-dependencies-for-the-authentication-context-option)
- [Microsoft Defender for Cloud Apps](/cloud-app-security/session-policy-aad?branch=pr-en-us-2082#require-step-up-authentication-authentication-context)
- [カスタム アプリケーション](../develop/developer-guide-conditional-access-authentication-context.md)

## <a name="next-steps"></a>次のステップ

- [条件付きアクセス:条件](concept-conditional-access-conditions.md)
- [Conditional Access common policies](concept-conditional-access-policy-common.md) (条件付きアクセスの一般的なポリシー)
- [クライアント アプリケーションの依存関係](service-dependencies.md)
