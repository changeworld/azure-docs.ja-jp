---
title: Microsoft Graph API
description: RESTful Web API である Microsoft Graph API を使用すると、Microsoft クラウド サービスのリソースにアクセスできます。
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/08/2021
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a0159f1c633806c26bda39ffe0c8a295bec46982
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2021
ms.locfileid: "129993704"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph API

RESTful Web API である Microsoft Graph API を使用すると、Microsoft クラウド サービスのリソースにアクセスできます。 アプリを登録し、ユーザーまたはサービスのための認証トークンを取得した後、Microsoft Graph API に要求を行うことができます。 詳しくは、「[Microsoft Graph の概要](/graph/overview)」をご覧ください。

Microsoft Graph では、次の Microsoft クラウド サービスのデータにアクセスするための REST API とクライアント ライブラリが公開されています。

- Microsoft 365 サービス:Delve、Excel、Microsoft Bookings、Microsoft Teams、OneDrive、OneNote、Outlook/Exchange、Planner、SharePoint
- Enterprise Mobility + Security サービス: Advanced Threat Analytics、Advanced Threat Protection、Azure Active Directory、Identity Manager、Intune
- Windows 10 サービス: アクティビティ、デバイス、通知
- Dynamics 365 Business Central

## <a name="versions"></a>バージョン

現在、次のバージョンの Microsoft Graph API を使用できます。

- **ベータ版**: ベータ版には、`https://graph.microsoft.com/beta` エンドポイントでアクセスできる API (現在はプレビュー段階) が含まれています。 ベータ版 API の使用を始めるには、「[Microsoft Graph ベータ版のエンドポイント リファレンス](/graph/api/overview?view=graph-rest-beta&preserve-view=true)」をご覧ください。
- **v1.0 バージョン**: v1.0 バージョンには、一般提供され、実稼働環境で使用できる API が含まれています。 v1.0 バージョンには、`https://graph.microsoft.com/v1.0` エンドポイントからアクセスできます。 v1.0 API の使用を始めるには、「[Microsoft Graph REST API v1.0 リファレンス](/graph/api/overview?view=graph-rest-1.0&preserve-view=true)」をご覧ください。

Microsoft Graph API バージョンについて詳しくは、「[Microsoft Graph のバージョン管理、サポートと重大な変更の方針](/graph/versioning-and-support)」をご覧ください。


## <a name="get-started"></a>はじめに

ユーザーやメール メッセージなどのリソースに対して読み取りまたは書き込みを行うには、次のようなパターンの要求を作成します。

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

作成された要求の要素について詳しくは、「[Microsoft Graph API を使用する](/graph/use-the-api)」をご覧ください。

Microsoft Graph API の機能にアクセスする方法がわかるクイックスタート サンプルが用意されています。 使用可能なサンプルでは、1 つの認証を使用して次の 2 つのサービスにアクセスします: Microsoft アカウントと Outlook。 各クイックスタートでは、Microsoft アカウント ユーザーのプロファイルの情報にアクセスし、予定表のイベントを表示します。
クイックスタートには、次の 4 つの手順が含まれます。

- プラットフォームを選択する
- アプリ ID (クライアント ID) を取得する
- サンプルをビルドする
- サインインして、予定表のイベントを表示する

クイックスタートを完了すると、実行できるアプリが完成します。 詳しくは、「[Microsoft Graph のクイック スタートのよくあるご質問](/graph/quick-start-faq)」をご覧ください。 サンプルを使い始めるには、「[Microsoft Graph のクイック スタート](https://developer.microsoft.com/graph/quick-start)」をご覧ください。

## <a name="tools"></a>ツール

**Microsoft Graph Explorer** は、Microsoft Graph API を使用して要求を作成し、テストすることができる Web ベースのツールです。 Microsoft Graph Explorer には、https://developer.microsoft.com/graph/graph-explorer でアクセスできます。

**Postman** は、Microsoft Graph API に要求を行う場合に使用できるもう 1 つのツールです。 Postman は https://www.getpostman.com でダウンロードできます。 Postman で Microsoft Graph を操作するには、[Microsoft Graph の Postman コレクション](/graph/use-postman)を使用します。

## <a name="next-steps"></a>次のステップ

使用状況情報やチュートリアルなど、Microsoft Graph の詳細については、以下を参照してください。

- [Microsoft Graph API を使用する](/graph/use-the-api)
- [Microsoft Graph のチュートリアル](/graph/tutorials)
