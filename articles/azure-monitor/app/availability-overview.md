---
title: Application Insights の可用性の概要
description: 繰り返し実施される Web テストを設定して、アプリまたは Web サイトの可用性と応答性を監視します。
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: 94fe55a5bce0e915a092fd32ad5acf6936d59e61
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/10/2021
ms.locfileid: "111967019"
---
# <a name="availability-tests-overview"></a>可用性テストの概要

お使いの Web アプリ/Web サイトをデプロイした後、繰り返されるテストを設定して、可用性と応答性を監視できます。 [Application Insights](./app-insights-overview.md) は、世界各地の複数のポイントから定期的にアプリケーションに Web 要求を送信します。 お使いのアプリケーションが応答していない場合、または応答が遅すぎる場合は、アラートを受信できます。

可用性テストは、パブリック インターネットからアクセスできる任意の HTTP または HTTPS エンドポイントに対して設定できます。 テストする Web サイトに対して、何らかの変更を行う必要はありません。 実際には、自分が所有しているサイトである必要もありません。 サービスが依存している REST API の可用性をテストできます。

## <a name="types-of-availability-tests"></a>可用性テストの種類

可用性テストには、次の 3 種類があります。

* [URL の ping テスト](monitor-web-app-availability.md): このカテゴリには、ポータルから作成できる 2 つの簡単なテストがあります。
* [複数ステップ Web テスト](availability-multistep.md):一連の Web 要求の記録であり、さらに複雑なシナリオをテストするために再生できます。 複数ステップ Web テストは Visual Studio Enterprise で作成され、ポータルにアップロードされて実行されます。
* [カスタム可用性追跡テスト](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability):可用性テストを実行するカスタム アプリケーションを作成する場合は、`TrackAvailability()` メソッドを使用して Application Insights に結果を送信できます。

> [!IMPORTANT]
> [URL の ping テスト](monitor-web-app-availability.md)と[複数ステップ Web テスト](availability-multistep.md)ではどちらも、パブリック インターネット DNS インフラストラクチャを使用して、テストされたエンドポイントのドメイン名を解決します。 つまり、プライベート DNS を使用する場合は、自分のテストのドメイン名もすべてパブリック ドメイン ネーム サーバーによって解決できることを確実にするか、それが可能でない場合は、代わりに[カスタム可用性追跡テスト](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)を使用できるようにする必要があります。

**Application Insights リソースごとに最大 100 個の可用性テストを作成できます。**

## <a name="troubleshooting"></a>トラブルシューティング

専用の[トラブルシューティングに関する記事](troubleshoot-availability.md)をご覧ください。

## <a name="next-step"></a>次のステップ

* [可用性のアラート](availability-alerts.md)
* [複数ステップ Web テスト](availability-multistep.md)
* [URL テスト](monitor-web-app-availability.md)
* [Azure Functions を使用してカスタム可用性テストを作成し、実行する](availability-azure-functions.md)
* [Azure Resource Manager テンプレートの Web テスト](/azure/templates/microsoft.insights/webtests?tabs=json)