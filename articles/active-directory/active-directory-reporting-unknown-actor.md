<properties
   pageTitle="Azure Active Directory での 'Unknown Actor' のレポート | Microsoft Azure"
   description="Azure Active Directory のレポートでの 'Unknown Actor' イベントの説明"
   services="active-directory"
   documentationCenter=""
   authors="kenhoff"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="11/09/2015"
   ms.author="kenhoff"/>

# Azure Active Directory での 'Unknown Actor' イベントのレポート

まれに、Azure AD のレポートの "Actor" または "User" フィールドで異常な値を参照する可能性があります。この動作は予期されているものであり、次の 2 つのイベントのいずれかによって発生します。

## ディレクトリで、サービス プリンシパルがユーザー コンテキストなしで動作しています

この場合、サービス プリンシパル (アプリケーション) は、実質的にユーザーとしてのサインインなしで、ディレクトリの更新を実行しています。これにより、サービス プリンシパルの ID が UPN ではなく、Actor として表示されます。次に例を示します。

![](./media/active-directory-reporting-unknown-actor/spd-actor.png)

これは既知のバグであり、解決に向けて鋭意取り組んでいます。

## イベントが処理される前に、ユーザーがディレクトリから削除されました

この場合、イベントを処理し、ユーザー名を関連付ける前に、ユーザーがディレクトリから削除されています。次に例を示します。

![](./media/active-directory-reporting-unknown-actor/unknown-actor.png)

これは既知のバグであり、解決に向けて鋭意取り組んでいます。

<!-- ![](./media/active-directory-reporting-unknown-actor/uid-actor.png) -->

<!---HONumber=Nov15_HO3-->