---
title: Azure Function App の設定の構成 | Microsoft Docs
description: Azure Function App の設定を構成する方法について説明します。
services: ''
documentationcenter: .net
author: ggailey777
manager: jeconnoc
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 92ca09040836dfc55a9d709b12a0ee01192d6bac
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65957396"
---
# <a name="how-to-manage-a-function-app-in-the-azure-portal"></a>Azure Portal で関数アプリを管理する方法 

Azure Functions では、関数アプリに個々の関数の実行コンテキストが用意されています。 関数アプリの動作は、特定の関数アプリによってホストされるすべての関数に適用されます。 このトピックでは、Azure Portal で関数アプリを構成して管理する方法について説明します。

まず、[Azure Portal](https://portal.azure.com) に移動し、Azure アカウントにサインインします。 ポータルの上部にある検索バーで関数アプリの名前を入力し、一覧からその関数アプリを選択します。 関数アプリを選択すると、次のページが表示されます。

![Azure Portal の関数アプリの概要](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

関数アプリの管理に必要なすべての機能には、概要ページからアクセスできます (特に **[[アプリケーションの設定]](#settings)** と **[[プラットフォーム機能]](#platform-features)** )。

## <a name="settings"></a>アプリケーションの設定

**[アプリケーションの設定]** タブでは、関数アプリに使用される設定を管理します。

![Azure portal の関数アプリの設定。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

これらの設定は暗号化されて格納されているため、ポータルで値を表示するには **[値を表示する]** を選択する必要があります。

設定を追加するには、 **[新しいアプリケーション設定]** を選択して新しいキーと値のペアを追加します。

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

ローカルで関数アプリを開発すると、これらの値は local.settings.json プロジェクト ファイルに保持されます。

## <a name="platform-features"></a>プラットフォーム機能

![関数アプリの [プラットフォーム機能] タブ。](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

関数アプリは、Azure App Service プラットフォームで実行され、管理されます。 このため、Azure の主要 Web ホスティング プラットフォームのほとんどの機能にアクセスできます。 **[プラットフォーム機能]** タブでは、関数アプリで使用できる App Service プラットフォームの多くの機能にアクセスできます。 

> [!NOTE]
> 関数アプリが従量課金ホスティング プランで実行されている場合は、一部の App Service 機能が使用できません。

このトピックの残りの部分では、Azure Portal で使用できる次の App Service 機能について説明します。

+ [App Service Editor](#editor)
+ [Console](#console)
+ [高度なツール (Kudu)](#kudu)
+ [デプロイ オプション](#deployment)
+ [CORS](#cors)
+ [認証](#auth)
+ [API の定義](#swagger)

App Service の設定を使用する方法の詳細については、[Azure App Service の設定の構成](../app-service/configure-common.md)に関するページをご覧ください。

### <a name="editor"></a>App Service Editor

| | |
|-|-|
| ![関数アプリの App Service Editor。](./media/functions-how-to-use-azure-function-app-settings/function-app-appsvc-editor.png)  | App Service Editor は、JSON 構成ファイルとコード ファイルを変更するために使用できる高度なポータル内エディターです。 このオプションを選択すると、別のブラウザー タブが基本的なエディターで起動します。 これにより、Git リポジトリとの統合、コードの実行とデバッグ、および関数アプリの設定変更を行うことができます。 このエディターでは、既定の関数アプリ ブレードと比較して、関数の開発環境が強化されています。    |

![App Service Editor](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

### <a name="console"></a>コンソール

| | |
|-|-|
| ![Azure Portal の関数アプリ コンソール](./media/functions-how-to-use-azure-function-app-settings/function-app-console.png) | ポータル内コンソールは、コマンド ラインから関数アプリを操作するのに適した開発者ツールです。 一般的なコマンドには、ディレクトリやファイルの作成、ナビゲーション、バッチ ファイルやスクリプトの実行などがあります。 |

![関数アプリ コンソール](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

### <a name="kudu"></a>高度なツール (Kudu)

| | |
|-|-|
| ![Azure Portal の関数アプリ Kudu](./media/functions-how-to-use-azure-function-app-settings/function-app-advanced-tools.png) | App Service 用の高度なツール (Kudu とも呼ばれます) を使用すると、関数アプリの高度な管理機能にアクセスできます。 Kudu から、システム情報、アプリ設定、環境変数、サイト拡張機能、HTTP ヘッダー、およびサーバー変数を管理します。 また、`https://<myfunctionapp>.scm.azurewebsites.net/` など、関数アプリの SCM エンドポイントにアクセスして、**Kudu** を起動することもできます |

![Kudu の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)


### <a name="a-namedeploymentdeployment-options"></a><a name="deployment">デプロイ オプション

| | |
|-|-|
| ![Azure Portal の関数アプリのデプロイ オプション](./media/functions-how-to-use-azure-function-app-settings/function-app-deployment-source.png) | Functions を使用すると、ローカル コンピューターで関数コードを開発できます。 その後、ローカル関数アプリ プロジェクトを Azure にアップロードすることができます。 Functions を使用すると、従来の FTP アップロード以外に、GitHub、Azure DevOps、Dropbox、Bitbucket などの一般的な継続的インテグレーション ソリューションを使用して、関数アプリをデプロイできます。 詳細については、「[Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)」を参照してください。 FTP またはローカル Git を使用して手動でアップロードするには、[デプロイ資格情報を構成](functions-continuous-deployment.md#credentials)する必要もあります。 |


### <a name="cors"></a>CORS

| | |
|-|-|
| ![Azure Portal の関数アプリ CORS](./media/functions-how-to-use-azure-function-app-settings/function-app-cors.png) | 悪意のあるコードがサービスで実行されるのを防ぐために、App Service は、外部ソースから関数アプリへの呼び出しをブロックします。 Functions ではクロス オリジン リソース共有 (CORS) がサポートされ、許可されたオリジンの "ホワイトリスト" を定義できます。関数が受け入れるのは、このホワイトリストに登録されたソースからのリモート要求です。  |

![Function App の CORS の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

### <a name="auth"></a>認証

| | |
|-|-|
| ![Azure Portal の関数アプリの認証](./media/functions-how-to-use-azure-function-app-settings/function-app-authentication.png) | 関数が HTTP トリガーを使用するとき、呼び出しに、最初に認証を必須とすることができます。 App Service では、Azure Active Directory 認証と、Facebook、Microsoft、Twitter などのソーシャル プロバイダーを使用したサインインがサポートされます。 特定の認証プロバイダーの構成の詳細については、[Azure App Service での認証の概要](../app-service/overview-authentication-authorization.md)に関するページを参照してください。 |

![関数アプリの認証を構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)


### <a name="swagger"></a>API の定義

| | |
|-|-|
| ![Azure Portal の関数 アプリ API Swagger の定義](./media/functions-how-to-use-azure-function-app-settings/function-app-api-definition.png) | Functions では Swagger がサポートされ、クライアントが、HTTP によってトリガーされる関数をもっと簡単に使用できます。 Swagger を使用した API 定義の作成の詳細については、[Azure App Service での CORS を使用した RESTful API のホスト](../app-service/app-service-web-tutorial-rest-api.md)に関するページをご覧ください。 Functions Proxies を使用して、複数の関数に対して 1 つの API サーフェスを定義することをもできます。 詳細については、「[Azure Functions Proxies の操作](functions-proxies.md)」を参照してください。 |

![Function App の API の構成](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-apidef.png)



## <a name="next-steps"></a>次の手順

+ [Azure App Service の設定の構成](../app-service/configure-common.md)
+ [Azure Functions の継続的なデプロイ](functions-continuous-deployment.md)



