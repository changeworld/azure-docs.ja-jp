---
title: Azure Search .NET Management SDK バージョン 2 へのアップグレード
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Search .NET Management SDK バージョン 2 にアップグレードします。 新機能と必要なコード変更について説明します。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847528"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Azure Search .NET Management SDK のバージョンのアップグレード

> [!Important]
> このコンテンツはまだ準備中の段階です。 Azure Search Management .NET SDK のバージョン 3.0 が NuGet 上で提供されています。 新しいバージョンへのアップグレード方法が含まれるように、この移行ガイドを更新しています。 
>

バージョン 1.0.2 以前の [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk) を使用している場合、この記事を参考にして、バージョン 2 を使用するようにアプリケーションをアップグレードできます。

Azure Search .NET Management SDK のバージョン 2 には、以前のバージョンからの変更がいくつか含まれています。 ほとんどは小さなものなので、コードの変更に必要な作業は最小限で済みます。 新しいバージョンの SDK を使用するようにコードを変更する方法については、「 [アップグレードの手順](#UpgradeSteps) 」を参照してください。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>バージョン 2 の新機能
Azure Search .NET Management SDK のバージョン 2 のターゲットは、以前の SDK のバージョンと同じ Azure Search Management REST API の一般公開バージョン (具体的には 2015-08-19) です。 この SDK に対する変更は、SDK 自体の使いやすさを向上させるためのクライアント側の変更に限定されています。 これらの変更には、以下が含まれます。

* `Services.CreateOrUpdate` とその非同期バージョンは、`SearchService` のプロビジョニングを自動的にポーリングし、サービスのプロビジョニングが完了するまで戻ることはなくなりました。 これにより、そのようなポーリング コードを自分で記述する必要がなくなります。
* サービスのプロビジョニング を手動でポーリングする場合は、新しい `Services.BeginCreateOrUpdate` メソッドまたはその非同期バージョンのいずれかを使用できます。
* 新しいメソッド `Services.Update` とその非同期バージョンが SDK に追加されています。 これらのメソッドでは、HTTP PATCH を使用して、サービスの増分更新をサポートします。 たとえば、目的の `partitionCount` プロパティと `replicaCount` プロパティのみを含むこれらのメソッドに `SearchService` インスタンスを渡すことで、サービスをスケーリングできるようになりました。 `Services.Get` を呼び出し、返された `SearchService` を変更して `Services.CreateOrUpdate` に渡すという古い方法は、引き続きサポートされますが、必要ではなくなりました。 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、 `Microsoft.Azure.Management.Search` の NuGet 参照を更新します。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 コードの構成方法に応じて、正常にリビルドされます。 リビルドされれば、準備は完了です。

ビルドが失敗した場合、(たとえば単体テストのために) SDK インターフェイスを実装したが、それらが変更されていた可能性があります。 これを解決するには、`BeginCreateOrUpdateWithHttpMessagesAsync` などの新しいメソッドを実装する必要があります。

すべてのビルド エラーを修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。 SDK の新機能の詳細については、「[バージョン 2 の新機能](#WhatsNew)」をご覧ください。

## <a name="next-steps"></a>次のステップ
SDK についてのご意見をお待ちしております。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest) に質問を投稿してください。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 問題のタイトルに、必ず "[search]" とラベルを付けてください。
