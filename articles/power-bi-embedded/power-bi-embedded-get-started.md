<properties
   pageTitle="Microsoft Power BI Embedded プレビューを使ってみる"
   description=""
   services="power-bi-embedded"
   documentationCenter=""
   authors="dvana"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="03/29/2016"
   ms.author="derrickv"/>

# Microsoft Power BI Embedded プレビューを使ってみる

**Microsoft Power BI Embedded** は、アプリケーション開発者が自分のアプリケーションに対話型の Power BI レポートを追加できるようにする Azure サービスです。**Power BI Embedded** は既存のアプリケーションで動作します。アプリケーションの設計を変更したりユーザーがログインする方法を変更したりする必要はありません。

Power BI Embedded の詳細については、「[What is Microsoft Power BI Embedded (Microsoft Power BI Embedded について)](power-bi-embedded-what-is-power-bi-embedded.md)」を参照してください。

「[What is Microsoft Power BI Embedded (Microsoft Power BI Embedded について)](power-bi-embedded-what-is-power-bi-embedded.md)」に説明されているように、**Microsoft Power BI Embedded** のリソースは [Azure ARM API](https://msdn.microsoft.com/library/mt712306.aspx) を介してプロビジョニングされます。この場合、プロビジョニング対象のリソースは **Power BI ワークスペース コレクション**です。次のセクションでは、ワークスペース コレクションを作成する方法を紹介します。

![](media\power-bi-embedded-get-started\introduction.png)

## ワークスペース コレクションの作成
**ワークスペース コレクション**はトップレベルの Azure リソースであり、アプリケーションに埋め込まれるコンテンツのコンテナーです。**ワークスペース コレクション**は、次の 2 つの方法で作成できます。

   -	Azure ポータルを使用して手動で作成する
   -	Azure Resource Manager (ARM) API を使用してプログラムで作成する

ここでは、Azure ポータルを使用して**ワークスペース コレクション**を作成する手順について説明します。

   1.	**Azure ポータル** ([http://portal.azure.com](http://portal.azure.com)) を開き、サインインします。

   2.	上のパネルの **[+ 新規]** をクリックします。

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.	**[データ + 分析]** の **[Power BI Embedded]** をクリックします。
   4.	**作成ブレード**で、必要な情報を入力します。**価格**については、「[Power BI Embedded の価格](http://go.microsoft.com/fwlink/?LinkID=760527)」を参照してください。

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. **[作成]** をクリックします。

**ワークスペース コレクション**のプロビジョニングには数分かかります。処理が完了すると、**ワークスペース コレクション ブレード**が表示されます。

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

この**作成ブレード**には、ワークスペースを作成し、コンテンツをデプロイする API を呼び出すために必要な情報が含まれています。

次のセクションでは、**アクセス キー**を使って、API 要求を認証するための**アプリ トークン**を生成する方法について説明します。

<a name="view-access-keys"/>
## Power BI API のアクセス キーの表示

Power BI REST API を呼び出すために必要な最も重要な情報の 1 つが**アクセス キー**です。アクセス キーは、API 要求の認証に使用される**アプリ トークン**を生成するために使用します。**アクセス キー**を表示するには、**[設定] ブレード**の **[アクセス キー]** をクリックします。**アプリ トークン**の詳細については、「[How does app token flow work (アプリ トークンのフローの動作)](power-bi-embedded-get-started-sample.md#key-flow)」を参照してください。

   ![](media\power-bi-embedded-get-started\access-keys.png)

2 つのキーがあることに注意してください。

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

これらのキーをコピーし、アプリケーションに安全に格納します。これらのキーを使用すると**ワークスペース コレクション**のすべての内容にアクセスできるため、これらのキーをパスワードと同じように慎重に扱うことが重要です。

キーは 2 つ表示されますが、一度に使用するキーは 1 つのみです。2 つ目のキーは、サービスへのアクセスを中断することなく定期的にキーを再生成できるようにするために提供されます。

アプリケーション用の Power BI のインスタンスと**アクセス キー**が準備できたら、レポートをアプリにインポートできます。レポートのインポート方法を説明する前に、次のセクションで、アプリに埋め込む Power BI データセットとレポートの作成について説明します。

## アプリに埋め込む Power BI データセットとレポートの作成

アプリケーション用の Power BI のインスタンスと**アクセス キー**が準備できたら、埋め込む Power BI データセットとレポートを作成する必要があります。データセットとレポートは、**Power BI Desktop** を使用して作成できます。[Power BI Desktop は無料で](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)ダウンロードできます。あるいは、[Retail Analysis Sample PBIX](http://go.microsoft.com/fwlink/?LinkID=780547) をダウンロードしてすぐに開始できます。**Power BI Desktop** の使用方法については、「[Getting Started with Power BI Desktop (Power BI Desktop の概要)](https://powerbi.microsoft.com/ja-JP/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop)」を参照してください。

**Power BI Desktop** でデータ ソースに接続するには、データのコピーを **Power BI Desktop** にインポートする方法と、**DirectQuery** を使ってデータ ソースに直接接続する方法があります。

**インポート**する方法と **DirectQuery** を使う方法の違いを次に示します。

|インポート | DirectQuery
|---|---
|テーブル、列、*およびデータ*が **Power BI Desktop** にインポートまたはコピーされます。視覚化の際、**Power BI Desktop** はデータのコピーを照会します。基になるデータに加えられた変更を表示するには、更新する (現在の完全なデータセットをもう一度インポートする) 必要があります。|*テーブルおよび列*のみが **Power BI Desktop** にインポートまたはコピーされます。視覚化の際、**Power BI Desktop** は基になるデータ ソースを照会します。そのため、常に最新のデータが表示されます。

データ ソースに接続する方法の詳細については、「[Connect to a data source (データ ソースへの接続)](power-bi-embedded-connect-datasource.md)」を参照してください。

**Power BI Desktop** で作業を保存すると、PBIX ファイルが作成されます。このファイルには、レポートが含まれます。さらに、データをインポートした場合は、完全なデータセットが PBIX に含まれます。また、**DirectQuery** を使用した場合は、データセット スキーマのみが PBIX に含まれます。[Power BI インポート API](https://msdn.microsoft.com/library/mt711504.aspx) を使用して、PBIX をワークスペースにプログラムでデプロイします。

> [AZURE.NOTE] **Power BI Embedded** には、データセットが指し示すサーバーとデータベースを変更したり、データベースへの接続にデータセットが使用するサービス アカウント資格情報を設定したりするための追加の API が用意されています。「[Post SetAllConnections (Patch Gateway Datasource)](https://msdn.microsoft.com/library/mt711505.aspx)」および「[Patch Gateway Datasource (Patch Gateway Datasource)](https://msdn.microsoft.com/library/mt711498.aspx)」を参照してください。

## 次のステップ
これまでの手順で、ワークスペース コレクションに加え、最初のレポートとデータセットを作成しました。次は、**Power BI Embedded** 用のコードの記述方法について学習しましょう。出発点として、[作業の開始に役立つサンプル](power-bi-embedded-get-started-sample.md)の Web アプリが用意されています。このサンプルでは、次の操作の方法がわかります。

  -	コンテンツをプロビジョニングする
      - ワークスペースを作成する
      - PBIX ファイルをインポートする
      - 接続文字列を更新し、データセットの資格情報を設定する

  -	安全にレポートを埋め込む

## 関連項目
- [Get started with Microsoft Power BI Embedded sample (Microsoft Power BI Embedded のサンプルの使用)](power-bi-embedded-get-started-sample.md)
- [What is Microsoft Power BI Embedded (Microsoft Power BI Embedded について)](power-bi-embedded-what-is-power-bi-embedded.md)
- [Power BI Desktop の概要](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started)
- [Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
- [Power BI Embedded の価格](http://go.microsoft.com/fwlink/?LinkID=760527)

<!---HONumber=AcomDC_0413_2016-->