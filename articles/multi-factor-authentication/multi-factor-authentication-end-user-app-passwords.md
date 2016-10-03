<properties
	pageTitle="Azure MFA のアプリ パスワードとは"
	description="このページは、アプリ パスワードの詳細と、Azure MFA に関連した用途を理解するのに役立ちます。"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>



# Azure Multi-Factor Authentication のアプリ パスワードとは

現在、特定の非ブラウザー アプリ (Exchange Active Sync を使用する Apple ネイティブ電子メールクライアントなど) は、多要素認証をサポートしていません。多要素認証はユーザーごとに有効にします。これは、ユーザーで多要素認証が有効になると、非ブラウザー アプリを使用しようとしても、操作を実行できないことを意味します。アプリ パスワードによってこれが発生します。

>[AZURE.NOTE] Office 2013 クライアントのための最新の認証
>
> Office 2013 クライアント (Outlook を含む) は新しい認証プロトコルをサポートするようになり、Multi-Factor Authentication をサポートするように有効化できます。つまり、Multi-Factor Authentication を有効にすると、Office 2013 クライアントでアプリ パスワードは不要になります。詳しくは、「[発表された Office 2013 の最新の認証のパブリック プレビュー](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/)」をご覧ください。

## アプリ パスワードを使用する方法

アプリ パスワードの使用方法の注意点を次に示します。

- 実際のパスワードは自動的に生成され、ユーザーが指定するわけではありません。これは、自動的に生成されたパスワードのほうが攻撃者から推測されづらく、より安全なためです。
- 現在、ユーザーあたりのパスワード数の制限は 40 個です。制限に達した後に作成しようとした場合、新しいものを作成できるよう既存のアプリ パスワードのいずれかを削除するよう求められます。
- アプリ パスワードは、アプリケーションごとではなく、デバイスごとに作成することをお勧めします。たとえば、ノート PC 用に 1 つのアプリ パスワードを作成し、そのアプリ パスワードをノート PC 上のすべてのアプリケーションで使用します。
- 最初にサインインするときにアプリ パスワードが提供されます。追加のアプリ パスワードが必要な場合は、作成できます。

![セットアップ](./media/multi-factor-authentication-end-user-app-passwords/app.png)

アプリ パスワードが取得できたら、これらの非ブラウザー アプリに対して、元のパスワードの代わりに使用します。たとえば、電話で多要素認証と Apple ネイティブ電子メール クライアントを使用している場合は、多要素認証をバイパスし、作業を続行できるようにアプリ パスワードを使用します。

## アプリ パスワードの作成と削除
最初のサインイン時に、使用可能なアプリ パスワードが提供されます。さらに、後でアプリ パスワードを作成したり、削除したりすることもできます。そうする方法は、多要素認証を使用する方法によって異なります。最も適切な方法を選択します。

多要素認証の使用方法|Description
:------------- | :------------- |
[Office 365 で使用する](#creating-and-deleting-app-passwords-with-office-365)| これは、Office 365 ポータルでアプリ パスワードを作成することを意味します。
[わからない](#creating-and-deleting-app-passwords-with-myapps-portal)|これは、[https://myapps.microsoft.com](https://myapps.microsoft.com) でアプリ パスワードを作成することを意味します。
[Microsoft Azure で使用する](#create-app-passwords-in-the-azure-portal)| これは、Azure ポータルでアプリ パスワードを作成することを意味します。

## Office 365 でのアプリ パスワードの作成と削除

Office 365 で多要素認証を使用している場合は、Office 365 ポータルでアプリ パスワードを作成し、削除します。

### Office 365 ポータルでアプリ パスワードを作成するには
--------------------------------------------------------------------------------

1. [Office 365 ポータル](https://login.microsoftonline.com/)にログオンします
2. 右上隅でウィジェットを選択し、Office 365 設定を選択します。
3. [追加のセキュリティ確認] をクリックします。
4. 右側の **[アカウントのセキュリティのために使用する電話番号を更新]** というリンクをクリックします。![セットアップ](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 設定を変更できるページに移動します。![クラウド](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 上部で、追加のセキュリティ検証の隣にある **[アプリ パスワード]** をクリックします。
7. **[作成]** をクリックします。![クラウド](./media/multi-factor-authentication-end-user-app-passwords-create-o365/apppass.png)
8. アプリ パスワードの名前を入力し、**[次へ]** をクリックします。![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
9. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create2.png)


### Office 365 ポータルでアプリ パスワードを削除するには
--------------------------------------------------------------------------------


1. [Office 365 ポータル](https://login.microsoftonline.com/)にログオンします
2. 右上隅でウィジェットを選択し、Office 365 設定を選択します。
3. [追加のセキュリティ確認] をクリックします。
4. 右側の **[アカウントのセキュリティのために使用する電話番号を更新]** というリンクをクリックします。![セットアップ](./media/multi-factor-authentication-end-user-manage/o365a.png)
5. 設定を変更できるページに移動します。![クラウド](./media/multi-factor-authentication-end-user-manage/o365b.png)
6. 上部で、追加のセキュリティ検証の隣にある **[アプリ パスワード]** をクリックします。
7. 削除するアプリ パスワードの横にある **[削除]** をクリックします。![アプリケーション パスワードの削除](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
8. **[はい]** をクリックし、削除を確定します。![削除の確定](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
9. アプリ パスワードを削除したら、**[閉じる]** をクリックできます。![閉じる](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Myapps ポータルでのアプリ パスワードの作成と削除
多要素認証を使用する方法がわからない場合、いつでも Myapps ポータルでアプリ パスワードを作成し、削除できます。

### Myapps ポータルでアプリ パスワードを作成するには

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。
2. 上部にある [プロファイル] を選択します。
3. [追加のセキュリティ確認] を選択します。![クラウド](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 設定を変更できるページに移動します。![セットアップ](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. 上部で、追加のセキュリティ検証の隣にある **[アプリ パスワード]** をクリックします。
6. **[作成]** をクリックします。![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create3.png)
7. アプリ パスワードの名前を入力し、**[次へ]** をクリックします。![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create1.png)
8. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。![アプリケーション パスワードの作成](./media/multi-factor-authentication-end-user-app-passwords/create2.png)

### Myapps ポータルでアプリ パスワードを削除するには

1. [https://myapps.microsoft.com](https://myapps.microsoft.com) にサインインします。
2. 上部にある [プロファイル] を選択します。
3. [追加のセキュリティ確認] を選択します。![クラウド](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. 設定を変更できるページに移動します。![セットアップ](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)
5. 上部で、追加のセキュリティ検証の隣にある **[アプリ パスワード]** をクリックします。
6. 削除するアプリ パスワードの横にある **[削除]** をクリックします。![アプリケーション パスワードの削除](./media/multi-factor-authentication-end-user-app-passwords/delete1.png)
7. **[はい]** をクリックし、削除を確定します。![削除の確定](./media/multi-factor-authentication-end-user-app-passwords/delete2.png)
8. アプリ パスワードを削除したら、**[閉じる]** をクリックできます。![閉じる](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)


## Azure ポータルでアプリ パスワードを作成する

Azure で多要素認証を使用している場合は、Azure ポータルでアプリ パスワードを作成します。

### Azure ポータルでアプリ パスワードを作成するには

1. Microsoft Azure 管理ポータルにサインインします。
2. 上部のユーザー名を右クリックし、[追加のセキュリティ確認] を選択します。
3. 確認ページの上部の [アプリ パスワード] を選択します。
4. **[作成]** をクリックします。
5. アプリ パスワードの名前を入力し、**[次へ]** をクリックします。
6. アプリ パスワードをクリップボードにコピーし、アプリに貼り付けます。![クラウド](./media/multi-factor-authentication-end-user-app-passwords-create-azure/app2.png)

### Azure ポータルでアプリ パスワードを削除するには

1. Microsoft Azure 管理ポータルにサインインします。
2. 上部のユーザー名を右クリックし、[追加のセキュリティ確認] を選択します。
3. 上部で、追加のセキュリティ検証の隣にある **[アプリ パスワード]** をクリックします。
4. 削除するアプリ パスワードの横にある **[削除]** をクリックします。
5. **[はい]** をクリックし、削除を確定します。
6. アプリ パスワードを削除したら、**[閉じる]** をクリックできます。![閉じる](./media/multi-factor-authentication-end-user-app-passwords/delete3.png)

<!---HONumber=AcomDC_0921_2016-->