---
title: Azure AD レポート API の概要 | Microsoft Docs
description: Azure Active Directory Reporting API の概要について説明します
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 8813b911-a4ec-4234-8474-2eef9afea11e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 1e6ad35702b15090202278cfdead62b245040302
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309622"
---
# <a name="get-started-with-the-azure-active-directory-reporting-api"></a>Azure Active Directory レポート API の概要

Azure Active Directory では、さまざまな[レポート](overview-reports.md)が提供されています。 これらのレポートのデータは、SIEM システム、監査、ビジネス インテリジェンス ツールなどのアプリケーションに非常に役立ちます。 

Azure AD レポート API を使うと、一連の REST ベースの API を使ってプログラムからデータにアクセスできます。 これらの API は、さまざまなプログラミング言語とツールから呼び出すことができます。

この記事では、関連する API を使ってレポート データにアクセスするためのロードマップを提供します。

問題が発生した場合は、「[Azure Active Directory のサポートを受ける方法](https://docs.microsoft.com/azure/active-directory/active-directory-troubleshooting-support-howto)」をご覧ください。

この記事は、Azure AD Graph API に適用されます。  Microsoft Graph API に関する同様の情報については、「[directoryAudit resource type](https://developer.microsoft.com/en-us/graph/docs/api-reference/beta/resources/directoryaudit)」(directoryAudit リソースの種類) をご覧ください。

## <a name="prerequisites"></a>前提条件

スクリプトを使って API にアクセスすることを計画している場合でも、レポート API にアクセスするには、次のことを行う必要があります。

1. ロールを割り当てる (セキュリティ閲覧者、セキュリティ管理者、全体管理者)
2. アプリケーションを登録する
3. アクセス許可を付与する
4. 構成設定を収集する

詳しい説明については、[Azure Active Directory レポート API にアクセスするための前提条件](howto-configure-prerequisites-for-reporting-api.md)に関するページをご覧ください。

## <a name="apis-with-graph-explorer"></a>Graph Explorer での API

[MSGraph エクスプローラー](https://developer.microsoft.com/graph/graph-explorer)を使用してサインインを確認し、API データを監査できます。 Graph エクスプローラーの UI で、両方のサインイン ボタンを使用してアカウントにサインインし、次に示すようにテナントの **AuditLog.Read.All** および **Directory.ReadAll** のアクセス許可を設定します。   

![Graph エクスプローラー](./media/concept-reporting-api/graph-explorer.png)

![アクセス許可の変更 UI](./media/concept-reporting-api/modify-permissions.png)

## <a name="use-certificates-to-access-the-azure-ad-reporting-api"></a>証明書を使用して Azure AD レポート API にアクセスする 

ユーザーによる操作を必要としないでレポート データを取得することを計画している場合は、Azure AD レポート API と共に証明書を使ってください。

詳しくは、「[Azure Active Directory Reporting API と証明書を使用したデータの取得](tutorial-access-api-with-certificates.md)」をご覧ください。


## <a name="next-steps"></a>次の手順

 * [監査 API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
 * [サインイン アクティビティ レポート API リファレンス](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
 * [Azure Active Directory Reporting API でのエラーのトラブルシューティング](troubleshoot-graph-api.md)


