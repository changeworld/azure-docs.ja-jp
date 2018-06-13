---
title: Power BI Embedded 移行ツールの使用 | Microsoft Docs
description: Power BI Embedded 移行ツールを使用して、Power BI ワークスペース コレクションのレポートを Power BI Embedded にコピーすることができます。
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/28/2017
ms.author: maghan
ms.openlocfilehash: 4f76b1efb509745653bfde0926f56032030f7d47
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2018
ms.locfileid: "31419903"
---
# <a name="use-the-power-bi-embedded-migration-tool"></a>Power BI Embedded 移行ツールの使用

Power BI Embedded 移行ツールを使用して、Power BI ワークスペース コレクションのレポートを Power BI Embedded にコピーすることができます。

ワークスペース コレクションから Power BI サービスへのコンテンツの移行は、現在のソリューションと並行して行うことができ、ダウンタイムを必要としません。

## <a name="limitations"></a>制限事項

* プッシュされたデータセットはダウンロードできません。Power BI サービス用の Power BI REST API を使用して作成し直す必要があります。
* 2016 年 11 月 26 日より前にインポートされた PBIX ファイルはダウンロードできなくなります。

## <a name="download"></a>[ダウンロード]

移行ツールのサンプルは [GitHub](https://github.com/Microsoft/powerbi-migration-sample) からダウンロードできます。 リポジトリを zip ファイルとしてダウンロードするか、ローカルに複製することができます。 ダウンロードしたら、Visual Studio 内で *powerbi-migration-sample.sln* を開いてビルドし、移行ツールを実行できます。

## <a name="migration-plans"></a>移行プラン

移行プランは、Power BI ワークスペース コレクション内のコンテンツと、それを Power BI Embedded に発行する方法を列挙した単なるメタデータです。

### <a name="start-with-a-new-migration-plan"></a>新しい移行プランを開始する

移行プランは、Power BI ワークスペース コレクションで使用し、Power BI Embedded に移行できる項目のメタデータです。 移行プランは XML ファイルとして保存されます。

まずは新しい移行プランの作成から始めます。 新しい移行プランを作成するには、次の手順を実行します。

1. **[ファイル]** > **[New Migration Plan]\(新しい移行プラン\)** を選択します。

    ![新しい移行プラン](media/migrate-tool/migrate-tool-plan.png)

2. **[Select Power BI Workspace Collections Resource Group]\(Power BI ワークスペース コレクション リソース グループの選択\)** ダイアログで、**[環境]** ドロップダウン リストから製品を選択します。

3. サインインするように求められます。 Azure サブスクリプションのログイン情報を使用します。

    > [!IMPORTANT]
    > これは、Power BI へのサインインに使用する Office 365 組織アカウントでは**ありません**。

4. Power BI ワークスペース コレクションのリソースを保存する Azure サブスクリプションを選択します。

    ![Azure サブスクリプションを選択します。](media/migrate-tool/migrate-tool-select-resource-group.png)

5. サブスクリプションの一覧の下で、目的のワークスペース コレクションを含む**リソース グループ**を選択し、**[選択]** を選択します。

    ![リソース グループの選択](media/migrate-tool/migrate-tool-select-resource-group2.png)

6. **[分析]** を選択します。 これにより、Azure サブスクリプション内の項目のインベントリが表示され、プランを開始することができます。

    ![[分析] ボタンの選択](media/migrate-tool/migrate-tool-analyze-group.png)

    > [!NOTE]
    > ワークスペース コレクションの数と、ワークスペース コレクション内に存在するコンテンツの数に応じて、分析には数分ほどかかる場合があります。

7. **分析**が完了すると、移行プランを保存するように求められます。

この時点で、移行プランは Azure サブスクリプションに接続されています。 移行プランの操作方法の流れについては、後述の説明を参照してください。 移行プランには、移行の分析と計画、ダウンロード、グループの作成、アップロードが含まれています。

### <a name="save-your-migration-plan"></a>移行プランを保存する

移行プランを保存して後から使用することができます。 保存すると、移行プランのすべての情報を含んだ XML ファイルが作成されます。

移行プランを保存するには、次の手順を実行します。

1. **[ファイル]** > **[Save Migration Plan]\(移行プランの保存\)** を選択します。

    ![[Save Migration Plan]\(移行プランの保存\) メニュー オプション](media/migrate-tool/migrate-tool-save-plan.png)

2. ファイルに名前を付けるか、生成されたファイル名をそのまま使用して、**[保存]** を選択します。

### <a name="open-an-existing-migration-plan"></a>既存の移行プランを開く

保存した移行プランを開き、移行の作業を続けることができます。

既存の移行プランを開くには、次の手順を実行します。

1. **[ファイル]** > **[Open Existing Migration Plan]\(既存の移行プランを開く\)** を選択します。

    ![[Open Existing Migration Plan]\(既存の移行プランを開く\) メニュー オプション](media/migrate-tool/migrate-tool-open-plan.png)

2. 移行ファイルを選択し、**[開く]** を選択します。

## <a name="step-1-analyze-and-plan-migration"></a>手順 1: 移行の分析と計画

**[Analyze & Plan Migration]\(移行の分析と計画\)** タブには、Azure サブスクリプションのリソース グループの現在の内容が表示されます。

![[Analyze & Plan Migration]\(移行の分析と計画\) タブ](media/migrate-tool/migrate-tool-step1.png)

ここでは例として *SampleResourceGroup* を見てみましょう。

### <a name="paas-topology"></a>PaaS トポロジ

これは、*[リソース グループ] > [Workspace collections]\(ワークスペース コレクション\) > [ワークスペース]* の一覧です。 リソース グループとワークスペース コレクションにはフレンドリ名が表示され、 ワークスペースには GUID が表示されます。

一覧の項目には、色と (#/#) 形式の数値も表示されます。 これは、ダウンロード可能なレポートの数を表しています。

黒色は、すべてのレポートをダウンロードできることを示します。 赤色は、一部のレポートをダウンロードできないことを示します。 左側の数値は、ダウンロードできるレポートの合計数を示します。 右側の数値は、グループ内のレポートの合計数を示します。

PaaS トポロジ内の項目を選択し、レポート セクションにレポートを表示することができます。

### <a name="reports"></a>レポート

レポート セクションに、使用できるレポートが一覧表示され、ダウンロードできるかどうかが示されます。

![Power BI ワークスペース コレクション内のレポートの一覧](media/migrate-tool/migrate-tool-analyze-reports.png)

### <a name="target-structure"></a>ターゲット構造

**ターゲット構造**は、ダウンロード先とアップロード方法をツールに指示するための場所です。

#### <a name="download-plan"></a>[Download plan]\(ダウンロード プラン\)

パスが自動的に作成されます。 このパスは、必要に応じて変更することができます。 パスを変更する場合は、**[Update paths]\(パスの更新\)** を選択する必要があります。

**この項目を設定しても、実際にダウンロードは実行されません。** レポートのダウンロード先の構造を指定するだけです。

#### <a name="upload-plan"></a>[Upload plan]\(アップロード プラン\)

ここでは、Power BI サービス内で作成されるアプリ ワークスペースに使用するプレフィックスを指定できます。 プレフィックスの後は、Azure に存在するワークスペースの GUID になります。

![グループ名のプレフィックスの指定](media/migrate-tool/migrate-tool-upload-plan.png)

**この項目を設定しても、実際に Power BI サービス内にグループは作成されません。** グループの名前付けの構造を定義するだけです。

プレフィックスを変更する場合は、**[Generate Upload Plan]\(アップロード プランの生成\)** を選択する必要があります。

必要に応じて、アップロード プラン内で直接、グループを右クリックしてグループ名を変更することもできます。

![[グループ名の変更] コンテキスト メニュー オプション](media/migrate-tool/migrate-tool-upload-report-rename-item.png)

> [!NOTE]
> *グループ*名に空白や無効な文字を含めることはできません。

## <a name="step-2-download"></a>手順 2: ダウンロード

**[ダウンロード]** タブには、レポートと関連するメタデータの一覧が表示されます。 エクスポートの状態と、以前のエクスポートの状態を確認できます。

![[ダウンロード] タブ](media/migrate-tool/migrate-tool-download-tab.png)

2 つのオプションがあります。

* 特定のレポートを選択し、**[選択したものをダウンロード]** を選択します。
* **[すべてダウンロード]** を選択します。

![[選択したものをダウンロード] ボタン](media/migrate-tool/migrate-tool-download-options.png)

正常にダウンロードされると、"*完了*" 状態になり、PBIX ファイルが存在することが反映されます。

ダウンロードが完了したら、**[グループの作成]** タブを選択します。

## <a name="step-3-create-groups"></a>手順 3: グループの作成

使用できるレポートをダウンロードしたら、**[グループの作成]** タブに移動します。このタブでは、作成した移行プランに基づいて、Power BI サービス内にアプリ ワークスペースが作成されます。 アプリ ワークスペースには、**[Analyze & Plan Migration]\(移行の分析と計画\)** 内の **[アップロード]** タブで指定した名前が付けられます。

![[グループの作成] タブ](media/migrate-tool/migrate-tool-create-groups.png)

アプリ ワークスペースを作成するには、**[Create Selected Groups]\(選択されているグループの作成\)** か **[Create All Missing Groups]\(見つからないすべてのグループの作成\)** を選択します。

これらのオプションのいずれかを選択すると、サインインするように求められます。 "*アプリ ワークスペースを作成する Power BI サービスの資格情報を使用します。*"

![Power BI のサインイン画面](media/migrate-tool/migrate-tool-create-group-sign-in.png)

これにより、Power BI サービス内にアプリ ワークスペースが作成されます。 ただし、レポートはアプリ ワークスペースにアップロードされません。

Power BI にサインインしてワークスペースが存在するか調べることで、アプリ ワークスペースが作成されたことを確認できます。 ワークスペースには何も表示されません。

![Power BI サービス内のアプリ ワークスペース](media/migrate-tool/migrate-tool-app-workspace.png)

ワークスペースの作成後、**[アップロード]** タブに移動できます。

## <a name="step-4-upload"></a>手順 4: アップロード

**[アップロード]** タブで、Power BI サービスにレポートをアップロードします。 [ダウンロード] タブでダウンロードしたレポートの一覧と、移行プランに基づいたターゲット グループ名が表示されます。

![[アップロード] タブ](media/migrate-tool/migrate-tool-upload-tab.png)

選択したレポートをアップロードするか、すべてのレポートをアップロードすることができます。 アップロードの状態をリセットして、レポートを再アップロードすることもできます。

同じ名前のレポートが存在する場合の対処方法を選択するオプションもあります。 **[中止]**、**[無視]**、**[上書き]** から選択できます。

![レポートが存在する場合の対処方法を選択するドロップダウン リスト](media/migrate-tool/migrate-tool-upload-report-same-name.png)

![選択項目のアップロードの結果](media/migrate-tool/migrate-tool-upload-selected.png)

### <a name="duplicate-report-names"></a>重複するレポート名

同じ名前のレポートがあり、内容が異なるレポートとわかっている場合は、レポートの **TargetName** を変更する必要があります。 名前を変更するには、移行プランの XML を手動で編集します。

移行ツールを閉じてから変更し、ツールと移行プランを開き直す必要があります。

上の例では、重複するレポートの 1 つが、同じ名前のレポートが存在するために失敗していることが示されています。 移行プランの XML を開くと、次のような内容が表示されます。

```
<ReportMigrationData>
    <PaaSWorkspaceCollectionName>SampleWorkspaceCollection</PaaSWorkspaceCollectionName>
    <PaaSWorkspaceId>4c04147b-d8fc-478b-8dcb-bcf687149823</PaaSWorkspaceId>
    <PaaSReportId>525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe</PaaSReportId>
    <PaaSReportLastImportTime>1/3/2017 2:10:19 PM</PaaSReportLastImportTime>
    <PaaSReportName>cloned</PaaSReportName>
    <IsPushDataset>false</IsPushDataset>
    <IsBoundToOldDataset>false</IsBoundToOldDataset>
    <PbixPath>C:\MigrationData\SampleResourceGroup\SampleWorkspaceCollection\4c04147b-d8fc-478b-8dcb-bcf687149823\cloned-525a8328-b8cc-4f0d-b2cb-c3a9b4ba2efe.pbix</PbixPath>
    <ExportState>Done</ExportState>
    <LastExportStatus>OK</LastExportStatus>
    <SaaSTargetGroupName>SampleMigrate</SaaSTargetGroupName>
    <SaaSTargetGroupId>6da6f072-0135-4e6c-bc92-0886d8aeb79d</SaaSTargetGroupId>
    <SaaSTargetReportName>cloned</SaaSTargetReportName>
    <SaaSImportState>Failed</SaaSImportState>
    <SaaSImportError>Report with the same name already exists</SaaSImportError>
</ReportMigrationData>
```

失敗した項目については、SaaSTargetReportName の名前を変更できます。

```
<SaaSTargetReportName>cloned2</SaaSTargetReportName>
```

移行ツールでプランを開き直し、失敗したレポートをアップロードします。

Power BI に戻ると、レポートとデータセットがアプリ ワークスペースにアップロードされていることを確認できます。

![Power BI サービス内のレポートの一覧](media/migrate-tool/migrate-tool-upload-app-workspace.png)

<a name="upload-local-file"></a>
### <a name="upload-a-local-pbix-file"></a>ローカルの PBIX ファイルをアップロードする

ローカル バージョンの Power BI Desktop ファイルをアップロードすることができます。 ツールを閉じて XML を編集し、**PbixPath** プロパティにローカル PBIX の完全なパスを指定します。

```
<PbixPath>[Full Path to PBIX file]</PbixPath>
```

Xml を編集した後は、移行ツール内でプランを再び開き、レポートをアップロードします。

<a name="directquery-reports"></a>
### <a name="directquery-reports"></a>DirectQuery レポート

DirectQuery レポートの接続文字列を更新する必要があります。 これは、*powerbi.com* 内で行うか、または Power BI Embedded (Paas) からプログラムで接続文字列を照会することができます。 例については、「[Extract DirectQuery connection string from PaaS report](migrate-code-snippets.md#extract-directquery-connection-string-from-power-bi-workspace-collections)」(PaaS レポートから DirectQuery 接続文字列を抽出する) を参照してください。

Power BI サービス内のデータセットの接続文字列を更新し、データ ソースの資格情報を設定できます。 これを行う方法については、次の例を参照してください。

* [Power BI Embedded で DirectQuery の接続文字列を更新する](migrate-code-snippets.md#update-directquery-connection-string-in-power-bi-embedded)
* [Power BI Embedded で DirectQuery の資格情報を設定する](migrate-code-snippets.md#set-directquery-credentials-in-power-bi-embedded)

## <a name="next-steps"></a>次の手順

Power BI ワークスペース コレクションから Power BI Embedded にレポートを移行したので、アプリケーションを更新し、このアプリ ワークスペースにレポートを埋め込むことができます。

詳細については、「[How to migrate Power BI Workspace Collection content to Power BI Embedded](migrate-from-power-bi-workspace-collections.md)」(Power BI Embedded に Power BI ワークスペース コレクション コンテンツを移行する方法) を参照してください。

ご質問は、 [Power BI コミュニティで質問してみてください](http://community.powerbi.com/)。