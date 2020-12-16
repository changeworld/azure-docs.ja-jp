---
title: トラブルシューティング - Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java のトラブルシューティング
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: cf27763f857cc1fd1aad5256d0c6cecf91251caf
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2020
ms.locfileid: "96855553"
---
# <a name="troubleshooting-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java のトラブルシューティング

この記事では、Java エージェントを使用して Java アプリケーションをインストルメント化する際にユーザーが直面する一般的な問題と、これらの問題を解決する手順について説明しています。

## <a name="self-diagnostic-log-file"></a>自己診断ログ ファイル

既定では、Application Insights Java 3.0 によって `applicationinsights-agent-3.0.0.jar` ファイルが配置されているディレクトリに `applicationinsights.log` という名前のログ ファイルが生成されます。

このログ ファイルは、発生している問題に関するヒントを得るために最初に確認する場所です。

## <a name="upgrade-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK からのアップグレード

詳細については、「[2.x SDK からのアップグレード](./java-standalone-upgrade-from-2x.md)」を参照してください。

## <a name="upgrade-from-30-preview"></a>3\.0 プレビューからのアップグレード

JSON 構造は 3.0 GA リリースで完全に変更されているため、3.0 プレビューからアップグレードする場合は、すべての[構成オプション](./java-standalone-config.md)を慎重に確認してください。

次のような変更です。

1.  構成ファイル名自体が `ApplicationInsights.json` から `applicationinsights.json` に変更されました。
2.  `instrumentationSettings` ノードがなくなりました。 `instrumentationSettings` のすべてのコンテンツがルート レベルに移動されました。 
3.  `sampling`、`jmxMetrics`、`instrumentation`、`heartbeat` などの構成ノードは、`preview` からルート レベルに移動されました。

## <a name="ssl-certificate-issues"></a>SSL 証明書の問題

既定の Java キーストアを使用している場合は、すべての CA ルート証明書が既に存在するため、他に SSL 証明書をインポートする必要はありません。

カスタム Java キーストアを使用している場合は、Application Insights エンドポイントの SSL 証明書をインポートすることが必要になる場合があります。

### <a name="some-key-terminology"></a>いくつかの主要な用語:
*キーストア* は、証明書、公開キー、および秘密キーのリポジトリです。 通常、JDK ディストリビューションには、それらを管理するための実行可能ファイル (`keytool`) があります。

次の例は、キーストアに SSL 証明書をインポートする単純なコマンドです。

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-the-ssl-certificate"></a>SSL 証明書をダウンロードして追加する手順:

1.  任意のブラウザーを開き、次に示すように、アプリケーションのインストルメント化に使用する接続文字列に含まれる `IngestionEndpoint` URL にアクセスします。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights の接続文字列":::

2.  ブラウザーの 'サイト情報を表示' (鍵) アイコンをクリックし、次に示すように '証明書' オプションをクリックします。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="SSL 証明書のキャプチャ":::

3.  [詳細] タブに移動し、[ファイルにコピー] をクリックします。
4.  [次へ] ボタンをクリックし、“Base-64 encoded X.509 (.CER)“ 形式を選択してから [次へ] を選択します。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="SSL 証明書のエクスポート ウィザード":::

5.  SSL 証明書の保存先ファイルを指定します。 最後に、[次へ] をクリックして完了します。 "エクスポートに成功しました" というメッセージが表示されます。
6.  証明書を取得できたら、次は Java キーストアに証明書をインポートします。 上記の[コマンド](#some-key-terminology)を使用して、証明書をインポートします。

> [!WARNING]
> 現在の証明書の有効期限が切れる前に新しい証明書を取得するには、これらの手順を繰り返す必要があります。 有効期限の情報は、次に示すように、証明書ポップアップの [詳細] タブで確認できます。

:::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 証明書の詳細":::
