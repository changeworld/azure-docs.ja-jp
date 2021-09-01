---
title: '方法: テナントで Active Directory 認証ライブラリ (ADAL) を使用しているすべてのアプリの完全な一覧を取得する |Microsoft'
titleSuffix: Microsoft identity platform
description: この攻略ガイドでは、テナントで ADAL を使用しているすべてのアプリの完全な一覧を取得します。
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 7f4dfaaaaae39245c54c8ee3fc7879fd58b8ea9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725761"
---
# <a name="how-to-get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>方法: テナントで ADAL を使用しているアプリの一覧を取得する

Active Directory 認証ライブラリ (ADAL) のサポートは 2022 年 6 月 30 日に終了します。 既存の OS バージョンで ADAL を使用しているアプリは引き続き動作しますが、テクニカル サポートとセキュリティ更新プログラムは終了します。 継続的なセキュリティ更新プログラムがなければ、ADAL を使用しているアプリは、最新のセキュリティ攻撃パターンに対してますます脆弱になります。 この記事では、Azure Monitor ブックを使用して、テナントで ADAL を使用するすべてのアプリの一覧を取得する方法について説明します。

## <a name="sign-ins-workbook"></a>サインイン ブック

ブックは、Azure Active Directory (Azure AD) ログで使用できる情報を収集して視覚化するクエリのセットです。 [サインイン ログ スキーマの詳細については、こちらを参照してください](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md)。 Azure AD 管理ポータルのサインイン ブックには現在、ADAL を使用しているアプリケーションとその使用頻度を判断するのに役立つテーブルが用意されています。 まず、アプリケーションの一覧の視覚化を表示する前に、ブックにアクセスする方法について詳しく説明します。

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>手順 1: 監査ログを Azure Monitor と統合する

ブックにアクセスする前に、[Azure AD のサインインと監査ログを Azure Monitor と統合](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)に関するページの手順に従います。 Azure Monitor の統合後に作成されたサインインと監査イベントのみが格納されます。

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>手順 2: Azure portal でサインイン ブックにアクセスする

Azure AD サインインと監査ログを、Azure Monitor 統合に指定されている Azure Monitor と統合したら、サインイン ブックにアクセスします。

   1. Azure portal にサインインします 
   1.  **[Azure Active Directory]**  > **[監視]**  > **[ブック]** に移動します 
   1. **[使用状況]** セクションで、 **[サインイン]** ブックを開きます 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="[サインイン] ブックを強調表示している Azure Active Directory ポータル ブック インターフェイスのスクリーンショット。":::

## <a name="step-3-identify-apps-that-use-adal"></a>手順 3: ADAL を使用するアプリを識別する

サインイン ブック ページの下部にあるテーブルには、最近 ADAL を使用したアプリの一覧が表示されます。 また、アプリの一覧をエクスポートすることもできます。 MSAL を使用するようにこれらのアプリを更新します。
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="Active Directory 認証ライブラリを使用するアプリを表示しているサインイン ブックのスクリーンショット。":::
    
ADAL を使用しているアプリがない場合、ブックには次に示すようなビューが表示されます。 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="Active Directory 認証ライブラリを使用するアプリがない場合のサインイン ブックのスクリーンショット。":::

## <a name="step-4-update-your-code"></a>手順 4: コードを更新する

ADAL を使用しているアプリを特定した後、下に示すアプリケーションの種類に応じて MSAL に移行します。

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>次のステップ

MSAL の詳細、たとえば使用情報や、さまざまなプログラミング言語とアプリケーションの種類で使用できるライブラリについては、こちらを参照してください。

- [MSAL でトークンを取得し、キャッシュする](msal-acquire-cache-tokens.md)
- [アプリケーション構成オプション](msal-client-application-configuration.md)
- [MSAL 認証ライブラリの一覧](reference-v2-libraries.md)
