---
title: Visual Studio Code で使用する Azure Stream Analytics の詳細 (プレビュー)
description: この記事では、Azure Stream Analytics ジョブをローカル プロジェクトにエクスポートし、ジョブ エントリを一覧表示して、Stream Analytics ジョブの CI/CD パイプラインを設定する方法を示します。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65080677"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Visual Studio Code で使用する Azure Stream Analytics の詳細 (プレビュー)

Visual Studio Code 用 Azure Stream Analytics 拡張機能は、Stream Analytics ジョブを管理するための軽量エクスペリエンスを開発者に提供します。 Azure Stream Analytics 拡張機能を使用すると、次のことができます。

- ジョブの[作成](quick-create-vs-code.md)、開始、停止
- 既存のジョブのローカル プロジェクトへのエクスポート
- ローカルでのクエリの実行
- CI/CD パイプラインの設定

## <a name="export-a-job-to-a-local-project"></a>ジョブのローカル プロジェクトへのエクスポート

ジョブをローカル プロジェクトにエクスポートするには、Visual Studio Code の **Stream Analytics Explorer** で、エクスポートするジョブを特定します。 次に、プロジェクトのフォルダーを選択します。 プロジェクトが選択したフォルダーにエクスポートされ、Visual Studio Code からジョブの管理を続行できます。 Visual Studio Code を使用した Stream Analytics ジョブの管理の詳細については、Visual Studio Code の[クイック スタート](quick-create-vs-code.md)を参照してください。

![Visual Studio Code での ASA ジョブのエクスポート](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>ローカルでのクエリの実行

Visual Studio Code の Azure Stream Analytics 拡張機能を使用して、Stream Analytics ジョブをサンプル データを使ってローカルでテストすることができます。

1. Stream Analytics ジョブを作成したら、**Ctrl + Shift + P** キーを使ってコマンド パレットを開きます。 次に「**ASA: Add Input**」と入力して選択します。

    ![Visual Studio Code での ASA 入力の追加](./media/vs-code-how-to/add-input.png)

2. **[ローカル入力]** を選択します。

    ![Visual Studio Code での ASA ローカル入力の追加](./media/vs-code-how-to/add-local-input.png)

3. **[+ New Local Input]\(新規ローカル入力\)** を選択します。

    ![Visual Studio Code での新しい ASA ローカル入力の追加](./media/vs-code-how-to/add-new-local-input.png)

4. クエリで使用したのと同じ入力エイリアスを入力します。

    ![新しい ASA ローカル入力エイリアスの追加](./media/vs-code-how-to/new-local-input-alias.png)

5. **LocalInput_DefaultLocalStream.json** ファイル内に、ローカル データ ファイルが配置されているファイル パスを入力します。

    ![Visual Studio でのローカル ファイル パスの入力](./media/vs-code-how-to/local-file-path.png)

6. クエリ エディターに戻り、**[ローカルで実行]** を選択します。

    ![クエリ エディターで [ローカルで実行] を選択](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>CI/CD パイプラインの設定

**asa-cicd ツール**の NPM パッケージを使用して、Azure Stream Analytics ジョブのための継続的インテグレーションと継続的なデプロイを有効にすることができます。 NPM パッケージは、Stream Analytics の Visual Studio Code プロジェクトの自動デプロイのためのツールを提供します。 Visual Studio Code をインストールしなくても、Windows、macOS、Linux で使用できます。

[パッケージをダウンロード](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar)したら、次のコマンドを使用して Azure Resource Manager のテンプレートを出力します。 **outputPath** が指定されていない場合、テンプレートは、プロジェクトの **bin** フォルダーの下の **Deploy** フォルダーに配置されます。

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>次の手順

* [Visual Studio Code で Azure Stream Analytics クラウド ジョブを作成する (プレビュー)](quick-create-vs-code.md)