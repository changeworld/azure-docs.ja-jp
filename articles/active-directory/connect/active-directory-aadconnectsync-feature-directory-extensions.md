---
title: "Azure AD Connect 同期: ディレクトリ拡張機能 | Microsoft Docs"
description: "このトピックでは、Azure AD Connect のディレクトリ拡張機能について説明します。"
services: active-directory
documentationcenter: 
author: billmath
manager: mtillman
editor: 
ms.assetid: 995ee876-4415-4bb0-a258-cca3cbb02193
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 9abd035b13a0d51c534eb8cac50c045012399a69
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/19/2018
---
# <a name="azure-ad-connect-sync-directory-extensions"></a>Azure AD Connect 同期: ディレクトリ拡張機能
ディレクトリ拡張機能を使用すると、オンプレミスの Active Directory から独自の属性を使用して、Azure AD のスキーマを拡張できます。 この機能により、独自に構築した LOB アプリで利用する属性を引き続きオンプレミスで管理することが可能です。 これらの属性は、[Azure AD Graph ディレクトリ拡張機能](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)または [Microsoft Graph](https://graph.microsoft.io/) を通じて利用できます。 それぞれ [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) と [Microsoft Graph Explorer](https://developer.microsoft.com/en-us/graph/graph-explorer) を使用して、利用可能な属性を表示できます。

現在のところ、これらの属性を使用する Office 365 ワークロードはありません。

インストール ウィザードのカスタム設定パスで、どの追加属性を同期するかを構成します。
![スキーマ拡張機能のウィザード](./media/active-directory-aadconnectsync-feature-directory-extensions/extension2.png)  
インストールでは、次の属性が表示されます。これらは有効な候補です。

* ユーザーおよびグループ オブジェクト型
* 単一値の属性: 文字列、ブール値、整数、バイナリ
* 複数値の属性: 文字列、バイナリ


>[!NOTE]
> Azure AD Connect では、複数値の AD 属性から Azure AD への同期が、複数値のディレクトリ拡張としてサポートされますが、Azure AD には、現在、複数値のディレクトリ拡張の使用をサポートする機能がありません。

属性の一覧は、Azure AD Connect のインストール中に作成されたスキーマ キャッシュから読み取られます。 その他の属性で Active Directory スキーマを拡張した場合は、これらの新しい属性が表示されるように[スキーマを更新する必要があります](active-directory-aadconnectsync-installation-wizard.md#refresh-directory-schema)。

Azure AD のオブジェクトは、最大で 100 個のディレクトリ拡張機能属性を持つことができます。 最大長は、250 文字です。 属性値がそれより長い場合は、同期エンジンによって切り捨てられます。

これらの属性が利用できるアプリケーションは、Azure AD Connect のインストール中に登録されます。 このアプリケーションは、Azure Portal で確認できます。  
![Schema Extension App](./media/active-directory-aadconnectsync-feature-directory-extensions/extension3new.png)

属性には、extension\_{AppClientId}\_ というプレフィックスが付きます。 AppClientId の値は、ご使用の Azure AD テナントに存在するすべての属性で同じになります。

これで、これらの属性を **Azure AD Graph** を通じて利用できるようになりました。

Azure AD Graph エクスプ ローラー経由で Azure AD Graph を照会できます ([https://graphexplorer.azurewebsites.net/](https://graphexplorer.azurewebsites.net/))。

![Graph](./media/active-directory-aadconnectsync-feature-directory-extensions/extension4.png)

または、**Microsoft Graph API** を通じて利用できます。

Microsoft Graph エクスプローラー経由で Microsoft Graph API を照会できます ([https://developer.microsoft.com/en-us/graph/graph-explorer#](https://developer.microsoft.com/en-us/graph/graph-explorer#)) 

>[!NOTE]
> 属性が返されるように、明示的に要求する必要があります。 次のように明示的に属性を選択することで、これを行います。https://graph.microsoft.com/beta/users/abbie.spencer@fabrikamonline.com?$select=extension_9d98ed114c4840d298fad781915f27e4_employeeID,extension_9d98ed114c4840d298fad781915f27e4_division 詳細については、[Microsoft Graph でのクエリ パラメーターの使用](https://developer.microsoft.com/en-us/graph/docs/concepts/query_parameters#select-parameter)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順
[Azure AD Connect Sync](active-directory-aadconnectsync-whatis.md) の構成に関するページをご覧ください。

「 [オンプレミス ID と Azure Active Directory の統合](active-directory-aadconnect.md)」をご覧ください。
