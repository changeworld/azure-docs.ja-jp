---
title: シングルテナント ワークフローと仮想ネットワーク間のトラフィックをセキュリティで保護する
description: Azure Logic Apps の仮想ネットワーク、ストレージ アカウント、およびシングルテナント ワークフロー間のトラフィックをセキュリティで保護します。
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 07/25/2021
ms.openlocfilehash: 4726df91efb18b2d9beec77606db449bd4aee3fa
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114652650"
---
# <a name="secure-traffic-between-virtual-networks-and-single-tenant-workflows-in-azure-logic-apps-using-private-endpoints"></a>プライベート エンドポイントを使用して Azure Logic Apps の仮想ネットワークとシングルテナント ワークフロー間のトラフィックをセキュリティで保護する

ロジック アプリ ワークフローと仮想ネットワークの間で安全かつプライベートに通信するために、受信トラフィック用に "*プライベート エンドポイント*" を設定し、送信トラフィック用に仮想ネットワーク統合を使用できます。

プライベート エンドポイントとは、Azure Private Link を使用するサービスにプライベートかつ安全に接続するネットワーク インターフェイスです。 このサービスは、Azure Logic Apps、Azure Storage、Azure Cosmos DB、SQL などの Azure サービスでも、独自の Private Link サービスでもかまいません。 プライベート エンドポイントによって、ご自分の仮想ネットワークのプライベート IP アドレスが使用され、実質的に、そのサービスが仮想ネットワークに組み込まれます。

この記事では、受信トラフィック、送信トラフィック、およびストレージ アカウント接続に対して、プライベート エンドポイントを経由してアクセスを設定する方法について説明します。

詳細については、次のドキュメントを確認してください。

- [Azure プライベート エンドポイントとは](../private-link/private-endpoint-overview.md)

- [Azure Private Link とは](../private-link/private-link-overview.md)

- [Azure Logic Apps のシングルテナント ロジック アプリ ワークフローとは](single-tenant-overview-compare.md)

## <a name="prerequisites"></a>前提条件

新規または既存の Azure 仮想ネットワークに、委任のないサブネットが含まれている必要があります。 このサブネットは、仮想ネットワークからプライベート IP アドレスを配置して割り当てるために使用されます。

詳細については、次のドキュメントを確認してください。

- [クイック スタート:Azure Portal を使用した仮想ネットワークの作成](../virtual-network/quick-create-portal.md)

- [サブネットの委任とは](../virtual-network/subnet-delegation-overview.md)

- [サブネットの委任を追加または削除する](../virtual-network/manage-subnet-delegation.md)

<a name="set-up-inbound"></a>

## <a name="set-up-inbound-traffic-through-private-endpoints"></a>プライベート エンドポイントを経由する受信トラフィックを設定する

ワークフローへの受信トラフィックをセキュリティで保護するには、大まかに次の手順を行います。

1. 要求トリガーや HTTP + Webhook トリガーなど、受信要求を受信して処理できる組み込みトリガーを使用して、ワークフローを開始します。 このトリガーにより、呼び出し可能なエンドポイントを使用するワークフローが設定されます。

1. 仮想ネットワーク内にプライベート エンドポイントを追加します。

1. テスト呼び出しを行って、エンドポイントへのアクセスを確認します。 ロジック アプリ ワークフローをこのエンドポイントを設定した後に呼び出すには、仮想ネットワークに接続されている必要があります。

### <a name="prerequisites-for-inbound-traffic-through-private-endpoints"></a>プライベート エンドポイントを経由する受信トラフィックの前提条件

[最重要の前提条件である仮想ネットワーク設定](#prerequisites)に加えて、要求を受け取ることができる組み込みトリガーを使って開始される新しいまたは既存のシングルテナント ベースのロジック アプリ ワークフローが必要です。

たとえば、要求トリガーによって、ワークフローを含む他の呼び出し元からの受信要求を受信して処理できるエンドポイントがワークフローに作成されます。 このエンドポイントから、ワークフローの呼び出しとトリガーに使用できる URL が提供されます。 この例では、要求トリガーを使用して手順を続行します。

詳細については、次のドキュメントを確認してください。

- [Azure Logic Apps でシングルテナント ロジック アプリのワークフローを作成する](create-single-tenant-workflows-azure-portal.md)

- [Azure Logic Apps を使用して受信 HTTP 要求を受信して応答する](../connectors/connectors-native-reqres.md)

### <a name="create-the-workflow"></a>ワークフローを作成する

1. まだ作成していない場合は、シングルテナント ベースのロジック アプリと空のワークフローを作成します。

1. デザイナーが開いたら、ワークフローの最初のステップとして要求トリガーを追加します。

   > [!NOTE]
   > 要求トリガーと Webhook トリガーは仮想ネットワーク内からのみ呼び出すことができます。 マネージド API Webhook トリガーとアクションは、呼び出しを受信するためにパブリック エンドポイントが必要なため、機能しません。 

1. シナリオの要件に基づいて、ワークフローで実行する他のアクションを追加します。

1. 完了したら、ワークフローを保存します。

詳細については、[Azure Logic Apps でシングルテナント ロジック アプリのワークフローを作成する](create-single-tenant-workflows-azure-portal.md)に関する記事をご確認ください。

#### <a name="copy-the-endpoint-url"></a>エンドポイントの URL をコピーする

1. ワークフロー メニューで、 **[概要]** を選択します。

1. **[概要]** ページで、後で使用できるように **ワークフロー URL** をコピーして保存します。

   ワークフローをトリガーするには、要求を呼び出すか、この URL に送信します。

1. 要求を呼び出すか、URL に送信することで、URL が機能することを確認します。 Postman など、要求を送信する任意のツールを使用できます。

### <a name="set-up-private-endpoint-connection"></a>プライベート エンドポイント接続を設定する

1. ロジック アプリのメニューの **[設定]** で、 **[ネットワーク]** を選択します。

1. **[ネットワーク]** ページの **[プライベート エンドポイント接続]** で、 **[プライベート エンドポイント接続の構成]** を選択します。

1. **[プライベート エンドポイント接続]** ページで、 **[追加]** を選択します。

1. **[プライベート エンドポイントの追加]** ペインが表示されるので、エンドポイントに関して要求される情報を入力します。

   詳細については、「[プライベート エンドポイントのプロパティ](../private-link/private-endpoint-overview.md#private-endpoint-properties)」をご確認ください。

1. プライベート エンドポイントが Azure によって正常にプロビジョニングされたら、ワークフロー URL の呼び出しを再試行します。

   今回は、予想どおりに `403 Forbidden` エラーが発生します。これは、プライベート エンドポイントが設定され、正しく動作していることを意味します。

1. 接続が正しく動作するようにするには、そのプライベート エンドポイントがある同じ仮想ネットワーク内に仮想マシンを作成し、ロジック アプリ ワークフローを呼び出してみてください。

### <a name="considerations-for-inbound-traffic-through-private-endpoints"></a>プライベート エンドポイントを経由する受信トラフィックに関する考慮事項

- 仮想ネットワークの外部からアクセスした場合、監視ビューはトリガーとアクションからの入力と出力にアクセスできません。

- Visual Studio Code または Azure CLI からのデプロイは、仮想ネットワーク内からのみ機能します。 デプロイ センターを使用して、ロジック アプリを GitHub リポジトリにリンクできます。 その後、Azure インフラストラクチャを使用してコードをビルドし、デプロイできます。 

  GitHub インテグレーションを機能させるには、ロジック アプリから `WEBSITE_RUN_FROM_PACKAGE` 設定を削除するか、値を `0` に設定します。

- Private Link を有効にしても、送信トラフィックには影響なく、App Service インフラストラクチャを引き続き経由します。

<a name="set-up-outbound"></a>

## <a name="set-up-outbound-traffic-through-private-endpoints"></a>プライベート エンドポイントを経由する送信トラフィックを設定する

ロジック アプリからの送信トラフィックをセキュリティで保護するために、ロジック アプリを仮想ネットワークと統合できます。 既定では、ロジック アプリからの送信トラフィックは、`10.0.0.0/8`、`172.16.0.0/12`、`192.168.0.0/16` などのプライベート アドレスに向かうときのみ、ネットワーク セキュリティ グループ (NSG) とユーザー定義ルート (UDR) の影響を受けます。 しかしながら、すべての送信トラフィックをご自分の仮想ネットワーク経由でルーティングすることによって、NSG、ルート、ファイアウォールへのすべての送信トラフィックを対象にすることができます。 すべての送信トラフィックが、統合サブネット上の NSG と UDR の影響を受けるようにするには、ロジック アプリの設定 `WEBSITE_VNET_ROUTE_ALL` を `1` に設定します。

> [!IMPORTANT]
> Logic Apps ランタイムが動作するには、バックエンド ストレージへの接続が中断されないようにする必要があります。 Azure でホストされるマネージド コネクタを機能させるには、マネージド API サービスへの接続が中断されないようにする必要があります。

ロジック アプリで仮想ネットワーク内のプライベート ドメイン ネーム サーバー (DNS) ゾーンを使用するようにするには、WEBSITE_DNS_SERVER を168.63.129.16 に設定して、アプリで確実に vNET 内のプライベート DNS ゾーンを使用するようにします

### <a name="considerations-for-outbound-traffic-through-private-endpoints"></a>プライベート エンドポイントを経由する送信トラフィックに関する考慮事項

仮想ネットワーク統合を設定しても、受信トラフィックには影響なく、App Service の共有エンドポイントが引き続き使用されます。 受信トラフィックをセキュリティで保護するには、「[プライベート エンドポイントを経由する受信トラフィックを設定する](#set-up-inbound)」をご確認ください。

詳細については、次のドキュメントを確認してください。

- [アプリを Azure 仮想ネットワークに統合する](../app-service/web-sites-integrate-with-vnet.md)
- [ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)
- [仮想ネットワーク トラフィックのルーティング](../virtual-network/virtual-networks-udr-overview.md)

## <a name="connect-to-storage-account-with-private-endpoints"></a>プライベート エンドポイントを使用してストレージ アカウントに接続する

仮想ネットワーク内のリソースのみが接続できるように、ストレージ アカウントのアクセスを制限できます。 Azure Storage は、ストレージ アカウントへのプライベート エンドポイントの追加をサポートしています。 ロジック アプリ ワークフローで、これらのエンドポイントを使用してストレージ アカウントと通信できます。

ロジック アプリの設定で、次のいずれかのオプションを選択して、プライベート エンドポイントがあるストレージ アカウントの接続文字列に `AzureWebJobsStorage` を設定します。

- **Azure portal**: ロジック アプリのメニューで、 **[構成]** を選択します。 `AzureWebJobsStorage` 設定をストレージ アカウントの接続文字列で更新します。

- **Visual Studio Code**: プロジェクトのルートレベルにある **local.settings.json** ファイルで、`AzureWebJobsStorage` 設定をストレージ アカウントの接続文字列で更新します。

 詳細については、「[Azure Storage のプライベート エンドポイントを使用する](../storage/common/storage-private-endpoints.md)」のドキュメントをご確認ください。

### <a name="considerations-for-private-endpoints-on-storage-accounts"></a>ストレージ アカウントのプライベート エンドポイントに関する考慮事項

- テーブル、キュー、BLOB、ファイル ストレージ サービスごとに異なるプライベート エンドポイントを作成します。

- この設定を使用して、すべての送信トラフィックを仮想ネットワーク経由でルーティングします。

  `"WEBSITE_VNET_ROUTE_ALL": "1"`

- ロジック アプリで仮想ネットワーク内のプライベート ドメイン ネーム サーバー (DNS) ゾーンを使用するには、ロジック アプリの `WEBSITE_DNS_SERVER` 設定を `168.63.129.16` に設定します。

- ロジック アプリをデプロイするときに、パブリックにアクセスできる別のストレージ アカウントが必要です。 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 設定を、そのストレージ アカウントの接続文字列に設定してください。

- ロジック アプリでプライベート エンドポイントを使用する場合は、[GitHub インテグレーション](https://docs.github.com/en/github/customizing-your-github-workflow/about-integrations)を使用してデプロイします。

  ロジック アプリでプライベート エンドポイントを使用しない場合は、Visual Studio Code からデプロイし、`WEBSITE_RUN_FROM_PACKAGE` 設定を `1` に設定できます。 

## <a name="next-steps"></a>次の手順

- [どこでも使用できる Logic Apps: Logic Apps (シングルテナント) を使用したネットワークの可能性](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)
