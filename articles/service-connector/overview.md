---
title: Service Connector とは
description: Service Connector を使用する一般的なユース ケース シナリオについて理解を深め、Service Connector の主な利点を学びます。
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 799385d85e83f42835eb6e70ffb0b5a2b868e1f9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372645"
---
# <a name="what-is-service-connector"></a>Service Connector とは

Service Connector サービスを使用すると、Azure コンピューティング サービスを他のバッキング サービスに簡単に接続できます。 このサービスでは、管理プレーンでコンピューティング サービスとターゲット バッキング サービス間のネットワーク設定および接続情報 (環境変数の生成など) を構成します。 開発者は任意の SDK またはライブラリを使用し、接続情報を使ってターゲット バッキング サービスに対してデータ プレーン操作を実行します。 

この記事では、Service Connector サービスの概要について説明します。

## <a name="what-is-service-connector-used-for"></a>Service Connector の用途

Azure コンピューティング サービスで実行され、バッキング サービスを必要とするすべてのアプリケーションが Service Connector を使用できます。 Service Connector を使用して、サービス間の接続エクスペリエンスを簡素化できる例をいくつか示します。

* **WebApp + DB:** Service Connector を使用して、PostgreSQL、MySQL、SQL DB、Cosmos DB を App Service に接続します。  
* **WebApp + ストレージ:** Service Connector を使用して Azure ストレージ アカウントに接続し、App Service で任意のストレージ製品を簡単に使用できます。
* **Spring Cloud + データベース:** Service Connector を使用して、PostgreSQL、MySQL、SQL DB、Cosmos DB を Spring Cloud アプリケーションに接続します。
* **Spring Cloud + Apache Kafka:** Service Connector を使用して、Spring Cloud アプリケーションを Confluent Cloud 上の Apache Kafka に接続できます。

サポートされているサービスとアプリケーション パターンの詳細については、「[Service Connector でサポートされているサービス](#what-services-are-supported-in-service-connector)」を参照してください。

## <a name="what-are-the-benefits-using-service-connector"></a>Service Connector を使用する利点

**単一のコマンドまたは数回のクリックでターゲット バッキング サービスに接続:**

Service Connector は使いやすいように設計されています。 ターゲット サービス インスタンス、コンピューティング サービスとターゲット サービス間の認証の種類、接続を作成するアプリケーション クライアントの種類の 3 つの必須パラメーターが要求されます。 開発者は、Azure Connection CLI または Azure portal のガイド付きエクスペリエンスを使用して、接続を簡単に作成できます。

**[接続の状態] を使用して接続の問題を監視または特定:**

サービス接続が作成されたら、 開発者は接続の正常性状態を検証して確認できます。 Service Connector では、切断された接続を修正するためのアクションを提案できます。

## <a name="what-services-are-supported-in-service-connector"></a>Service Connector でサポートされているサービス

> [!NOTE]
> Service Connector はパブリック プレビュー段階です。 製品チームは、サポートされるサービスの種類を積極的に追加しています。

**コンピューティング サービス:**

* Azure App Service
* Azure Spring Cloud

**ターゲット サービス:**

* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure Cosmos DB (SQL、MangoDB、Gremlin、Cassandra、Table)
* Azure Storage (Blob、Queue、File、Table Storage)
* Azure Key Vault
* Azure SignalR Service
* Confluent Cloud での Apache Kafka

## <a name="how-to-use-service-connector"></a>Service Connector の使用方法

Azure アプリケーションで Service Connector を使用する方法は主に 2 つあります。

* **Azure Connection CLI:** Azure CLI の接続コマンド グループを使用して、サービス間接続を作成、一覧表示、検証、削除します。
* **Azure portal の Service Connector エクスペリエンス:** ポータルのガイド付きエクスペリエンスを使用してサービス間接続を作成し、階層リストで接続を管理します。

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、Service Connector を使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [クイックスタート: Azure CLI を使用した App Service の Service Connector](./quickstart-cli-app-service-connection.md)

> [!div class="nextstepaction"]
> [クイックスタート: Azure portal を使用した App Service の Service Connector](./quickstart-portal-app-service-connection.md)

> [!div class="nextstepaction"]
> [クイックスタート: Azure CLI を使用した Spring Cloud サービスの Service Connector](./quickstart-cli-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [クイックスタート: Azure portal を使用した Spring Cloud の Service Connector](./quickstart-portal-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Service Connector の概念について学習する](./concept-service-connector-internals.md)
