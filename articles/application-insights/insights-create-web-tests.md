<properties 
	pageTitle="Web テストの作成方法" 
	description="Azure で Web テストを作成する方法について説明します。" 
	services="application-insights" 
	authors="alancameronwills" 
	manager="kamrani"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="2015-01-17" 
	ms.author="awills"/>

# Microsoft Azure Web サイトの Web テスト
Azure Web サイトは引き続き実行されているでしょうか。正常に応答しており、十分速いでしょうか。Web テストを構成し、Web サイトを定期的にテストしてください。サイトがダウンした場合や、応答が遅いか不適切な場合は、電子メール アラートが配信されます。時間の経過に伴う、サイトの可用性と応答時間を示すグラフが表示されます。  

*他の Web サイトもテストが必要ですか。Azure 以外の Web アプリケーションについては、[Application Insights の Web テスト][の可用性]を使用します。*

![Browse Hub](./media/insights-create-web-tests/Inisghts_WebTestBlade.png)

基本プランまたは標準プランを使用している任意の Azure Web サイトに対して、可用性の監視を設定できます。最大 3 つの Web テストを作成し、最大 3 つの地理的な場所からこれらの各テストを実行することができます。どのような方法でも Web サイトを変更する必要はありません。

デプロイ中、または計画された停止期間は、Web テストを一時停止することもできます。その結果、全体の可用性は影響を受けません。全体的な可用性は、選択したさまざまな場所を含め、すべての Web テストを対象にして計算されます。

## Web テストを設定する方法
1. Web テストを構成するには、最初に、使用中の Web サイトが**基本**と**標準**のどちらかであることを確認します。
2. 次に、**[Web サイト]** ブレードの **[Web テスト]** パーツをクリックします。  
    ![Configure Web Tests](./media/insights-create-web-tests/Insights_ConfigurePart.png)
3. **[Web テストの作成]** ブレードで、Web テストに名前を付け、テストの実行対象となる URL を指定します。  
    ![Create Web Test](./media/insights-create-web-tests/Insights_CreateTest.png)
4. 次に、8 つの場所のうち 3 つを選択します。
5. HTTP ステータス コードのチェックや、サイト自体に含まれている文字列など、成功の条件を指定します。
6. その後、感度や電子メール送信先などを含め、アラートの設定を選択します。  
    ![Alerts](./media/insights-create-web-tests/Inisghts_AlertCreation.png)
    - 高い感度を指定すると、ただ 1 つの場所でテストの失敗が検出されるたびにアラートを作成します。
    - 中程度の感度を指定すると、10 分以内に少なくとも半分の場所で障害が確認される必要があります。
    - 低い感度を指定すると、15 分以内にすべての場所でテストが失敗することが必要です。

完了した時点で、**[作成]** をクリックします。Web テストを作成した後、指定した場所から 5 分ごとにテストが実行されます。そのため、最初にデータが表示されるまでに、多少の時間がかかることがあります。

### 場所について
ユーザーが世界中のさまざまな部分からサイトにアクセスするのと同じ方法で、これらの場所から Web サイトに要求を送信します。サイトが米国で使用できず、ヨーロッパで引き続き使用できる場合は、サーバーではなくネットワークに問題があることがわかります。

### 成功の条件の使用
通常、HTTP ステータス コードが 200 に等しいことをテストします。この値は、サーバーが URI を認識し、ページを返したことを意味します。

コンテンツの一致文字列としてワイルドカードを使用することはできませんが、任意のプレーンテキストをテストすることができます。

## 困ったことに - サイトがダウンした場合
Web テストの結果、成功の条件を満たさなかった場合は、そのテストは失敗したテストというマークを付けられ、Web サイトの全体的な可用性が低下します。失敗したテスト (また、成功したテスト) は、特定の Web テスト ブレードにある散布図で表示されます。  

![Failed Test](./media/insights-create-web-tests/Insights_FailedWebTest.png)

失敗したテストを分析して、失敗の理由を決定することができます。失敗した Web テストを表示し、Visual Studio の Web テスト結果ファイルをダウンロードして開き、テストが失敗した理由を分析して理解します。

![Open in VS](./media/insights-create-web-tests/Insights_OpenInVS.png)

[availability]: ../app-insights-monitor-web-app-availability/

<!--HONumber=46--> 
 