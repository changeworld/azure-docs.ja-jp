<properties
   pageTitle="セキュリティ レビューを実行する方法 | Microsoft Azure"
   description="Azure Privileged Identity Management 拡張機能で特権 ID にロールを追加する方法について説明します。"
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
   ms.date="03/17/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management: セキュリティ レビューの実行方法


[セキュリティ レビューが開始](active-directory-privileged-identity-management-how-to-start-security-review.md)されていると、非常に簡単に特権付きアクセスを確認できます。

## レビュー担当者の場合: アクセスの許可または拒否

### 自分がレビューする
1. PIM のメイン メニューで、**[管理アクセスをレビューする]** をクリックします。セキュリティ レビューの一覧が表示されます。
2. 一覧でアクセスを変更する**ユーザー**を選択します。注: アクセスが実際に変更されます。このプロセスは単に、ロールのアクセスを変更するユーザーのチェックリストを作成します。少なくとも 1 人のユーザーを選択すると、**[アクセスを許可]** および **[アクセスを拒否]** ボタンが有効になります。
3. 選択したユーザーの **[アクセスを許可]** または **[アクセスを拒否]** をクリックします。通知が Azure ポータルのメイン メニューに表示され、レビューの一覧が表示されなくなります。**[Azure AD ロールのレビュー]** ブレードを閉じます。

### 自己レビュー
1. ユーザーは、自分のアクセスをレビューする必要があることを示す電子メールを受け取ります。電子メールには、Azure ポータルにログインするためのリンクが含まれます。
2. ログインすると、ユーザーは **[アクセスを許可]** または **[アクセスを拒否]** ボタンをクリックして自分のアクセスを許可または拒否できます。名前が一覧に表示されなくなります。

## レビュー マネージャーの場合: セキュリティ レビューの管理

## レビューの完了または停止
1. PIM ダッシュボードに戻ります。
2. **[セキュリティ レビュー]** ボックスの一覧で、完了するセキュリティ レビューをクリックします。セキュリティ レビューの詳細ブレードが表示されます。
3. **[レビューを停止]** クリックして、レビューを完了または停止します。レビューが保存されて、ブレードが表示されなくなります。

## レビューのエクスポート
Excel または CSV ファイルを使用できる他のプログラムで使用するために、レビューをエクスポートできます。

1. PIM ダッシュボードに戻ります。
2. ダッシュボードの **[セキュリティ レビュー]** セクションをクリックします。**[セキュリティ レビュー]** ブレードが表示されます。
3. エクスポートするセキュリティ レビューをクリックします。セキュリティ レビューの詳細ブレードが表示されます。
4. **[エクスポート]** ボタンをクリックします。CSV ファイルのダウンロードが開始します。

## レビューの削除

> [AZURE.WARNING] 削除の前に注意は表示されないので、*本当に削除してよいのか*確認してください。

1. PIM ダッシュボードに戻ります。
2. ダッシュボードの **[セキュリティ レビュー]** セクションをクリックします。**[セキュリティ レビュー]** ブレードが表示されます。
3. 削除するセキュリティ レビューをクリックします。セキュリティ レビューの詳細ブレードが表示されます。
4. **[削除]** ボタンをクリックします。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->