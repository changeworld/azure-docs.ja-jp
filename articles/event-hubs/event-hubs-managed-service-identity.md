---
title: Azure Event Hubs での管理対象サービス ID (プレビュー) | Microsoft Docs
description: Azure Event Hubs で管理対象サービス ID を使用します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.date: 07/05/2018
ms.author: shvija
ms.openlocfilehash: f87a04b3b78bab124ca1b75006ed2c93a74f9198
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005835"
---
# <a name="managed-service-identity-preview"></a>管理対象サービス ID (プレビュー)

管理対象サービス ID (MSI) は、アプリケーション コードが実行されるデプロイに関連付けられた、セキュリティで保護された ID を作成できる Azure 間機能です。 この ID は、アプリケーションに必要な特定の Azure リソースにアクセスするためのカスタム アクセス許可を付与するアクセス制御ロールに関連付けることができます。 

MSI では、Azure プラットフォームがこのランタイム ID を管理します。 ID 自体またはアクセスする必要のあるリソースについて、アクセス キーをアプリケーションのコードまたは構成で保存して保護する必要はありません。 Azure App Service アプリケーションまたは MSI サポートが有効な仮想マシンで実行されている Event Hubs クライアント アプリは、SAS ルールと SAS キーや、その他のアクセス トークンを処理する必要はありません。 クライアント アプリに必要なのは、Event Hubs 名前空間のエンドポイント アドレスだけです。 アプリが接続すると、Event Hubs は操作でクライアントに MSI コンテキストをバインドします。これについては後で例を示します。

管理対象サービス ID に関連付けられると、Event Hubs クライアントは承認済みのすべての操作を実行できます。 MSI を Event Hubs のロールに関連付けることによって承認が付与されます。 

## <a name="event-hubs-roles-and-permissions"></a>Event Hubs のロールとアクセス許可

最初のパブリック プレビュー リリースでは、Event Hubs 名前空間の "所有者" ロールと "共同作成者" ロールにのみ管理対象サービス ID を追加できます。これにより、この名前空間のすべてのエンティティに対するフル コントロールが ID に付与されます。 ただし、名前空間トポロジを変更する管理操作は、最初は Azure Resource Manager を使う場合にのみサポートされ、ネイティブの Event Hubs REST 管理インターフェイスを使う場合はサポートされません。 このサポートは、.NET Framework クライアントの [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) オブジェクトを管理対象 ID 内で使用できないことも意味します。 
 
## <a name="use-event-hubs-with-a-managed-service-identity"></a>管理対象サービス ID での Event Hubs の使用

以下のセクションでは、管理対象サービス ID で実行されるサンプル アプリケーションを作成し、デプロイするために必要な手順、その ID に Event Hubs 名前空間へのアクセス権を付与する方法、アプリケーションがその ID を使って Event Hubs とやり取りするしくみについて説明します。

この概要では、[Azure App Service](https://azure.microsoft.com/services/app-service/) でホストされる Web アプリケーションについて説明します。 VM でホストされるアプリケーションに必要な手順もほぼ同じです。

### <a name="create-an-app-service-web-application"></a>App Service Webアプリケーションを作成する

まず、App Service ASP.NET アプリケーションを作成します。 Azure でこれを行う方法がわからない場合は、[こちらのチュートリアル](../app-service/app-service-web-get-started-dotnet-framework.md)に従ってください。 ただし、チュートリアルで示す MVC アプリケーションを作成する代わりに、Web フォーム アプリケーションを作成してください。

### <a name="set-up-the-managed-service-identity"></a>管理対象サービス ID を設定する

アプリケーションを作成したら、Azure Portal で新しく作成された Web アプリに移動し (チュートリアルにも示されています)、**[管理対象サービス ID]** ページに移動してこの機能を有効にします。 

![](./media/event-hubs-managed-service-identity/msi1.png)
 
機能を有効にすると、Azure Active Directory に新しいサービス ID が作成され、App Service ホストに構成されます。

### <a name="create-a-new-event-hubs-namespace"></a>新しい Event Hubs 名前空間を作成する

次に、MSI のプレビューをサポートする Azure リージョンのいずれか (**米国東部**、**米国東部 2**、または**西ヨーロッパ**) で、[Event Hubs 名前空間を作成](event-hubs-create.md)します。 

ポータルで名前空間の **[アクセス制御 (IAM)]** ページに移動し、**[追加]** をクリックして、管理対象サービス ID を**所有者**ロールに追加します。 そのためには、**[アクセス許可の追加]** パネルの **[選択]** フィールドで Web アプリケーションの名前を検索し、エントリをクリックします。 その後、 **[保存]** をクリックします。

![](./media/event-hubs-managed-service-identity/msi2.png)
 
これで、Web アプリケーションの管理対象サービス ID は、Event Hubs 名前空間と以前に作成したイベント ハブにアクセスできるようになりました。 

### <a name="run-the-app"></a>アプリの実行

次に、作成した ASP.NET アプリケーションの既定のページを変更します。 [こちらの GitHub リポジトリ](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/MSI/EventHubsMSIDemoWebApp)の Web アプリケーション コードを使用することもできます。 

>[!NOTE] 
> MSI 機能のプレビュー中は、新しい API にアクセスするために、[Service Bus ライブラリのプレビュー バージョン](https://www.nuget.org/packages/WindowsAzure.ServiceBus/4.2.2-preview)を必ず使用してください。 

アプリを開始した後、ブラウザーで EventHubsMSIDemo.aspx を参照します。 または、スタート ページとして設定します。 コードは EventHubsMSIDemo.aspx.cs ファイルにあります。 いくつかの入力フィールドと、Event Hubs に接続してイベントを送受信するための **send** ボタンおよび **receive** ボタンを備えた最小限の Web アプリケーションが作成されます。 

[MessagingFactory](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) オブジェクトを初期化する方法に注意してください。 共有アクセス トークン (SAS) トークン プロバイダーを使用するのではなく、コードで `TokenProvider.CreateManagedServiceIdentityTokenProvider(ServiceAudience.EventHubAudience)` を呼び出して管理対象サービス ID のトークン プロバイダーを作成します。 そのため、保持および使用するシークレットはありません。 管理対象サービス ID コンテキストから Event Hubs へのフローと承認ハンドシェイクは、トークン プロバイダーによって自動的に処理されます。これは SAS の使用よりも単純なモデルです。

これらの変更を行ったら、アプリケーションを発行して実行します。 適切な発行データを簡単に取得するには、Visual Studio で発行プロファイルをダウンロードしてインポートします。

![](./media/event-hubs-managed-service-identity/msi3.png)
 
メッセージを送受信するには、名前空間の名前と作成したエンティティの名前を入力し、**[send]** または **[receive]** をクリックします。 
 
管理対象サービス ID は、Azure 環境内と、その ID を構成した App Service デプロイでのみ機能します。 また、現時点では、管理対象サービス ID は App Service デプロイ スロットでは機能しません。

## <a name="next-steps"></a>次の手順

Event Hubs の詳細については、次のリンクを参照してください。

* [Event Hubs のチュートリアル](event-hubs-dotnet-standard-getstarted-send.md)を開始する
* [Event Hubs の FAQ](event-hubs-faq.md)
* [Event Hubs の価格詳細](https://azure.microsoft.com/pricing/details/event-hubs/)
* [Event Hubs を使用するサンプル アプリケーション](https://github.com/Azure/azure-event-hubs/tree/master/samples)