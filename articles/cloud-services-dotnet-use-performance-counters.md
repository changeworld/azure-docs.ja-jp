<properties linkid="dev-net-commons-tasks-profiling" urlDisplayName="Performance Profiling" pageTitle="Use Performance Counters in Azure (.NET)" metaKeywords="Azure performance counters, Azure performance profiling, Azure performance counters C#, Azure performance profiling C#" description="Learn how to enable and collect data from performance counters in Azure applications. " metaCanonical="" services="cloud-services" documentationCenter=".NET" title="Using performance counters in Azure" authors="ryanwi" solutions="" manager="timlt" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="ryanwi"></tags>

# Azure でのパフォーマンス カウンターの使用

Azure アプリケーションでパフォーマンス カウンターを使用してデータを収集すると、システムのボトルネックを特定したり、システムやアプリケーションのパフォーマンスを調整したりするのに役立ちます。Windows Server 2008、Windows Server 2012、IIS、ASP.NET で使用可能なパフォーマンス カウンターは、Azure アプリケーションの状態を把握するために収集して使用できます。

Azure SDK 2.0 以降を使用して Visual Studio 2012 または Visual Studio 2013 内でデプロイ前または実行時にパフォーマンス カウンターを構成することができます。詳細については、「[Azure 診断の構成][Azure 診断の構成]」を参照してください。アプリケーションでパフォーマンス カウンターを手動で構成する方法の詳細については、「[パフォーマンス カウンターを構成する方法][パフォーマンス カウンターを構成する方法]」を参照してください。

ログやトレース方法を作成する方法と、診断機能やその他の手法で問題のトラブルシューティングを行う方法の詳細なガイダンスについては、「[Azure アプリケーション開発のトラブルシューティングのベスト プラクティス][Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]」を参照してください。

## <a name="nextsteps"> </a> 次のステップ

より複雑なトラブルシューティング シナリオを実装する方法については、次のリンク先を参照してください。

-   [クラウド サービスのパフォーマンスのテスト][クラウド サービスのパフォーマンスのテスト]
-   [Troubleshooting Best Practices for Developing Azure Applications (Azure アプリケーション開発時のトラブルシューティングのベスト プラクティス)][Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]
-   [クラウド サービスの監視方法][クラウド サービスの監視方法]
-   [オートスケーリング アプリケーション ブロックの使用方法][オートスケーリング アプリケーション ブロックの使用方法]
-   [伸縮性と弾力性があるクラウド アプリケーションの作成][伸縮性と弾力性があるクラウド アプリケーションの作成]

## <a name="additional"> </a> その他のリソース

-   [Enabling Diagnostics in Azure (Azure の診断機能)][Enabling Diagnostics in Azure (Azure の診断機能)]
-   [Windows Azure 診断を使用したログ データの収集][Windows Azure 診断を使用したログ データの収集]
-   [クラウド サービスのデバッグ][クラウド サービスのデバッグ]

  [Azure 診断の構成]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn186185.aspx
  [パフォーマンス カウンターを構成する方法]: http://msdn.microsoft.com/ja-jp/library/azure/dn535595.aspx
  [Azure アプリケーション開発のトラブルシューティングのベスト プラクティス]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh771389.aspx
  [クラウド サービスのパフォーマンスのテスト]: http://msdn.microsoft.com/ja-jp/library/azure/hh369930.aspx
  [クラウド サービスの監視方法]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-how-to-monitor/
  [オートスケーリング アプリケーション ブロックの使用方法]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-dotnet-autoscaling-application-block/
  [伸縮性と弾力性があるクラウド アプリケーションの作成]: http://msdn.microsoft.com/ja-jp/library/hh680949(PandP.50).aspx
  [Enabling Diagnostics in Azure (Azure の診断機能)]: http://azure.microsoft.com/ja-jp/documentation/articles/cloud-services-dotnet-diagnostics/
  [Windows Azure 診断を使用したログ データの収集]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg433048.aspx
  [クラウド サービスのデバッグ]: http://msdn.microsoft.com/ja-jp/library/windowsazure/ee405479.aspx
