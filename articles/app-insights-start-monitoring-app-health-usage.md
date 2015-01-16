<properties title="Application Insights" pageTitle="Application Insights - アプリケーションの状態と利用状況の監視を開始する" description="内部設置型または Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />

# Application Insights - アプリケーションの状態と利用状況の監視を開始する

*Application Insights はプレビュー段階です。*

Application Insights を使用すると、ライブ アプリケーションの以下の事柄について監視できます。

* **可用性** - 世界中から数分ごとにご使用の URL についてテストします。
* **パフォーマンス**   - パフォーマンスに関する問題と例外を検出して診断します。
* **利用状況** - ご使用のアプリケーションを使っているユーザーの実行内容を把握し、それらのユーザーにとってより良いアプリケーションとなるようにします。

別の方法については、「[Application Insights を開始する」][start]を参照してください。

## <a name="add"></a>アプリケーションの正常性と利用状況の監視

[Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (またはそれ以降) と [Microsoft Azure](http://azure.com) のアカウントが必要です。

### 新しいプロジェクトの場合

Visual Studio 2013 に新しいプロジェクトを作成するとき、Application Insights が選択されていることを確認してください。 


![Create an ASP.NET project](./media/appinsights/appinsights-01-vsnewp1.png)

初めての場合には、Microsoft Azure プレビュー版にログインまたはサインインするように求められます (Visual Studio Online アカウントとは異なります)。

Azure リソースの名前をプロジェクトの名前とは別にする場合、または同じグループの別のプロジェクトとしてリソースを表示する場合には、**[設定の構成]** を使用します。 

*Application Insights オプションはありませんか。Visual Studio 2013 Update 3 を使用しているか、[拡張機能と更新プログラム] で Application Insights Tools が有効になっているか、および作成しているのが Web、Windows ストア、Windows Phone のどのプロジェクトであるかを確認します。*

### 既存のプロジェクトの場合

ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。

![Choose Add Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Web 利用状況分析を設定するものの最初にデータの一部を参照する場合には、複数の手順が関係します。*


### <a name="run"></a>2. プロジェクトを実行する

F5 を使用してアプリケーションを実行して、試します。別のページが開きます。

Visual Studio で、受け取ったイベント数を確認できます。

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. モニター データを表示する

プロジェクトから Application Insights を開きます。

![Right-click your project and open the Azure portal](./media/appinsights/appinsights-04-openPortal.png)


**[アプリケーションの使用状況]** タイル内でデータを探します。最初、1 つまたは 2 つのポイントだけが表示されます。次に例を示します。

![Click through to more data](./media/appinsights/appinsights-41firstHealth.png)

いずれかのタイルをクリックして、詳細を表示します。レポート内の表示内容を変更できます。[アプリケーションの使用状況レポートの構成方法の詳細について参照してください。][perf]


### <a name="deploy"></a>4. アプリケーションをデプロイする

アプリケーションをデプロイし、データ累積を確認します。



ライブ アプリケーションになった後、[Web テストを設定][availability]し、引き続きライブ状態であることを確認します。 

![Example application monitor in Application Insights](./media/appinsights/appinsights-00-appblade.png)

### ポータルでアプリケーションの名前を変更しますか

プロジェクトの利用統計情報の送信先となるリソースを変更できます。 

(「リソース」とは、結果が表示される指定の Application Insights ブレードのことです。複数のリソースを 1 つのグループにまとめることができます。たとえば、複数のプロジェクトが 1 つのビジネス アプリケーションの構成要素となっている場合などです)。 

ソリューション エクスプローラーで ApplicationInsights.config を右クリックし、**[Application Insights の更新]** を選択します。これによりウィザードが開き、別の既存のリソースを選択するか、または新しいリソースを作成できます。

その後、ポータルに戻り、古いリソースを削除します。

## <a name="video"></a>ビデオ

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>次のステップ

[Web アプリケーションの利用状況を追跡する][usage]

[Web アプリケーションのパフォーマンスを監視する][perf]

[診断ログを取得して検索する][diagnostic]

[トラブルシューティング][qna]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]


