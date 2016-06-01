<properties
   pageTitle="Azure AD Privileged Identity Management セキュリティ ウィザード"
   description="Azure Active Directory Privileged Identity Management 拡張機能を初めて使用するときは、セキュリティ ウィザードが表示されます。この記事では、ウィザードを使用する手順について説明します。"
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
   ms.date="04/15/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management セキュリティ ウィザード

組織内で Azure Privileged Identity Management (PIM) を初めて実行するユーザーには、ウィザードが表示されます。このウィザードを使用すると、特権 ID のセキュリティ リスクと、PIM を使用してそのリスクを軽減する方法がわかります。既存のロール割り当てを後で変更する場合は、ウィザードで実行する必要はありません。

## ウィザードの内容

組織で PIM の使用を開始する前の段階では、すべてのロール割り当ては永続的な割り当てになっています。そのため、ユーザーが現在その権限を必要としていない場合でも、常にロールは割り当てられている状態です。ウィザードの最初の手順には、高い権限を持つロールの一覧と、それらのロールに割り当てられたユーザーの数が表示されます。ロールの中によく知らないものがある場合は、そのロールの詳細を表示して、ユーザーを詳しく確認できます。

ウィザードの 2 番目の手順では、管理者のロール割り当てを変更できます。

> [AZURE.WARNING] ここで重要なのは、グローバル管理者が少なくとも 1 人存在し、組織アカウント (Microsoft アカウントではない) を持つセキュリティ管理者が複数存在していることです。セキュリティ管理者が 1 人しかいない場合、そのアカウントが削除されると、組織は PIM を管理できなくなります。また、ユーザーが Microsoft アカウントを使用している場合は、ロールの割り当てを一時的な割り当てに変更しないでください。そのロールのアクティブ化に MFA を要求すると、ユーザーはロールからロックアウトされます。


変更を行った後は、ウィザードは表示されなくなります。ウィザードを実行したユーザーや他のセキュリティ管理者が次に PIM を使用するときには、PIM ダッシュボードが表示されます。

- ロールに対するユーザーの追加や削除、または永続的から一時的への割り当ての変更を行う場合は、「[ユーザー ロールを追加または削除する方法](active-directory-privileged-identity-management-how-to-add-role-to-user.md)」を参照してください。
- PIM を管理するためのアクセス権を付与するユーザーを増やす場合は、「[PIM へのアクセス権を付与する方法](active-directory-privileged-identity-management-how-to-give-access-to-pim.md)」を参照してください。



## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0518_2016-->