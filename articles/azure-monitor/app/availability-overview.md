---
title: Application Insights 可用性テスト
description: 繰り返し実施される Web テストを設定して、アプリまたは Web サイトの可用性と応答性を監視します。
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737090"
---
# <a name="application-insights-availability-tests"></a>Application Insights 可用性テスト

お使いの Web アプリまたは Web サイトをデプロイした後、繰り返されるテストを設定して、可用性と応答性を監視できます。 [Application Insights](./app-insights-overview.md) は、世界各地の複数のポイントから定期的にアプリケーションに Web 要求を送信します。 お使いのアプリケーションが応答していない場合、または応答が遅すぎる場合は、アラートを受信できます。

可用性テストは、パブリック インターネットからアクセスできる任意の HTTP または HTTPS エンドポイントに対して設定できます。 テストする Web サイトに対して、何らかの変更を行う必要はありません。 実際には、自分が所有しているサイトである必要もありません。 サービスが依存している REST API の可用性をテストできます。

## <a name="types-of-tests"></a>テストの種類

可用性テストには、次の 4 種類があります。

* [URL ping テスト (クラシック)](monitor-web-app-availability.md): ポータルを使用してこの単純なテストを作成し、エンドポイントが応答するかどうかを検証し、その応答に関連付けられているパフォーマンスを測定できます。 また、依存する要求の解析などの高度な機能と結合されたカスタム成功基準を設定し、再試行が可能になります。
* [標準テスト (プレビュー)](availability-standard-tests.md): この単一の要求テストは、URL ping テストに似ています。 これには、SSL 証明書の有効性、プロアクティブな有効期間チェック、HTTP 要求動詞 (`GET`、`HEAD`、または `POST` など)、カスタム ヘッダー、および HTTP 要求に関連付けられたカスタム データが含まれます。
* [複数ステップ Web テスト (クラシック)](availability-multistep.md): 一連の Web 要求のこの記録を再生して、より複雑なシナリオをテストできます。 複数ステップ Web テストは Visual Studio Enterprise で作成され、ポータルにアップロードされ、ここでそれらを実行できます。
* [カスタム TrackAvailability テスト](availability-azure-functions.md): 可用性テストを実行するカスタム アプリケーションを作成することにした場合、[TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) メソッドを使用して、結果を Application Insights に送信できます。

> [!IMPORTANT]
> [URL の ping テスト](monitor-web-app-availability.md)と[複数ステップ Web テスト](availability-multistep.md)ではどちらも、パブリック インターネットの DNS インフラストラクチャを使用して、テストされたエンドポイントのドメイン名を解決します。 プライベート DNS を使用している場合は、パブリック ドメイン ネーム サーバーがテストのすべてのドメイン名を削除できる必要があります。 それができない場合は、代わりに[カスタム TrackAvailability テスト](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)を使用できます。

Application Insights リソースごとに最大 100 個の可用性テストを作成できます。

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティングに関する記事](troubleshoot-availability.md)をご覧ください。

## <a name="next-steps"></a>次のステップ

* [可用性のアラート](availability-alerts.md)
* [複数ステップ Web テスト](availability-multistep.md)
* [URL テスト](monitor-web-app-availability.md)
* [Azure Functions を使用してカスタム可用性テストを作成して実行する](availability-azure-functions.md)
* [Azure Resource Manager テンプレートの Web テスト](/azure/templates/microsoft.insights/webtests?tabs=json)