<properties
   pageTitle="Azure AD Connect 同期: ディレクトリ拡張機能 | Microsoft Azure"
   description="このトピックでは、Azure AD Connect のディレクトリ拡張機能について説明します。"
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/19/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-sync:-directory-extensions"></a>Azure AD Connect 同期: ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure AD のスキーマを拡張できます。 この機能により、独自に構築した LOB アプリで利用する属性を引き続きオンプレミスで管理することが可能です。 これらの属性は、[Azure AD Graph ディレクトリ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)または [Microsoft Graph](https://graph.microsoft.io/) を通じて利用できます。 それぞれ [Azure AD Graph Explorer](https://graphexplorer.cloudapp.net) と [Microsoft Graph Explorer](https://graphexplorer2.azurewebsites.net/) を使用して、利用可能な属性を表示できます。

現在のところ、これらの属性を使用する Office 365 ワークロードはありません。

インストール ウィザードのカスタム設定パスで、どの追加属性を同期するかを構成します。
![Schema Extension Wizard](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)インストールでは、以下の属性が表示されます。これらは有効な候補です。

- ユーザーおよびグループ オブジェクト型
- 単一値の属性: 文字列、ブール値、整数、バイナリ
- 複数値の属性: 文字列、バイナリ

属性の一覧は、Azure AD Connect のインストール中に作成されたキャッシュから読み取られます。 その他の属性で Active Directory スキーマを拡張した場合は、これらの新しい属性が表示されるように [スキーマを更新する必要があります](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema) 。

オブジェクトは、最大で 100 個のディレクトリ拡張機能属性を持つことができます。 最大長は、250 文字です。 属性値がそれより長い場合は、同期エンジンによって切り捨てられます。

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。 このアプリケーションは、Azure ポータルで確認できます。  
![Schema Extension App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3.png)

これで、これらの属性を Graph を通じて利用できるようになりました。  
![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

属性には、extension\_{AppClientId}\_ というプレフィックスが付きます。 AppClientId の値は、ご使用の Azure AD ディレクトリに存在するすべての属性で同じになります。

## <a name="next-steps"></a>次のステップ
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。



<!--HONumber=Oct16_HO2-->


