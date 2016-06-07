<properties
   pageTitle="セキュリティ レビューを開始する方法 | Microsoft Azure"
   description="Azure Privileged Identity Management アプリケーションで特権 ID のセキュリティ レビューを作成する方法について説明します。"
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
   ms.date="05/19/2016"
   ms.author="kgremban"/>

# Azure AD Privileged Identity Management でセキュリティ レビューを開始する方法

ユーザーが持っているアクセス特権がユーザーには不要になった場合、そのロールの割り当ては "古く" なっています。このような古くなったロールの割り当てに関連するリスクを軽減するために、特権ロール管理者は、ユーザーに与えられているロールを定期的に確認する必要があります。このドキュメントでは、Azure AD Privileged Identity Management (PIM) でセキュリティ レビューを開始する手順を説明します。

## セキュリティ レビューを開始する
> [AZURE.NOTE] Azure ポータルのダッシュボードに PIM アプリケーションをまだ追加していない場合は、「[Azure Privileged Identity Management の使用](active-directory-privileged-identity-management-getting-started.md)」の手順を参照してください。

PIM アプリケーションのメイン ページには、セキュリティ レビューを開始する方法が 3 つあります。

- **[セキュリティ レビュー]**、**[レビュー]**、**[レビュー]** ボタンの順にクリックする
- **[ロール]**、**[レビュー]** ボタンの順にクリックする
- 確認するロールをロールの一覧から選択し、**[レビュー]** ボタンをクリックする

**[レビュー]** ボタンをクリックすると、**[役割のレビューを開始する]** ブレードと **[レビューする役割を選択する]** ブレードが表示されます。**[レビューする役割を選択する]** 項目が既に選択されています。

### レビューするロールを選択する

1. **[レビューする役割を選択する]** ブレードのロールの一覧でロールを選択します。一度に 1 つのロールのみ選択できます。**[レビューするロールの選択]** ブレードが **[レビュー担当者の選択]** ブレードに変わります。レビュー担当者を選択するときは 2 つのオプションがあります。
  - [Me] (自分) - 他の管理者の関与なしでセキュリティ レビューの動作を確認する場合は、このオプションを使用します。
  - [Self review by role members] (ロールのメンバーによる自己レビュー) - ユーザーに自分のロール割り当てを確認してもらう場合は、このオプションを使用します。
2. どちらかを選択して、レビューの詳細に関する作業を始めます。**[既定値の変更]** ブレードが表示されます。

### 自分でレビューする

レビュー担当者として [自分] オプションを選択した場合は、セキュリティ レビューに進みます。レビュー実行の詳細については、「[Azure Privileged Identity Management: セキュリティ レビューの実行方法](active-directory-privileged-identity-management-how-to-perform-security-review.md)」を参照してください。

### ロール メンバーによる自己レビュー

ユーザーに自身のロール割り当てを確認してもらう場合は、次の手順に従って、レビューを設定して通知を送信します。

1. **[名前]** フィールドにレビューの名前を入力します。内容がわかり、追跡しやすい一意の名前をレビューに付けます。
2. レビューの開始日を **[開始日]** フィールドに入力します。
3. レビューの終了日を **[終了日]** フィールドに入力します。レビューの終了日を設定するときは次の点を考慮する必要があります。
  - レビュー対象のユーザー数。
  - ユーザーが Azure ポータルに PIM アプリケーションを追加してレビューを実行できるようになるまでの時間。
4. **[既定値を変更する]** ブレードの **[OK]** ボタンをクリックします。ブレードが閉じます。
5. **[Start a review of a role]** (ロールのレビューの開始) ブレードで **[OK]** ボタンをクリックします。ブレードが閉じます。通知が Azure ポータルのメイン メニューに表示されます。**[更新]** ボタンクリックしてダッシュボードを更新します。セキュリティ レビューが **[セキュリティ レビュー]** セクションに表示されます。
6. ロールの各ユーザーに、PIM アプリケーションを追加して[自分の管理アクセスをレビューする](active-directory-privileged-identity-management-how-to-perform-security-review.md)必要があることを通知します。  

## セキュリティ レビューの管理

レビュー担当者が Azure AD PIM ダッシュボードでレビューを完了すると、[セキュリティのレビュー] セクションで進行状況を追跡できます。ディレクトリのアクセス権は、[レビューが完了する](active-directory-privileged-identity-management-how-to-complete-review.md)まで変更されません。


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## PIM の内容一覧
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0525_2016-->