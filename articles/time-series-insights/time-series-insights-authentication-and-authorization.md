---
title: API の認証と承認 - Azure Time Series Insights | Microsoft Docs
description: この記事では、Azure Time Series Insights API を呼び出すカスタム アプリケーションの認証と承認を構成する方法を説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: beefad41a270233336bb9134268c98341e81a7cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "81380808"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Azure Time Series Insights API の認証と承認

このドキュメントでは、新しい Azure Active Directory ブレードを使用して Azure Active Directory にアプリを登録する方法について説明します。 Azure Active Directory に登録されたアプリを使用すると、ユーザーは、Time Series Insights 環境に関連付けられた Azure Time Series Insight API を使用する認証と承認を受けることができます。

> [!IMPORTANT]
> Azure Time Series Insights は、次の認証ライブラリの両方をサポートしています:
> * より新しい [Microsoft 認証ライブラリ (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * [Azure Active Directory 認証ライブラリ (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="service-principal"></a>サービス プリンシパル

以下のセクションでは、アプリケーションを構成して、そのアプリの代わりに Time Series Insights API にアクセスする方法について説明します。 アプリケーションは、Azure Active Directory を介して独自のアプリケーション資格情報を使用し、Time Series Insights 環境で参照データをクエリまたは発行することができます。

## <a name="summary-and-best-practices"></a>概要とベスト プラクティス

Azure Active Directory のアプリ登録フローには、主に 3 つの手順があります。

1. Azure Active Directory に[アプリケーションを登録する](#azure-active-directory-app-registration)。
1. [Time Series Insights 環境へのデータ アクセス](#granting-data-access)をアプリケーションに承認します。
1. **クライアント アプリ**の  **からトークンを取得するには、** [申請 ID]`https://api.timeseries.azure.com/` と[[クライアント シークレット]](#client-app-initialization) を使用します。 トークンは、Time Series Insights API の呼び出しに使用できます。

**手順 3** に従い、アプリケーションとユーザーの資格情報を分離すると、次のことが可能になります。

* 独自のアクセス許可とは異なるアクセス許可を、アプリ ID に割り当てることができます。 通常、こうしたアクセス許可はアプリで必要な操作のみに制限されます。 たとえば、アプリが特定の Time Series Insights 環境でデータの読み取りだけを行うようにすることが可能です。
* **クライアント シークレット**またはセキュリティ証明書を使用して、作成しているユーザーの認証資格情報から、アプリのセキュリティを分離します。 その結果、アプリケーションの資格情報は特定のユーザーの資格情報に依存しません。 ユーザーのロールが変わっても、アプリケーションは必ずしも新しい資格情報や追加の構成を必要としません。 ユーザーが自分のパスワードを変更した場合、アプリケーションへのすべてのアクセスに新しい資格情報やキーは必要ありません。
* (存在する必要がある) 特定のユーザーの資格情報ではなく、**クライアント シークレット**またはセキュリティ証明書を使用して無人スクリプトを実行します。
* Azure Time Series Insights API へのアクセスをセキュリティで保護するには、パスワードではなくセキュリティ証明書を使用します。

> [!IMPORTANT]
> Azure Time Series Insights セキュリティ ポリシーを構成するときは、**懸念事項の分離** (このシナリオについては前述を参照) の原則に従います。

> [!NOTE]
> * この記事では、シングルテナント アプリケーション (1 つの組織内でのみ実行することを目的としたアプリケーション) に焦点を絞って説明します。
> * 通常は、組織内で実行される基幹業務アプリケーションには、シングルテナント アプリケーションが使用されます。

## <a name="detailed-setup"></a>詳細なセットアップ

### <a name="azure-active-directory-app-registration"></a>Azure Active Directory のアプリ登録

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

### <a name="granting-data-access"></a>データ アクセスの許可

1. Time Series Insights 環境のための **[データ アクセス ポリシー]** を選択して、 **[追加]** を選択します。

   [![Time Series Insights 環境に新しいデータ アクセス ポリシーを追加する](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. **[ユーザーの選択]** ダイアログ ボックスで、Azure Active Directory のアプリ登録セクションの **[アプリケーション名]** または **[申請 ID]** のいずれかを貼り付けます。

   [![[ユーザーの選択] ダイアログ ボックスでアプリケーションを検索する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. ロールを選択します。 データのクエリを実行するには **[閲覧者]** を選択し、データのクエリを実行して参照データを変更するには **[共同作成者]** を選択します。 **[OK]** を選択します。

   [![[ユーザー ロールの選択] ダイアログ ボックスで、[閲覧者] または [共同作成者] を選択する](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. **[OK]** を選択してポリシーを保存します。

   > [!TIP]
   > 高度なデータ アクセスのオプションについては、[データ アクセスの許可](./time-series-insights-data-access.md)に関する記事を参照してください。

### <a name="client-app-initialization"></a>クライアント アプリの初期化

* 開発者は、[Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) または [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) を使用して Azure Time Series Insights で認証することができます。

* たとえば、ADAL を使用して認証するには、次のようにします:

   1. アプリケーションに代わってトークンを取得するには、Azure Active Directory のアプリ登録セクションの **[申請 ID]** と **[クライアント シークレット]** (アプリケーション キー) を使用します。

   1. C# では、次のコードでアプリケーションに代わってトークンを取得できます。 サンプルの詳細については、[C# を使用したデータの詳細](time-series-insights-query-data-csharp.md)に関する記事を参照してください。

        [!code-csharp[csharpquery-example](~/samples-tsi/csharp-tsi-ga-sample/Program.cs?range=170-199)]

   1. トークンは、アプリケーションが Time Series Insights API を呼び出すときに、`Authorization` ヘッダーで渡すことができます。

* また、開発者は MSAL を使用して認証を選択することができます。 詳細については、[MSAL への移行](https://docs.microsoft.com/azure/active-directory/develop/msal-net-migration)に関するページを確認し、「[C# を使用して Azure Time Series Insights 環境の GA 参照データを管理する](time-series-insights-manage-reference-data-csharp.md)」の記事を参照してください。 

## <a name="common-headers-and-parameters"></a>一般的なヘッダーとパラメーター

このセクションでは、Time Series Insights GA や Preview API に対するクエリの作成に使用される、一般的な HTTP 要求ヘッダーとパラメーターについて説明します。 API 固有の要件は、[Time Series Insights REST API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/)に詳しく記載されています。

> [!TIP]
> REST API の使用方法、HTTP 要求の作成方法、および HTTP 応答の処理方法の詳細については、[Azure REST API リファレンス](https://docs.microsoft.com/rest/api/azure/) を参照してください。

### <a name="authentication"></a>認証

[Time Series Insights REST APIs](https://docs.microsoft.com/rest/api/time-series-insights/) に対して認証されたクエリを実行するには、任意の REST クライアント (Postman、JavaScript、C#) を使用して、有効な OAuth 2.0 ベアラー トークンを [Authorization ヘッダー](/rest/api/apimanagement/2019-12-01/authorizationserver/createorupdate)内で渡す必要があります。 

> [!TIP]
> チャートやグラフと共に [JavaScript クライアント SDK](https://tsiclientsample.azurewebsites.net/) を使用して、プログラムによって Time Series Insights API で認証を行う方法については、Azure Time Series Insights の[クライアント SDK のサンプルの視覚化](https://github.com/microsoft/tsiclient/blob/master/docs/API.md)に関する記事を参照してください。

### <a name="http-headers"></a>HTTP ヘッダー

必要な要求ヘッダーを以下に示します。

| 必要な要求ヘッダー | 説明 |
| --- | --- |
| 承認 | Time Series Insights で認証するには、有効な OAuth 2.0 ベアラートークンを **Authorization** ヘッダーに渡す必要があります。 | 

> [!IMPORTANT]
> そのトークンは、まさしくその `https://api.timeseries.azure.com/` リソース (トークンの "audience" とも呼ばれます) に向けて発行される必要があります。
> * したがって、[Postman](https://www.getpostman.com/) **AuthURL** は`https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=https://api.timeseries.azure.com/` のようになります
> * `https://api.timeseries.azure.com/` は有効ですが、`https://api.timeseries.azure.com` は有効ではありません。

省略可能な要求ヘッダーを以下に示します。

| 省略可能な要求ヘッダー | 説明 |
| --- | --- |
| Content-type | `application/json` のみがサポートされています。 |
| x-ms-client-request-id | クライアント要求 ID。 サービスでは、この値です。 これにより、サービスでは、サービス間の操作を追跡できるようになります。 |
| x-ms-client-session-id | クライアントセッション ID。 サービスでは、この値です。 これにより、サービスでは、サービス間の関連する操作のグループを追跡できるようになります。 |
| x-ms-client-application-name | この要求を生成したアプリケーションの名前。 サービスでは、この値です。 |

省略可能ですが、推奨される応答ヘッダーを以下に示します。

| 応答ヘッダー | 説明 |
| --- | --- |
| Content-type | サポートされるのは `application/json` のみです。 |
| x-ms-request-id | サーバーで生成された要求 ID。 要求の調査についての Microsoft への問い合わせに使用できます。 |
| x-ms-property-not-found-behavior | GA API オプションの応答ヘッダー。 指定できる値は、`ThrowError` (既定値) または `UseNull`です。 |

### <a name="http-parameters"></a>HTTP パラメーター

> [!TIP]
> 必須および省略可能なクエリの情報の詳細については、[参照ドキュメント](https://docs.microsoft.com/rest/api/time-series-insights/)を参照してください。

必須の URL クエリ文字列パラメーターは、API バージョンによって異なります。

| Release | 使用可能な API バージョンの値 |
| --- |  --- |
| 一般公開 | `api-version=2016-12-12`|
| プレビュー | `api-version=2018-11-01-preview` |
| プレビュー | `api-version=2018-08-15-preview` |

省略可能な URL クエリ文字列パラメーターには、HTTP 要求の実行時間のタイムアウト設定が含まれます。

| 省略可能なクエリパラメーター | 説明 | Version |
| --- |  --- | --- |
| `timeout=<timeout>` | HTTP 要求実行のサーバー側のタイムアウト。 [環境イベントの取得](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-events-api) API と[環境集計の取得](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api#get-environment-aggregates-api) API にのみ適用できます。 タイムアウト値は、`"PT20S"` など、ISO 8601 の期間の形式で指定され、その範囲は `1-30 s` である必要があります。 既定値は `30 s` です。 | GA |
| `storeType=<storeType>` | ウォームストアが有効になっているプレビュー環境では、`WarmStore` または `ColdStore`のいずれかでクエリを実行できます。 クエリ内のこのパラメーターは、クエリを実行する必要があるストアを定義します。 定義されていない場合は、コールドストアでクエリが実行されます。 ウォームストアに対してクエリを実行するには、**storeType** を `WarmStore`に設定する必要があります。 定義されていない場合は、コールドストアに対してクエリが実行されます。 | プレビュー |

## <a name="next-steps"></a>次のステップ

- GA Time Series Insights API を呼び出すサンプル コードについては、[C# を使用したデータの照会](./time-series-insights-query-data-csharp.md)に関する記事を参照してください。

- プレビュー Time Series Insights API コード サンプルについては、[C# を使用したプレビュー データの照会](./time-series-insights-update-query-data-csharp.md)に関する記事を参照してください。

- API リファレンスについては、[クエリ API リファレンス](https://docs.microsoft.com/rest/api/time-series-insights/ga-query-api)のドキュメントを参照してください。

- [サービス プリンシパルを作成する](../active-directory/develop/howto-create-service-principal-portal.md)方法について学習します。
