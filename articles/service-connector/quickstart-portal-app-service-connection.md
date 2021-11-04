---
title: クイックスタート - Azure portal から App Service にサービス接続を作成する
description: Azure portal から App Service にサービス接続を作成する方法を示すクイックスタート
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 04f1c00340e8d46d495a28e3f0fd973e93991b77
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017431"
---
# <a name="quickstart-create-a-service-connection-in-app-service-from-azure-portal"></a>クイックスタート : Azure portal から App Service にサービス接続を作成する

このクイックスタートでは、サービス コネクタを使用して Azure portal から App Service に新しいサービス接続を作成する方法を示します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

このクイックスタートは、少なくとも 1 つの App Service が Azure 上で既に実行されていることを前提としています。 App Service がない場合は、[作成します](../app-service/quickstart-dotnetcore.md)。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure アカウントで Azure portal ([https://portal.azure.com/](https://portal.azure.com/)) にサインインします。

## <a name="create-a-new-service-connection-in-app-service"></a>App Service に新しいサービス接続を作成する

サービス コネクタを使用して、新しいサービス接続を App Service に作成します。

1. Azure portal の左側にある **[すべてのリソース]** ボタンを選択します。 フィルターに「**App Service**」と入力し、一覧で、使用する App Service の名前をクリックします。
2. 左側の目次から **[Service Connector (Preview)]\(サービス コネクタ (プレビュー)\)** を選択します。 **[作成]** を選択します。
3. 以下の設定値を選択または入力します。

    | 設定      | 推奨値  | 説明                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **サブスクリプション** | ご使用のサブスクリプションのいずれか | ターゲット サービス (接続先となるサービス) が存在するサブスクリプション。 既定値は、このアプリ サービスが存在するサブスクリプションです。 |
    | **[サービスの種類]** | Storage Blob コンテナー | ターゲット サービスの種類。 Storage Blob コンテナーがない場合は、[作成する](../storage/blobs/storage-quickstart-blobs-portal.md)か、別のサービスの種類を使用できます。 |
    | **Connection Name** | 生成された一意の名前 | アプリ サービスとターゲット サービスの間の接続を識別する接続名  |
    | **ストレージ アカウント** | ご使用のストレージ サーバー | 接続先のターゲット データベース サービス。 別のサービスの種類を選択する場合は、対応するターゲット サービス インスタンスを選択します。 |
    | **クライアントの種類** | この App Service の同じアプリ スタック | 選択したターゲット サービスで動作するアプリケーション スタック。 既定値は App Service ランタイム スタックから取得されます。 |

4. **[確認と作成]** を選択して、接続の設定を確認します。 次に **[作成]** を選択して、サービス接続の作成を開始します。

## <a name="view-service-connections-in-app-service"></a>App Service でサービス接続を表示する

1. **[Service Connector (Preview)]\(サービス コネクタ (プレビュー)\)** に、ターゲット サービスへの App Service 接続が表示されます。

1. **>** ボタンをクリックして一覧を展開すると、アプリケーション コードに必要な環境変数が表示されます。

1. **[...]** ボタンをクリックし、 **[検証]** を選択すると、接続の検証の詳細が右側のポップアップ ブレードに表示されます。

## <a name="next-steps"></a>次のステップ

以下のチュートリアルに従って、Service Connector を使用する独自のアプリケーションの構築を開始してください。

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + ストレージ](./tutorial-csharp-webapp-storage-cli.md)

> [!div class="nextstepaction"]
> [チュートリアル: Azure CLI を使用した WebApp + PostgreSQL](./tutorial-django-webapp-postgres-cli.md)
