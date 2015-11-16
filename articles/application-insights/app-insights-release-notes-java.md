<properties
	pageTitle="Application Insights for Java のリリース ノート"
	description="Java SDK 用の最新の更新プログラム。"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>
<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/21/2015"
	ms.author="awills"/>

# Java 向けの Application Insights SDK のリリース ノート

[Application Insights SDK for Java](app-insights-java-get-started.md) では、実行中のアプリに関するテレメトリを [Application Insights](http://azure.microsoft.com/services/application-insights/) に送信し、その使用状況とパフォーマンスを分析できます。

#### アプリケーションに SDK をインストールするには

「[SDK for Java を使ってみる](app-insights-java-get-started.md)」をご覧ください。

#### 最新の SDK にアップグレードするには

アップグレード後に、ApplicationInsights.xml に対して行ったカスタマイズをもう一度マージする必要があります。このファイルをコピーし、新しいファイルと比較します。

*Maven か Gradle を使用している場合*

1. pom.xml か build.gradle で特定のバージョン番号を指定した場合は、それを更新します。
2. プロジェクトの依存関係を更新します。

*それ以外の場合*

* 最新バージョンの [Azure Libraries for Java](http://dl.msopentech.com/lib/PackageForWindowsAzureLibrariesForJava.html) をダウンロードして、旧バージョンと置き換えます。

新旧の ApplicationInsights.xml を比較します。確認できる変更の多くは、モジュールの追加と削除によって発生しています。以前のカスタマイズをもう一度設定します。

## バージョン 1.0.2
- コード内にインストルメンテーション キーを明示的に指定する場合に、そのインストルメンテーション キーが構成ファイルに指定されたインストルメンテーション キーを使用してオーバーライドされるのを防止します。
- すべての正常な HTTP 状態コードを処理し、関連する HTTP 要求を「成功した」としてレポートします。
- ConfigurationFileLocator によってスローされたすべての例外を処理します。


## バージョン 1.0.1
- [Java エージェント](app-insights-java-agent.md)は、次の項目に関する依存関係情報を収集します。
	- HttpClient、OkHttp、RestTemplate (Spring) 経由で行われた HTTP 呼び出し。
	- クライアント経由で行われた Radius への呼び出し。構成可能なしきい値が渡されると、SDK は、呼び出し引数もフェッチします。
	- Oracle DB クライアントと Apache Derby DB クライアントを使用して行われた JDBC 呼び出し。
	- 準備されたステートメントに対する ‘executeBatch’ クエリ タイプのサポート。SDK はステートメントをバッチ番号付きで表示します。
	- JDBC クライアントをサポートするクエリ プランの提供 (MySql、PostgreSql)。クエリ プランは構成できるしきい値が交差した場合のみフェッチされます。

## バージョン 1.0.0
- CollectD 用の Application Insights ライター プラグインのサポートを追加しました。
- Application Insights Java エージェントのサポートを追加しました。
- HttpClient バージョン 4.2 以降のサポートでの互換性の問題を修正しました。

## バージョン 0.9.6
- Java SDK が servlet v2.5 および HttpClient pre-v4.3 に対応するようになりました。
- Java EE インターセプターのサポートを追加しました。
- Logback アペンダーから重複する依存関係を削除しました。

## バージョン 0.9.5  

- Cookie の解析エラーによりカスタム イベントがユーザーやセッションに関連付けられなくなる問題を修正しました。  
- ApplicationInsights.xml 構成ファイルの場所を解決するためのロジックが改善されました。
- 匿名ユーザーとセッションの Cookie は、サーバー側では生成されません。Web アプリのユーザーおよびセッションの追跡を実装するために JavaScript SDK によるインストルメンテーションが必要になりました。JavaScript SDK の Cookie は引き続き適用されます。この変更により、ユーザー数とセッション数の大幅な再計算が発生する場合があることに注意してください。これは、ユーザーが開始したセッションのみがカウントされるようになったためです。

## バージョン 0.9.4

- 32 ビット Windows コンピューターからパフォーマンス カウンターを収集できます。
- 新しい ```trackDependency``` API メソッドを利用し、依存関係を手動で追跡できます。
- 報告された項目に ```SyntheticSource``` プロパティを追加し、利用統計情報項目に合成のタグを付けることができます。

<!---HONumber=Nov15_HO2-->