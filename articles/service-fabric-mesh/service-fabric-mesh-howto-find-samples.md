---
title: Azure Service Fabric Mesh のサンプル | Microsoft Docs
description: Service Fabric Mesh の各種サンプル アプリケーションを見つける方法について説明します。
services: service-fabric-mesh
keywords: ''
author: v-vasuke
ms.author: v-vasuke
ms.date: 12/03/2018
ms.topic: get-started-article
ms.service: service-fabric-mesh
manager: jeconnoc
ms.openlocfilehash: d31ee60977bb4ac11a93cfe1e72134c65baf839c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53161247"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh のサンプル

次の表は、利用可能な Service Fabric Mesh のサンプル アプリケーションをまとめたものです。 これらのサンプルのソース コードには、Service Fabric リソース モデルを使用して特定のシナリオを実現する方法が示されています。

テンプレートを Azure に直接デプロイする方法については、[GitHub のサンプル テンプレートページ](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)をご覧ください。


|サンプル テンプレート|シナリオの説明|ソース コード|開発者ツール|
|------------|--------------------|----------|----------------------|
| [Hello World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | コンテナーでホストされる静的な Web ページです。 Linux の場合は nginx、Windows の場合は IIS が使用されます | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 要件はありません |
| [Counter App for Azure File Volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter) | Azure Files ベースのボリュームをコンテナー内にマウントして状態を保存します。 <br><br> **注:** このテンプレートを使用するには、Azure Files のファイル共有が既にプロビジョニングされている必要があります (説明は[こちら](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)) | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolistapp) | DNS ベースの解決を使用するフロントエンドおよびバックエンド サービスを使用してアプリケーションを作成します。 [こちら](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore)のチュートリアルで使用されています | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh Tooling |
| [Visual Objects App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | アプリケーション内でマイクロサービスをスケーリングし、アップグレードします。 | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh Tooling |
| [Voting App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/votingapp) | DNS ベースの解決を使用するフロントエンドおよびバックエンド サービスを使用してアプリケーションを作成します | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Windows バージョンでは Visual Studio Mesh Tooling、Linux バージョンでは VS Code / dotnet cli を使用できます |
| [Counter App for Service Fabric Reliable Volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter)| Service Fabric Reliable Disk ベースのボリュームをコンテナー内マウントして、状態を格納します。| [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
