---
title: Azure Time Series Insights の API によって認証と承認を行う方法 | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237050"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションで使用される認証と承認を構成する方法を説明します。

> [!TIP]
> Azure Active Directory の Time Series Insights 環境に対する[データ アクセスの許可](./time-series-insights-data-access.md)について参照します。

## <a name="service-principal"></a>サービス プリンシパル

このセクションと後続のセクションでは、アプリケーションを構成して、そのアプリケーションの代わりに Time Series Insights API にアクセスする方法について説明します。 このアプリケーションでは、ユーザー資格情報ではなくアプリケーションの資格情報を使って、Time Series Insights 環境にある参照データをクエリしたり公開したりすることができます。

## <a name="best-practices"></a>ベスト プラクティス

Time Series Insights にアクセスする必要があるアプリケーションがある場合

1. Azure Active Directory アプリを設定します。
1. Time Series Insights 環境に[データ アクセス ポリシーを割り当てます](./time-series-insights-data-access.md)。

以下のことから、ユーザーの資格情報ではなく、アプリケーションを使用することが望ましいです。

* 独自のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可はアプリで必要な操作のみに制限されます。 たとえば、アプリが特定の Time Series Insights 環境でデータの読み取りだけを行うようにすることが可能です。
* お客様の責任が変わっても、アプリの資格情報を変更する必要はありません。
* 無人インストール用スクリプトを実行するときに、証明書またはアプリケーション キーを使用して認証を自動化できます。

以下のセクションでは、Azure portal でそれらの手順を行う方法を示します。 この記事では、シングルテナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。 通常は、組織内で実行される基幹業務アプリケーションには、シングルテナント アプリケーションが使用されます。

## <a name="set-up-summary"></a>概要の設定

セットアップ フローは、次の 3 つの手順で構成されます。

1. Azure Active Directory にアプリケーションを作成する。
1. このアプリケーションを承認して Time Series Insights 環境にアクセスする。
1. アプリケーション ID とキーを使って、`https://api.timeseries.azure.com/` からトークンを取得する。 トークンは、Time Series Insights API の呼び出しに使用できます。

## <a name="detailed-setup"></a>詳細なセットアップ

1. Azure Portal で、 **[Azure Active Directory]**  >  **[アプリの登録]**  >  **[新しいアプリケーションの登録]** の順に選びます。

   [![Azure Active Directory での新しいアプリケーションの登録](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. アプリケーションに名前を付けて、種類として **[Web アプリ/API]** を選択し、 **[サインオン URL]** に任意の有効な URI を選択して、 **[作成]** をクリックします。

   [![Azure Active Directory にアプリケーションを作成する](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. 新しく作成されたアプリケーションを選択し、アプリケーション ID を普段使用しているテキスト エディターにコピーします。

   [![アプリケーション ID をコピーする](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. **[キー]** を選択してキー名を入力し、有効期限を選択して、 **[保存]** をクリックします。

   [![アプリケーション キーを選択する](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![キー名と有効期限を入力し、[保存] をクリックする](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. 普段使用しているテキスト エディターにキーをコピーします。

   [![アプリケーション キーをコピーする](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Time Series Insights 環境のための **[データ アクセス ポリシー]** を選択して、 **[追加]** をクリックします。

   [![Time Series Insights 環境に新しいデータ アクセス ポリシーを追加する](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **[ユーザーの選択]** ダイアログ ボックスに、アプリケーション名 (**手順 2** より) またはアプリケーション ID (**手順 3** より) を貼り付けます。

   [![[ユーザーの選択] ダイアログ ボックスでアプリケーションを検索する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. ロール (データを照会する場合は **[閲覧者]** 、データを照会して参照データを変更する場合は **[共同作成者]** ) を選択して、 **[OK]** をクリックします。

   [![[ロールの選択] ダイアログ ボックスで、[閲覧者] または [共同作成者] を選択する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **[OK]** をクリックしてポリシーを保存します。

1. アプリケーション ID (**手順 3** より) とアプリケーション キー (**手順 5** より) を使用して、アプリケーションの代わりにトークンを取得します。 トークンは、アプリケーションが Time Series Insights API を呼び出すときに、`Authorization` ヘッダーで渡すことができます。

    C# を使用している場合は、次のコードを使って、アプリケーションのためのトークンを取得できます。 サンプルの詳細については、[C# を使用したデータの照会](time-series-insights-query-data-csharp.md)に関する記事をご覧ください。

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

ご利用のアプリケーションの**アプリケーション ID** と**キー**を使用し、Azure Time Series Insight で認証します。

## <a name="next-steps"></a>次の手順

- Time Series Insights API を呼び出すサンプル コードについては、[C# を使用したデータの照会](time-series-insights-query-data-csharp.md)に関する記事をご覧ください。

- API リファレンスについては、[API リファレンスのクエリ](/rest/api/time-series-insights/ga-query-api)に関するページをご覧ください。

- [サービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)方法について学習します。
