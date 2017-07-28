---

title: "Azure AD レポート API の概要 | Microsoft Docs"
description: "Azure Active Directory Reporting API の概要について説明します"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/18/2017
ms.author: dhanyahk;markvi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: fd750c1bf960678e0fecef61a67b73781d9777ef
ms.contentlocale: ja-jp
ms.lasthandoff: 06/20/2017


---
# <a name="getting-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory Reporting API の概要

Azure Active Directory は多様なレポートを提供しています。 これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに非常に役立ちます。 Azure AD レポート API は、一連の REST ベースの API を使用してプログラムによってデータにアクセスできるようにします。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、Azure AD のレポート API の使用を開始するために必要な情報を提供します。
次のセクションでは、監査 API およびサインイン API の使用に関して詳しく説明します。 その他すべての API については、[Azure AD のレポートとイベント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-reports-and-events-preview)に関する記事を参照してください。

よく寄せられる質問については、こちらの [FAQ](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-faq) を参照してください。 問題については、[サポート チケットを提出](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-troubleshooting-support-howto)してください。

## <a name="learning-map"></a>学習マップ
1. **準備** - API サンプルをテストするには、事前に、 [Azure AD Reporting API にアクセスするための前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)を完了する必要があります。
2. **さらに詳しく** - レポート API の概要を取得します。
   
   * [監査 API のサンプルの使用](active-directory-reporting-api-audit-samples.md) 
   * [サインイン アクティビティ レポート API のサンプルの使用](active-directory-reporting-api-sign-in-activity-samples.md)
3. **カスタマイズ** - 独自のソリューションを作成します。 
   
   * [監査 API リファレンスの使用](active-directory-reporting-api-audit-reference.md) 
   * [サインイン アクティビティ レポート API リファレンスの使用](active-directory-reporting-api-sign-in-activity-reference.md)

## <a name="next-steps"></a>次のステップ
提供されているすべての Azure AD Graph API エンドポイントについては、 [https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta](https://graph.windows.net/tenant-name/reports/$metadata?api-version=beta)にナビゲートしてください。


