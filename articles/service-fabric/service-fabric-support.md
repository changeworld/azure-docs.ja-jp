---
title: Azure Service Fabric のサポート オプションを学習する | Microsoft Docs
description: サポートされている Azure Service Fabric クラスターのバージョンと、サポート チケットを提出するためのリンク
services: service-fabric
documentationcenter: .net
author: pkcsf
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/13/2018
ms.author: pkc
ms.openlocfilehash: 596e71be75453874492aac15d91cb6153c2076f5
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2018
ms.locfileid: "39112892"
---
# <a name="azure-service-fabric-support-options"></a>Azure Service Fabric のサポート オプション

アプリケーション ワークロードを実行している Service Fabric クラスターに適切なサポートを提供するために、Microsoft では、さまざまなオプションを用意しました。 必要なサポートのレベルや問題の重大度に応じて、適切なオプションを選ぶことができます。 

## <a name="report-production-issues-or-request-paid-support-for-azure"></a>運用上の問題を報告するか、Azure の有料サポートを要求する

Azure にデプロイされた Service Fabric クラスター上の問題を報告する場合は、[Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) または [Microsoft サポート ポータル](http://support.microsoft.com/oas/default.aspx?prid=16146)でサポートのチケットを開きます。

各項目の詳細情報
 
- [Azure に関する Microsoft からのサポート](https://azure.microsoft.com/support/plans/?b=16.44)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

<a id="getlivesitesupportonprem"></a>

## <a name="report-production-issues-or-request-paid-support-for-standalone-service-fabric-clusters"></a>運用上の問題を報告するか、スタンドアロン Service Fabric クラスターの有料サポートを要求する

オンプレミスまたは他のクラウドにデプロイされた Service Fabric クラスター上の問題を報告する場合は、[Microsoft サポート ポータル](http://support.microsoft.com/oas/default.aspx?prid=16146)でサポートのチケットを開きます。

各項目の詳細情報

- [オンプレミスに関する Microsoft によるプロフェッショナル サポート](https://support.microsoft.com/en-us/gp/offerprophone?wa=wsignin1.0)。
- [Microsoft プレミア サポート](https://support.microsoft.com/en-us/premier)。

## <a name="report-azure-service-fabric-issues"></a>Azure Service Fabric の問題を報告する 
Microsoft では、Service Fabric の問題を報告するための GitHub リポジトリを設置しました。  また、次のフォーラムも積極的に監視しています。

### <a name="github-repo"></a>GitHub のリポジトリ 
Azure Service Fabric の問題は、[Service-Fabric-issues git リポジトリ](https://github.com/Azure/service-fabric-issues)で報告してください。 このリポジトリは、Azure Service Fabric に関する問題の報告と追跡、簡単な機能に関する要求を行うためのものです。 **稼働中のサイトの問題の報告にはこれを使用しないでください**。

### <a name="stackoverflow-and-msdn-forums"></a>StackOverflow と MSDN フォーラム
[StackOverflow の Service Fabric タグ][stackoverflow]と [MSDN の Service Fabric フォーラム][msdn-forum]は、プラットフォームのしくみやそのプラットフォームで特定のタスクを実行する方法について質問する場合に最適です。

### <a name="azure-feedback-forum"></a>Azure フィードバック フォーラム
[Service Fabric 用の Azure フィードバック フォーラム][uservoice-forum]は、ユーザーが製品について持っている多くの機能に関するアイデアを提起するのに最適な場所です。Microsoft は、最も人気のあるリクエストが中長期的な計画に含まれるように再検討します。 Microsoft では、コミュニティ内で自身の提案への支持を集めることをお勧めしています。


## <a name="supported-service-fabric-versions"></a>サポートされている Service Fabric のバージョン

サポートされている Service Fabric のバージョンが常にクラスターで実行されているようにします。 Microsoft が Service Fabric の新バージョン リリースをアナウンスした日から最短で 60 日後には、以前のバージョンがサポート期間の終了として指定されます。 新バージョンのリリースは、[Service Fabric チーム ブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)でお知らせします。

サポートされている Service Fabric のバージョンをクラスターで実行し続ける方法の詳細については、次のドキュメントを参照してください。

- [Azure クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade.md)
- [スタンドアロンの Windows Server クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)
 
サポートされている Service Fabric のバージョンとそのサポートの終了日の一覧を次に示します。

| **クラスター内の Service Fabric ランタイム** | **クラスター バージョンから直接アップグレードできます。** |**互換性のある SDK / NuGet パッケージのバージョン** | **サポートの終了日** |
| --- | --- |--- | --- |
| 5.3.121 より前のすべてのクラスター バージョン | 5.1.158* |バージョン 2.3 以前 |2017 年 1 月 20 日 |
| 5.3.* | 5.1.158.* |バージョン 2.3 以前 |2017 年 2 月 24 日 |
| 5.4.* | 5.1.158.* |バージョン 2.4 以前 |2017 年 5 月 10 日       |
| 5.5.* | 5.4.164.* |バージョン 2.5 以前 |2017 年 8 月 10 日    |
| 5.6.* | 5.4.164.* |バージョン 2.6 以前 |2017 年 10 月 13 日   |
| 5.7.* | 5.4.164.* |バージョン 2.7 以前 |2017 年 12 月 15 日  |
| 6.0.* | 5.6.205.* |バージョン 2.8 以前 |2018 年 3 月 30 日     | 
| 6.1.* | 5.7.221.* |バージョン 3.0 以前 |2018 年 7 月 15 日      |
| 6.2.* | 6.0.232.* |バージョン 3.1 以前 |2018 年 9 月 15 日 |
| 6.3.* | 6.1.480.* |バージョン 3.2 以前 |最新バージョンのため、終了日はありません |

## <a name="service-fabric-preview-versions---unsupported-for-production-use"></a>Service Fabric プレビュー バージョン - 運用環境で使用がサポートされていません。
時折、フィードバックをいただきたい重要な機能を持つバージョンをプレビューとしてリリースします。 これらのプレビュー バージョンは、テスト目的でのみ使用するようにしてください。 運用環境のクラスターは常に、サポートされていて、安定した Service Fabric のバージョンを実行する必要があります。 プレビュー バージョンのメジャーおよびマイナーのバージョン番号は常に 255 で始まります。 たとえば、Service Fabric 255.255.5703.949 のバージョンを参照すると、このリリース バージョンはテスト クラスター内のみで使用されるべきであり、プレビュー段階です。 これらのプレビュー リリースは、[Service Fabric チームのブログ](https://blogs.msdn.microsoft.com/azureservicefabric)でも発表され、含まれる機能の詳細情報があります。

これらのプレビュー リリースの有償サポート オプションはありません。 [Azure Service Fabric の問題を報告する](https://docs.microsoft.com/azure/service-fabric/service-fabric-support#report-azure-service-fabric-issues)に含まれるオプションのいずれかを使用して、質問またはフィードバックを提供してください。

## <a name="next-steps"></a>次の手順

- [Azure クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade.md)
- [スタンドアロンの Windows Server クラスターで Service Fabric のバージョンをアップグレードする](service-fabric-cluster-upgrade-windows-server.md)

<!--references-->
[msdn-forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureServiceFabric
[stackoverflow]: http://stackoverflow.com/questions/tagged/azure-service-fabric
[uservoice-forum]: https://feedback.azure.com/forums/293901-service-fabric
[acom-docs]: http://aka.ms/servicefabricdocs
[sample-repos]: http://aka.ms/servicefabricsamples
