---
title: 分析データにプログラムでアクセスするための前提条件
description: プログラムを使用してコマーシャル マーケットプレースの分析データにアクセスする前に満たす必要がある要件について説明します。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: b61608c0cb53ab808c5d3d789ec5ddc318c6923d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106804"
---
# <a name="prerequisites-to-programmatically-access-analytics-data"></a>分析データにプログラムでアクセスするための前提条件

プログラムを使用してコマーシャル マーケットプレースの分析データにアクセスするには、次の要件を満たす必要があります。

## <a name="commercial-marketplace-enrollment"></a>コマーシャル マーケットプレースに登録する

プログラムを使用してコマーシャル マーケットプレースの分析データにアクセスするには、コマーシャル マーケットプレース プログラムへの登録と、パートナーセンターのアカウントが必要です。 方法については、「[パートナー センターでコマーシャル マーケットプレース アカウントを作成する](create-account.md)」を参照してください。

## <a name="create-azure-active-directory-application"></a>Azure Active Directory アプリケーションを作成する

通常のユーザー資格情報を使用して、コマーシャル マーケットプレースの分析データにプログラムでアクセスすることはできません。 Analytics API にアクセスするには、Azure Active Directory (Azure AD) アプリケーションとシークレットを作成する必要があります。 Azure AD アプリケーションとシークレットの作成方法については、「[クイック スタート: Microsoft ID プラットフォームにアプリケーションを登録する](../active-directory/develop/quickstart-register-app.md)」を参照してください。

## <a name="associate-the-azure-ad-application-to-the-partner-center-tenant"></a>Azure AD アプリケーションをパートナー センター テナントに関連付ける

Azure portal で作成した Azure AD アプリケーションをパートナー センター アカウントにリンクする必要があります。 手順は次のとおりです。

1. [パートナー センター](https://partner.microsoft.com/dashboard)にサインインします。
1. 右上にある歯車アイコンを選択し、 **[アカウント設定]** を選択します。
1. **[アカウント設定]** メニューで、 **[ユーザー管理]** を選択します。
1. **[Azure AD アプリケーション]** を選択し、 **[+ Azure AD アプリケーションの作成]** を選択します。
1. Azure portal で作成した Azure AD アプリケーションを選択し、 **[次へ]** を選択します。
1. **[マネージャー (Windows)]** チェックボックスをオンにして、 **[追加]** を選択します。

    :::image type="content" source="./media/analytics-programmatic-access/azure-ad-roles.png" alt-text="ロールを選択するためのチェックボックスが表示されている [Azure AD アプリケーションの作成] ページを示しています。":::

## <a name="generate-an-azure-ad-token"></a>Azure AD トークンを生成する

アプリケーション (クライアント) ID を使用して、Azure AD トークンを生成する必要があります。 この ID を使用すると、Microsoft ID プラットフォームでクライアント アプリケーションを、また前の手順のクライアント シークレットを一意に識別することができます。 Azure AD トークンを生成する手順については、「[クライアント資格情報を使用したサービス間の呼び出し (共有シークレットまたは証明書)](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)」を参照してください。

> [!NOTE]
> トークンの有効期間は 1 時間です。

## <a name="next-steps"></a>次のステップ

- [プログラムによるアクセスのパラダイム](analytics-programmatic-access.md)