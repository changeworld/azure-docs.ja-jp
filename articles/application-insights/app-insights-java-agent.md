<properties 
	pageTitle="Java Web アプリでの依存関係、例外、および実行時間の監視" 
	description="Extended monitoring of your Java website with Application Insights (Application Insights を使用した Java Web サイトの監視の拡張)" 
	services="application-insights" 
    documentationCenter="java"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Java Web アプリでの依存関係、例外、および実行時間の監視

*Application Insights はプレビュー段階です。*

[Java Web アプリを Application Insights でインストルメント化][java]した場合、Java エージェントを使用して、コードを変更することなく、詳細な分析を行うことができます。

* **リモートの依存関係:** [JDBC](http://docs.oracle.com/javase/7/docs/technotes/guides/jdbc/) ドライバー (MySQL、SQL Server、PostgreSQL、SQLite など) を通じてアプリケーションで行われた呼び出しに関するデータ。
* **例外の検出:** コードで処理される例外に関するデータ。
* **メソッドの実行時間:** 特定のメソッドの実行にかかる時間に関するデータ。

Java エージェントを使用するには、これをサーバーにインストールします。Web アプリを [Application Insights Java SDK][java] を使用してインストルメント化する必要があります。

## Jave 用の Application Insights エージェントのインストール

1. Java サーバーを実行しているコンピューターで[エージェントをダウンロード](http://go.microsoft.com/fwlink/?LinkId=618633)します。
2. アプリケーション サーバーのスタートアップ スクリプトを編集し、次の JVM を追加します。

    `javaagent:`*エージェント JAR ファイルへの完全パス*

    たとえば、Linux マシンの Tomcat で以下を実行します。

    `export JAVA_OPTS="$JAVA_OPTS -javaagent:<full path to agent JAR file>"`


3. アプリケーション サーバーを再起動します。

## エージェントの構成

`AI-Agent.xml` という名前のファイルを作成し、エージェントの JAR ファイルの場所と同じフォルダーに配置します。

xml ファイルの内容を設定します。次の例を編集して、必要に応じて、機能を含めるか省略します。

```XML

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsightsAgent>
      <Instrumentation>
        
        <!-- Collect remote dependency data -->
        <BuiltIn enabled="true"/>

        <!-- Collect data about caught exceptions 
             and method execution times -->

        <Class name="com.myCompany.MyClass">
           <Method name="methodOne" 
               reportCaughtExceptions="true"
               reportExecutionTime="true"
               />

           <!-- Report on the particular signature
                void methodTwo(String, int) -->
           <Method name="methodTwo"
              reportExecutionTime="true"
              signature="(Ljava/lang/String:I)V" />
        </Class>
        
      </Instrumentation>
    </ApplicationInsightsAgent>

```

レポートの例外や、個々のメソッドのメソッド タイミングを有効にする必要があります。

既定では、`reportExecutionTime` は true、`reportCaughtExceptions` は false です。

## データの表示

Application Insights リソースで、集計されたリモートの依存関係やメソッドの実行時間が [[パフォーマンス] タイルに][metrics]表示されます。

依存関係、例外、メソッドのレポートの個々のインスタンスを検索するには、[[検索]][diagnostic] を開きます。

## 疑問がある場合 問題が発生した場合

[Java のトラブルシューティング](app-insights-java-troubleshoot.md)



<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[eclipse]: app-insights-java-eclipse.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[metrics]: app-insights-metrics-explorer.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=July15_HO3-->