<properties 
	pageTitle="Application Insights によって使用される IP アドレス | Microsoft Azure"
	description="Application Insights で必要なサーバー ファイアウォール例外" 
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/24/2016" 
	ms.author="awills"/>
 
# Application Insights によって使用される IP アドレス

[Visual Studio Application Insights](app-insights-overview.md) サービスは、多くの IP アドレスを使用します。監視しているアプリがファイアウォールの背後でホストされている場合は、これらのアドレスを確認する必要があります。

> [AZURE.NOTE] これらは静的アドレスですが、しばしば変更の必要が生じることがあります。


## 送信ポート

Application Insights SDK や Status Monitor がポータルにデータを送信できるように、サーバーのファイアウォールでいくつかの送信ポートを開く必要があります。

|目的|URL|IP|ポート
|---|---|---|---
| テレメトリ|dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com| 40\.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221|使用します
|LiveStream|rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |可変|使用します



+ Status Monitor の構成 - 変更を加える場合にのみ必要です。
 -	`management.core.windows.net:443`
 -	`management.azure.com:443`
 -	`login.windows.net:443`
 -	`login.microsoftonline.com:443`
 -	`secure.aadcdn.microsoftonline-p.com:443`
 -	`auth.gfx.ms:443`
 -	`login.live.com:443`
+ Status Monitor のインストール:
 +	`packages.nuget.org:443`

この一覧は時々変更される場合があります。

## 可用性テスト

これは[可用性 Web テスト](app-insights-monitor-web-app-availability.md)の実行元のアドレスの一覧です。アプリで Web テストを実行しようとするが、Web サーバーが特定のクライアントの処理に制限されている場合は、可用性テスト サーバーからの着信トラフィックを許可する必要があります。

これらのアドレスからの着信トラフィック用にポート 80 (http) と 443 (https) を開きます。

```

157.55.14.43
157.55.14.44
157.55.14.47
157.55.14.49
157.55.14.50
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.64
157.55.14.65
202.89.228.57
202.89.228.67
202.89.228.68
202.89.228.69
207.46.14.51
207.46.14.52
207.46.14.55
207.46.14.56
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.63
207.46.14.64
207.46.14.65
207.46.14.67
207.46.14.68
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
207.46.56.67
207.46.71.37
207.46.71.38
207.46.71.51
207.46.71.52
207.46.71.54
207.46.71.55
207.46.71.57
207.46.71.58
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.157
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.162
207.46.98.171
207.46.98.172
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.61
213.199.178.63
213.199.178.64
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.55.244.15
65.55.244.16
65.55.244.17
65.55.244.18
65.55.244.37
65.55.244.40
65.55.244.42
65.55.244.44
65.55.244.46
65.55.244.47
65.55.82.81
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38

```  

## データ アクセス API



|URI|IP|ポート
|---|---|---
|api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io|13\.82.26.252<br/>40.76.213.73|80,443
|dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com|13\.82.24.149<br/>40.114.82.10|80,443





 

<!---HONumber=AcomDC_0824_2016-->