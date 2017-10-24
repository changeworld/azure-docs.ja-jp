---
title: "カスタム コネクタの概要 - Azure Logic Apps | Microsoft Docs"
description: "統合シナリオをサポートおよび展開するためのカスタム コネクタの作成の概要"
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 0515b21bc7c7cc737e14fda016606bbea1aab476
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="custom-connectors-overview"></a>カスタム コネクタの概要

コードを記述しなくても、[Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)、[Microsoft Flow](https://flow.microsoft.com)、または [Microsoft PowerApps](https://powerapps.microsoft.com) を使用してワークフローやアプリを作成できます。 データやビジネス プロセスの統合を支援するために、これらのサービスでは [100 種類以上のコネクタ](../connectors/apis-list.md)を提供しています。Azure や SQL Server などの Microsoft のサービスおよび製品用のコネクタだけでなく、GitHub、Salesforce、Twitter などの他のサービス用のコネクタも用意されています。 

ただし、事前に作成されたコネクタとして使用できない API、サービス、システムを呼び出すことが必要な場合もあります。 ユーザーのビジネス ニーズや生産性のニーズに合わせてカスタマイズされたシナリオをサポートするために、独自のトリガーとアクションを含む "*カスタム コネクタ*" を作成できます。
カスタム コネクタにより、サービスや製品の統合、リーチ、見つけやすさ、用途が拡大されるので、顧客による導入を増やし、導入を促進することができます。

たとえば、次の図は、Web API、その API 用に作成されたカスタム Logic Apps コネクタ、そのカスタム コネクタを使用して API で動作するロジック アプリ間の対話を示しています。

![Web API、カスタム コネクタ、ロジック アプリの概念の概要](./media/custom-connector-overview/custom-connector-conceptual.png)

この概要では、コネクタを作成、セキュリティ保護、登録、使用し、必要に応じて共有または認定するための一般的な作業について簡単に説明します。

![カスタム コネクタの作成手順](./media/custom-connector-overview/authoring-steps.png)

## <a name="prerequisites"></a>前提条件

コネクタを最初から最後まで作成するには、次のものが必要です。

* Azure サブスクリプション。 サブスクリプションがない場合は、[無料の Azure アカウント](https://azure.microsoft.com/free/)をご利用いただけます。 また、[従量課金のサブスクリプション](https://azure.microsoft.com/pricing/purchase-options/)にサインアップすることもできます。

* 何らかの種類の認証済みアクセスを使用する RESTful API。 詳細については、「[Create custom connectors from Web APIs (Web API からカスタム コネクタを作成する)](../logic-apps/custom-connector-build-web-api-app-tutorial.md)」をご覧ください。 コネクタのトリガーとアクションに使用できるパターンについては、「[Create custom APIs that you can call from logic app workflows (ロジック アプリ ワークフローから呼び出すことができるカスタム API を作成する)](../logic-apps/logic-apps-create-api-app.md)」をご覧ください。

* 次のいずれかが必要です。

  * [OpenAPI 2.0 ファイル](https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md) (旧称 Swagger)
  * OpenAPI 定義の URL
  * API の [Postman Collection](../logic-apps/custom-connector-api-postman-collection.md) 

  上記のいずれもない場合は、ガイダンスが提供されます。

* 任意: カスタム コネクタのアイコンとして使用する画像

## <a name="1-build-your-restful-api"></a>1.RESTful API を作成する

技術的には、コネクタは OpenAPI (旧称 Swagger) 仕様に基づく REST API のラッパーです。コネクタにより、基になるサービスは Logic Apps、Flow、または PowerApps と対話することができます。 そのため、カスタム コネクタを作成する前に、まず、完全に機能している API が必要となります。 

API には、任意の言語とプラットフォームをご利用いただけます。 Microsoft のテクノロジーの場合、次のプラットフォームのいずれかを使用することをお勧めします。

* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure Web Apps](https://azure.microsoft.com/services/app-service/web/)
* [Azure API Apps](https://azure.microsoft.com/services/app-service/api/)

たとえば、[このチュートリアル](../logic-apps/custom-connector-build-web-api-app-tutorial.md)では、Web API からカスタム コネクタを作成する方法を説明しています。 コネクタのトリガーとアクションに使用できるパターンについては、「[Create custom APIs that you can call from logic app workflows (ロジック アプリ ワークフローから呼び出すことができるカスタム API を作成する)](../logic-apps/logic-apps-create-api-app.md)」をご覧ください。

## <a name="2-secure-your-api"></a>手順 2.API をセキュリティで保護する

API とコネクタには、次の認証標準をご利用いただけます。

   * [OAuth 2.0](https://oauth.net/2/) ([Azure Active Directory](https://azure.microsoft.com/develop/identity/)、または Dropbox、GitHub、SalesForce などの特定のサービスを含む)
   * 汎用 OAuth 2.0
   * [基本認証](https://swagger.io/docs/specification/authentication/basic-authentication/)
   * [API キー](https://swagger.io/docs/specification/authentication/api-keys/)

Azure Portal で API の Azure Active Directory (Azure AD) Authentication を設定できるので、コードによって認証を実装する必要はありません。 あるいは、API のコードで認証を要求し、強制できます。 

詳細については、次の該当するチュートリアルに従ってください。

* [Logic Apps: カスタム コネクタのセキュリティ保護](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Flow: カスタム コネクタのセキュリティ保護](https://ms.flow.microsoft.com/documentation/customapi-azure-resource-manager-tutorial/)
* [PowerApps: カスタム コネクタのセキュリティ保護](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/)

## <a name="3-describe-your-api"></a>手順 3.API を記述する 

API が何らかの種類の認証済みアクセスを使用する場合、Logic Apps、Flow、または PowerApps が API と通信できるように、API のインターフェイスと操作を記述する必要があります。
次の業界標準定義のいずれかを使用します。

* [OpenAPI 2.0 ファイル](https://swagger.io/)。既存の OpenAPI ファイルを使用して作成を開始できます。

  OpenAPI を初めて使用する場合は、swagger.io サイトの「[Getting started with Swagger (Swagger の概要)](http://swagger.io/getting-started/)」をご覧ください。
  OpenAPI ファイルの例については、[Text Analytics API のドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/export?DocumentFormat=Swagger&ApiName=Azure)をご覧ください。 

* OpenAPI ファイルを自動的に生成する Postman Collection。 OpenAPI ファイルがまだなく、このファイルを作成しない場合でも、Postman Collection を使用してカスタム コネクタを簡単に作成できます。

  Postman を初めて使用する場合は、Postman のサイトから [Postman アプリをインストール](https://www.getpostman.com/apps)します。 詳細については、[Postman を使用したカスタム コネクタの記述](../logic-apps/custom-connector-api-postman-collection.md)に関する記事をご覧ください。

> [!IMPORTANT]
> ファイル サイズは 1 MB 未満である必要があります。

バックグラウンドでは、Logic Apps、Flow、PowerApps は最終的に OpenAPI を使用し、Postman Collection を解析して、コレクションを OpenAPI 定義ファイルに変換します。 OpenAPI 2.0 と Postman Collection では使用する形式が異なりますが、どちらも言語に依存しない、コンピューターが読み取り可能なドキュメントであり、API の操作とパラメーターを記述します。 これらのドキュメントは、API で使用される言語とプラットフォームに応じて、さまざまなツールで生成できます。 OpenAPI ファイルは、コネクタの登録時に作成することもできます。

たとえば、次のような REST API エンドポイントから OpenAPI ファイルまたは Postman Collection を作成できます。

* 公開されているコネクタ ([Spotify](https://developer.spotify.com/)、[Slack](https://api.slack.com/)、[Rackspace](http://docs.rackspace.com/) など)

* クラウド ホスティング プロバイダー (Azure、Heroku、Google Cloud など) にデプロイされている作成済みの API

* ネットワークにデプロイされたカスタム基幹業務 API (API がパブリック インターネット上で公開されている場合のみ)

## <a name="4-register-your-connector"></a>4.コネクタを登録する

登録プロセスにより、Logic Apps、Flow、PowerApps は API の特性 (記述、必要な認証、操作、各操作のパラメーターと出力など) を認識することができます。 登録プロセスを開始するときに、OpenAPI ファイルまたは Postman Collection を指定できます。これにより、登録ウィザードにメタデータ フィールドが自動的に設定されます。 これらのフィールドの値はいつでも編集できます。

![API を記述する](./media/custom-connector-overview/choose-api-definition-file.png)

コネクタを登録するには、次の該当するチュートリアルに従ってください。

* [Logic Apps: コネクタの登録](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: コネクタの登録](https://ms.flow.microsoft.com/documentation/register-custom-api/#register-your-custom-connector)
* [PowerApps: コネクタの登録](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector)

## <a name="5-use-your-connector-in-a-logic-app-flow-or-app"></a>5.ロジック アプリ、フロー、またはアプリでコネクタを使用する 

Microsoft が管理するコネクタを使用する場合と同様に、カスタム コネクタをご利用いただけます。 たとえば、ロジック アプリ ワークフローで、API への接続を作成できるようにカスタム コネクタを追加し、Microsoft が管理するコネクタの操作を呼び出す場合と同様に、API が提供する操作を呼び出します。

## <a name="6-share-your-connector"></a>6.コネクタを共有する 

Logic Apps、Flow、または PowerApps でリソースを共有する場合と同様に、コネクタを組織のユーザーと共有できます。 共有は任意ですが、コネクタを他のユーザーと共有することが望ましい場合があります。

登録済みのカスタム コネクタは、認定を受けていなくても、Microsoft が管理するコネクタと同様に機能します。ただし、これらのコネクタを表示および使用できるのは、コネクタの作成者と、ロジック アプリがデプロイされているリージョンで同じ Azure Active Directory テナントとロジック アプリの Azure サブスクリプションを持つユーザーに "*限られます*"。 これらの境界の外側の外部ユーザー (たとえば、Logic Apps、Flow、PowerApps のすべてのユーザー) とコネクタを共有する方法については、次の手順で説明します。

> [!IMPORTANT]
> コネクタを共有すると、他のユーザーがそのコネクタに依存し始める可能性があります。 
> "***コネクタを削除すると、そのコネクタへのすべての接続が削除されます。***"

* [Logic Apps: コネクタの共有](../logic-apps/logic-apps-custom-connector-register.md)
* [Flow: コネクタの共有](https://ms.flow.microsoft.com/documentation/register-custom-api/#share-your-custom-connector)
* [PowerApps: コネクタの共有](https://powerapps.microsoft.com/tutorials/register-custom-api/#share-your-custom-connector)

## <a name="7-certify-your-connector"></a>7.コネクタを認定する

必要に応じて、Logic Apps、Flow、PowerApps のすべてのユーザーとコネクタを共有するには、コネクタを提出して Microsoft の認定を受けます。 このプロセスでは、Microsoft がコネクタを公開する前に、コネクタのレビュー、技術および内容のコンプライアンスの確認、Logic Apps、Flow、PowerApps の機能の検証が行われます。 [Microsoft の認定を受けるためにコネクタを提出する方法](../logic-apps/custom-connector-submit-certification.md)に関する記事をご覧ください。

## <a name="get-support"></a>サポートを受ける

* オンボードや開発でサポートが必要な場合は、[condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) まで電子メールをお送りください。Microsoft では、開発者の質問や問題に対応するために、このアカウントを積極的に監視し、質問や問題を適切なチームに転送しています。 

* よくあるご質問については、[カスタム コネクタに関する FAQ](../logic-apps/custom-connector-faq.md) のページをご覧ください。

## <a name="next-steps"></a>次のステップ

* [Web API からカスタム コネクタを作成する](../logic-apps/custom-connector-build-web-api-app-tutorial.md)
* [カスタム コネクタの認証を設定する](../logic-apps/custom-connector-azure-active-directory-authentication.md)
* [Postman Collection を使用してカスタム コネクタを記述する](../logic-apps/custom-connector-api-postman-collection.md)
* [Microsoft の認定を受けるためにカスタム コネクタを提出する](../logic-apps/custom-connector-submit-certification.md)
