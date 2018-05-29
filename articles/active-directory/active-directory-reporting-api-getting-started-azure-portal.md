---
title: Azure AD レポート API の概要 | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2018
ms.author: dhanyahk;markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: a818c9b0e2c9097f45d2fdd39676ef6807d06ca5
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33929088"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API の概要

Azure Active Directory では、さまざまな[レポート](active-directory-reporting-azure-portal.md)が提供されています。 これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに非常に役立ちます。 

Azure AD レポート API を使うと、一連の REST ベースの API を使ってプログラムからデータにアクセスできます。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、関連する API を使ってレポート データにアクセスするためのロードマップを提供します。

問題が発生した場合は、「[Azure Active Directory のサポートを受ける方法](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)」をご覧ください。


## <a name="prerequisites"></a>前提条件

スクリプトを使って API にアクセスすることを計画している場合でも、レポート API にアクセスするには、次のことを行う必要があります。

1. ロールを割り当てる (セキュリティ閲覧者、セキュリティ管理者、全体管理者)
2. アプリケーションを登録する
3. アクセス許可を付与する
4. 構成設定を収集する


 
詳しい説明については、[Azure Active Directory レポート API にアクセスするための前提条件](active-directory-reporting-api-prerequisites-azure-portal.md)に関するページをご覧ください。


## <a name="recommendation"></a>推奨 

ユーザーによる操作を必要としないでレポート データを取得することを計画している場合は、Azure AD レポート API と共に証明書を使うことを検討する必要があります。

詳しくは、「[Azure AD Reporting API と証明書を使ってデータを取得する](active-directory-reporting-api-with-certificates.md)」をご覧ください。


## <a name="explore"></a>確認

レポート API の概要を把握します。
   
   - [監査 API のサンプルの使用](active-directory-reporting-api-audit-samples.md) 
 
   - [サインイン アクティビティ レポート API のサンプルの使用](active-directory-reporting-api-sign-in-activity-samples.md)


## <a name="customize"></a>[カスタマイズ]  

独自のソリューションを作成します。 
   
   - [監査 API リファレンスの使用](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 

   - [サインイン アクティビティ レポート API リファレンスの使用](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



