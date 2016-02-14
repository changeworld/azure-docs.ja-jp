<properties
   pageTitle="Azure Privileged Identity Management: MFA を要求する方法"
   description="Azure Privileged Identity Management 拡張機能で特権 ID の MFA (多要素認証) を要求する方法について説明します。"
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
   ms.date="01/21/2016"
   ms.author="kgremban"/>

# Azure Privileged Identity Management: MFA を要求する方法

すべての管理者に多要素認証を要求することを強くお勧めします。

##Azure Privileged Identity Management での MFA の要求
PIM 管理者としてログインすると、特権アカウントで多要素認証 (MFA) を要求する必要があることを示す通知を受け取ります。PIM ダッシュボードでセキュリティ通知をクリックすると、MFA を必要とする管理者アカウントの一覧が新しいブレードに表示されます。複数のロールを選択して MFA を要求してから **[修正]** ボタンをクリックするか、個別のロールの横にある省略記号をクリックしてから **[修正]** ボタンをクリックします。

さらに、ダッシュボードの [ロール] セクションで特定のロールをクリックし、ロール ブレードで **[設定]** をクリックして多要素認証の **[有効]** を選択することでロールの MFA を有効にすることによって、ロールによる MFA 要件を変更できます。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->