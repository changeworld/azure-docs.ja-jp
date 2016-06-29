<properties
	pageTitle="Azure AD Connect 同期: Azure AD サービス アカウントを管理する方法 | Microsoft Azure"
	description="このトピックでは、Azure AD サービス アカウントを復元する方法を説明します。"
	services="active-directory"
    keywords="Azure AD Connect 同期コネクタ サービス アカウントのパスワードをリセットする方法"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect 同期: Azure AD サービス アカウントを管理する方法
Azure AD コネクタで使用されるサービス アカウントは、無料のサービスであると想定されています。その資格情報をリセットする必要がある場合、このトピックが役立ちます。たとえば、グローバル管理者が PowerShell を使用してサービス アカウントのパスワードを誤ってリセットしてしまった場合などです。

## 資格情報をリセットする
Azure AD コネクタで定義されたサービス アカウントが認証の問題のために Azure AD に接続できない場合、パスワードをリセットすることができます。

1. Azure AD Connect 同期サーバーにサインインし、PowerShell を起動します。
2. `Add-ADSyncAADServiceAccount` を実行します。 ![PowerShell コマンドレット addadsyncaadserviceaccount](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Azure AD グローバル管理者の資格情報を指定します。

このコマンドレットは、Azure AD と同期エンジンの両方で、サービス アカウントのパスワードをリセットして更新します。

## 次のステップ
「[オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。

<!---HONumber=AcomDC_0615_2016-->