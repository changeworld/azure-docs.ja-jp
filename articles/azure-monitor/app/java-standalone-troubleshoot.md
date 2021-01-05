---
title: Azure Monitor Application Insights for Java のトラブルシューティング
description: Azure Monitor Application Insights の Java エージェントのトラブルシューティング方法について説明します
ms.topic: conceptual
ms.date: 11/30/2020
ms.custom: devx-track-java
ms.openlocfilehash: 14644f76b7de53b2b6ee3f04131daaf59267a5ff
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507644"
---
# <a name="troubleshooting-guide-azure-monitor-application-insights-for-java"></a>トラブルシューティング ガイド:Azure Monitor Application Insights for Java

この記事では、Application Insights の Java エージェントを使用して Java アプリケーションをインストルメントする際に直面する可能性のある一般的な問題のいくつかを取り上げます。 また、これらの問題を解決する手順についても説明します。 Application Insights は、Azure Monitor プラットフォーム サービスの機能です。

## <a name="check-the-self-diagnostic-log-file"></a>自己診断ログ ファイルを確認する

既定では、Application Insights の Java 3.0 エージェントにより、`applicationinsights-agent-3.0.0.jar` ファイルが保持されているディレクトリに `applicationinsights.log` という名前のログ ファイルが生成されます。

このログ ファイルは、発生している問題に関するヒントを得るために最初に確認する場所です。

## <a name="upgrade-from-the-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK からアップグレードする

アプリケーションで Application Insights Java 2.x SDK を既に使用している場合は、使用し続けてかまいません。 これは Java 3.0 エージェントによって検出されます。 詳細については、「[Application Insights Java 2.x SDK からのアップグレード](./java-standalone-upgrade-from-2x.md)」をご覧ください。

## <a name="upgrade-from-application-insights-java-30-preview"></a>Application Insights Java 3.0 Preview からアップグレードする

Java 3.0 Preview エージェントからアップグレードする場合は、すべての[構成オプション](./java-standalone-config.md)を注意深く確認してください。 JSON 構造体は、3.0 一般公開 (GA) リリースで完全に変更されています。

次のような変更です。

-  構成ファイル名自体が `ApplicationInsights.json` から `applicationinsights.json` に変更されました。
-  `instrumentationSettings` ノードがなくなりました。 `instrumentationSettings` のすべてのコンテンツがルート レベルに移動されました。 
-  `sampling`、`jmxMetrics`、`instrumentation`、`heartbeat` などの構成ノードは、`preview` からルート レベルに移動されました。

## <a name="some-logging-is-not-auto-collected"></a>一部のログ記録が自動収集されない

ログ記録は、最初にログ記録フレームワークの構成されたしきい値を満たし、次に Application Insights の構成されたしきい値も満たす場合にのみキャプチャされます。

特定のログ記録ステートメントがログ記録フレームワークで構成されているしきい値を満たしているかどうかを確認する最善の方法は、通常のアプリケーション ログ (ファイルやコンソールなど) に表示されているかどうかを確認することです。

詳細については、[自動収集されたログ記録の構成](./java-standalone-config.md#auto-collected-logging)に関するページをご覧ください。

## <a name="import-ssl-certificates"></a>SSL 証明書をインポートする

既定の Java キーストアを使用している場合は、すべての CA ルート証明書が既に存在します。 追加の SSL 証明書をインポートする必要はありません。

カスタム Java キーストアを使用している場合は、Application Insights エンドポイントの SSL 証明書をインポートすることが必要になる場合があります。

### <a name="key-terminology"></a>主要な用語
*キーストア* は、証明書、公開キー、および秘密キーのリポジトリです。 通常、Java Development Kit ディストリビューションには、それらを管理するための実行可能ファイル (`keytool`) があります。

次の例は、キーストアに SSL 証明書をインポートする単純なコマンドです。

`keytool -importcert -alias your_ssl_certificate -file "your downloaded SSL certificate name".cer -keystore "Your KeyStore name" -storepass "Your keystore password" -noprompt`

### <a name="steps-to-download-and-add-an-ssl-certificate"></a>SSL 証明書をダウンロードして追加する手順

1.  任意のブラウザーを開き、アプリケーションのインストルメント化に使用する接続文字列に含まれる `IngestionEndpoint` URL にアクセスします。

    :::image type="content" source="media/java-ipa/troubleshooting/ingestion-endpoint-url.png" alt-text="Application Insights の接続文字列を示したスクリーンショット。":::

2.  ブラウザーの **[サイト情報を表示]** (鍵) アイコンを選択し、 **[証明書]** オプションを選択します。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-icon-capture.png" alt-text="サイト情報の証明書オプションのスクリーンショット。":::

3.  **[詳細]** タブにアクセスし、 **[ファイルへコピー]** を選択します。
4.  **[次へ]** ボタンをクリックし、"**Base-64 encoded X.509 (.CER)** " 形式を選択してから **[次へ]** を選択します。

    :::image type="content" source="media/java-ipa/troubleshooting/certificate-export-wizard.png" alt-text="形式が選択された、証明書のエクスポート ウィザードのスクリーンショット。":::

5.  SSL 証明書の保存先ファイルを指定します。 次に、 **[次へ]**  >  **[完了]** の順に選択します。 "エクスポートに成功しました" というメッセージが表示されます。
6.  証明書を取得できたら、次は Java キーストアに証明書をインポートします。 [上記のコマンド](#key-terminology)を使用して、証明書をインポートします。

> [!WARNING]
> 現在の証明書の有効期限が切れる前に新しい証明書を取得するには、これらの手順を繰り返す必要があります。 有効期限の情報は、 **[証明書]** ダイアログ ボックスの **[詳細]** タブで確認できます。
>
> :::image type="content" source="media/java-ipa/troubleshooting/certificate-details.png" alt-text="SSL 証明書の詳細を示すスクリーンショット。":::
