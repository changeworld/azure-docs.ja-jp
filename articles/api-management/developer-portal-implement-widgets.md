---
title: 開発者ポータルでウィジェットを実装する
titleSuffix: Azure API Management
description: 外部 API のデータを使用し、それを API Management 開発者ポータルに表示するウィジェットを実装する方法について説明します。
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: af2ac28df5b41606d9199d6fa36c3f683e86c59d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639909"
---
# <a name="implement-widgets-in-the-developer-portal"></a>開発者ポータルでウィジェットを実装する

このチュートリアルでは、外部 API のデータを使用し、それを API Management 開発者ポータルに表示するウィジェットを実装します。

このウィジェットは、サンプルの [Conference API](https://conferenceapi.azurewebsites.net/?format=json) からセッションの説明を取得します。 セッション識別子は、指定のウィジェット エディターを使用して設定されます。

開発プロセスの参考として、API Management 開発者ポータルの [GitHub リポジトリ](https://github.com/Azure/api-management-developer-portal/)の `examples` フォルダー (`/examples/widgets/conference-session`) にある完成したウィジェットを参照してください。

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="発行されたウィジェットのスクリーンショット":::

## <a name="prerequisites"></a>前提条件

* 開発者ポータルの最新リリース用に[ローカル環境](developer-portal-self-host.md#step-1-set-up-local-environment)を設定します。

* [Pagebits のウィジェットの構造](https://paperbits.io/wiki/widget-anatomy)を理解しておく必要があります。


## <a name="copy-the-scaffold"></a>スキャフォールディングをコピーする

新しいウィジェットを構築するための開始点として、`/scaffolds` フォルダーの `widget` スキャフォールディングを使用します。

1. `/scaffolds/widget` フォルダーを `/community/widgets` にコピーします。
1. フォルダーの名前を `conference-session` に変更します。

## <a name="rename-exported-module-classes"></a>エクスポートされたモジュール クラスの名前を変更する

これらのファイルの `Widget` プレフィックスを `ConferenceSession` に置き換えて、エクスポートされたモジュール クラスの名前を変更します。

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
たとえば、`widget.design.module.ts` ファイルで、`WidgetDesignModule` を `ConferenceSessionDesignModule` に変更します。
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
to 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>ウィジェットを登録する

各ファイルに次の行を追加して、ポータルのルート モジュールにウィジェットのモジュールを登録します。

1. `src/apim.design.module.ts` - デザイン時の依存関係を登録するモジュール。

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` - 公開時の依存関係を登録するモジュール。

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` - ランタイム依存関係。

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>ウィジェットをポータルに配置する

複製されたスキャフォールディングを接続し、開発者ポータルで使用できるようになりました。

1. `npm start` コマンドを実行します。

1. アプリケーションが読み込まれたら、ページに新しいウィジェットを配置します。 これは、ウィジェット セレクターの `Community` カテゴリに `Your widget` という名前で表示されています。

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="ウィジェット セレクターのスクリーンショット":::

1. **Ctrl**+**S** (macOS では **⌘**+**S**) キーを押してページを保存します。

    > [!NOTE]
    > デザイン時に、**Ctrl** (または **⌘**) キーを押して Web サイトと対話することもできます。

## <a name="add-custom-properties"></a>カスタム プロパティを追加する

このウィジェットでセッションの説明を取得するには、セッション識別子を認識する必要があります。 各インターフェイスとクラスに `Session ID` プロパティを追加します。

このウィジェットでセッションの説明を取得するには、セッション識別子を認識する必要があります。 各インターフェイスとクラスにセッション ID プロパティを追加します。

1. `widgetContract.ts` - ウィジェットの構成を永続化する方法を定義したデータ コントラクト (データ層)。

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - モデル (ビジネス層) - システム内のウィジェットの主な表現。 エディターによって更新され、プレゼンテーション層によってレンダリングされます。

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - ビュー モデル (プレゼンテーション層) - 開発者ポータルが HTML テンプレートを使用してレンダリングする UI フレームワーク固有のオブジェクト。

    > [!NOTE]
    > このファイルの内容は何も変更する必要はありません。

## <a name="configure-binders"></a>バインダーを構成する

データ ソースからウィジェット プレゼンテーションへの `sessionNumber` のフローを有効にします。 `ModelBinder` と `ViewModelBinder` エンティティを編集します。

1. `widgetModelBinder.ts` は、コントラクトに記述されているデータを使用してモデルを準備するのに役立ちます。

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` は、開発者ポータルが特定の UI フレームワークでモデルを (ビュー モデルとして) どのように提示する必要があるかを認識しています。

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>デザイン時のウィジェット テンプレートを調整する

各スコープのコンポーネントは、それぞれ独立して実行されます。 これらは独立した依存関係挿入コンテナー、独自の構成、ライフサイクルなどを持っています。さらに、さまざまな UI フレームワーク (この例では Knockout JS) を使用することもできます。

デザイン時の観点から見ると、ランタイム コンポーネントは、特定の属性やコンテンツを含む HTML タグにすぎません。 構成は必要に応じてプレーンなマークアップで渡されます。 この例のように単純なケースでは、パラメーターは属性で渡されます。 構成がより複雑な場合は、指定の構成プロバイダー (`ISettingsProvider` など) によって取得された必要な設定の識別子を使用できます。

1. `ko/widgetView.html` ファイルを更新します。

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    開発者ポータルで "*デザイン時*" または "*発行時*" に `attr` バインディングを実行すると、次のような HTML が生成されます。

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    次に、実行時に、`widget-runtime` コンポーネントが `sessionNumber` を読み取り、これを初期化コードで使用します (下記参照)。

1. 作成時にセッション ID を割り当てるように、`widgetHandlers.ts` ファイルを更新します。

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>ランタイム ビュー モデルを修正する

ランタイム コンポーネントとは、Web サイト自体で実行されているコードです。 たとえば、API Management 開発者ポータルでは、これらは動的コンポーネント ("*API の詳細*"、"*API コンソール*" など) の背後にあるすべてのスクリプトであり、コード サンプルの生成や要求の送信などの操作を処理します。

ランタイム コンポーネントのビュー モデルには、次のメソッドとプロパティが必要です。

- 外部から渡されるコンポーネント入力パラメーターとして使用される (`Param` デコレータでマークされた) `sessionNumber` プロパティ (デザイン時に生成されるマークアップ。前の手順を参照してください)。
- ウィジェット テンプレートにバインドされた `sessionDescription` プロパティ (この記事の後半にある `widget-runtime.html` を参照してください)。
- ウィジェットが作成され、そのすべてのパラメーターが割り当てられた後に呼び出される (`OnMounted` デコレータを持つ) `initialize` メソッド。 ここで、`sessionNumber` を読み取り、`HttpClient` を使用して API を呼び出すことをお勧めします。 `HttpClient` は、IoC (制御の反転) コンテナーによって挿入された依存関係です。

- まず、開発者ポータルでウィジェットが作成され、そのすべてのパラメーターが割り当てられます。 次に、`initialize` メソッドが呼び出されます。

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>ウィジェット テンプレートを調整する

セッションの説明を表示するようにウィジェットを更新します。

説明をレンダリングするために、`ko/runtime/widget-runtime.html` ファイルで段落タグと `markdown` (または `text`) バインドを使用します。

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>ウィジェット エディターを追加する

セッション `107` の説明を取得するようにウィジェットが構成されました。 コード内で既定のセッションとして `107` を指定しました。 すべてが正しく行われたことを確認するために、`npm start` を実行し、開発者ポータルにページの説明が表示されることを確認します。

ここで、これらの手順を実行して、ユーザーがウィジェット エディターを使用してセッション ID を設定できるようにします。

1. `ko/widgetEditorViewModel.ts` ファイルを更新します。

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    エディター ビュー モデルでは、これまでと同じ方法が使用されますが、`@Event()` で修飾された新しいプロパティ `onChange` があります。 これは、モデルに対する変更をリスナー (この場合はコンテンツ エディター) に通知するために、コールバックを接続します。

1. `ko/widgetEditorView.html` ファイルを更新します。

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. `npm start` をもう一度実行します。 ウィジェット エディターで `sessionNumber` を変更できるようになります。 ID を `108` に変更し、変更を保存して、ブラウザーのタブを最新の情報に更新します。問題が発生している場合は、再度ウィジェットをページに追加する必要がある可能性があります。

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="ウィジェット エディターのスクリーンショット":::

## <a name="rename-the-widget"></a>ウィジェットの名前を変更する

`constants.ts` ファイルのウィジェット名を変更します。

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> このウィジェットをリポジトリに投稿する場合、`widgetName` はそのフォルダー名と同じである必要があり、表示名から派生したものにする必要があります (小文字とスペースはダッシュで置き換えます)。 カテゴリは `Community` のままにする必要があります。

## <a name="next-steps"></a>次の手順


開発者ポータルの詳細については、次を参照してください。

- [Azure API Management 開発者ポータルの概要](api-management-howto-developer-portal.md)

- [ウィジェットを投稿する](developer-portal-widget-contribution-guidelines.md) - コミュニティへの投稿を歓迎します。ご検討ください。

- コミュニティによって投稿されたウィジェットの使用方法については、[コミュニティ ウィジェットの使用](developer-portal-use-community-widgets.md)に関する記事を参照してください。
