---
title: クイックスタート - Azure portal から Spring Cloud にサービス接続を作成する
description: Azure portal から Spring Cloud にサービス接続を作成する方法を示すクイックスタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f242eb1557a1b44f3698e06f33dc2a943658af15
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017427"
---
# <a name="quickstart-create-a-service-connection-in-spring-cloud-from-azure-portal"></a>クイックスタート : Azure portal から Spring Cloud にサービス接続を作成する

このクイックスタートでは、サービス コネクタを使用して Azure portal から Spring Cloud に新しいサービス接続を作成する方法を示します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

このクイックスタートは、少なくとも 1 つの Spring Cloud アプリケーションが Azure 上で既に実行されていることを前提としています。 Spring Cloud アプリケーションがない場合は、[作成します](../spring-cloud/quickstart.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure portal ([https://portal.azure.com/](https://portal.azure.com/)) にサインインします。

## <a name="create-a-new-service-connection-in-spring-cloud"></a>Spring Cloud に新しいサービス接続を作成する

サービス コネクタを使用して、新しいサービス接続を Spring Cloud に作成します。

1. Azure portal の左側にある **[すべてのリソース]** ボタンを選択します。 フィルターに「**Spring Cloud**」と入力し、一覧で、使用する Spring Cloud の名前をクリックします。
1. **[アプリ]** を選択し、一覧からアプリケーション名を選択します。 
1. **[デプロイ]** を選択し、一覧の **既定値** を選択します。 左側の目次から **[Service Connector (Preview)]\(サービス コネクタ (プレビュー)\)** を選択します。 **[作成]** を選択します。
1. 以下の設定値を選択または入力します。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | ご使用のサブスクリプションのいずれか | ターゲット サービス (接続先となるサービス) が存在するサブスクリプション。 既定値は、このアプリ サービスが存在するサブスクリプションです。 |
    | **[サービスの種類]** | Storage Blob コンテナー | ターゲット サービスの種類。 Storage Blob コンテナーがない場合は、[作成する](../storage/blobs/storage-quickstart-blobs-portal.md)か、別のサービスの種類を使用できます。 |
    | **Connection Name** | 生成された一意の名前 | アプリ サービスとターゲット サービスの間の接続を識別する接続名  |
    | **ストレージ アカウント** | ご使用のストレージ サーバー | 接続先のターゲット データベース サービス。 別のサービスの種類を選択する場合は、対応するターゲット サービス インスタンスを選択します。 |

4. **[確認と作成]** を選択して、接続の設定を確認します。 次に **[作成]** を選択して、サービス接続の作成を開始します。

## <a name="view-service-connections-in-spring-cloud"></a>Spring Cloud でサービス接続を表示する

1. **[Service Connector (Preview)]\(サービス コネクタ (プレビュー)\)** に、ターゲット サービスへの Spring Cloud 接続が表示されます。

1. **>** ボタンをクリックして一覧を展開すると、Spring Boot アプリケーションに必要なプロパティが表示されます。

1. **[...]** ボタンをクリックし、 **[検証]** を選択すると、接続の検証の詳細が右側のポップアップ ブレードに表示されます。

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、サービス コネクタを使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [チュートリアル: Spring Cloud + MySQL](./tutorial-java-spring-mysql.md)

> [!div class="nextstepaction"]
> [チュートリアル: Confluent Cloud での Spring Cloud + Apache Kafka](./tutorial-java-spring-confluent-kafka.md)
