---
title: Azure Digital Twins API の認証の理解 | Microsoft Docs
description: Azure Digital Twins を使用して API を接続および認証する
author: lyrana
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/02/2018
ms.author: lyrana
ms.openlocfilehash: dc5570b188bfdc0e1be78aa2bd5c5d92e884f377
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2018
ms.locfileid: "49638020"
---
# <a name="connect-and-authenticate-to-apis"></a>API に接続および認証する

Azure Digital Twins は、Azure Active Directory (Azure AD) を使用してユーザーを認証し、アプリケーションを保護します。 Azure AD は、さまざまな最新アーキテクチャ向けの認証をサポートしています。そのいずれも、業界標準のプロトコルである OAuth 2.0 または OpenID Connect に基づいています。 さらに、Azure AD は、シングル テナント アプリケーションと基幹業務 (LOB) アプリケーションを構築する開発者、さらには、マルチテナント アプリケーションの開発を目指す開発者を支援します。

Azure AD の概要については、[基礎ページ](https://docs.microsoft.com/azure/active-directory/fundamentals/index)にアクセスしてステップ バイ ステップ ガイド、概念、およびクイック スタートを確認してください。

アプリケーションまたはサービスを Azure AD と統合するには、まず開発者がアプリケーションを Azure AD に登録しておく必要があります。 詳しい手順とスクリーン ショットについては、[こちら](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-add-azure-ad-app)の手順をご覧ください。

Azure AD でサポートされる [5 つの主要なアプリケーション シナリオ](https://docs.microsoft.com/azure/active-directory/develop/v2-app-types)は、次のとおりです。

* シングル ページ アプリケーション (SPA): ユーザーは、Azure AD によって保護されたシングル ページ アプリケーションにサインインする必要があります。
* Web ブラウザー対 Web アプリケーション: ユーザーは、Azure AD によって保護された Web アプリケーションにサインインする必要があります。
* ネイティブ アプリケーション対 Web API: スマートフォン、タブレット、または PC で実行されるネイティブ アプリケーションは、Azure AD によって保護された Web API からリソースを取得するために、ユーザーを認証する必要があります。
* Web アプリケーション対 Web API: Web アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。
* デーモンまたはサーバー アプリケーション対 Web API: Web ユーザー インターフェイスを備えていないデーモン アプリケーションまたはサーバー アプリケーションは、Azure AD によって保護された Web API からリソースを取得する必要があります。

Microsoft Azure 認証ライブラリでは、さまざまな方法で Active Directory トークンを取得できます。 ライブラリの詳細とコード サンプルについては、[こちら](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki)をご覧ください。

## <a name="calling-digital-twins-from-a-middle-tier-web-api"></a>中間層 Web API からの Digital Twins の呼び出し

Digital Twins ソリューションを構築するとき、開発者は一般的に Digital Twins API ダウンストリームを呼び出す中間層アプリケーションまたは API を作成します。 ユーザーはまず中間層アプリケーションに対して認証を行い、ダウンストリームを呼び出すときには代理のトークン フローが使用されます。 代理フローを統合する方法に関する詳細な手順については、[こちらのページ](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)にアクセスしてください。 [こちら](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapi-onbehalfof/)でコード サンプルを確認することもできます。


## <a name="test-with-the-postman-client"></a>Postman クライアントでのテストの実行

Digital Twins API を起動して実行するには、API 環境として Postman などのクライアントを使用できます。 Postman は複雑な HTTP 要求をすばやく作成するのに役立ちます。 以下の手順は Postman UI 内で Digital Twins を呼び出すのに必要な Azure AD トークンを取得する方法に焦点を当てています。


1. https://www.getpostman.com/ に移動してアプリをダウンロードします。
1. [こちら](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad)の手順に従って Azure Active Directory アプリケーションを作成します (または既存の登録を再利用することもできます)。 
1. 要求されたアクセス許可に "Azure Digital Twins" を追加して [委任されたアクセス許可] を選択します。 [アクセス許可の付与] をクリックして確定することを忘れずに。
1. アプリケーション マニフェストを開き、oauth2AllowImplicitFlow を true に設定します。
1. 応答 URL を [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) に構成します。
1. **[承認] タブ**をクリックし、**[OAuth 2.0]** を選択して **[Get New Access Token]\(新しいアクセス トークンの取得\)** を選択します。

    |**フィールド**  |**値** |
    |---------|---------|
    | 付与タイプ | 暗黙 |
    | コールバック URL | [https://www.getpostman.com/oauth2/callback](https://www.getpostman.com/oauth2/callback) |
    | 認証 URL | [https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0](https://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0)
    | クライアント ID | 手順 1 で作成または別の用途のために作り変えた Azure AD アプリのアプリケーション ID を使用する |
    | Scope (スコープ) | 空白のままにする |
    | 州 | 空白のままにする |
    | クライアント認証 | 基本 Auth ヘッダーとして送信する |

1. **[Request Token]\(要求トークン\)** をクリックします。

    >[!NOTE]
    >「OAuth 2 couldn’t be completed」(OAuth 2 を完了できませんでした) というエラー メッセージを受け取った場合は、次の操作を試してください。
    > * Postman を閉じて再起動し、もう一度実行する。
   
1. 下へスクロールし、**[Use Token]\(トークンの使用\)** をクリックします。

## <a name="next-steps"></a>次の手順

Azure Digital Twins のセキュリティについては、「[ロールの割り当てを作成および管理する](./security-create-manage-role-assignments.md)」をご覧ください。
