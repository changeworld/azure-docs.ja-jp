---
title: 'Azure AD Connect 同期: ディレクトリ拡張機能 | Microsoft Docs'
description: このトピックでは、Azure AD Connect のディレクトリ拡張機能について説明します。
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: a19fa124396bf9c9db4a60591b74f5425ce9670b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2018
ms.locfileid: "42146605"
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同期: ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure Active Directory (Azure AD) のスキーマを拡張できます。 この機能により、オンプレミスで引き続き管理する属性を使用して LOB アプリを構築できます。 これらの属性は、[Azure AD Graph API ディレクトリ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)または [Microsoft Graph](https://graph.microsoft.io/) を通じて利用できます。 使用可能な属性を表示するには、それぞれ [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) と [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) を使用します。

現在のところ、これらの属性を使用する Office 365 ワークロードはありません。

インストール ウィザードのカスタム設定パスで、どの追加属性を同期するかを構成します。

![スキーマ拡張機能のウィザード](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  

インストールでは、次の属性が表示されます。これらは有効な候補です。

* ユーザーおよびグループ オブジェクト型
* 単一値の属性: 文字列、ブール値、整数、バイナリ
* 複数値の属性: 文字列、バイナリ


>[!NOTE]
> Azure AD Connect では、複数値の Active Directory 属性から Azure AD への同期が、複数値のディレクトリ拡張としてサポートされます。 ただし、Azure AD には、現在、複数値のディレクトリ拡張の使用をサポートする機能がありません。

属性の一覧は、Azure AD Connect のインストール中に作成されたスキーマ キャッシュから読み取られます。 その他の属性で Active Directory スキーマを拡張した場合、このような新しい属性が表示されるようにするには、[スキーマを更新する](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema)必要があります。

Azure AD のオブジェクトでは、ディレクトリ拡張機能に対して最大 100 個の属性を持つことができます。 最大長は 250 文字です。 属性値がそれより長い場合は、同期エンジンによって切り捨てられます。

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。 このアプリケーションは、Azure Portal で確認できます。

![スキーマ拡張機能アプリ](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

属性には、拡張機能 \_{AppClientId}\_ というプレフィックスが付きます。 AppClientId の値は、使用している Azure AD テナントに存在するすべての属性で同じになります。

これで、これらの属性を Azure AD Graph API を通じて利用できるようになりました。 これにクエリを実行するには、[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) を使用します。

![Azure AD Graph Explorer](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer#) を使用して、Microsoft Graph API で属性にクエリを実行します。

>[!NOTE]
> 属性が返されるように要求する必要があります。 このように、明白に属性を選びます：https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division. 
>
> 詳細については、[Microsoft Graph: クエリ パラメーターの使用](https://developer.microsoft.com/graph/docs/concepts/query_parameters#select-parameter)に関するトピックをご覧ください。

## <a name="next-steps"></a>次の手順
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。
