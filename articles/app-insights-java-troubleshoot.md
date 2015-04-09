<properties 
	pageTitle="Java Web プロジェクトでの Application Insights のトラブルシューティング" 
	description="トラブルシューティング ガイド、質問と回答" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="awills"/>
 
# Java 用 Application Insights のトラブルシューティングおよび Q&A

[Java で Visual Studio Application Insights][java] を使用するうえでの疑問または問題はありませんか?ここでは、いくつかのヒントを紹介します。


## ビルド エラー

*Eclipse で、Maven または Gradle を使用して Application Insights SDK を追加すると、ビルドまたはチェックサムの検証エラーが発生します。*

* 依存 <version> 要素にワイルドカード文字を含むパターン (<version>[0.9,)</version> など) を使用している場合、代わりに特定のバージョン (<version>0.9.1</version> など) を指定してみてください。

## データが表示されない 

*Application Insights が正常に追加された後でアプリを実行したところ、ポータルにデータが表示されません。*

* 少し待ってから、[最新の情報に更新] をクリックします。現時点では、自動での更新は行われません。
* プロジェクトのリソース フォルダーにある ApplicationInsights.xml ファイル内で、インストルメンテーション キーが定義されていることを確認してください。
* この xml ファイルに `<DisableTelemetry>true</DisableTelemetry>` ノードが存在しないことを確認してください。
* ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。
* Microsoft Azure のスタート ボードで、サービス状態マップを確認してください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
* プロジェクトのリソース フォルダーにある ApplicationInsights.xml ファイル内で、ルート ノードの下に <SDKLogger /> 要素を追加して IDE コンソール ウィンドウへのログを有効にし、[Error] から始まるエントリを調べます。


## 使用状況データがない

*要求と応答時間についてのデータは表示されますが、ページ ビュー、ブラウザー、またはユーザーについてのデータが表示されません。*

サーバーからテレメトリを送信するためのアプリ設定は正常に行われています。次の手順は、[Web ブラウザーからテレメトリを送信するように Web ページを設定する][usage]ことです。

または、使用するクライアントが[電話やその他のデバイス][platforms]内のアプリの場合、そのアプリからテレメトリを送信できます。 

クライアントおよびサーバー テレメトリの両方の設定に、同じインストルメンテーション キーを使用します。データは同じ Application Insights リソース内に表示され、クライアントとサーバーのイベントを関連付けることができるようになります。



## テレメトリの無効化

*テレメトリの収集を無効にする方法を教えてください。*

コード内で以下のように指定します。

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);


**または** 

プロジェクトのリソース フォルダーにある ApplicationInsights.xml を更新します。ルート ノードの下に次の内容を追加します。

    <DisableTelemetry>true</DisableTelemetry>

XML メソッドを使用するうえで、値を変更した場合はアプリケーションを再起動する必要があります。

## ターゲットの変更

*プロジェクトからデータを送信する Azure リソースの変更方法を教えてください。*

* [新しいリソースのインストルメンテーション キーを取得します。][java]
* Eclipse の Azure Toolkit を使用してプロジェクトに Application Insights を追加した場合、Web プロジェクトを右クリックし、**[Azure]**、**[Configure Application Insights]** の順に選択して、キーを変更します。
* それ以外の場合は、プロジェクトのリソース フォルダーにある ApplicationInsights.xml 内のキーを更新します。


## Azure のスタート画面

*[今、画面に Azure ポータル](http://portal.azure.com)を表示しています。このマップから、自分のアプリについて何か情報が得られるのでしょうか?*

いいえ、そのマップは世界中の Azure サーバーの正常性を表しています。

*Azure のスタート画面 (ホーム画面) から、アプリに関するデータを見つける方法を教えてください。*

[Application Insights 用にアプリを設定][java]してあると仮定します。[参照] をクリックし、[Application Insights] を選択して、アプリ用に作成したアプリ リソースを選択します。次からその場所にすばやくアクセスできるように、スタート画面にアプリをピン留めすることができます。

## イントラネット サーバー

*イントラネット上のサーバーを監視できますか?*

はい、お使いのサーバーがパブリック インターネットを介して Application Insights ポータルにテレメトリを送信できるなら、可能です。 

ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。

## データの保持 

*ポータルでのデータ保持期間はどのくらいですか?セキュリティで保護されていますか?*

[データの保持とプライバシー][data]に関するページを参照してください。

## 次のステップ

*Java サーバー アプリ用に Application Insights を設定しました。ほかには何ができるか教えてください。*

* [Web ページの可用性の監視][availability]
* [Web ページの使用状況の監視][usage]
* [使用状況の追跡およびデバイス アプリでの問題の診断][platforms]
* [アプリの使用状況を追跡するためのコードを記述する][track]
* [診断ログのキャプチャ][javalogs]


## 問い合わせ

* [スタック オーバーフロー](http://stackoverflow.com/questions/tagged/ms-application-insights)

[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]





<!--HONumber=49-->