---
title: Application Insights JavaScript SDK の Angular プラグイン
description: Application Insights JavaScript SDK の Angular プラグインをインストールして使用する方法。
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: 7ac83d0c43026b431370fab1d8c49aec1adf6659
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107312724"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK の Angular プラグイン

Application Insights JavaScript SDK の Angular プラグインを使用すると、以下が有効になります。

- ルーターの変更の追跡
- キャッチされない例外の追跡

> [!WARNING]
> Angular プラグインは、ECMAScript 3 (ES3) と互換性がありません。

## <a name="getting-started"></a>作業の開始

npm パッケージをインストールします。

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>基本的な使用方法

アプリの入力コンポーネントで Application Insights のインスタンスを設定します。

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

キャッチされない例外を追跡するには、次のように `app.module.ts` で ApplicationinsightsAngularpluginErrorService を設定します。

```js
import { ApplicationinsightsAngularpluginErrorService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
  ...
  providers: [
    {
      provide: ErrorHandler,
      useClass: ApplicationinsightsAngularpluginErrorService
    }
  ]
  ...
})
export class AppModule { }
```

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights の JavaScript SDK ドキュメント](javascript.md)を参照してください
- [GitHub の Angular プラグイン](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
