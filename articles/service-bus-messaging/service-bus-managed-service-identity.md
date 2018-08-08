---
title: Azure Service Bus での管理対象サービス ID (プレビュー) | Microsoft Docs
description: Azure Service Bus で管理対象サービス ID を使用します。
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2018
ms.author: sethm
ms.openlocfilehash: 30df312e349bd6f6ebd1f38141075382be2522a2
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2018
ms.locfileid: "39397986"
---
# <a name="managed-service-identity-preview"></a>管理対象サービス ID (プレビュー)

管理対象サービス ID (MSI) は、アプリケーション コードが実行されるデプロイに関連付けられた、セキュリティで保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。

MSI では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体やアクセスする必要のあるリソース用に、アクセス キーをアプリケーション コードや構成に保存して保護する必要はありません。 Azure App Service アプリケーションまたは MSI サポートが有効な仮想マシンで実行されている Service Bus クライアント アプリは、SAS ルールと SAS キーや、その他のアクセス トークンを処理する必要はありません。 クライアント アプリに必要なのは、Service Bus メッセージング名前空間のエンドポイント アドレスだけです。 アプリが接続すると、Service Bus はこの記事で後述する例に示す操作で MSI コンテキストをクライアントにバインドします。 

管理対象サービス ID に関連付けられると、Service Bus クライアントは承認済みのすべての操作を実行できます。 MSI を Service Bus のロールに関連付けることによって承認が付与されます。 

## <a name="service-bus-roles-and-permissions"></a>Service Bus のロールとアクセス許可

最初のパブリック プレビュー リリースでは、管理対象サービス ID は Service Bus 名前空間の "所有者" ロールと "共同作成者" ロールにのみ追加できます。これにより、この名前空間のすべてのエンティティに対するフル コントロールが ID に付与されます。 ただし、名前空間トポロジを変更する管理操作は、最初は Azure Resource Manager でのみサポートされ、ネイティブの Service Bus REST 管理インターフェイスではサポートされません。 このサポートは、.NET Framework クライアントの [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) オブジェクトを管理対象 ID 内で使用できないことも意味します。

## <a name="use-service-bus-with-a-managed-service-identity"></a>管理対象サービス ID での Service Bus の使用

以下のセクションでは、管理対象サービス ID で実行されるサンプル アプリケーションを作成してデプロイするために必要な手順、その ID に Service Bus メッセージング名前空間へのアクセス権を付与する方法、アプリケーションがその ID を使用して Service Bus エンティティとやり取りするしくみについて説明します。

この概要では、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされる Web アプリケーションについて説明します。 VM でホストされるアプリケーションに必要な手順もほぼ同じです。

### <a name="create-an-app-service-web-application"></a>App Service Webアプリケーションを作成する

まず、App Service ASP.NET アプリケーションを作成します。 Azure でこれを行う方法がわからない場合は、[こちらのチュートリアル](../app-service/app-service-web-get-started-dotnet-framework.md)に従ってください。 ただし、チュートリアルで示す MVC アプリケーションを作成する代わりに、Web フォーム アプリケーションを作成してください。

### <a name="set-up-the-managed-service-identity"></a>管理対象サービス ID を設定する

アプリケーションを作成したら、Azure Portal で新しく作成された Web アプリに移動し (チュートリアルにも示されています)、**[管理対象サービス ID]** ページに移動してこの機能を有効にします。 

![](./media/service-bus-managed-service-identity/msi1.png)

機能を有効にすると、Azure Active Directory に新しいサービス ID が作成され、App Service ホストに構成されます。

### <a name="create-a-new-service-bus-messaging-namespace"></a>新しい Service Bus メッセージング名前空間を作成する

次に、RBAC のプレビューをサポートする Azure リージョンのいずれか (**米国東部**、**米国東部 2**、または**西ヨーロッパ**) で、[Service Bus メッセージング名前空間を作成](service-bus-create-namespace-portal.md)します。 

ポータルで名前空間の **[アクセス制御 (IAM)]** ページに移動し、**[追加]** をクリックして、管理対象サービス ID を**所有者**ロールに追加します。 そのためには、**[アクセス許可の追加]** パネルの **[選択]** フィールドで Web アプリケーションの名前を検索し、エントリをクリックします。 その後、 **[保存]** をクリックします。

![](./media/service-bus-managed-service-identity/msi2.png)
 
これで、Web アプリケーションの管理対象サービス ID は、Service Bus 名前空間と以前に作成したキューにアクセスできるようになりました。 

### <a name="run-the-app"></a>アプリの実行

次に、作成した ASP.NET アプリケーションの既定のページを変更します。 [こちらの GitHub リポジトリ](https://github.com/Azure-Samples/app-service-msi-servicebus-dotnet)の Web アプリケーション コードを使用します。  

Default.aspx ページはランディング ページです。 コードは Default.aspx.cs ファイルにあります。 いくつかの入力フィールドと、Service Bus に接続してメッセージを送受信するための **send** ボタンおよび **receive** ボタンを備えた最小限の Web アプリケーションが作成されます。

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) オブジェクトを初期化する方法に注意してください。 共有アクセス トークン (SAS) トークン プロバイダーを使用するのではなく、コードで `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.ServiceBusAudience)` を呼び出して管理対象サービス ID のトークン プロバイダーを作成します。 そのため、保持および使用するシークレットはありません。 管理対象サービス ID コンテキストから Service Bus へのフローと承認ハンドシェイクは、トークン プロバイダーによって自動的に処理されます。これは SAS の使用よりも単純なモデルです。

これらの変更を行ったら、アプリケーションを発行して実行します。 適切な発行データを簡単に取得するには、Visual Studio で発行プロファイルをダウンロードしてインポートします。

![](./media/service-bus-managed-service-identity/msi3.png)
 
メッセージを送受信するには、名前空間の名前と作成したエンティティの名前を入力し、**[send]** または **[receive]** をクリックします。


> [!NOTE]
> - マネージド サービス ID は、Azure 環境内の App Services、Azure VM、およびスケール セット上でのみ機能します。 .NET アプリケーションの場合は、Service Bus NuGet パッケージで使用される Microsoft.Azure.Services.AppAuthentication ライブラリがこのプロトコルの抽象化を提供し、ローカル開発エクスペリエンスをサポートします。 このライブラリを使うと、Visual Studio、Azure CLI 2.0、または Active Directory 統合認証のユーザー アカウントを使って、開発用マシン上でローカルにコードをテストすることもできます。 このライブラリでのローカル開発オプションの詳細については、「[Service-to-service authentication to Azure Key Vault using .NET](../key-vault/service-to-service-authentication.md)」 (.NET を使用した Azure Key Vault に対するサービス間認証) を参照してください。  
> 
> - 現在、マネージド サービス ID は、App Service デプロイ スロットでは機能しません。

## <a name="next-steps"></a>次の手順

Service Bus メッセージングの詳細については、次のトピックをご覧ください。

* [Service Bus の基礎](service-bus-fundamentals-hybrid-solutions.md)
* [Service Bus のキュー、トピック、サブスクリプション](service-bus-queues-topics-subscriptions.md)
* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus のトピックとサブスクリプションの使用方法](service-bus-dotnet-how-to-use-topics-subscriptions.md)