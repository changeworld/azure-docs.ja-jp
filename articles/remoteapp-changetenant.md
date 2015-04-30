
<properties 
    pageTitle=" RemoteApp での Azure Active Directory テナントの変更"
    description="RemoteApp に関連付けられている Azure Active Directory テナントの変更方法について説明します。 " 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="02/19/2015" 
    ms.author="elizapo" />



# RemoteApp での Azure Active Directory テナントの変更

RemoteApp では、Azure Active Directory (Azure AD) を使用してユーザーのアクセスを許可します。使用可能な唯一の Azure AD テナントは、Azure サブスクリプションに関連付けられているテナントです。ポータルの [設定] ページで、関連付けられているサブスクリプションを確認できます。[サブスクリプション] タブの [ディレクトリ] 列に注目します。 

別のテナントを使用する場合は、この手順を使用して、サブスクリプションの関連付けを変更します。

1. ポータルで、RemoteApp サービスへのアクセス許可を付与した Azure AD ユーザーを削除します。


2. サービス管理者として、Microsoft アカウント (以前の Live ID) を設定します。(**[設定]、[管理者]** の順にクリックします。)
	1. 右上隅の現在ログインしているユーザーをクリックして、**[明細の表示]** をクリックします。
	2. サブスクリプションを選択して、**[サブスクリプション詳細の編集]** をクリックします。
	3. 必要な変更を加えます。



3. ポータルからサインアウトし、前の手順で指定した Microsoft アカウントを使用して再度サインインします。


4. **[新規]、[アプリケーション サービス]、[Active Directory]、[カスタム作成]、[既存のディレクトリの使用]** の順にクリックします。このサブスクリプションに関連付ける Azure AD テナントを追加します。


5. **[設定]、[サブスクリプション]** の順にクリックしてサブスクリプションを選択し、**[ディレクトリの編集]** をクリックします。使用する Azure AD テナントを選択します。



これで、新しい Azure AD テナントを使用して、Azure サブスクリプションへのアクセスを制御したり、RemoteApp 内のユーザー アクセスを構成することができます。

<!--HONumber=52-->