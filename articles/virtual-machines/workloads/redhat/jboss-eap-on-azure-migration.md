---
title: JBoss EAP から Azure Virtual Machines の仮想マシン スケール セットの移行ガイド
description: このガイドでは、エンタープライズ Java アプリケーションを別のアプリケーション サーバーから JBoss EAP に、また従来のオンプレミス サーバーから Azure RHEL VM と仮想マシン スケール セットに移行する方法について説明します。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 06/08/2021
ms.openlocfilehash: ab19ecb14f3aa7701205e038fe59b92e297decbd
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130262966"
---
# <a name="how-to-migrate-java-applications-to-jboss-eap-on-azure-vms-and-virtual-machine-scale-sets"></a>Java アプリケーションを Azure VM と仮想マシン スケール セット上の JBoss EAP に移行する方法

このガイドでは、Java アプリケーションをそのまま "リフト アンド シフト" するクラウド戦略の場合に、[Red Hat JBoss Enterprise Application Platform (EAP)](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 上のエンタープライズ Java アプリケーションを、従来のオンプレミス サーバーから、Azure Red Hat Enterprise Linux (RHEL) Virtual Machines (VM) と仮想マシン スケール セットに移行する方法について説明します。 一方、"リフト アンド オプティマイズ" する場合は、代わりに、コンテナー化されたアプリケーションを Red Hat Gallery から [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/) と JBoss EAP イメージに移行したり、Java アプリ コードを Azure App Service インスタンス上の JBoss EAP に直接ドロップしたりすることもできます。

## <a name="best-practice-starting-with-azure-marketplace-offers-and-quickstarts"></a>Azure Marketplace のオファーとクイックスタートで始める場合のベスト プラクティス

Red Hat と Microsoft は提携して、[Azure Marketplace オファーに JBoss EAP](https://aka.ms/AMP-JBoss-EAP) のセットを追加し、Azure への移行の確固たる出発点を提供しています。 ドキュメントでオファーとプランの一覧を参照し、既存のデプロイに最もよく合うものを選択してください。 [Azure での JBoss EAP のベスト プラクティス](./jboss-eap-on-azure-best-practices.md)に関する記事を参照してください

よい出発点になる既存のオファーがない場合は、Azure VM と他の利用可能なリソースを使用してデプロイを手動で再現できます。 詳細については、「[IaaS とは](https://azure.microsoft.com/overview/what-is-iaas/)」を参照してください。

### <a name="azure-marketplace-offers"></a>Azure Marketplace オファー

Red Hat は Microsoft と連携して、以下のオファリングを Azure Marketplace で発行しています。 これらのオファーには、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/) から、または [Azure portal](https://azure.microsoft.com/features/azure-portal/) からアクセスできます。 詳細については、「[Azure Marketplace プランを使用して Azure VM と仮想マシン スケール セットに Red Hat JBoss Enterprise Platform (EAP) をデプロイする](./jboss-eap-marketplace-image.md)」方法に関する記事を参照してください。

この Marketplace オファーには、JBoss EAP と RHEL のバージョンのさまざまな組み合わせと、柔軟なサポート サブスクリプション モデルが含まれています。 JBoss EAP は常に BYOS (サブスクリプション持ち込み) で利用できますが、RHEL の場合は、BYOS または PAYG (従量課金制) を選択することができます。 Azure Marketplace オファーには、スタンドアロン VM、クラスター化 VM、クラスター化された仮想マシン スケール セットとしての JBoss EAP on RHEL のプラン オプションが含まれています。 6 つのプランは次のとおりです。

- JBoss EAP 7.3 on RHEL 8.3 スタンドアロン VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 スタンドアロン VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (BYOS)

### <a name="azure-quickstart-templates"></a>Azure クイックスタート テンプレート

Azure Marketplace のオファーと共に、EAP on Azure を体験するために使用できるクイックスタート テンプレートがあります。 これらのクイックスタートには、あらかじめ構築された ARM テンプレートとスクリプトが含まれており、さまざまな構成とバージョンの組み合わせで JBoss EAP on Azure をデプロイすることができます。 ソリューションのアーキテクチャには次のものが含まれます。

- JBoss EAP on RHEL スタンドアロン VM
- JBoss EAP on RHEL クラスター化 VM
- JBoss EAP on RHEL クラスター化仮想マシン スケール セット

すぐに始めるには、自分の JBoss EAP on RHEL バージョンの組み合わせとよく一致するクイックスタート テンプレートのいずれかを選択します。 詳細については、[JBoss EAP on Azure のクイックスタート](./jboss-eap-on-rhel.md)に関するドキュメントを参照してください。 

## <a name="prerequisites"></a>前提条件

* **アクティブなサブスクリプションがある Azure アカウント** - Azure サブスクリプションがない場合は、[Visual Studio Subscription サブスクライバー特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) (旧 MSDN) をアクティブ化するか、[無料でアカウントを作成する](https://azure.microsoft.com/pricing/free-trial)ことができます。

- **JBoss EAP のインストール** - JBoss EAP 用の Red Hat Subscription Management (RHSM) エンタイトルメントがある Red Hat アカウントが必要です。 このエンタイトルメントにより、Red Hat によるテストと認定が済んだ JBoss EAP バージョンをダウンロードすることができます。  EAP エンタイトルメントがない場合は、[Red Hat Developer Subscription for Individuals](https://developers.redhat.com/register) を使用して無料の開発者サブスクリプションにサインアップできます。 登録されると、[Red Hat カスタマー ポータル](https://access.redhat.com/management/)で必要な資格情報 (プール ID) を確認できます。

- **RHEL のオプション** - 従量課金制 (PAYG) またはサブスクリプション持ち込み (BYOS) のどちらかを選択します。 BYOS の場合は、ソリューション テンプレートを使用して Marketplace オファーをデプロイする前に、[Red Hat Cloud Access](https://access.redhat.com/) [RHEL Gold Image](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/) をアクティブにする必要があります。 [こちらの手順](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide)に従って、Microsoft Azure で使用するために RHEL Gold イメージを有効にします。

- **[Azure コマンド ライン インターフェイス (CLI)](/cli/azure/overview)** 。

- **Java ソース コードと [Java Development Kit (JDK) のバージョン](https://www.oracle.com/java/technologies/javase-downloads.html)**

- **[JBoss EAP 7.2 に基づく Java アプリケーション](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/index)** または **[JBoss EAP 7.3 に基づく Java アプリケーション](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/development_guide/index#get_started_developing_applications)** .

**RHEL のオプション** - PAYG または BYOS を選択します。 BYOS の場合は、Azure Marketplace オファーを使用するために [Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index) RHEL Gold Image をアクティブ化する必要があります。 BYOS オファーは、Azure portal の [プライベート オファー] セクションに表示されます。 

**製品のバージョン**

* [JBoss EAP 7.2](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2)
* [JBoss EAP 7.3](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3)
* [RHEL 7.7](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM)
* [RHEL 8.0](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM)

## <a name="migration-flow-and-architecture"></a>移行フローとアーキテクチャ

このセクションでは、JBoss EAP アプリケーションを、別のアプリケーション サーバーから JBoss EAP で実行するために、および従来のオンプレミス サーバーから Microsoft Azure クラウド環境に移行するための、無料のツールについて説明します。 

### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit for Applications (MTA)

EAP 関連の移行プロジェクトを実行する前の、計画サイクルの開始時に、Java アプリケーションを移行するには、Red Hat MTA を使用することをお勧めします。 MTA は、[広範な変換と使用の事例](https://developers.redhat.com/products/mta/use-cases)で、大規模な Java アプリケーションの最新化と移行のプロジェクトをサポートする、ツールのアセンブリです。 アプリケーション コードの分析の時間が短縮され、作業量の見積もりがサポートされ、コードの移行が促進され、クラウドとコンテナーへのアプリケーションの移動が支援されます。

:::image type="content" source="./media/migration-toolkit.png" alt-text="画像には、移行ツールキット アプリのダッシュボード ページが示されています。":::

Red Hat MTA を使用すると、他のアプリケーション サーバーから Red Hat JBoss EAP にアプリケーションを移行できます。

## <a name="pre-migration"></a>移行前

移行を確実に成功させるには、開始する前に、次のセクションで説明する評価とインベントリの手順を完了します。

### <a name="validate-the-compatibility"></a>互換性を検証する

移行を計画する前に、現在のデプロイ モデルとバージョンを検証することをお勧めします。 現在のバージョンがサポートされていない場合は、アプリケーションの大幅な変更が必要になる場合があります。

MTA では、Oracle WebLogic Server などのサードパーティ製エンタープライズ アプリケーション サーバーから JBoss EAP への移行がサポートされており、JBoss EAP の最新リリースにアップグレードされます。

次の表では、サポートされている最も一般的な移行パスについて説明します。

**表 - サポートされている移行パス: ソースからターゲットへ**

|ソース プラットフォーム ⇒ | JBoss EAP 6 | JBoss EAP 7 | Red Hat OpenShift | OpenJDK 8、11 | Apache Camel 3 | RH ランタイム上の Spring Boot | Quarkus
|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| Oracle WebLogic Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| IBM WebSphere Application Server | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 4 | &#x2714; | &#x2714; | X<sup>1</sup> | &#x2714; | - | - | - |
| JBoss EAP 5 | &#x2714; | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| JBoss EAP 7 | N/A | &#x2714; | &#x2714; | &#x2714; | - | - | - |
| Oracle JDK | - | - | &#x2714; | &#x2714; | - | - | - |
| Apache Camel 2 | - | - | &#x2714; | &#x2714; | &#x2714; | - | - |
| SpringBoot | - | - | &#x2714; | &#x2714; | - | &#x2714; | &#x2714; |
| Java アプリケーション | - | - | &#x2714; | &#x2714; | - | - | - |

<sup>1</sup> 現在、この移行パスについてのルールは MTA から提供されていませんが、Red Hat のコンサルティングが、任意のソース プラットフォームから JBoss EAP 7 への移行を支援できます。

MTA の[システム要件](https://access.redhat.com/documentation/en/migration_toolkit_for_applications/5.0/html-single/introduction_to_the_migration_toolkit_for_applications/index#system_requirements_getting-started-guide)を確認することもできます。

移行を計画する前に、[JBoss EAP 7.3 でサポートされている構成](https://access.redhat.com/articles/2026253#EAP_73)と [JBoss EAP 7.2 でサポートされている構成](https://access.redhat.com/articles/2026253#EAP_72)を確認してください。

Java の現在のバージョンを取得するには、サーバーにサインインして、次のコマンドを実行します。

```
java -version
```

### <a name="validate-operating-mode"></a>動作モードを検証する

JBoss EAP は、RHEL、Windows Server、Oracle Solaris でサポートされています。 JBoss EAP は、個別のインスタンスを管理するためのスタンドアロン サーバー動作モードで、または単一の制御ポイントからインスタンスのグループを管理するためのマネージド ドメイン動作モードで実行されます。

JBoss EAP マネージド ドメインは、Microsoft Azure ではサポートされません。 サポートされているのは、スタンドアロンの JBoss EAP サーバー インスタンスのみです。 スタンドアロンの JBoss EAP サーバーを使用する JBoss EAP クラスターの構成は Azure でサポートされており、これは Azure Marketplace オファーによりクラスター化された VM または仮想マシン スケール セットが作成される方法であることに、注意してください。

### <a name="inventory-server-capacity"></a>サーバー容量をインベントリする

現在の運用サーバーのハードウェア (メモリ、CPU、ディスクなど) と、平均およびピーク時の要求数とリソース使用率を文書化します。 この情報は、選択した移行パスに関係なく必要になります。 サイズの詳細については、「[Cloud Services のサイズ](../../../cloud-services/cloud-services-sizes-specs.md)」を参照してください。

### <a name="inventory-all-secrets"></a>すべてのシークレットをインベントリする

[Azure Key Vault](https://azure.microsoft.com/services/key-vault/) や [Azure App Configuration](https://azure.microsoft.com/services/app-configuration/) などの "サービスとしての構成" テクノロジが登場する前は、"シークレット" の明確に定義された概念はありませんでした。 代わりに、現在 "シークレット" を呼ばれるものとして効果的に機能する、さまざまな種類の構成設定のセットがありました。 JBoss EAP などのアプリ サーバーでは、これらのシークレットは多くの異なる構成ファイルと構成ストアにあります。 すべてのシークレットとパスワードについて、実稼働サーバー上のすべてのプロパティと構成ファイルを確認します。 必ず、WAR ファイル内の *jboss-web.xml* を確認してください。 また、パスワードや資格情報を含む構成ファイルがアプリケーション内に見つかる場合もあります。 Azure Key Vault の詳細については、「[Azure Key Vault の基本的な概念](../../../key-vault/general/basic-concepts.md)」参照してください。

### <a name="inventory-all-certificates"></a>すべての証明書をインベントリする

パブリック SSL エンドポイントで使用されるすべての証明書を文書化します。 次のコマンドを実行して、運用サーバー上のすべての証明書を表示できます。

```cli
keytool -list -v -keystore <path to keystore>
```

### <a name="inventory-jndi-resources"></a>JNDI リソースをインベントリする

- すべての Java Naming and Directory Interface (JNDI) リソースのインベントリを行います。 Java Message Service (JMS) ブローカーなど、一部については、移行または再構成が必要になる場合があります。

### <a name="insideyyour-application"></a>InsideyYour アプリケーション 

WEB-INF/jboss-web.xml または WEB-INF/web.xml、あるいはその両方のファイルを調べます。

### <a name="document-data-sources"></a>データ ソースの文書化

アプリケーションでデータベースを使用する場合は、次の情報を把握する必要があります。

* DataSource の名前は何ですか
* 接続プールの構成
* Java Database Connectivity (JDBC) ドライバーの JAR ファイルはどこで見つかりますか

詳細については、JBoss EAP のドキュメントの [JBoss EAP DataSources](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.4/html/configuration_guide/datasource_management) に関する記事を参照してください。

### <a name="determine-whether-and-how-the-file-system-is-used"></a>ファイル システムが使用されているかどうかとその使用方法を判断する

アプリケーション サーバーでファイル システムを使用する場合は、再構成や、まれにアーキテクチャの変更が必要になります。 ファイル システムは、JBoss EAP モジュールまたはアプリケーション コードによって使用される場合があります。 次のセクションに記載された一部または全部のシナリオを確認できます。

**読み取り専用の静的コンテンツ**

現在、アプリケーションで静的コンテンツを提供している場合は、そのための別の場所が必要になります。 静的コンテンツを Azure Blob Storage に移動し、グローバルな高速ダウンロードのために [Azure Content Delivery Network (CDN)](../../../cdn/index.yml) を追加することを検討できます。 詳細については、「[Azure Storage での静的 Web サイト ホスティング](../../../storage/blobs/storage-blob-static-website.md)」と[「クイック スタート:Azure ストレージ アカウントと Azure CDN との統合](../../../cdn/cdn-create-a-storage-account-with-cdn.md)」を参照してください。

**動的に公開される静的コンテンツ**

アプリケーションによってアップロードまたは生成されるが、作成後に変更できない静的コンテンツをアプリケーションで許可する場合は、前述のように [Azure Blob Storage](../../../storage/blobs/index.yml) と Azure CDN を使用し、[Azure Function](../../../azure-functions/index.yml) でアップロードと CDN の更新を処理します。 「[Azure Functions を使用した静的コンテンツのアップロードと CDN の事前読み込み](https://github.com/Azure-Samples/functions-java-push-static-contents-to-cdn)」で、ご利用いただけるサンプルの実装を提供しています。

**動的または内部のコンテンツ**

アプリケーションで頻繁に書き込みおよび読み取りされるファイル (一時データ ファイルなど) や、アプリケーションでのみ表示できる静的ファイルには、[Azure Storage](../../../storage/index.yml) 共有を永続ボリュームとしてマウントできます。 詳細については、「[Azure Kubernetes Service で Azure Files を含む永続ボリュームを動的に作成して使用する](../../../aks/azure-files-dynamic-pv.md)」を参照してください。

### <a name="determine-whether-a-connection-to-on-premises-is-needed"></a>オンプレミスへの接続が必要かどうかを判断する

アプリケーションからオンプレミスのサービスのいずれかにアクセスする必要がある場合、Azure の接続サービスの 1 つをプロビジョニングする必要があります。 詳しくは、「[オンプレミス ネットワークの Azure への接続](/azure/architecture/reference-architectures/hybrid-networking/)」をご覧ください。 または、オンプレミスのリソースで公開されている一般公開の API を使用するように、アプリケーションをリファクタリングする必要があります。

### <a name="determine-whether-jms-queues-or-topics-are-in-use"></a>JMS キューまたはトピックが使用中かどうか確認する

アプリケーションで JMS キューまたはトピックを使用している場合は、外部でホストされている JMS サーバーにそれらを移行する必要があります。 Azure Service Bus と Advanced Message Queuing Protocol (AMQP) は、JMS を使用している場合の優れた移行方法となります。 詳細については、[Azure Service Bus と AMQP 1.0 での JMS の使用](../../../service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp.md)に関する記事または「[Azure Service Bus キューとの間でメッセージを送受信する (Java)](../../../service-bus-messaging/service-bus-java-how-to-use-queues.md)」を参照してください

JMS 永続ストアが構成されている場合は、それらの構成を把握して、移行後に適用する必要があります。

### <a name="determine-whether-your-application-is-composed-of-multiple-wars"></a>アプリケーションが複数の WAR で構成されているかどうか確認する

アプリケーションが複数の WAR で構成されている場合は、これらの各 WAR を個別のアプリケーションとして扱い、それぞれについてこのガイドに従う必要があります。

### <a name="determine-whether-your-application-is-packaged-as-an-ear"></a>アプリケーションが EAR としてパッケージ化されているかどうか確認する

アプリケーションが EAR ファイルとしてパッケージ化されている場合は、必ず application.xml ファイルを調べて、構成を把握してください。

### <a name="identify-all-outside-processes-and-daemons-running-on-the-production-servers"></a>実稼働サーバーで実行されているすべての外部プロセスとデーモンを特定する

デーモンの監視など、アプリケーション サーバーの外部で実行されているプロセスがある場合は、それらを削除するか、別の場所に移行する必要があります。


## <a name="migration"></a>移行

### <a name="provision-the-target-infrastructure"></a>ターゲット インフラストラクチャをプロビジョニングする

移行を始めるには、最初に JBoss EAP インフラストラクチャをデプロイする必要があります。 デプロイには複数のオプションがあります

- [**Azure Virtual Machine**](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)
- [**Azure 仮想マシン スケール セット**](../../../virtual-machine-scale-sets/overview.md)
- [**Azure App Service**](/azure/developer/java/ee/jboss-on-azure)
- [**Azure Red Hat OpenShift (ARO) for Containers**](https://azure.microsoft.com/services/openshift)
- [**Azure Container Service**](https://azure.microsoft.com/product-categories/containers/)

環境を構築する前に、Azure Marketplace の概要に関するセクションを参照して、デプロイ インフラストラクチャを評価してください。

### <a name="perform-the-migration"></a>移行する

移行を支援できるツールがあります。

* [移行に関してアプリケーションを分析するための Red Hat Application Migration Toolkit](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#use_windup_to_analyze_applications_for_migration)。
* [サーバー構成を移行するための JBoss Server Migration Tool](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migration_tool_server_migration_tool)

サーバーの構成を古い JBoss EAP バージョンから新しい JBoss EAP バージョンに移行するには、[JBoss Server Migration Tool](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate_server_migration_tool_option) を使用すること、または[管理 CLI 移行操作](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.2/html/migration_guide/index#migrate__migrate_operation_option)を利用して手動で移行を実行することができます。

### <a name="run-red-hat-application-migration-toolkit"></a>Red Hat Application Migration Toolkit を実行する

[対話型モードで JBoss Server Migration Tool を実行する](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/using_the_jboss_server_migration_tool/index#migration_tool_server_run_interactive_mode)ことができます。 既定では、JBoss Server Migration Tool は対話形式で実行されます。 このモードを使用すると、移行するサーバー構成を正確に選択できます。

[非対話型モードで JBoss Server Migration Tool を実行する](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_run_noninteractive_mode)こともできます。 このモードでは、プロンプトなしで実行できます。

### <a name="review-the-result-of-jboss-server-migration-toolkit-execution"></a>JBoss サーバー移行ツールキットの実行結果を確認する

移行が完了したら、*EAP_HOME/standalone/configuration/* ディレクトリと *EAP_HOME/domain/configuration/* ディレクトリで、移行されたサーバー構成ファイルを確認します。 詳細については、「[Reviewing the Results of JBoss Server Migration Tool Execution (JBoss Server Migration Tool の実行結果の確認)](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/using_the_jboss_server_migration_tool/running_the_server_migration_tool#migration_tool_server_results)」を参照してください。

### <a name="expose-the-application"></a>アプリケーションを公開する

お使いの環境に適した次の方法を使用して、アプリケーションを公開できます。

* サーバーとアプリケーションにアクセスするための[パブリック IP を作成](../../../virtual-network/ip-services/virtual-network-public-ip-address.md#create-a-public-ip-address)します。
* 同じ Virtual Network (VNet) 内の別のサブネット (新しいサブネット) に、[同じ Virtual Network 内のジャンプ VM を作成](../../windows/quick-create-portal.md#create-virtual-machine)し、ジャンプ VM 経由でサーバーにアクセスします。 このジャンプ VM は、アプリケーションを公開するために使用できます。
* 別の Virtual Network で [VNet ピアリングを使用してジャンプ VM を作成](../../windows/quick-create-portal.md#create-virtual-machine)し、サーバーにアクセスして、[仮想ネットワーク ピアリング](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)を使用してアプリケーションを公開します。
* [Application Gateway](../../../application-gateway/quick-create-portal.md#create-an-application-gateway) を使用してアプリケーションを公開します
* [外部 Load Balancer](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources) (ELB) を使用してアプリケーションを公開します。

## <a name="post-migration"></a>移行後

「移行前」ステップで定義した移行の目標に到達したら、エンド ツー エンドの受け入れテストを実施して、すべてが予期したとおりに機能することを確認します。 移行後の機能強化に関するトピックをいくつか記載していますが、もちろん以下に限定されません。

* Azure Storage を使用して、VM にマウントされた静的コンテンツを提供します。 詳細については、[VM へのデータ ディスクのアタッチまたはデタッチ](../../../devtest-labs/devtest-lab-attach-detach-data-disk.md)に関するページを参照してください
* Azure DevOps を使用して、移行した JBoss クラスターにアプリケーションをデプロイします。 詳細については、[Azure DevOps の使用開始に関するドキュメント](/azure/devops/get-started)を参照してください。
* [Application Gateway](../../../application-gateway/index.yml) の使用を検討してください。
* 高度な負荷分散サービスを使用してネットワーク トポロジを強化します。 詳細については、「[Azure で負荷分散サービスを使用する](../../../traffic-manager/traffic-manager-load-balancing-azure.md)」を参照してください。
* Azure マネージド ID をマネージド シークレットに利用して、ロールベースのアクセス制御 (RBAC) を Azure リソースに割り当てます。 詳細については、「[Azure リソースのマネージド ID とは](../../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。
* Azure Key Vault を使用して、"シークレット" として機能するすべての情報を格納します。 詳細については、「[Azure Key Vault の基本的な概念](../../../key-vault/general/basic-concepts.md)」を参照してください。

## <a name="resource-links-and-support"></a>リソースのリンクとサポート

サポート関連の質問、問題、またはカスタマイズの要件については、[Red Hat サポート](https://access.redhat.com/support)または [Microsoft Azure サポート](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)に問い合わせてください。

* [JBoss EAP](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/getting_started_with_jboss_eap_for_openshift_online/introduction) の詳細を確認する
* [Red Hat Subscription Manager (Cloud Access)](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index) の詳細を確認する
* [Azure 仮想マシン](https://azure.microsoft.com/overview/what-is-a-virtual-machine/)の詳細情報
* [Azure 仮想マシン スケール セット](../../../virtual-machine-scale-sets/overview.md)の詳細を確認する
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/) の詳細を確認する
* [Azure App Service on Linux](../../../app-service/overview.md#app-service-on-linux) の詳細を確認する
* [Azure Storage](../../../storage/common/storage-introduction.md) の詳細を確認する
* [Azure ネットワーク](../../../networking/fundamentals/networking-overview.md)の詳細を確認する

## <a name="next-steps"></a>次のステップ
* [Azure Marketplace から RHEL の VM または VM スケール セットに JBoss EAP をデプロイする](https://aka.ms/AMP-JBoss-EAP)
* [Azure App Service 向けの Java アプリを構成する](../../../app-service/configure-language-java.md)
* [JBoss EAP を Azure App Service にデプロイする方法](https://github.com/JasonFreeberg/jboss-on-app-service)のチュートリアル
* [Azure App Service Migration Assistance を使用する](https://azure.microsoft.com/services/app-service/migration-assistant/)
* [Red Hat Migration Toolkit for Applications を使用する](https://developers.redhat.com/products/mta)