---
title: Form Recognizer のサンプル ラベル付けツールのデプロイ方法
titleSuffix: Azure Cognitive Services
description: 監視された学習に役立てるため、Form Recognizer のサンプル ラベル付けツールをデプロイするさまざまな方法について説明します。
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: pafarley
ms.openlocfilehash: fa419d7dd9668ac2ce8f2b0eb904117c7e22692d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/01/2020
ms.locfileid: "78207833"
---
# <a name="deploy-the-sample-labeling-tool"></a>サンプル ラベル付けツールのデプロイ

Form Recognizer のサンプル ラベル付けツールは、Docker コンテナーで実行されるアプリケーションです。 これには、監視された学習の目的でフォーム ドキュメントに手動でラベルを付けるために使用できる便利な UI が用意されています。 [ラベルを使用したトレーニング](./quickstarts/label-tool.md)のクイックスタートでは、最も一般的なシナリオである、お使いのローカル コンピューターでツールを実行する方法を説明しています。 

このガイドでは、サンプル ラベル付けツールをデプロイして実行する別の方法について説明します。 

## <a name="deploy-with-azure-container-instances"></a>Azure Container Instances を使用したデプロイ

ラベル ツールは、Docker Web アプリ コンテナーで実行できます。 まず、Azure portal で[新しい Web アプリ リソースを作成](https://ms.portal.azure.com/#create/Microsoft.WebSite)します。 フォームにご自分のサブスクリプションとリソース グループの詳細を入力します。 必須フィールドに次の情報を入力します。
* **発行**:Docker コンテナー
* **オペレーティング システム**:Linux

次のページで、Docker コンテナーのセットアップに関する次のフィールドを入力します。

* **オプション**: 単一コンテナー
* **イメージのソース**:Azure Container Registry
* **アクセスの種類**: パブリック
* **イメージとタグ**: mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest

次の手順は省略可能です。 アプリのデプロイが完了したら、それを実行して、ラベル ツールにオンラインでアクセスできます。

### <a name="connect-to-azure-ad-for-authorization"></a>承認のために Azure AD に接続する

自分の資格情報を持つユーザーのみがサインインしてアプリを使用できるように、ご使用の Web アプリを Azure Active Director (AAD) に接続することをお勧めします。 [App Service アプリの構成](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)の手順に従って、AAD に接続します。

## <a name="next-steps"></a>次のステップ

[ラベルを使用したトレーニング](./quickstarts/label-tool.md)のクイックスタートに戻って、ツールを使用してトレーニング データに手動でラベルを付け、監視された学習を行う方法について学習します。
