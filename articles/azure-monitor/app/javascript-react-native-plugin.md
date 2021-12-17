---
title: Application Insights JavaScript SDK の React Native プラグイン
description: Application Insights JavaScript SDK の React Native プラグインをインストールして使用する方法について説明します。
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6b407fdc0614d94862257ce3bebc0f58f971f9d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735834"
---
# <a name="react-native-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK の React Native プラグイン

Application Insights JavaScript SDK 用の React Native プラグインでは、デバイス情報が収集されます。既定では、このプラグインによって、以下が収集されます。

- **一意のデバイス ID** (インストール ID とも呼ばれます)。
- **デバイス モデル名** (iPhone X、Samsung Galaxy Fold、Huawei P30 Pro など)
- **デバイスの種類** (たとえば、受話器やタブレットなど)

## <a name="requirements"></a>必要条件

`@microsoft/applicationinsights-web` のバージョン 2.0.0 以上を使用する必要があります。 このプラグインは、react-native アプリでのみ機能します。 これは、[Expo フレームワークを使用するアプリ](https://docs.expo.io/)では動作しないため、[React Native の作成] でも動作しません。

## <a name="getting-started"></a>作業の開始

[react-native-device-info](https://www.npmjs.com/package/react-native-device-info) パッケージをインストールしてリンクします。 自分のアプリを使用して最新のデバイス名を収集するには、`react-native-device-info` パッケージを最新の状態に保ちます。

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>プラグインの初期化

このプラグインを使用するには、プラグインを構築し、既存の Application Insights インスタンスに `extension` として追加する必要があります。

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights JavaScript SDK ドキュメント](javascript.md)を参照してください。
- Kusto クエリ言語と Log Analytics でのデータのクエリの詳細については、[ログ クエリの概要](../../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。
