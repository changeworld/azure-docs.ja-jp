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
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91843671"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Application Insights JavaScript SDK の Angular プラグイン

Application Insights JavaScript SDK の Angular プラグインを使用すると、以下が有効になります。

- ルーターの変更の追跡
- Angular コンポーネントの使用状況の統計情報

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
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

`trackMetric` メソッドを使用して Angular コンポーネントの使用状況を追跡するには、`app.module.ts` ファイルのプロバイダーの一覧に、プロバイダーとして `AngularPluginService` を追加します。

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

コンポーネントの有効期間を追跡するには、そのコンポーネントの `ngOnDestroy` メソッドで `trackMetric` を呼び出します。 コンポーネントが破棄されるときには、ユーザーがページに滞在した時間とコンポーネント名を送信する `trackMetric` イベントがトリガーされます。

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>次のステップ

- JavaScript SDK の詳細については、[Application Insights の JavaScript SDK ドキュメント](javascript.md)を参照してください
- [GitHub の Angular プラグイン](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)