<properties
   pageTitle="Azure Privileged Identity Management: ロールをアクティブ化する方法"
   description="Azure Privileged Identity Management 拡張機能で特権 ID のロールをアクティブ化する方法について説明します。"
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

# Azure Privileged Identity Management: ロールをアクティブ化または非アクティブ化する方法

## ロールのアクティブ化または非アクティブ化

1. [Azure ポータル](https://portal.azure.com/)にログインします。
2. 「[Azure Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)」の手順に従って、Azure PIM を Azure ポータル ダッシュボードに配置します。
3. セキュリティ ウィザードの手順が完了すると、Azure PIM のメイン メニューが表示されます。
4. **[ロールのアクティブ化]** をクリックします。
5. 割り当てられているロールの一覧が表示されます。
6. アクティブ化するロールをクリックします。
7. **[アクティブ化]** をクリックします。**[ロール アクティブ化要求]** ブレードが表示されます。
8. テキスト フィールドにアクティブ化要求の理由を入力します。
9. **[OK]** をクリックします。ロールがアクティブ化されます。
10. ロールをアクティブ化した後は、**[非アクティブ化]** をクリックしてロールを非アクティブ化できます。さらに、「[ユーザー ロールの追加または削除](active-directory-privileged-identity-management-how-to-add-role-to-user.md)」の手順を使用して、ロールをユーザーから削除することもできます。


ロールのアクティブ化の設定の詳細については、「[How to Configure Role Activation (ロールのアクティブ化の構成方法)](active-directory-privileged-identity-management-how-to-configure-role-activation.md)」を参照してください。

ロール アクティブ化設定に固有のセキュリティ通知の詳細については、「[セキュリティ通知の構成方法](active-directory-privileged-identity-management-how-to-configure-security-alerts)」を参照してください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0204_2016-->