---
title: Azure Service Fabric Mesh のサンプルを探す
description: 次に示すのは、利用可能な Service Fabric Mesh のサンプル アプリケーションの目録です。 これらのサンプルのソース コードには、Service Fabric リソース モデルを使用して特定のシナリオを実現する方法が示されています。
ms.date: 12/03/2018
ms.topic: conceptual
ms.openlocfilehash: c944ddc26f2e2d099cf9552acb8287c363d0c768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "75461909"
---
# <a name="find-service-fabric-mesh-samples"></a>Service Fabric Mesh のサンプル

次の表は、利用可能な Service Fabric Mesh のサンプル アプリケーションをまとめたものです。 これらのサンプルのソース コードには、Service Fabric リソース モデルを使用して特定のシナリオを実現する方法が示されています。

テンプレートを Azure に直接デプロイする方法については、[GitHub のサンプル テンプレートページ](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/README.md)をご覧ください。

|サンプル テンプレート|シナリオの説明|ソース コード|開発者ツール|
|------------|--------------------|----------|----------------------|
| [Hello World App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/helloworld) | コンテナーでホストされる静的な Web ページです。 Linux の場合は nginx、Windows の場合は IIS が使用されます | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld) | 要件はありません |
| [Counter App for Azure File Volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.md) | Azure Files ベースのボリュームをコンテナー内にマウントして状態を保存します。 <br><br> **注:** このテンプレートを使用するには、Azure Files のファイル共有が既にプロビジョニングされている必要があります (説明は[こちら](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share)) | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
| [TodoListApp](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/todolist) | DNS ベースの解決を使用するフロントエンドおよびバックエンド サービスを使用してアプリケーションを作成します。 [こちら](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore)のチュートリアルで使用されています | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/todolistapp) | Visual Studio Mesh Tooling |
| [Visual Objects App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/visualobjects) | アプリケーション内でマイクロサービスをスケーリングし、アップグレードします。 | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects) |  Visual Studio Mesh Tooling |
| [Voting App](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/voting) | DNS ベースの解決を使用するフロントエンドおよびバックエンド サービスを使用してアプリケーションを作成します | [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) | Windows バージョンでは Visual Studio Mesh Tooling、Linux バージョンでは VS Code / dotnet CLI を使用できます |
| [Counter App for Service Fabric Reliable Volumes](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/templates/counter/readme.sfreliablevolume.md)| Service Fabric Reliable Disk ベースのボリュームをコンテナー内マウントして、状態を格納します。| [ソース コード](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter) | Visual Studio Mesh Tooling |
