<properties
   pageTitle="Service Fabric とコンテナーの概要 | Microsoft Azure"
   description="Service Fabric に関する説明と、コンテナーを使用してマイクロサービス アプリケーションをデプロイする方法の概要を示します。 この記事では、コンテナーの使い方と、Service Fabric で利用可能な機能の概要について説明しています。"
   services="service-fabric"
   documentationCenter=".net"
   authors="msfussell"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/25/2016"
   ms.author="msfussell"/>


# <a name="preview:-service-fabric-and-containers"></a>プレビュー: Service Fabric とコンテナー 

>[AZURE.NOTE] この機能は Linux 向けのプレビューの段階にあり、現時点で Windows Server では使用できません。 Windows Server 向けのプレビューは、Windows Server 2016 GA の後に行われる Service Fabric の次回のリリースで提供され、それ以降のリリースでサポートされます。

## <a name="introduction"></a>はじめに
Service Fabric は、マシンのクラスター全体のサービスの[オーケストレーター](service-fabric-cluster-resource-manager-introduction.md)です。 [Service Fabric プログラミング モデル](service-fabric-choose-framework.md)の使用や[ゲスト実行可能ファイル](service-fabric-deploy-existing-app.md)のデプロイなどの多くの方法で、サービスを開発することができます。 既定では、Service Fabric はこれらのサービスをプロセスとしてデプロイし、アクティブ化します。 プロセスとしてこれらを実施することで、これまでになくアクティブ化が高速になり、クラスターにおけるリソースの使用に無駄もなくなります。 さらに、Service Fabric では、コンテナー イメージ内のサービスもデプロイできます。重要なこととして、プロセスとしてのサービスとコンテナー内のサービスは両方とも、同じアプリケーション内で混在させることができます。 シナリオに応じて、両方のやり方の長所を活用できます。

## <a name="what-are-containers?"></a>コンテナーとは
コンテナーとは、カプセル化された、個別にデプロイが可能なコンポーネントです。同じカーネル上でそれぞれが分離されたインスタンスとして動作し、オペレーティング システム レベルでの仮想化を活かすことができます。 これは、各アプリケーションとそのランタイム、依存関係、システム ライブラリがコンテナー内で動作することを意味します。その際は各コンテナーごとに、オペレーティング システム構成の分離されたビューへの完全なプライベート アクセスが提供されます。 移植性のみならず、この高度なセキュリティおよびリソースの分離は、Service Fabric でコンテナーを利用するうえでの主なメリットとなります。この方法でなければ、サービスはプロセスとして実行されるためです。 

コンテナーは、システムの基礎となる OS をアプリケーションで仮想化する、仮想化テクノロジです。 さまざまなレベルで分離されたアプリケーションを実行するための変更不可能な環境を提供します。 コンテナーはカーネル上で直接動作し、ファイルシステムなどのリソースの分離されたビューを備えています。 VM と比較して、コンテナーには次の利点があります。

1.  **サイズが小さい**: コンテナーは、各レイヤーごとに 1 つの記憶域スペースと小規模な差分を使用するだけなので、より効率的です。

2.  **起動が高速**: コンテナーでは OS を起動する必要がないため、VM よりかなり早く起動が完了します。通常は数秒で使用できるようになります。

3.  **移植性**: コンテナー化されたアプリケーションのイメージを、クラウドまたはオンプレミスで実行されるように移植することができます。VM への移植または物理マシン上への直接の移植が可能です。

4.  **リソース ガバナンスを実現し** 、コンテナーがホスト上で利用する物理リソースを制限できるようにします。

## <a name="container-types"></a>コンテナーの種類
Service Fabric では、次の種類のコンテナーがサポートされています。

### <a name="docker-containers-on-linux"></a>Linux 上の Docker コンテナー
Docker は、Linux カーネル コンテナー上でコンテナーを作成、管理するための上位レベルの API と、コンテナー イメージの格納と取得に使用する中央リポジトリである Docker Hub を提供しています。 

### <a name="windows-server-containers"></a>Windows Server コンテナー
Windows Server 2016 には、分離レベルの異なる 2 種類のコンテナーが用意されています。 Windows Server コンテナーは、名前空間とファイル システムが分離されていても、カーネルをコンテナーの実行されているホストと共有するという点で、Docker コンテナーと似ています。 Linux では、cgroups や名前空間によって分離を行うのが通例でした。Windows Server コンテナーも同様に動作します。 

Windows Hyper-V コンテナーでは、各コンテナーが OS カーネルを他のコンテナーおよびホストと共有しないため、より高度な分離とセキュリティが提供されます。 この高度なセキュリティ用の分離により、Hyper-V コンテナーは、悪意のあるマルチ テナントのシナリオに特に効果を発揮します。

次の図は、OS で利用可能なさまざまな種類の仮想化と分離のレベルを示しています。
![Service Fabric プラットフォーム][Image1]

## <a name="scenarios-for-using-containers"></a>コンテナーを使用するシナリオ
コンテナーの一般的な使用例をいくつか示します。

1. **IIS リフト アンド シフト**。 既存の ASP.NET MVC アプリを継続して使用する場合、ASP.NET Core に移行するよりも、コンテナーに格納した方が便利です。 これらの ASP.NET MVC アプリは IIS に依存します。 これらのアプリは、事前に作成された IIS イメージからコンテナー イメージにパッケージ化し、Service Fabric でデプロイすることができます。 IIS イメージの作成方法については、「 [Windows Server のコンテナー イメージ](https://msdn.microsoft.com/virtualization/windowscontainers/quick_start/quick_start_images) 」を参照してください。


2. **コンテナーと Service Fabric マイクロサービスの混在使用**。 アプリケーションの一部として、既存のコンテナー イメージを使用できます。 たとえば、アプリケーションの Web フロントエンドとして [NGINX コンテナー](https://hub.docker.com/_/nginx/) を使用したり、より多くのバックエンド計算の実現のために Reliable Services で構築したステートフル サービスを使用したりすることが可能です。 このシナリオの例としては、ゲーム アプリケーションなどがあります。


3. **"うるさい隣人" サービスの影響の軽減**。 コンテナーのリソース ガバナンス機能により、サービスがホスト上で使用するリソースを制限することができます。 大量のリソースを消費する可能性のあるサービス (例: 操作などの実行時間の長いクエリ) があり、他のパフォーマンスに影響が出る場合、リソース ガバナンス機能を備えたコンテナーに格納することを検討してください。

## <a name="service-fabric-support-for-containers"></a>Service Fabric によるコンテナーのサポート
現在、Service Fabric では、Linux での Docker コンテナーのデプロイと、Windows Server 2016 での Windows Server コンテナーのデプロイをサポートしています。 Hyper-V コンテナーのサポートは、将来のリリースで追加されます。 

Service Fabric の [アプリケーション モデル](service-fabric-application-model.md)では、コンテナーは複数のサービス レプリカが配置されたアプリケーション ホストを表します。 コンテナー向けに、次のシナリオがサポートされています。

1.  **ゲスト コンテナー**: [ゲスト実行可能ファイルのシナリオ](service-fabric-deploy-existing-app.md) と同様に、コンテナー内の既存のアプリケーションをデプロイすることができます。 たとえば、Node.js、JavaScript、その他の任意のコード (EXE) などです。


2.  **コンテナー内のステートレス サービス**: Reliable Services と Reliable Actors のプログラミング モデルを使用したステートレス サービスです。 現在は Linux でのみサポートされています。 Windows コンテナーでのステートレス サービスのサポートは、将来のリリースで追加されます。

 
3.  **コンテナー内のステートフル サービス**: Linux での Reliable Actors プログラミング モデルを使用したステートフル サービスです。 Reliable Services は、Linux では現在サポートされていません。  Windows コンテナーでのステートフル サービスのサポートは、将来のリリースで追加されます。

Service Fabric には、コンテナー化されたマイクロサービスで構成されたアプリケーションの構築に役立つ、いくつかのコンテナー機能が用意されています。 これをコンテナー化されたサービスといいます。 機能は次のとおりです。

- コンテナー イメージのデプロイとアクティブ化
- リソース ガバナンス
- リポジトリの認証
- コンテナー ポートからホスト ポートへのマッピング
- コンテナー間での検出と通信
- 環境変数の構成と設定の機能

## <a name="next-steps"></a>次のステップ
この記事では、Service Fabric がコンテナー オーケストレーターとなるコンテナーについて説明したほか、Service Fabric がコンテナーをサポートするために提供している機能についても説明しました。 次のステップとして、各機能の例の紹介と、その使用方法の解説に進みます。 

[Service Fabric へのコンテナーのデプロイ](service-fabric-deploy-container.md)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png




<!--HONumber=Oct16_HO2-->


