
<properties
    pageTitle="Azure RemoteApp で Azure Active Directory テナントを変更する"
    description="Azure RemoteApp に関連付けられている Azure Active Directory テナントの変更方法について説明します。"
    services="remoteapp"
    documentationCenter="" 
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/12/2015"
    ms.author="elizapo" />



# Azure RemoteApp で Azure Active Directory テナントを変更する

Azure RemoteApp では Azure Active Directory (Azure AD) を使用してユーザーのアクセスを許可します。使用可能な唯一の Azure AD テナントは、Azure サブスクリプションに関連付けられているテナントです。ポータルの [設定] ページで、関連付けられているサブスクリプションを確認できます。[サブスクリプション] タブの [ディレクトリ] 列に注目します。

> [AZURE.NOTE]この変更を完了するには、まず、すべての Azure RemoteApp コレクションの既存の Azure Active Directory テナントからすべてのユーザーを削除します。これを行うには、Azure ポータルに移動し、[Azure RemoteApp] タブに進み、すべての Azure RemoteApp コレクションを開きます。**[ユーザー]** タブに進み、現在の Azure Active Directory テナントに属しているユーザーを削除します。すべての既存の Azure RemoteApp コレクションに対して繰り返します。この作業を行わないと、コレクションを作成またはパッチできません。

別のテナントを使用する場合は、この手順を使用して、サブスクリプションの関連付けを変更します。

1. ポータルで、Azure RemoteApp コレクションへのアクセス許可を付与した Azure AD ユーザーを削除します。


2. サービス管理者として、Microsoft アカウント (以前の Live ID) を設定します。(**[設定]、[管理者]** の順にクリックします。)
	1. 右上隅の現在ログインしているユーザーをクリックし、**[明細の表示]** をクリックします。
	2. サブスクリプションを選択し、**[サブスクリプション詳細の編集]** をクリックします。
	3. 必要な変更を加えます。



3. ポータルからサインアウトし、前の手順で指定した Microsoft アカウントを使用して再度サインインします。


4. **[新規]、[App Services]、[Active Directory]、[カスタム作成]、[既存のディレクトリの使用]** の順にクリックします。このサブスクリプションに関連付ける Azure AD テナントを追加します。


5. **[設定]、[サブスクリプション]** の順にクリックしてサブスクリプションを選択し、**[ディレクトリの編集]** をクリックします。使用する Azure AD テナントを選択します。



これで新しい Azure AD テナントを使用し、Azure サブスクリプションへのアクセスを制御したり、Azure RemoteApp 内のユーザー アクセスを構成したりできます。

<!---HONumber=Oct15_HO3-->