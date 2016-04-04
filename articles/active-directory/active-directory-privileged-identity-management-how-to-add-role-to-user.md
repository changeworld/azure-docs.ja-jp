<properties
   pageTitle="ユーザー ロールを追加または削除する方法 | Microsoft Azure"
   description="Azure Active Directory Privileged Identity Management 拡張機能で特権 ID にロールを追加する方法について説明します。"
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

# Azure AD Privileged Identity Management: ユーザー ロールを追加または削除する方法

## ユーザー ロールの追加または削除
Privileged Identity Management (PIM) インターフェイスの **[管理対象ユーザーの追加]** ブレードに移動するには、複数の方法があります。それぞれのクリック シーケンスを次に示します。

- ダッシュボード > [管理者ロール] の [ユーザー] > [追加] または [削除]
- ダッシュボード > [ロールの概要] > [すべてのユーザー] 一覧 > [追加] または [削除]
- ダッシュボード > ロール テーブルでユーザー ロールをクリック (たとえば [グローバル管理者]) > [追加] または [削除]

## ユーザーをロールに追加する
**[管理対象ユーザーの追加]** ブレードに移動した後、以下の手順を実行します。

1. **[ロールの選択]** をクリックします。ロール テーブルでユーザー ロールをクリックしてここに移動した場合は、ロールは既に選択されています。
2. ロールの一覧からロールを選択します。たとえば、**[パスワード管理者]** を選択します。**[ユーザーの選択]** ブレードが開きます。
3. [検索] フィールドにユーザーの名前を入力します。ユーザーがディレクトリに存在する場合は、入力すると似た名前の他のユーザーと共にアカウントが表示されます。
4. 一覧でユーザーを選択し、**[完了]** をクリックします。
5. **[OK]** をクリックして選択を保存します。選択したユーザーが一覧に表示されます。ロールは一時的です。
6. ロールを永続的にする場合は、一覧のユーザーをクリックします。ユーザーの情報が新しいブレードに表示されます。ユーザー情報メニューで **[永続的にする]** を選択します。

    ユーザーが Azure Multi-Factor Authentication (MFA) に登録できないか、Microsoft アカウントを使用する場合は、これを行う必要があります。一時的な管理者は、アクティブ化時に MFA への登録が求められます。

7. **[アクティブ化]** をクリックし、ユーザーのこのロールをアクティブ化する要求を開始します。**[要求の理由]** テキスト フィールドにアクティブ化の理由を入力します。この時点で、このユーザーのロールが自動的にアクティブになり、グローバル管理者に通知が送信されます。

## ユーザーをロールから削除する
1. 上で説明したパスの 1 つを使用してユーザー ロールの一覧でユーザーに移動します。
2. ユーザーの一覧でユーザーをクリックします。
3. **[削除]** をクリックします。確認メッセージが表示されます。
4. **[はい]** をクリックしてユーザーからロールを削除します。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## 次のステップ
[AZURE.INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]

<!---HONumber=AcomDC_0323_2016-->