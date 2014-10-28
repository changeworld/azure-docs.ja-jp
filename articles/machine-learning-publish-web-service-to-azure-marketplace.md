<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Azure Marketplace への Azure ML Web サービスの発行

このドキュメントの内容:

-   [はじめに][はじめに]
-   [発行プロセスの概要][発行プロセスの概要]
-   [Azure Marketplace への発行ガイドライン][Azure Marketplace への発行ガイドライン]
-   [Machine Learning 固有のオプション][Machine Learning 固有のオプション]

<!--Anchors-->

## はじめに

Azure Marketplace は、外部の顧客が使用する有料または無料のサービスとして Azure Machine Learning Web サービスを発行する機能を提供します。このドキュメントでは、プロセスの概要と使用開始のためのガイドラインへのリンクを示します。このプロセスを使用することで、他の開発者が Web サービスをアプリケーション内で使用できるようになります。

## 発行プロセスの概要

Azure ML Web サービスを Azure Marketplace に発行するための手順を次に示します。

1.  Azure ML RRS (要求応答サービス) または BES (バッチ実行サービス) Web サービスを作成して発行します。
2.  Azure の管理ポータルから、サービスを運用環境にデプロイします。
3.  発行済みの Web サービスの URL を使用して、Azure Marketplace に発行します。
4.  発行プロセスの概要は次のとおりです。<http://msdn.microsoft.com/ja-jp/library/azure/hh580725.aspx>
5.  送信すると、サービスの内容が確認され、顧客が購入するにはこのサービスが承認される必要があります。発行プロセスには数営業日かかります。現在、可能な限り短縮できるように取り組んでいます。今後、最新情報を随時お知らせいたします。

## Azure Marketplace への発行ガイドライン

1.  発行者として登録する必要があります。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563872.aspx>
2.  料金プランなど、サービスについての情報を提供する必要があります。無料サービスまたは有料サービスのいずれを提供するかを決定します。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563873.aspx>
3.  有料にするには、銀行や税などの、支払いの情報を提供する必要があります。詳細については、次のリンクを参照してください。<http://msdn.microsoft.com/ja-jp/library/azure/hh563873.aspx>

## Machine Learning 固有のオプション

1.  新しいサービスを作成するには、**[データ サービス]** を選択し、**[新しいデータ サービスの作成]** をクリックします。

    ![Azure Marketplace][Azure Marketplace]

	<br />

2.  **[データ サービス]** タブをクリックし、データ ソースとして **[Web サービス]** をクリックします。

    ![Azure Marketplace][1]

3.  **[サービス URL]** には、Web サービスの URL を指定します。

    -   Azure ML Studio の左側のメニューの **[Web サービス]** をクリックします。
    -   Marketplace で発行する Web サービスをクリックします。
    -   **[ダッシュボード]** ページで、RRS サービスの **[API ヘルプ ページ]** をクリックします。
    -   OData エンドポイント アドレスをコピーします。

	<br />

4.  認証するには、**[認証スキーム]** として **[ヘッダー]** を選択します。

    -   **[ヘッダー名]** に「Authorization」と入力します。
    -   **[ヘッダー値]** には次のように入力します。
        -   ML Studio の Web サービスの **[ダッシュボード]** ページで、**API キー**をコピーします。
        -   **[ヘッダー値]** に、「Bearer」(引用符なしで)、スペースの順に入力し、API キーを貼り付けます。
    -   **[このサービスは OData]** チェック ボックスをオンにします。

	<br />

5.  カテゴリは次のとおりです。
    -   **[機械学習]** チェック ボックスがオンになっていることを確認します。

  [はじめに]: #introduction
  [発行プロセスの概要]: #overview-of-the-publishing-process
  [Azure Marketplace への発行ガイドライン]: #guidelines-for-publishing-to-azure-marketplace
  [Machine Learning 固有のオプション]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
