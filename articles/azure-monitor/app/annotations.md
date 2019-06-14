---
title: Application Insights のリリース注釈 | Microsoft Docs
description: Application Insights で、メトリックス エクスプローラーのグラフにデプロイ マーカーまたはビルド マーカーを追加します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2019
ms.locfileid: "66476177"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Application Insights のメトリック グラフの注釈

[メトリックス エクスプローラー](../../azure-monitor/app/metrics-explorer.md)のグラフの注釈により、新しいビルドのデプロイ先やその他の重要なイベントが示されます。 これにより、変更内容がアプリケーションのパフォーマンスに影響を与えたかどうかを簡単に把握できます。 注釈は、[Azure DevOps Services ビルド システム](https://docs.microsoft.com/azure/devops/pipelines/tasks/)により自動で作成されます。 PowerShell から作成することにより、任意のイベントにフラグを設定する注釈を作成することもできます。

> [!NOTE]
> この記事には、非推奨の**クラシック メトリック エクスペリエンス**が反映されています。 現在、注釈は、クラシック エクスペリエンスと **[ブック](../../azure-monitor/app/usage-workbooks.md)** においてのみ使用できます。 現在のメトリック エクスペリエンスについて詳しくは、[こちらの記事](../../azure-monitor/platform/metrics-charts.md)をご覧ください。

![注釈の例](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Azure DevOps Services ビルドを使用したリリース注釈

リリース注釈は、Azure DevOps Services のクラウド ベースの Azure Pipelines サービスの機能です。

### <a name="install-the-annotations-extension-one-time"></a>注釈拡張機能のインストール (1 回限り)
リリース注釈を作成できるようにするには、Visual Studio Marketplace で入手可能な Azure DevOps Services 拡張機能のいずれかをインストールする必要があります。

1. [Azure DevOps Services](https://azure.microsoft.com/services/devops/) プロジェクトにサインインします。
2. Visual Studio Marketplace で [リリース注釈拡張機能を取得](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations)して、Azure DevOps Services 組織に追加します。

![Azure DevOps 組織を選択してからインストールします。](./media/annotations/1-install.png)

これは、Azure DevOps Services 組織に対して一度だけ行う必要があります。 これで、リリース注釈を組織のプロジェクトに対して構成できるようになりました。

### <a name="configure-release-annotations"></a>リリース注釈を構成する

Azure DevOps Services リリース テンプレートごとに別個の API キーを取得する必要があります。

1. [Microsoft Azure portal](https://portal.azure.com) にサインインし、アプリケーションを監視する Application Insights リソースを開きます (まだリソースを作成していない場合は、[この時点で作成します](../../azure-monitor/app/app-insights-overview.md))。
2. **[API アクセス]** タブを開き、 **[Application Insights ID]** をコピーします。
   
    ![portal.azure.com で、Application Insights リソースを開き、[設定] を選択します。 [API アクセス] を開きます。 アプリケーション ID をコピーする](./media/annotations/2-app-id.png)

4. 別のブラウザー ウィンドウで、Azure DevOps Services のデプロイを管理するリリース テンプレートを開きます (または作成します)。
   
    タスクを追加し、メニューから [Application Insights Release Annotation]\(Application Insights リリース注釈) タスクを選択します。

   ![プラス記号、[タスクの追加] の順にクリックし、[Application Insights Release Annotation]\(Application Insights リリース注釈\) を選択します。 Application Insights ID を貼り付けます。](./media/annotations/3-add-task.png)

    [API アクセス] タブからコピーした**アプリケーション ID** を貼り付けます。
   
    ![Application Insights ID を貼り付けます。](./media/annotations/4-paste-app-id.png)

5. Azure ウィンドウに戻り、新しい API キーを作成し、それをコピーします。
   
    ![[Azure] ウィンドウの [API アクセス] タブで、[API キーの作成] をクリックします。](./media/annotations/5-create-api-key.png)

    ![[API キーの作成] タブでコメントを指定し、[書き込み] 注釈にチェックを入れ、[キーの生成] をクリックします。 新しいキーをコピーします。](./media/annotations/6-create-api-key.png)

6. リリース テンプレートの [構成] タブを開きます。
   
    `ApiKey`の変数の定義を作成します。
   
    ApiKey の変数の定義に API キーを貼り付けます。
   
    ![[Azure DevOps Services] ウィンドウで、[変数] タブを選択し、[追加] をクリックします。 名前を ApiKey に設定し、[値] に生成したキーを貼り付けて、ロック アイコンをクリックします。](./media/annotations/7-paste-api-key.png)
1. 最後に、リリース パイプラインを **[保存]** します。


## <a name="view-annotations"></a>注釈を表示する
これで、このリリース テンプレートを使用して新しいリリースをデプロイするたびに、注釈が Application Insights に送信されるようになりました。 注釈は、メトリックス エクスプローラーのグラフに表示されます。

要求元、ソース管理の分岐、リリース パイプライン、環境などを含む、リリースに関する詳細を表示するための注釈マーカー (薄いグレーの矢印) をクリックします。

![任意のリリース注釈マーカーをクリックします。](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>PowerShell からカスタム注釈を作成する
(Azure DevOps Services を利用せずに) あらゆるプロセスから注釈を作成することもできます。 


1. [GitHub から Powershell スクリプト](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)のローカル コピーを作成します。

2. [API アクセス] タブからアプリケーション ID を取得して API キーを作成します。

3. 次のようなスクリプトを呼び出します。

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

このスクリプトは、過去に関する注釈を作成するなどの場合に簡単に修正できます。

## <a name="next-steps"></a>次の手順

* [作業項目を作成する](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [PowerShell でのオートメーション](../../azure-monitor/app/powershell.md)
