<properties
   pageTitle="Azure AD Privileged Identity Management: 多要素認証を要求する方法"
   description="Azure Active Directory Privileged Identity Management 拡張機能で特権 ID の多要素認証 (MFA) を要求する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/08/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: MFA を要求する方法

すべての管理者に多要素認証を要求することをお勧めします。

## Azure AD Privileged Identity Management での MFA の要求

PIM 管理者としてサインインすると、特権アカウントで多要素認証 (MFA) を要求する必要があることを示す通知を受け取ります。PIM ダッシュボードでセキュリティ通知をクリックすると、MFA を必要とする管理者アカウントの一覧が新しいブレードに表示されます。複数のロールを選択して MFA を要求してから **[修正]** ボタンをクリックするか、個別のロールの横にある省略記号をクリックしてから **[修正]** ボタンをクリックします。

さらに、ダッシュボードの [ロール] セクションで特定のロールをクリックし、ロール ブレードで **[設定]** をクリックして多要素認証の **[有効]** を選択することでロールの MFA を有効にすることによって、ロールによる MFA 要件を変更できます。

## Azure AD PIM で MFA を検証する方法

> [AZURE.IMPORTANT] 現在、Microsoft アカウント (@outlook.com、@live.com、@hotmail.com など) では Azure MFA に登録することはできないため、高い特権ロールの一時的な管理者として許可されません。Microsoft アカウントを使用してワークロードの管理を続行する必要がある場合は、ここで永続的な管理者に変換してください。

ユーザーがロールをアクティブ化するときに MFA を検証するためのオプションは 2 つあります。

最も簡単な方法は、特権ロールをアクティブ化するユーザーの Azure MFA を利用することです。これを行うには、まず、必要に応じてそのユーザーにライセンスが付与されており、Azure MFA に登録されていることを確認します。この方法の詳細については、「[クラウドでの Azure Multi-Factor Authentication Server の概要](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md#assigning-an-azure-mfa-azure-ad-premium-or-enterprise-mobility-license-to-users)」を参照してください。ユーザーのサインイン時に MFA を適用するように Azure AD を構成することをお勧めしますが、必須ではないことに注意してください。MFA は Azure AD PIM 自体で確認されるためです。

また、ユーザーをオンプレミスで認証する場合は、ID プロバイダーで MFA を行うようにすることができます。たとえば、Azure AD にアクセスする前にスマートカード ベースの認証を要求するように AD フェデレーション サービスを構成した場合は、「[Azure Multi-Factor Authentication および AD FS を使用したクラウド リソースのセキュリティ保護](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md)」の手順に従って、Azure AD に要求を送信するように AD FS を構成します。ユーザーがロールをアクティブ化しようとしたときに、Azure AD PIM は、適切な要求を受信してからユーザーに対して MFA が既に検証済みであることを受け入れます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0316_2016-->