<properties
   pageTitle="アクセス レビューを完了する方法 | Microsoft Azure"
   description="Azure AD Privileged Identity Management でのアクセス レビューの開始後に、レビューを完了して結果を確認する方法について説明します"
   services="active-directory"
   documentationCenter=""
   authors="kgremban"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="06/30/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management でアクセス レビューを完了する方法


[セキュリティ レビューが開始](active-directory-privileged-identity-management-how-to-start-security-review.md)されると、特権ロール管理者は特権アクセスの状況を確認できるようになります。ユーザーにアクセスを確認するよう求める電子メールは Azure AD Privileged Identity Management (PIM) によって自動的に送信されます。電子メールが届かなかったユーザーがいる場合は、セキュリティ管理者が[セキュリティ レビューを実行する方法](active-directory-privileged-identity-management-how-to-perform-security-review.md)についての指示をユーザーに送信します。

セキュリティ レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## セキュリティ レビューの管理

1. [Azure ポータル](https://portal.azure.com/)に移動し、ダッシュボードで **Azure AD Privileged Identity Management** アプリケーションを選択します。
2. ダッシュボードの **[アクセス レビュー]** セクションをクリックします。
3. 管理するアクセス レビューを選択します。

アクセス レビューの [詳細] ブレードには、このレビューを管理するためのオプションが多数あります。

![PIM アクセス レビューのボタン - スクリーンショット][1]

### リマインダーを送信

ユーザーが各自をレビューできるようにアクセス レビューが設定された場合、**[リマインダーを送信]** をクリックすると通知が送信されます。

### Stop

すべてのアクセス レビューには終了日が設定されていますが、**[停止]** ボタンを使用するとレビューを早期に終了することができます。この時点までにレビューを行っていないユーザーは、レビューが停止されるとレビューを行うことができなくなります。レビューを停止後に再開することはできません。

### 適用

終了日に達するか手動で停止されたためにアクセス レビューが完了した後で、**[適用]** ボタンをクリックすると、レビューの結果が適用されます。レビューでユーザーのアクセスが拒否された場合は、この手順によりそのユーザーのロール割り当てが削除されます。

### エクスポート

セキュリティ レビューの結果を手動で適用する場合は、レビューをエクスポートできます。**[エクスポート]** ボタンをクリックすると、CSV ファイルのダウンロードが開始されます。Excel など、CSV ファイルを開くことができるプログラムで結果を管理できます。

### 削除

そのレビューが今後も必要なければ、削除します。**[削除]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。

> [AZURE.IMPORTANT] 削除する前に警告は表示されないため、そのレビューを削除してよいのか確認してください。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]


<!--Image references-->

[1]: ./media/active-directory-privileged-identity-management-how-to-complete-review/PIM_review_buttons.png

<!---HONumber=AcomDC_0706_2016-->