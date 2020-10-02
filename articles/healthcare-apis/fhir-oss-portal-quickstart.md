---
title: Azure portal:オープンソースの FHIR Server for Azure をデプロイする - Azure API for FHIR
description: このクイックスタートでは、Azure portal を使用して Microsoft のオープンソースの FHIR サーバーをデプロイする方法について説明します。
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 57ab6bca820c4c25a9a56e4a801aa7d917d317ec
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978588"
---
# <a name="quickstart-deploy-open-source-fhir-server-using-azure-portal"></a>クイック スタート:Azure portal を使用してオープンソースの FHIR サーバーをデプロイする

このクイックスタートでは、Azure portal を使用して、オープンソースの FHIR サーバーを Azure にデプロイする方法について説明します。 ここでは、[オープンソース リポジトリ](https://github.com/Microsoft/fhir-server)にある手軽なデプロイ リンクを使用します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="github-open-source-repository"></a>GitHub オープンソース リポジトリ

[GitHub デプロイ ページ](https://github.com/Microsoft/fhir-server/blob/master/docs/DefaultDeployment.md)に移動し、[Deploy to Azure]\(Azure へのデプロイ\) ボタンを探します。

>[!div class="mx-imgBorder"]
>![オープンソース デプロイ ページ](media/quickstart-oss-portal/deployment-page-oss.png)

デプロイ ボタンをクリックすると、Azure portal が開きます。

## <a name="fill-in-deployment-parameters"></a>デプロイのパラメーターを入力する

リソース グループの新規作成を選択し、名前を付けます。 それ以外で必要なパラメーターは、サービスの名前と Sql Admin のパスワードだけです。

>[!div class="mx-imgBorder"]
>![カスタム デプロイのパラメーター](media/quickstart-oss-portal/deployment-custom-parameters.png)

詳細を入力したら、デプロイを開始することができます。

## <a name="validate-fhir-server-is-running"></a>FHIR サーバーが実行されていることを確認する

デプロイが完了したら、ブラウザーに `https://SERVICENAME.azurewebsites.net/metadata` を指定して機能ステートメントを取得できます。 サーバーから最初の応答が返されるまでに 1 分ほど時間がかかります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループおよびすべての関連リソースを削除できます。 そのためには、プロビジョニング済みのリソースがあるリソース グループを選択し、 **[リソース グループの削除]** を選択して、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ご利用のサブスクリプションに Microsoft Open Source FHIR Server for Azure をデプロイしました。 Postman を使用して FHIR API にアクセスする方法を学習するには、Postman のチュートリアルに進んでください。
 
>[!div class="nextstepaction"]
>[Postman を使用して FHIR API にアクセスする](access-fhir-postman-tutorial.md)