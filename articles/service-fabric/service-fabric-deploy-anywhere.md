<properties
   pageTitle="Windows Server および Linux での Azure Service Fabric クラスターの作成 | Microsoft Azure"
   description="Service Fabric クラスターは Windows Server または Linux で実行されます。つまり、Windows Server または Linux を実行できる任意の場所に Service Fabric アプリケーションをデプロイしてホストできます。"
   services="service-fabric"
   documentationCenter=".net"
   authors="Chackdan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2016"
   ms.author="chackdan"/>

# Windows Server または Linux でのスタンドアロン Service Fabric クラスターの作成
Azure Service Fabric クラスターは、Windows Server または Linux を実行するすべての VM またはコンピューター上に作成できます。つまり、オンプレミスかクラウド プロバイダーかに関係なく、相互接続された一連の Windows Server コンピューターまたは Linux コンピューターがある任意の環境に Service Fabric アプリケーションをデプロイして実行できます。

**注**: Azure でのクラスターの作成は、Azure リソース モデル テンプレートまたは Azure ポータルを使用して行う必要があります。詳細については、「[Azure リソース マネージャー テンプレートを使用して Service Fabric クラスターをセットアップする](service-fabric-cluster-creation-via-arm.md)」または「[Azure ポータルから Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-portal.md)」を参照してください。

Service Fabric には、オンプレミスにこれらのスタンドアロン Service Fabric クラスターを作成するためのインストール パッケージが用意されています。この方法の主なメリットの 1 つは、アプリケーションの実行場所を選択できるため、Service Fabric を使用するアプリケーションをビルドする際にベンダー ロックインが生じない点です。また、アプリケーションを実行する環境の要件は顧客によって異なるため、この機能を使用すると、幅広い顧客ベースにアプローチできる可能性が高まります。たとえば、医療機関や金融機関の顧客のニーズは、自動車メーカーや旅行会社の顧客とは異なる場合があります。

## サポートされているオペレーティング システム
クラスターは、次のオペレーティング システムが実行されている VM 上またはコンピューター上に作成できます。

* Windows Server 2012 R2
* Windows Server 2016 (一般公開として発表された後)
* Linux (間もなく)

Windows Server の詳細については、[Windows Server での Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)に関する記事をご覧ください

## クラスターの作成と構成
Service Fabric には、ダウンロードできるインストール パッケージが用意されています。このパッケージをダウンロードした後、JSON 構成ファイルを変更してクラスターの設定を指定する必要があります。クラスターの設定を編集した後、セットアップ スクリプトを実行して、クラスター設定で指定した複数のコンピューターにまたがるクラスターを作成します。スクリプトを実行して一連のコンピューターからクラスターを削除することもできます。

## クラウドのデプロイとオンプレミスのデプロイの比較
オンプレミスに Service Fabric クラスターを作成するプロセスは、一連の VM がある任意のクラウド上にクラスターを作成するプロセスとほぼ同じです。VM をプロビジョニングするための最初の手順は、使用しているクラウド プロバイダーまたはオンプレミス環境に左右されます。一連の VM を相互にネットワーク接続できる状態にした後は、Service Fabric パッケージの設定、クラスター設定の編集、クラスターの作成と管理のスクリプトの実行の手順はオンプレミスの場合と同じです。これにより、新しいホスティング環境の使用を決めたときに、Service Fabric クラスターの運用と管理の経験を移転できます。

## スタンドアロンの Service Fabric クラスターを作成する利点
* ベンダーへのロックインが発生しないため、クラスターを作成する場所をユーザーが選択できます。
* Service Fabric アプリケーションは、一度記述すると、複数のホスティング環境で最小限の変更で実行できます。
* Service Fabric アプリケーションの構築に関する知識は、ホスティング環境が変わっても適用できます。
* Service Fabric クラスターの実行と管理に関する運用経験は、環境が変わっても活かせます。
* ホスティング環境による制約がないため、広範な顧客にアプローチできます。
* データ センターまたはクラウド プロバイダーにブラックアウトが発生した場合でもサービスを別のデプロイ環境に移行できるため、広範囲に及ぶサービス停止に対する信頼性と保護が強化されています。

## オンプレミスに作成されたスタンドアロンの Service Fabric クラスターと比較した場合の Azure 上の Service Fabric クラスターのメリット
オンプレミスのオプションと比較して、Azure で Service Fabric クラスターを実行することにはメリットがあります。そのため、クラスターの実行場所に関して特別なニーズがない場合は、Azure で実行することをお勧めします。Azure では、他の Azure の機能やサービスとの統合が提供されているため、クラスターの操作と管理が容易で信頼性が高くなります。

* **Azure ポータル**: Azure ポータルを使用すると、クラスターの作成と管理が容易になります。

* **Azure リソース マネージャー**: Azure リソース マネージャーを使用すると、クラスターでユニットとして使用されているリソースの管理が容易になり、コストの追跡と要求を簡素化できます。
* **Azure リソースとしての Service Fabric クラスター**: Service Fabric クラスターは ARM リソースなので、Azure の他の ARM リソースと同じようにモデル化できます。
* **Azure インフラストラクチャとの統合**: Service Fabric は、OS、ネットワーク、およびその他のアップグレードに関して基になる Azure インフラストラクチャと連携するため、アプリケーションの可用性と信頼性が向上します。
* **診断**: Azure では、Azure 診断および Log Analytics との統合が提供されます。
* **自動スケール**: Azure 上のクラスターの場合、仮想マシン スケール セットであるため、組み込みの自動スケール機能が提供されます。オンプレミスおよび他のクラウド環境では、自動スケール機能を独自に構築するか、Service Fabric で公開されているクラスター スケーリング用 API を手動で使用してスケールする必要があります。

## 次のステップ
Windows Server を実行する VM またはコンピューターでのクラスターの作成: [Windows Server での Service Fabric クラスターの作成](service-fabric-cluster-creation-for-windows-server.md)

Linux を実行している VM またはコンピューターでのクラスターの作成: [Linux 上の Service Fabric](service-fabric-linux-overview.md)

<!---HONumber=AcomDC_0921_2016-->