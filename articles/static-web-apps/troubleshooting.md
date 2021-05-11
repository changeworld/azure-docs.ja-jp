---
title: Azure Static Web Apps のトラブルシューティング
description: Azure Static Web Apps の使用時の問題を追跡する場合の最初のステップ。
services: static-web-apps
author: geektrainer
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 04/07/2021
ms.author: chrhar
ms.openlocfilehash: 082c3af694932f4e864b99aa8c2cdc7d1f5fca30
ms.sourcegitcommit: a5dd9799fa93c175b4644c9fe1509e9f97506cc6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108210161"
---
# <a name="troubleshooting-deployment-and-runtime-errors"></a>デプロイおよびランタイム エラーのトラブルシューティング

この記事では、静的 Web アプリのデプロイとその他の問題のトラブルシューティングのためのステップバイステップ ガイドを紹介します。

## <a name="retrieve-deployment-error-messages"></a>デプロイ エラー メッセージを取得する

Azure Static Web Apps のデプロイ ワークフローでは、次のコマンドを自動的に実行する Node.js [Oryx ビルド プロセス](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/nodejs.md#build)が使用されます。

```bash
npm install
npm run build # if specified in package.json
npm run build:azure # if specified in package.json
```

このプロセスによって生成されたエラーは、GitHub ワークフロー実行に記録されます。

1. 静的 Web アプリの GitHub リポジトリに移動します。
1. **[Actions]\(アクション\)** を選択します。

    > [!NOTE]
    > 失敗したワークフローの実行は、緑色のチェックマークではなく赤色の *X* で表示されます

1. 検証するワークフロー実行のリンクを選択します。
1. **[ビルドおよびデプロイ ジョブ]** を選択して、デプロイの詳細を開きます
1. **[ビルド & デプロイ]** を選択してログを表示します

    ![静的 Web アプリのデプロイ ログのスクリーンショット](./media/troubleshooting/build-deploy-log.png)

1. ログとエラー メッセージを確認します。

    > [!NOTE]
    > 一部の警告エラーメッセージは、 *.oryx_prod_node_modules* や *ワークスペース* に関する注意事項など、赤で表示されている可能性があります。 これらは通常のデプロイ プロセスの一部です。

いずれかのパッケージのインストールに失敗した場合や、その他の問題が発生した場合、`npm` コマンドをローカルで実行した場合と同様に、元のエラー メッセージがここに表示されます。

### <a name="confirm-folder-configuration"></a>フォルダー構成を確認する

Azure Static Web Apps では、アプリケーションをホストするために使用するフォルダーが認識されている必要があります。 この構成は、ワークフローの最後にビルド プロセスによって確認されます。 エラーは検証ステップ時にログに記録されます。

エラー ログに次のいずれかのエラー メッセージが表示されている場合は、ワークフローのフォルダー構成が正しくないことを示しています。

| エラー メッセージ | 説明 |
| --- | --- |
|アプリ ディレクトリの場所: '/*folder*' は無効です。 このディレクトリを検出できませんでした。 | ワークフローにリポジトリ構造が反映されていることを確認します。 |
| アプリのビルドで成果物フォルダー '*folder*' を生成できませんでした。 | ワークフロー ファイルに `folder` プロパティが正しく構成されていることを確認します。 |
| API ディレクトリが指定されていないか、指定されたディレクトリが見つかりませんでした。 | ワークフローで `api` フォルダーの値が定義されていないため、Azure Functions は作成されません。 |

ワークフローには、3 つのフォルダーの場所が指定されています。 デプロイ前に、これらの設定が、プロジェクトと、ソース コードを変換するすべてのツールの両方に一致していることを確認してください。

| 構成設定 | 説明 |
| --- | --- |
| `app_location` | デプロイされるソース コードのルートの場所。 この設定は通常、 */* またはプロジェクトの JavaScript と HTML の場所になります。 |
| `output_location` | webpack などのバンドラーから、任意のビルド プロセスによって作成されたフォルダーの名前。 このフォルダーは両方とも、ビルド プロセスによって、`app_location` の下のサブディレクトリに作成される必要があります |
| `api_location` |Azure Static Web Apps によってホストされる Azure Functions アプリケーションのルートの場所。 これは、プロジェクトのすべての Azure Functions のルート フォルダー (通常は *api*) を指します。 |

> [!NOTE]
> 正しくない `api_location` 構成によってエラー メッセージが生成されても、Azure Static Web Apps にはサーバーレス コードが必要ないため、正常にビルドされる可能性があります。

## <a name="review-server-errors"></a>サーバー エラーを確認する

[Application Insights](../azure-monitor/app/app-insights-overview.md) を使用して、ランタイム エラー メッセージを見つけます。 インスタンスをまだ作成していない場合は、[Azure Static Web Apps の監視](monitor.md)に関するページを参照してください。 Application Insights では、各エラーによって生成される完全なエラー メッセージとスタック トレースがログに記録されます。

> [!NOTE]
> Application Insights のインストール後に生成されたエラー メッセージのみを表示できます。

1. Azure portal 内で、静的 Web アプリに関連付けられている **リソース グループ** を開きます。
1. 静的 Web アプリと同じ名前を持つ Application Insights インスタンスを選択します (上記の手順を使用して作成した場合)。
1. *[調査]* で、 **[エラー]** を選択します。
1. 右側の *[ドリル イン]* まで、下にスクロールします。
1. 右下隅の *[ドリル イン]* の下で、ボタンに最近失敗した操作の数が表示されます。

    ![エラー画面のスクリーンショット](./media/troubleshooting/app-insights-errors.png)

1. **[x 個の操作]** というボタンを選択して、最近失敗した操作を表示するパネルを開きます。

    ![操作画面のスクリーンショット](./media/troubleshooting/app-insights-operations.png)

1. 一覧から選択して、エラーを調査します。

    ![エラーの詳細画面のスクリーンショット](./media/troubleshooting/app-insights-details.png)

## <a name="environment-variables"></a>環境変数

多くの Web アプリケーションでは、機密または環境固有の設定を格納するために、環境変数が使用されています。 Azure Static Web Apps では、アプリケーション設定から環境変数がサポートされています。

アプリケーション設定は、アプリケーションの環境変数を設定するキーと値のペアです。 変数は、環境変数へのアクセスに一般的な、同じ構文を使用して、アプリケーションで使用できます。

デプロイ時に、すべての環境変数がアプリケーション設定として設定されていることを再確認します。

1. Azure portal で静的 Web アプリを開く
1. **[構成]** を選択します。 *[構成]* 画面に、すべてのアプリケーション設定の一覧が表示されます。

    ![静的 Web アプリの [構成] 画面のスクリーンショット](media/troubleshooting/app-settings.png)

次の手順に従って、新しい変数を追加します。

1. **[追加]** を選択します。
1. **[名前]** を設定します。
1. **[値]** を設定します。
1. **[OK]** を選択します。
1. **[保存]** を選択します。
