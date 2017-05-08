---
title: "Azure Service Fabric について | Microsoft Docs"
description: "Azure Service Fabric の概要とファースト ステップ ガイド。 Service Fabric についてと、マイクロサービスで構成されたスケーラブルかつ信頼性が高く、管理しやすいアプリケーションを開発する方法について説明します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/18/2017
ms.author: ryanwi
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 94b697d01d31463d1744d7e8d4f1169a2f366242
ms.lasthandoff: 04/19/2017


---
# <a name="so-you-want-to-learn-about-service-fabric"></a>Service Fabric に興味をお持ちでしょうか。
この入門書では、Service Fabric の簡単な概要、中核となる概念と用語の紹介、ファースト ステップ ガイド、および Service fabric の各領域の概要を提供します。  この入門書には、包括的な内容の一覧が含まれていませんが、Service Fabric の各領域の概要とファースト ステップ ガイドの記事へのリンクを掲載しています。 

## <a name="the-five-minute-overview"></a>5 分間の概説
Azure Service Fabric は、拡張性と信頼性に優れたマイクロサービスのパッケージ化とデプロイ、管理を簡単に行うことができる分散システム プラットフォームです。  Service Fabric は、クラウド アプリケーションの開発と管理における重要な課題に対処します。 開発者と管理者は Service Fabric を使用することで、複雑なインフラストラクチャの問題の解決を回避できます。  そのうえ、スケーラブルで信頼性が高く、管理可能であることがわかっているため、ミッション クリティカルな要求の厳しいワークロードの実装に集中できます。 Service Fabric は、これらのエンタープライズ クラスの Tier-1 クラウド スケール アプリケーションを構築して管理するための次世代のミドルウェア プラットフォームの代表例です。  

次の Channel9 の短いビデオでは、Service Fabric とマイクロサービスを紹介しています。<center><a target="_blank" href="https://aka.ms/servicefabricvideo">  
<img src="./media/service-fabric-content-roadmap/OverviewVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="the-detailed-overview"></a>概要の詳細
Service Fabric を使用すると、マイクロサービスで構成されるスケーラブルで信頼性の高いアプリケーションを構築して管理できます。  これらのマイクロサービスは、クラスターと呼ばれるマシンの共有プールで、きわめて高密度で実行します。 分散型のスケーラブルなステートレスおよびステートフル マイクロサービスを構築するための高度なランタイムを提供します。 また、デプロイ済みのアプリケーションをプロビジョニング、デプロイ、監視、アップグレード/修正、削除するための包括的なアプリケーション管理機能も提供します。  詳しくは、「[Service Fabric の概要](service-fabric-overview.md)」をご覧ください。

なぜ設計にマイクロサービスを採用しているのでしょうか。 すべてのアプリケーションは時間の経過に伴って進化します。 成功したアプリケーションは便利であることで進化します。 現在の要件についておよびその要件が将来どうなるかについてどれほどのことをご存じでしょうか。 (アプリケーションは後で再設計可能であると承知した上で) 簡単なアプリケーションを概念実証として完成させることが推進要因となることがあります。 一方で、企業がクラウドの構築について話すとき、成長と使用状況に対する期待があります。 問題はその成長と規模が予想できないことです。 開発者は、アプリケーションをスケールして予測できない成長や使用状況に対応できることを把握しながら、迅速にプロトタイプを構築する必要があります。  「[マイクロサービスとは](service-fabric-overview-microservices.md)」では、マイクロサービス設計アプローチがこれらの課題をどのようにして克服できるか、およびマイクロサービスを作成して個別にスケールアップ/スケールダウン、テスト、デプロイ、管理する方法について説明します。

Service Fabric はさまざまな種類のビジネス アプリケーションやサービスを作成し実行できる信頼性の高い柔軟なプラットフォームを提供します。  また、(あらゆる言語で記述された) 任意の既存のアプリケーションを実行できます。  これらのアプリケーションとマイクロサービスはステートレスまたはステートフルが考えられますが、最大限に効率化するために仮想マシン間でリソース分散されます。 Service Fabric 独自のアーキテクチャでは、リアルタイムに近いデータ分析、メモリ内のコンピューティング、並列トランザクション、アプリケーションでのイベント処理を実行できます。 リソース要件の変化に応じて、[アプリケーションを簡単にスケール アップまたはスケール ダウン](service-fabric-concepts-scalability.md) (実際にはスケール インまたはスケール アウト) できます。 作成できるアプリケーションやサービスのカテゴリおよびお客様導入事例については、「[アプリケーションのシナリオ](service-fabric-application-scenarios.md)」および「[パターンとシナリオ](service-fabric-patterns-and-scenarios.md)」に関する記事を参照してください。

次の Microsoft Virtual Academy のビデオでは、Service Fabric の主要概念について説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">  
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

## <a name="get-started-and-create-your-first-app"></a>導入および最初のアプリの作成 
Service Fabric SDK とツールを使用して Windows、Linux、または MacOS の各環境でアプリを開発し、それらのアプリを Windows または Linux で実行されているクラスターにデプロイできます。  次のガイドに従ってアプリをデプロイします。デプロイにかかる時間は数分です。  最初のアプリケーションを実行後、既製の[サンプル アプリ](http://aka.ms/servicefabricsamples)をいくつかダウンロードして実行します。 具体的には、[入門サンプル](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)から始めてください。

### <a name="on-windows"></a>Windows の場合
Service Fabric SDK には、Service Fabric アプリケーションの作成、デプロイ、およびデバッグのためのテンプレートとツールを提供する Visual Studio 用アドインが含まれます。 これらのトピックでは、Visual Studio で最初のアプリケーションを作成し、それをお使いの開発用コンピューターで実行するプロセスについて説明します。

[開発環境のセットアップ](service-fabric-get-started.md)
[最初のアプリの作成 (C#)](service-fabric-create-your-first-application-in-visual-studio.md)

#### <a name="practical-hands-on-labs"></a>実践的なハンズオン ラボ
この広範な[ハンズオン ラボ (パート 1)](https://msdnshared.blob.core.windows.net/media/2016/07/SF-Lab-Part-I.docx) で Service Fabric のエンド ツー エンドの開発フローに慣れることができます。  ステートレス サービスの作成、監視と正常性レポートの構成、アプリケーションのアップグレードを実行する方法を学習します。 パート 1 の後、[ハンズオン ラボ (パート 2)](http://aka.ms/sflab2) を実行すると、ステートフル サービスについて学習できます。

次の Channel9 のビデオでは、Visual Studio で C# アプリケーションを作成するプロセスについて説明します。  
<center><a target="_blank" href="https://channel9.msdn.com/Blogs/Azure/Creating-your-first-Service-Fabric-application-in-Visual-Studio">  
<img src="./media/service-fabric-content-roadmap/first-app-vid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-linux"></a>Linux の場合
Service Fabric では、.NET Core と Java の両方で Linux 上のサービスを構築するための SDK を提供しています。 「[開発環境をセットアップする](service-fabric-get-started-linux.md)」、「[最初のアプリを作成する (Java)](service-fabric-create-your-first-linux-application-with-java.md)」、「[最初のアプリを作成する (C#)](service-fabric-create-your-first-linux-application-with-csharp.md)」のトピックで、Linux で最初の Java または C# アプリケーションを作成し、それをお使いの開発用コンピューターで実行するプロセスについて説明します。

次の Microsoft Virtual Academy ビデオで、Linux で Java アプリを作成する手順について説明しています。  
<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=DOX8K86yC_206218965">  
<img src="./media/service-fabric-content-roadmap/LinuxVid.png" WIDTH="360" HEIGHT="244">  
</a></center>

### <a name="on-macos"></a>MacOS の場合
Linux クラスターで実行する Service Fabric アプリケーションを MacOS X で構築できます。 「[開発環境をセットアップする](service-fabric-get-started-mac.md)」および「[最初のアプリを作成する (Java)](service-fabric-create-your-first-linux-application-with-java.md)」記事では、お使いの Mac を開発用にセットアップする方法と、MacOS で Java アプリケーションを作成して Ubuntu 仮想マシンで実行する方法について説明します。

## <a name="core-concepts"></a>主要な概念
「[Service Fabric の用語](service-fabric-technical-overview.md)」、「[アプリケーション モデル](service-fabric-application-model.md)」、および「[サポートされるプログラミング モデル](service-fabric-choose-framework.md)」では詳細な概念や説明が提供されていますが、ここでは基本的な概念について説明します。

<table><tr><th>主要な概念</th><th>設計時</th><th>実行時</th></tr>
<tr><td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/CoreConceptsVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tlkI046yC_2906218965"><img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td>
<td><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=x7CVH56yC_1406218965">
<img src="./media/service-fabric-content-roadmap/RunTimeVid.png" WIDTH="240" HEIGHT="162"></a></td></tr>
</table>

### <a name="design-time-app-type-service-type-app-package-and-manifest-service-package-and-manifest"></a>設計時: アプリの種類、サービスの種類、アプリのパッケージとマニフェスト、サービス パッケージとマニフェスト
アプリケーションの種類は、一連のサービスの種類に割り当てられる名前/バージョンです。 ApplicationManifest.xml ファイル内に定義され、アプリケーション パッケージ ディレクトリに組み込まれた後、Service Fabric クラスターのイメージ ストアにコピーされます。 このアプリケーションの種類に基づいて名前付きアプリケーションを作成できます。作成したアプリケーションはクラスター内で実行します。 

サービスの種類は、サービスのコード パッケージとデータ パッケージ、構成パッケージに割り当てられる名前/バージョンです。 ServiceManifest.xml ファイル内に定義され、サービス パッケージ ディレクトリに組み込まれた後、このサービス パッケージ ディレクトリがアプリケーション パッケージの ApplicationManifest.xml ファイルによって参照されます。 クラスターには、名前付きアプリケーションを作成した後、そのアプリケーションの種類を構成するいずれかのサービスの種類から名前付きサービスを作成することができます。 サービスの種類は、その ServiceManifest.xml ファイルに記述します。  このファイルは実行時に読み込まれる実行コードのサービス構成設定と、サービスによって消費される静的データで構成されます。

![Service Fabric アプリケーションの種類とサービスの種類][cluster-imagestore-apptypes]

アプリケーション パッケージは、アプリケーションの種類の ApplicationManifest.xml ファイルが含まれるディスクのディレクトリで、アプリケーションの種類を構成するサービスの種類ごとに、対応するサービス パッケージを参照します。 たとえば電子メール アプリケーション タイプのアプリケーション パッケージであれば、Queue サービス パッケージやフロントエンド サービス パッケージ、データベース サービス パッケージへの参照が含まれることが考えられます。 アプリケーション パッケージ ディレクトリ内のファイルは、Service Fabric クラスターのイメージ ストアにコピーされます。 

サービス パッケージは、サービスの種類の ServiceManifest.xml ファイルが含まれるディスクのディレクトリで、そのサービスの種類のコード、静的データ、および構成パッケージを参照します。 サービス パッケージ ディレクトリ内のファイルは、アプリケーションの種類を定義した ApplicationManifest.xml ファイルから参照されます。 たとえばサービス パッケージは、データベース サービスを構成するコードや静的データ、構成パッケージを参照します。

### <a name="run-time-clusters-and-nodes-named-apps-named-services-partitions-and-replicas"></a>実行時: クラスターとノード、名前付きアプリ、名前付きサービス、パーティション、およびレプリカ
[Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターは多数のマシンにスケールできます。

クラスターに属しているコンピューターまたは VM をノードといいます。 それぞれのノードには、ノード名 (文字列) が割り当てられます。 ノードには、配置プロパティなどの特性があります。 それぞれのコンピューターまたは VM には、自動的に開始される Windows サービス (FabricHost.exe) が存在します。このサービスがコンピューターまたは VM の起動時に開始され、Fabric.exe と FabricGateway.exe の 2 つの実行可能ファイルを起動します。 ノードは、この 2 つの実行可能ファイルから成ります。 開発およびテストのシナリオでは、Fabric.exe と FabricGateway.exe の複数のインスタンスを実行することによって、1 台のコンピューターまたは VM で複数のノードをホストできます。

名前付きアプリケーションは、名前付きサービスのコレクションで、特定または複数の関数を実行します。 サービスは完全なスタンドアロンの機能を実行し (他のサービスから独立して開始、実行できる)、コード、構成、データで構成されます。 アプリケーション パッケージがイメージ ストアにコピーされた後、アプリケーション パッケージのアプリケーションの種類を (その名前/バージョンで) 指定することにより、そのアプリケーションのインスタンスをクラスター内に作成します。 アプリケーションの種類のインスタンスにはそれぞれ、URI 名が割り当てられます (例: *fabric:/MyNamedApp*)。 1 つのアプリケーションの種類から複数の名前付きアプリケーションをクラスター内で作成することが可能です。 異なるアプリケーションの種類から名前付きアプリケーションを作成することもできます。 個々の名前付きアプリケーションは別々に管理され、バージョニングされます。

名前付きアプリケーションを作成した後、そのいずれかのサービスの種類を (対応する名前/バージョンで) 指定することによって、特定のサービスの種類 (名前付きサービス) のインスタンスをクラスターに作成できます。 サービスの種類のインスタンスにはそれぞれ、対応する名前付きアプリケーションの URI に従属する URI 名が割り当てられます。 たとえば名前付きサービス "MyDatabase" を名前付きアプリケーション "MyNamedApp" 内に作成した場合、*fabric:/MyNamedApp/MyDatabase* のような URI が割り当てられます。 1 つの名前付きアプリケーションに複数の名前付きサービスを作成することができます。 名前付きサービスにはそれぞれ固有のパーティション構成とインスタンス/レプリカ数を割り当てることができます。 

ステートレスとステートフルの 2 種類のサービスがあります。  ステートレス サービスは永続的な状態を外部ストレージ サービス (Azure Storage、Azure SQL Database、Azure DocumentDB など) に保存します。 永続的なストレージがサービスにまったく存在しない場合は、ステートレス サービスを使用します。 ステートフル サービスは Service Fabric を使用して、その Reliable Collection や Reliable Actors のプログラミング モデルを介してサービスの状態を管理します。  

名前付きサービスを作成するときは、パーティション構成を指定します。 さまざまな状態を伴うサービスでは、データが複数のパーティションに分割されます。  各パーティションは、完全な状態のサービスの一部を担当し、クラスターのノード全体に分散されます。  パーティションには、ステートレスの名前付きサービスの場合はインスタンスが、ステートフルの名前付きサービスの場合はレプリカが格納されます。 通常、ステートレスの名前付きサービスは内部的な状態を持たないため、割り当てられるパーティションは 1 つだけです。 ステートフルの名前付きサービスでは、その状態がレプリカ内で管理されます。それぞれのパーティションが固有のレプリカ セットを持ちます。  読み取りおよび書き込み操作は、1 つのレプリカ (プライマリと呼ばれます) で実行されます。 書き込み操作からの状態の変更は、他の複数のレプリカ (アクティブ セカンダリと呼ばれます) にレプリケートされます。  

次の図は、アプリケーションとサービス インスタンス、パーティション、レプリカ間のリレーションシップを示しています。

![サービス内のパーティションとレプリカ][cluster-application-instances]

### <a name="partitioning-scaling-and-availability"></a>パーティション分割、スケーリング、および可用性
[パーティション分割](service-fabric-concepts-partitioning.md)は Service Fabric に固有のものではありません。 よく知られているパーティション分割の形式として、データのパーティション分割またはシャーディングがあります。 さまざまな状態を伴うステートフル サービスでは、データが複数のパーティションに分割されます。  各パーティションは、完全な状態のサービスの一部を担当します。 

各パーティションのレプリカがクラスターのノード間で分散され、名前付きのサービスの状態を[スケール](service-fabric-concepts-scalability.md)できます。 データ ニーズの拡大に応じて、パーティションが拡大し、Service Fabric はノード間でパーティションのバランスを再調整して、ハードウェア リソースを効率的に使用します。 新しいノードがクラスターに追加されると、Service Fabric は、増加したノード数全体で、パーティションのレプリカのバランスを再調整します。  アプリケーション全体のパフォーマンスが向上し、メモリへのアクセスの競合が減少します。  クラスター内のノードが効率的に使用されていない場合、クラスター内のノードの数を削減できます。  Service Fabric は、各ノードのハードウェアを効率的に利用できるように、減らされたノード数全体で、再度パーティション レプリカのバランスを再調整します。

パーティションには、ステートレスの名前付きサービスの場合はインスタンスが、ステートフルの名前付きサービスの場合はレプリカが格納されます。 通常、ステートレスの名前付きサービスは内部的な状態を持たないため、割り当てられるパーティションは 1 つだけです。 パーティションのインスタンスによってもたらされるのは[可用性](service-fabric-availability-services.md)です。  つまり 1 つのインスタンスで障害が発生しても他のインスタンスは正常動作を保ち、Service Fabric によって新しいインスタンスが作成されます。 ステートフルの名前付きサービスでは、その状態がレプリカ内で管理されます。それぞれのパーティションが固有のレプリカ セットを持ちます。  読み取りおよび書き込み操作は、1 つのレプリカ (プライマリと呼ばれます) で実行されます。 書き込み操作からの状態の変更は、他の複数のレプリカ (アクティブ セカンダリと呼ばれます) にレプリケートされます。  万一レプリカに障害が発生した場合は、Service Fabric が既存のレプリカから新しいレプリカを構築します。

## <a name="supported-programming-models"></a>サポートされるプログラミング モデル
Service Fabric には、サービスの記述と管理に使用できる複数の方法が用意されています。 サービスでは、Service Fabric API を使用するように選択して、プラットフォームの機能とアプリケーション フレームワークを最大限に活用できます。  または、サービスは、任意の言語で記述され、Service Fabric クラスターでホストされる任意のコンパイル済み実行可能プログラムにすることができます。 詳しくは、[サポートされるプログラミング モデル](service-fabric-choose-framework.md)に関する記事をご覧ください。

### <a name="guest-executables"></a>ゲスト実行可能ファイル
[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)は、Service Fabric クラスターで他のサービスとともにホストされる既存の任意の実行可能ファイル (任意の言語で記述されたもの) です。 ただし、ゲスト実行可能ファイルは、Service Fabric API に直接統合されません。  ゲスト実行可能ファイルは、プラットフォームに用意されているフルセットの機能 (カスタムの正常性および負荷のレポート、サービス エンドポイントの登録、ステートフル コンピューティングなど) を利用できません。

### <a name="containers"></a>コンテナー
既定では、Service Fabric はサービスをプロセスとしてデプロイし、アクティブ化します。 さらに、Service Fabric では、[コンテナー イメージ](service-fabric-containers-overview.md)内のサービスもデプロイできます。 重要なこととして、プロセスとしてのサービスとコンテナー内のサービスを同じアプリケーション内で混在させることができます。  現在、Service Fabric では、Linux での Docker コンテナーのデプロイと、Windows Server 2016 での Windows Server コンテナーのデプロイをサポートしています。 Service Fabric のアプリケーション モデルでは、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。  Service Fabric を使用して、既存のアプリケーション、ステートレス サービス、またはステートフル サービスをコンテナーにデプロイできます。  

### <a name="reliable-services"></a>Reliable Service
[Reliable Services](service-fabric-reliable-services-introduction.md) は、Service Fabric プラットフォームと統合されたサービス作成用の軽量のフレームワークで、プラットフォームの全機能を活用できます。 Reliable Services はステートレスにすることができます (Web サーバーや Azure Cloud Services の Worker ロールなど、ほとんどのサービス プラットフォームと同様)。 Azure DB や Azure Table Storage などの外部ソリューションに、状態が保持されます。 Reliable Services はステートフルになる場合もあり、状態は Reliable Collection を使用してサービスに直接維持されます。 状態はレプリケーションによって[高可用](service-fabric-availability-services.md)になり、[パーティション](service-fabric-concepts-partitioning.md)によって分散されます。いずれも Service Fabric で自動的に管理されます。

### <a name="reliable-actors"></a>Reliable Actor
[Reliable Actors](service-fabric-reliable-actors-introduction.md) フレームワークは Reliable Services 上に構築され、アクター設計パターンに基づいて、Virtual Actor パターンを実装するアプリケーション フレームワークです。 Reliable Actors フレームワークは、独立したコンピューティングのユニットと、アクターという単一スレッドの実行を含む状態を使用します。 Reliable Actors フレームワークには、アクターとプリセットされた状態の永続性とスケールアウト構成に対応する組み込みの通信が用意されています。

## <a name="app-lifecycle"></a>アプリケーション ライフサイクル
その他のプラットフォームと同様に、通常、Service Fabric のアプリケーションは、デザイン、開発、テスト、デプロイ、アップグレード、保守、削除のフェーズを進みます。 Service Fabric は、開発からデプロイ、日常的な管理、保守、最終的な使用停止に至るまで、クラウド アプリケーションの完全なアプリケーション ライフサイクルに対して高度なサポートを提供します。 そのサービス モデルにより、アプリケーションのライフサイクルで個別に関与するさまざまな役割が有効になります。 「[Service Fabric アプリケーションのライフサイクル](service-fabric-application-lifecycle.md)」では、API の概要と、Service Fabric アプリケーション ライフサイクルのフェーズ全体でさまざまな役割がその API をどのように使用するかを示します。  

アプリケーションのライフサイクル全体は、[PowerShell コマンドレット](/powershell/module/ServiceFabric/)、[C# API](/dotnet/api/system.fabric.fabricclient.applicationmanagementclient)、 [Java API](/java/api/system.fabric._application_management_client)、および[REST API](/rest/api/servicefabric/) を使用して管理できます。 [Visual Studio Team Services](service-fabric-set-up-continuous-integration.md) や [Jenkins](service-fabric-cicd-your-linux-java-application-with-jenkins.md) などのツールを使用して、継続的インテグレーション/継続的なデプロイ パイプラインをセットアップすることもできます。

次の Microsoft Virtual Academy のビデオでは、アプリケーション ライフサイクルを管理する方法を説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=My3Ka56yC_6106218965">
<img src="./media/service-fabric-content-roadmap/AppLifecycleVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="test-apps-and-services"></a>アプリとサービスのテスト
真のクラウド スケール サービスを作成するには、アプリとサービスが現実の障害に耐えられるかを検証することが不可欠です。  Fault Analysis Service は、Service Fabric で構築されたサービスをテストするために設計されています。 [Fault Analysis Service](service-fabric-testability-overview.md) を使用すると、アプリケーションに対して意味のある障害を誘発させ、完全なテスト シナリオを実行することができます。 これらのエラーとシナリオでは、サービスがその有効期間中に経験する多数の状態と遷移を、完全に管理された安全で一貫性のある方法で実行して検証します。

[アクション](service-fabric-testability-actions.md)は、個別の障害を使用してテストするためのサービスを対象にします。 サービス開発者は、複雑なシナリオを記述するための構成要素としてアクションを使用できます。 シミュレートされる障害の例を次に示します。

* ノードを再起動して、コンピューターまたは VM がリブートされる状況をシミュレートします。
* ステートフル サービスのレプリカを移動して、負荷分散、フェールオーバー、またはアプリケーションのアップグレードをシミュレートします。
* ステートフル サービスでのクォーラムの損失を発生させ、新しいデータを受け入れるための十分な "バックアップ" または "セカンダリ" レプリカがないことが原因で書き込み操作を続行できない、という状況を作ります。
* ステートフル サービスでのデータ損失を発生させ、すべてのインメモリ状態が完全に消去された状況を作ります。

[シナリオ](service-fabric-testability-scenarios.md)は、1 つまたは複数のアクションで構成される複雑な操作です。 Fault Analysis Service では、組み込みの完全なシナリオを 2 つ提供します。

* [混乱のシナリオ](service-fabric-controlled-chaos.md) - 長時間にわたり、クラスター全体で、連続して交互に配置された (グレースフルと非グレースフルの両方) 障害がシミュレートされます。
* [フェールオーバー シナリオ](service-fabric-testability-scenarios.md#failover-test) - 他のサービスに影響させずに、特定のサービス パーティションを対象にした、混乱のテスト シナリオの 1 つのバージョンです。

## <a name="clusters"></a>クラスター
[Service Fabric クラスター](service-fabric-deploy-anywhere.md)は、ネットワークで接続された一連の仮想マシンまたは物理マシンで、マイクロサービスがデプロイおよび管理されます。 クラスターは多数のマシンにスケールできます。 クラスターに属しているコンピューターまたは VM をクラスター ノードといいます。 それぞれのノードには、ノード名 (文字列) が割り当てられます。 ノードには、配置プロパティなどの特性があります。 それぞれのコンピューターまたは VM には、自動的に開始されるサービス (FabricHost.exe) が存在します。このサービスがコンピューターまたは VM の起動時に開始され、Fabric.exe と FabricGateway.exe の 2 つの実行可能ファイルを起動します。 ノードは、この 2 つの実行可能ファイルから成ります。 テストのシナリオでは、Fabric.exe と FabricGateway.exe の複数のインスタンスを実行することによって、1 台のコンピューターまたは VM で複数のノードをホストできます。

Windows Server または Linux を実行するあらゆる仮想マシンまたはコンピューター上に Service Fabric クラスターを作成できます。 オンプレミスか、Microsoft Azure 上か、任意のクラウド プロバイダー上かに関係なく、相互接続された一連の Windows Server コンピューターまたは Linux コンピューターがある任意の環境に、Service Fabric アプリケーションをデプロイして実行できます。

次の Microsoft Virtual Academy ビデオでは、Service Fabric クラスターの概念について説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tbuZM46yC_5206218965">
<img src="./media/service-fabric-content-roadmap/ClusterOverview.png" WIDTH="360" HEIGHT="244">
</a></center>

### <a name="clusters-on-azure"></a>Azure 上のクラスター
Azure で Service Fabric を実行すると、他の Azure の機能やサービスとの統合が提供されるため、クラスターの操作と管理が容易で信頼性が高くなります。  クラスターは、Azure Resource Manager リソースであるため、Azure で他の任意のリソースのようにクラスターをモデル化できます。 Resource Manager では、クラスターで使用するすべてのリソースを 1 つの単位として簡単に管理することもできます。  Azure 上のクラスターは、Azure 診断および Log Analytics と統合されます。  クラスターの NodeType は、[仮想マシン スケール セット](/azure/virtual-machine-scale-sets/index)であるため、自動スケール機能が組み込まれています。

[Azure Portal](service-fabric-cluster-creation-via-portal.md) 経由で、[テンプレート](service-fabric-cluster-creation-via-arm.md)から、または [Visual Studio](service-fabric-cluster-creation-via-visual-studio.md) から、Azure 上にクラスターを作成できます。

Linux 上の Service Fabric を使用すると、Windows 上と同じように、Linux で可用性とスケーラビリティの高いアプリケーションを構築、デプロイ、および管理できます。 Service Fabric フレームワーク (Reliable Services と Reliable Actors) を、Linux 上の C# (.NET Core) の他に、Java でも使用できるようになりました。  任意の言語またはフレームワークで[ゲスト実行可能サービス](service-fabric-deploy-existing-app.md)を構築することもできます。 さらに、プレビューでは、調整 Docker コンテナーもサポートされています。 Docker コンテナーは、ゲスト実行可能ファイルまたはネイティブ Service Fabric サービスを実行できます。これらは、Service Fabric フレームワークを使用します。 詳細については、「[Linux 上の Service Fabric](service-fabric-linux-overview.md)」をご覧ください。

Linux での Service Fabric はプレビュー版であるため、Windows ではサポートされているものの Linux ではサポートされていない機能が一部存在します。 詳細については、「[Linux での Service Fabric (プレビュー) と Windows での Service Fabric (一般公開) の違い](service-fabric-linux-windows-differences.md)」をご覧ください。

### <a name="standalone-clusters"></a>スタンドアロンのクラスター
Service Fabric には、オンプレミスまたは任意のクラウド プロバイダーにスタンドアロン Service Fabric クラスターを作成するためのインストール パッケージが用意されています。  スタンドアロン クラスターは、任意の場所でクラスターをホストする自由があります。  データがコンプライアンスや法的な制約を課せられる場合、またはデータをローカルに保持する場合は、独自のクラスターとアプリをホストできます。  Service Fabric アプリは、変更せずに複数のホスティング環境で実行できるため、アプリの構築の知識を 1 つのホスティング環境から別の環境に持ち越すことができます。 

[初めての Service Fabric スタンドアロン クラスターの作成](service-fabric-get-started-standalone-cluster.md)

Linux スタンドアロン クラスターはまだサポートされていません。

### <a name="cluster-security"></a>クラスターのセキュリティ
クラスターは、特に運用ワークロードが実行されている場合に、未承認ユーザーがクラスターに接続するのを防ぐためにセキュリティで保護する必要があります。 セキュリティで保護されていないクラスターを作成することはできますが、これを行うと、パブリック インターネットに管理エンドポイントを公開している場合に、匿名ユーザーがそのクラスターに接続できるようになります。  セキュリティ保護されていないクラスターに後からセキュリティを有効にすることはできません。クラスターのセキュリティは、クラスターの作成時に有効にします。

クラスターのセキュリティに関するシナリオは次のとおりです。
* ノード間のセキュリティ
* クライアントとノードの間のセキュリティ
* ロール ベースのアクセス制御 (RBAC)

詳細については、[クラスターのセキュリティ保護](service-fabric-cluster-security.md)に関するページを参照してください。

### <a name="scaling"></a>スケーリング
新しいノードがクラスターに追加されると、Service Fabric は、増加したノード数全体で、パーティションのレプリカとインスタンスのバランスを再調整します。  アプリケーション全体のパフォーマンスが向上し、メモリへのアクセスの競合が減少します。  クラスター内のノードが効率的に使用されていない場合、クラスター内のノードの数を削減できます。  Service Fabric は、各ノードのハードウェアを効率的に利用できるように、減らされたノード数全体で、再度パーティションのレプリカとインスタンスのバランスを再調整します。  Azure 上のクラスターは、[手動で](service-fabric-cluster-scale-up-down.md)または[プログラムで](service-fabric-cluster-programmatic-scaling.md)スケールできます。  スタンドアロン クラスターは[手動で](service-fabric-cluster-windows-server-add-remove-nodes.md)スケールできます。

### <a name="cluster-upgrades"></a>クラスターのアップグレード
定期的に、Service Fabric ランタイムの新しいバージョンがリリースされます。  常に[サポートされるバージョン](service-fabric-support.md)を実行しているように、クラスターで、ランタイム、またはファブリック、アップグレードを実行します。  ファブリックのアップグレードだけでなく、証明書やアプリケーション ポートなどのクラスター構成を更新することもできます。

Service Fabric クラスターはお客様が所有するリソースですが、一部は Microsoft によって管理されます。  Microsoft は、基になる OS の修正プログラムの適用や、クラスターでのファブリック アップグレードの実行を担当します。  クラスターは、新しいバージョンのファブリックがマイクロソフトからリリースされたときに自動アップグレードを適用するように設定できます。また、目的のサポートされているファブリック バージョンを選択するように設定することもできます。  ファブリックと構成のアップグレードは、Azure Portal から、またはリソース マネージャーから設定できます。  詳細については、「[Azure Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade.md)」をご覧ください。  

スタンドアロン クラスターは、ユーザーが完全に所有するリソースです。  基になる OS への修正プログラムの適用とファブリック アップグレードの開始はユーザーの責任です。  クラスターが [https://www.microsoft.com/download](https://www.microsoft.com/download) に接続できる場合、新しい Service Fabric ランタイム パッケージを自動的にダウンロードして、プロビジョニングするようにクラスターを設定できます。  これにより、アップグレードを開始できます。  クラスターが [https://www.microsoft.com/download](https://www.microsoft.com/download) にアクセスできない場合、インターネット接続されているコンピューターから新しいランタイム パッケージを手動でダウンロードしてアップグレードを開始できます。  詳細については、[スタンドアロン Service Fabric クラスターのアップグレード](service-fabric-cluster-upgrade-windows-server.md)に関するページをご覧ください。

## <a name="health-monitoring"></a>正常性の監視
Service Fabric では、特定のエンティティ (クラスター ノードやサービス レプリカなど) で、問題のあるクラスターおよびアプリケーションの条件にフラグを設定することを目的とする [正常性モデル](service-fabric-health-introduction.md) が導入されています。 正常性モデルは、正常性レポーター (システム コンポーネントとウォッチドッグ) を使用します。 その目標は、簡単かつ迅速な診断および修復です。 サービスの作成者は、正常性についてと[正常性レポートの設計](service-fabric-report-health.md#design-health-reporting)方法を事前に検討する必要があります。 正常性に影響する可能性があるすべての条件は、特にルートに近い問題にフラグを設定するために役立つ場合に、レポートする必要があります。 正常性の情報により、運用環境で、サービスが大規模に開始された後に、デバッグと調査にかかる時間と労力が削減されます。

Service Fabric レポーターは、識別された関心のある条件を監視します。 このレポーターは、そのローカル ビューに基づいて、これらの条件をレポートします。 [正常性ストア](service-fabric-health-introduction.md#health-store) は、すべてのレポーターによって送信された正常性データを集計して、エンティティがグローバルに正常な状態であるかどうかを判断します。 このモデルは、機能豊富、柔軟で、使いやすいように設計されています。 正常性レポートの品質によって、クラスターの正常性ビューがどの程度正確かが決定されます。 正常性の問題が誤って示される誤検知があると、正常性データを使用するアップグレードや他のサービスに悪影響が及びます。 このようなサービスの例として、修復サービスとアラート メカニズムがあります。 したがって、最適な方法で、関心のある条件をキャプチャするレポートを提供するには、ある程度の配慮が必要です。

レポートは、次のいずれかから作成できます。
* 監視対象の Service Fabric のサービス レプリカまたはインスタンス。
* Service Fabric のサービスとしてデプロイされる内部ウォッチドッグ (条件を監視して問題をレポートする Service Fabric ステートレス サービスなど)。 ウォッチドッグは、すべてのノードにデプロイするか、監視対象のサービスに関連付けることができます。
* Service Fabric のノードで実行されているが、Service Fabric のサービスとして実装されていない内部ウォッチドッグ。
* Service Fabric クラスター以外のリソースを調査する外部ウォッチドッグ (Gomez のような監視サービスなど)。

追加の設定なしで、Service Fabric コンポーネントは、クラスター内のすべてのエンティティの正常性をレポートします。  [システム正常性レポート](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)は、クラスターとアプリケーションの動作状況を視覚化し、正常性の問題を警告します。 システム正常性レポートは、アプリケーションとサービスを対象に、エンティティが実装されて正しく動作していることを Service Fabric ランタイムの観点から確認します。 レポートは、サービスのビジネス ロジックの正常性モニタリングやハングしたプロセスの検出を提供するものではありません。 サービスのロジックに固有の正常性情報を追加するには、サービスに[カスタム正常性レポートを実装](service-fabric-report-health.md)します。

Service Fabric には、正常性ストアに集計された[正常性レポートを表示する](service-fabric-view-entities-aggregated-health.md)ために複数の方法が用意されています。
* [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) またはその他の視覚化ツール
* 正常性クエリ ([PowerShell](/powershell/module/ServiceFabric/)、[C# FabricClient API](/api/system.fabric.fabricclient.healthclient) および [Java FabricClient API](/java/api/system.fabric._health_client)、または[REST API](/rest/api/servicefabric) を介して)。
* 正常性をプロパティの 1 つとして取得するエンティティの一覧を返す一般クエリ (PowerShell、API、または REST を使用)。

次の Microsoft Virtual Academy のビデオでは、Service Fabric の正常性モデルとその使用方法について説明しています。<center><a target="_blank" href="https://mva.microsoft.com/en-US/training-courses/building-microservices-applications-on-azure-service-fabric-16747?l=tevZw56yC_1906218965">
<img src="./media/service-fabric-content-roadmap/HealthIntroVid.png" WIDTH="360" HEIGHT="244">
</a></center>

## <a name="next-steps"></a>次のステップ
* [Azure でのクラスター](service-fabric-cluster-creation-via-portal.md)または [Windows でのスタンドアロン クラスター](service-fabric-cluster-creation-for-windows-server.md)を作成する方法を学びます。
* [Reliable Services](service-fabric-reliable-services-quick-start.md) または [Reliable Actors](service-fabric-reliable-actors-get-started.md) プログラミング モデルを使用してサービスを作成してみます。
* [Cloud Services から移行する](service-fabric-cloud-services-migration-differences.md)方法を学びます。
* [サービスを監視および診断する](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)方法を学びます。 
* [アプリとサービスをテストする](service-fabric-testability-overview.md)方法を学びます。
* [クラスター リソースを管理および調整する](service-fabric-cluster-resource-manager-introduction.md)方法を学びます。
* [Service Fabric サンプル](http://aka.ms/servicefabricsamples)を確認します。
* [Service Fabric のサポート オプション](service-fabric-support.md)について学びます。
* [チームのブログ](https://blogs.msdn.microsoft.com/azureservicefabric/)で記事やお知らせを読みます。


[cluster-application-instances]: media/service-fabric-content-roadmap/cluster-application-instances.png
[cluster-imagestore-apptypes]: ./media/service-fabric-content-roadmap/cluster-imagestore-apptypes.png

