<properties
   pageTitle="Azure Privileged Identity Management: セキュリティ レビューを開始する方法"
   description="Azure Privileged Identity Management 拡張機能で特権 ID のセキュリティ プレビューを作成する方法について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="IHenkel"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/21/2015"
   ms.author="inhenk"/>

# Azure Privileged Identity Management: セキュリティ レビューを開始する方法

## セキュリティ レビューの開始
最終的には、Azure ポータルの他の場所でセキュリティ レビューを実行できるようになります。このドキュメントでは、**Privileged Identity Management (PIM)** インターフェイスでセキュリティ レビューを開始する手順を説明します。

管理者が認識していないユーザーがいる可能性、またはユーザーがジョブやプロジェクトを変更して新しい仕事では特権アクセスが不要になっている可能性があります。このような「古い」ロールの割り当てに関連するリスクを軽減するためには、管理者はセキュリティ レビューを開始することによってユーザーに与えられているロールを確認できます。

## セキュリティ レビューを開始するパス
> [AZURE.NOTE]Azure ポータルで PIM ダッシュボードをまだ作成していない場合は、「[Getting Started with Azure Privileged Identity Management (Azure Privileged Identity Management の概要)](active-directory-privileged-identity-management-getting-started.md)」の手順を参照してください。

Azure PIM ダッシュボードからは次の方法でレビューを開始できます。

- ダッシュボード > [セキュリティ レビュー] > [レビュー] > [レビュー] ボタン
- ダッシュボード > [ロール] > [レビュー] ボタン
- ダッシュボード > ロール一覧でレビューするロールをクリック > [レビュー] ボタン

## セキュリティ レビューを開始する

**[レビュー]** ボタンをクリックすると、**[ロールのレビューの開始]** ブレードと **[レビューするロールの選択]** ブレードが表示され、**[レビューするロールの選択]** 項目が選択されています。

### レビューするロールを選択する

1. **[レビューするロールの選択]** ブレードのロールの一覧でロールを選択します。一度に 1 つのロールのみ選択できます。**[レビューするロールの選択]** ブレードが **[レビュー担当者の選択]** ブレードに変わります。レビュー担当者を選択するときは 2 つのオプションがあります。
  - 自分で - 他の管理者の関与なしでセキュリティ レビューの動作を確認する場合、この機能を使用します。
  - ロールのメンバーによる自己レビュー - ユーザーに自分のロール割り当てを自分で確認させる場合、この機能を使用します。
2. どちらかを選択して、レビューの詳細に関する作業を始めます。**[既定値の変更]** ブレードが表示されます。

### 自己レビュー

1. **[名前]** フィールドにレビューの名前を入力します。レビューには内容がわかる追跡しやすい一意の名前を付けることをお勧めします。
2. レビューの開始日を **[開始日]** フィールドに入力します。
3. レビューの終了日を **[終了日]** フィールドに入力します。レビューの終了日を設定するときは次の点を考慮する必要があります。
  - レビュー対象のユーザー数。
  - ユーザーが拡張機能を追加してレビューを実行できるようになるまでの時間。
4. **[既定値の変更]** ブレードの [OK] ボタンをクリックします。ブレードが閉じます。
5. **[ロールのレビューの開始]** ブレードで [OK] ボタンをクリックします。ブレードが閉じます。ポータルのメイン メニューに通知が表示されます。**[更新]** ボタンクリックしてダッシュボードを更新します。セキュリティ レビューが **[セキュリティ レビュー]** セクションに表示されます。
6. ロールの各ユーザーに、拡張機能を追加して自分の管理アクセスをレビューする必要があることを通知します。次のステップを参照してください。
6. [管理アクセスをレビューする](active-directory-privileged-identity-management-how-to-perform-security-review.md)

### 自分でレビューする

レビュー担当者として [自分] オプションを選択した場合は、セキュリティ レビューに進みます。レビュー実行の詳細については、「[Azure Privileged Identity Management: セキュリティ レビューの実行方法](active-directory-privileged-identity-management-how-to-perform-security-review.md)」を参照してください。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## PIM の内容一覧
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=Sept15_HO4-->