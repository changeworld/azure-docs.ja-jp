---
title: プライベート可用性テスト - Azure Monitor Application Insights
description: プライベート テストを使用してファイアウォールの内側で実行される内部サーバーで可用性テストを使用する方法について説明します。
ms.topic: conceptual
ms.date: 05/14/2021
ms.openlocfilehash: 713a57db66042c562e212d65c6dd265c2aab47cd
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858445"
---
# <a name="private-testing"></a>プライベート テスト

ファイアウォールの内側で実行される内部サーバーで可用性テストを使用する場合は、パブリック ping テストの有効化と切断/イングレスなしシナリオという 2 つの解決策が考えられます。

## <a name="public-ping-test-enablement"></a>パブリック ping テストの有効化

> [!NOTE]
> 環境へのイングレスを許可しない場合は、「[切断またはイングレス シナリオなし](#disconnected-or-no-ingress-scenarios)」セクションに記載されている方法を使用します。

 内部 Web サイトのパブリック DNS レコードがあることを確認します。 DNS を解決できない場合、テストは失敗します。 [内部アプリケーションのカスタム ドメイン名を作成します。](../../cloud-services/cloud-services-custom-domain-name-portal.md#add-an-a-record-for-your-custom-domain)

サービスからの受信要求を許可するようにファイアウォールを構成します。

- [サービス タグ](../../virtual-network/service-tags-overview.md)は、個々の IP を承認したり、最新のリストを維持したりすることなく、Azure サービスを有効にする簡単な方法です。 Azure Firewall とネットワーク セキュリティ グループでサービス タグを使用して、サービスによるアクセスを許可できます。 **ApplicationInsightsAvailability** は、ping テスト サービス専用のサービス タグです。
    1. [Azure ネットワーク セキュリティ グループ](../../virtual-network/network-security-groups-overview.md)を使用する場合は、ネットワーク セキュリティ グループ リソースに移動し、 *[設定]* で **[受信セキュリティ規則]** を選択し、 **[追加]** を選択します。

         :::image type="content" source="media/availability-private-test/add.png" alt-text="ネットワーク セキュリティ グループ リソースの [受信セキュリティ規則] タブのスクリーンショット。":::

    1. 次に、ソースとして *[サービス タグ]* を選択し、ソース サービス タグとして *[ApplicationInsightsAvailability]* を選択します。 サービス タグからの着信トラフィック用にオープン ポート 80 (http) と 443 (https) を使用します。

        :::image type="content" source="media/availability-private-test/service-tag.png" alt-text="サービス タグのソースを含む [受信セキュリティ規則の追加] タブのスクリーンショット。":::

- エンドポイントが Azure の外部でホストされている場合、またはシナリオでサービス タグが使用できない場合は、[Web テスト エージェントの IP アドレス](ip-addresses.md)を個別に許可リストに登録する必要があります。 [サービス タグ API](../../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api) を使用して、PowerShell、Azure CLI、または REST 呼び出しから直接 IP 範囲のクエリを実行できます。[JSON ファイル](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)をダウンロードして、現在のサービス タグと IP アドレスの詳細の一覧を取得することもできます。
    1. ネットワーク セキュリティ グループ リソースで、 *[設定]* の **[受信セキュリティ規則]** を選択し、 **[追加]** を選択します。
    1. 次に、ソースとして *[IP アドレス]* を選択し、[ソース IP アドレス/CIRD 範囲] のコンマ区切りの一覧に IP アドレスを追加します。

         :::image type="content" source="media/availability-private-test/ip-addresses.png" alt-text="IP アドレスのソースを含む [受信セキュリティ規則の追加] タブのスクリーンショット。":::

## <a name="disconnected-or-no-ingress-scenarios"></a>切断またはイングレスなしシナリオ

テスト サーバーには Application Insights のインジェスト エンドポイントへの送信アクセスが必要ですが、これは受信要求を許可する代替方法と比べて、セキュリティ リスクが大幅に小さくなります。 結果は [可用性 Web テスト] タブに表示され、Azure portal によって作成されたテストで使用可能なものを簡単に体験できます。 カスタム可用性テストも、分析、検索、およびメトリックの可用性の結果として表示されます。

1. [Azure Private Link](../logs/private-link-security.md) を使用して、Application Insights リソースと切断された環境を接続します。
1. 内部サーバーまたはエンドポイントを定期的にテストするカスタム コードを作成します。 このコードを、ファイアウォールの内側にあるテスト サーバーで [Azure Functions](availability-azure-functions.md) またはバック グラウンドプロセスを使用して実行できます。 テスト プロセスで、コア SDK パッケージの `TrackAvailability()` API を使用して、その結果を Application Insights に送信できます。

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティングに関する記事](troubleshoot-availability.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure Private Link](../logs/private-link-security.md)
* [可用性のアラート](availability-alerts.md)
* [URL テスト](monitor-web-app-availability.md)
* [Azure Functions を使用してカスタム可用性テストを作成して実行する](availability-azure-functions.md)
