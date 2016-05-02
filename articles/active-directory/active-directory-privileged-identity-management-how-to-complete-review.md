<properties
   pageTitle="セキュリティ レビューを完了する方法 |Microsoft Azure"
   description="Azure Privileged Identity Management アプリケーションでレビューを完了する方法について説明します。"
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

# Azure AD Privileged Identity Management でセキュリティ レビューを完了する方法


[セキュリティ レビューが開始](active-directory-privileged-identity-management-how-to-start-security-review.md)されると、セキュリティ管理者は特権アクセスの状況を確認できます。ユーザーにアクセスを確認するよう求める電子メールは Azure AD Privileged Identity Management (PIM) によって自動的に送信されます。電子メールが届かなかったユーザーがいる場合は、セキュリティ管理者が[セキュリティ レビューを実行する方法](active-directory-privileged-identity-management-how-to-perform-security-review.md)についての指示をユーザーに送信します。

セキュリティ レビューの期間が終わった後、またはすべてのユーザーが自己レビューを完了した後に、この記事に記載されている手順に従って、レビューを管理し、結果を表示することができます。

## セキュリティ レビューの管理

1. [Azure ポータル](https://portal.azure.com/)に移動し、ダッシュボードで **Azure AD Privileged Identity Management** アプリケーションを選択します。
2. ダッシュボードの **[セキュリティ レビュー]** セクションをクリックします。**[セキュリティ レビュー]** ブレードが表示されます。
3. 管理するセキュリティ レビューを選択します。セキュリティ レビューの詳細ブレードが表示されます。

セキュリティ レビューの詳細ブレードには、そのレビューを管理するためのオプションが 3 つあります。レビューの完了、詳細のエクスポート、詳細の削除のいずれかを選択できます。

### レビューの完了または停止

ユーザーがアクセスを拒否された場合、セキュリティ管理者はレビューを完了して、ディレクトリからそのユーザー ロール割り当てを削除できます。**[レビューを終了する]** ボタンをクリックすると、レビューがアーカイブされます。

### レビューのエクスポート

セキュリティ レビューの結果を手動で適用する場合は、レビューをエクスポートできます。**[エクスポートする]** ボタンをクリックすると、CSV ファイルのダウンロードが開始されます。Excel など、CSV ファイルを開くことができるプログラムで結果を管理できます。

### レビューの削除

> [AZURE.IMPORTANT] 削除する前に警告は表示されないため、そのレビューを削除してよいのか確認してください。

そのレビューが今後も必要なければ、削除します。**[削除する]** ボタンをクリックすると、レビューが PIM アプリケーションから削除されます。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0420_2016-->