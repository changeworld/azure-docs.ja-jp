---
title: クイックスタート - Red Hat Enterprise Linux (RHEL) 上の JBoss Enterprise Application (EAP) を Azure の VM および仮想マシン スケール セットに
description: Azure の RHEL VM および仮想マシン スケール セット上の Red Hat JBoss EAP を使用してエンタープライズ Java アプリケーションをデプロイする方法。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: quickstart
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.assetid: 8a4df7bf-be49-4198-800e-db381cda98f5
ms.date: 10/30/2020
ms.openlocfilehash: d97148393d3158e38f9740d4a8f8e17dd04326d5
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/30/2020
ms.locfileid: "93135117"
---
# <a name="deploy-enterprise-java-applications-to-azure-with-jboss-eap-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上の JBoss EAP を使用してエンタープライズ Java アプリケーションを Azure にデプロイする

これらのクイックスタート テンプレートでは、[RHEL](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) と [JBoss EAP](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) を Azure Virtual Machines (VM) または Virtual Machine Scale Sets にデプロイする方法を説明します。 デプロイ内には、デプロイを検証するためのサンプル Java アプリが用意されています。 JBoss EAP は、オープンソースのアプリケーション サーバー プラットフォームです。 Java アプリケーションにエンタープライズ レベルのセキュリティ、スケーラビリティ、パフォーマンスを提供します。 RHEL は、オープンソースのオペレーティング システム (OS) プラットフォームです。 既存のアプリをスケーリングすると共に、最新のテクノロジをすべての環境にわたってロールアウトすることができます。 JBoss EAP と RHEL には、あらゆる環境でエンタープライズ Java アプリケーションを構築、実行、デプロイ、管理するために必要な要素がすべて含まれています。 オンプレミスであれ、仮想環境であれ、また、プライベート、パブリック、ハイブリッドのどのクラウドでも、卓越したオープンソース ソリューションとなります。

## <a name="prerequisite"></a>前提条件 

* アクティブなサブスクリプションが含まれる Azure アカウント。 Azure サブスクリプションを入手するには、[Visual Studio サブスクライバー向けの Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)をアクティブにするか、[アカウントを無料で作成](https://azure.microsoft.com/pricing/free-trial)します。

* JBoss EAP のインストール - JBoss EAP の Red Hat Subscription Management (RHSM) エンタイトルメントを有する Red Hat アカウントが必要です。 このエンタイトルメントを通じて、Red Hat によるテストと認定が済んだ JBoss EAP バージョンをダウンロードすることができます。  EAP のエンタイトルメントがない場合は、先に進む前に [JBoss EAP 評価サブスクリプション](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)を入手してください。 新しい Red Hat サブスクリプションを作成するには、[Red Hat カスタマー ポータル](https://access.redhat.com/)に移動して、アカウントを設定します。
F
* [Azure コマンド ライン インターフェイス](https://docs.microsoft.com/cli/azure/overview)。

* RHEL のオプション - 従量課金制 (PAYG) またはサブスクリプション持ち込み (BYOS) のどちらかを選択してください。 BYOS を選択した場合、クイックスタート テンプレートをデプロイする前に [Red Hat Cloud Access](https://access.redhat.com/) RHEL Gold Image をアクティブにする必要があります。

## <a name="java-ee--jakarata-ee-application-migration"></a>Java EE と Jakarata EE アプリケーションの移行

### <a name="migrate-to-jboss-eap"></a>JBoss EAP への移行
JBoss EAP 7.2 および 7.3 は、Java Enterprise Edition (Java EE) 8 と Jakarta EE 8 の仕様に対する認定済みの実装です。 JBoss EAP には、高可用性 (HA) クラスタリング、メッセージング、分散キャッシュなどの機能に関して、事前構成済みのオプションが用意されています。 加えて、JBoss EAP が備える各種の API およびサービスを使用して、アプリケーションを作成、デプロイ、実行することもできます。  JBoss EAP の詳細については、[JBoss EAP 7.2 の概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html-single/introduction_to_jboss_eap/index)または [JBoss EAP 7.3 の概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/introduction_to_jboss_eap/index)に関するページを参照してください。

 #### <a name="applications-on-jboss-eap"></a>JBoss EAP 上のアプリケーション

* Web サービス アプリケーション - Web サービスは、異なるソフトウェア アプリケーション間で相互運用を実現する標準的な手段となっています。 実行環境となるプラットフォームやフレームワークは、アプリケーションごとに異なる場合があります。 内部の異種サブシステムの通信を支援するのが、これらの Web サービスとなります。 詳細については、[EAP 7.2 上の Web サービス アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_web_services_applications/index)または [EAP 7.3 上の Web サービス アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_web_services_applications/index)に関するページを参照してください。

* Enterprise Java Beans (EJB) アプリケーション - EJB 3.2 は、分散型、トランザクショナル、セキュア、移植可能を特徴とする Java EE および Jakarta EE アプリケーションを開発するための API です。 EJB では、Enterprise Bean と呼ばれるサーバー側コンポーネントを使用して、再利用を促進する分離された方法でアプリケーションのビジネス ロジックを実装します。 詳細については、[EAP 7.2 上の EJB アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/developing_ejb_applications/index)または [EAP 7.3 上の EJB アプリケーションの開発](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/developing_ejb_applications/index)に関するページを参照してください。

* Hibernate アプリケーション - 開発者および管理者は JBoss EAP を使用して、Java Persistence API (JPA) または Hibernate アプリケーションを開発、デプロイすることができます。 Hibernate Core は、Java 言語のためのオブジェクト リレーショナル マッピング フレームワークです。 オブジェクト指向のドメイン モデルをリレーショナル データベースにマッピングするためのフレームワークが備わっているため、アプリケーションから直接データベースを操作する必要がありません。 Hibernate Entity Manager は、[JPA 2.1 仕様](https://www.jcp.org/en/jsr/overview)によって定義されたプログラミング インターフェイスとライフサイクル ルールを実装します。 このラッパーが Hibernate Annotations と連携することで、完成度の高い Hibernate Core 上に完全 (かつスタンドアロン) な JPA ソリューションが実装されます。 Hibernate の詳細については、[EAP 7.2 上の JPA](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/development_guide/java_persistence_api) または [EAP 7.3 上の Jakarta Persistence](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/development_guide/java_persistence_api) に関するページを参照してください。

#### <a name="red-hat-migration-toolkit-for-applications-mta"></a>Red Hat Migration Toolkit for Applications (MTA)
[Red Hat Migration Toolkit for Applications (MTA)](https://developers.redhat.com/products/mta/overview) は、Java アプリケーション サーバー用の移行ツールです。 他のアプリ サーバーから JBoss EAP に移行するには、このツールを使用します。 [Eclipse IDE](https://www.eclipse.org/ide/)、[Red Hat CodeReady Workspaces](https://developers.redhat.com/products/codeready-workspaces/overview)、[Visual Studio Code (VS Code)](https://code.visualstudio.com/docs/languages/java) for Java 用の IDE プラグインで動作します。  MTA は、次の機能を備えた無料のオープンソース ツールです。
* アプリケーションの分析を自動化する
* 作業量の見積もりを支援する
* コードの移行を促進する
* コンテナー化をサポートする
* Azure Workload Builder と連携する

### <a name="migrate-jboss-eap-from-on-premises-to-azure"></a>オンプレミスから Azure への JBoss EAP の移行
Azure Marketplace の JBoss EAP on RHEL プランを使用すると、Azure VM のインストールとプロビジョニングが 20 分かからずに行えます。 これらのプランには、[Azure Marketplace](https://azuremarketplace.microsoft.com/) からアクセスできます。

この Marketplace プランには、お客様の要件をサポートするために、EAP と RHEL のバージョンのさまざまな組み合わせが用意されています。 JBoss EAP は常に BYOS (サブスクリプション持ち込み) となりますが、RHEL OS については、BYOS か PAYG (従量課金制) かを選択することができます。 Azure Marketplace の JBoss EAP on RHEL プランでは、スタンドアロンの VM とクラスター化された VM のどちらかを選択できるようになっています。

* JBoss EAP 7.2 on RHEL 7.7 VM (PAYG)
* JBoss EAP 7.2 on RHEL 8.0 VM (PAYG)
* JBoss EAP 7.3 on RHEL 8.0 VM (PAYG)
* JBoss EAP 7.2 on RHEL 7.7 VM (BYOS)
* JBoss EAP 7.2 on RHEL 8.0 VM (BYOS)
* JBoss EAP 7.3 on RHEL 8.0 VM (BYOS)

これから Azure への移行に取り組む方のために、Azure Marketplace の各プランと共にクイックスタート テンプレートが用意されています。 これらのクイックスタートには、あらかじめ構築された Azure Resource Manager (ARM) テンプレートとスクリプトが含まれており、さまざまな構成とバージョンの組み合わせで JBoss EAP on RHEL をデプロイすることができます。 内容は次のとおりです。

* ロード バランサー (LB)
* 負荷分散と VM のためのプライベート IP
* 1 つのサブネットを含む仮想ネットワーク (VNET)
* VM 構成 (クラスターまたはスタンドアロン)
* サンプル Java アプリケーション

これらのテンプレートのソリューション アーキテクチャには以下が含まれます。

* スタンドアロンの RHEL VM 上の JBoss EAP
* 複数の RHEL VM にわたってクラスター化された JBoss EAP
* Azure 仮想マシン スケール セットを使用してクラスター化された JBoss EAP

#### <a name="linux-workload-migration-for-jboss-eap"></a>JBoss EAP の Linux ワークロードの移行
Azure Workload Builder を使用すると、オンプレミスの Java アプリを Azure に移行する際に、概念実証 (POC)、評価、移行プロセスがシンプルになります。 Workload Builder は Azure Migrate 検出ツールと連携し、JBoss EAP サーバーを識別します。 その後、JBoss EAP サーバー デプロイ用の Ansible プレイブックを動的に生成します。 他のアプリ サーバーから JBoss EAP にサーバーを移行するために、Red Hat MTA ツールが活用されます。 移行を簡略化するための手順は、次のとおりです。
* 評価 - Azure VM または仮想マシン スケール セットを使用する JBoss EAP クラスターを評価する
* アセスメント - アプリケーションとインフラストラクチャをスキャンする
* インフラストラクチャの構成 - ワークロード プロファイルを作成する
* デプロイとテスト - ワークロードをデプロイ、移行、テストする
* デプロイ後の構成 - データ、監視、セキュリティ、バックアップなどを統合する

## <a name="server-configuration-choice"></a>サーバー構成の選択

RHEL VM のデプロイでは、PAYG と BYOS のどちらかを選択できます。 [Azure Marketplace](https://azuremarketplace.microsoft.com) から入手したイメージでは、PAYG が既定となります。 独自の RHEL OS イメージを所有している場合は、BYOS タイプの RHEL VM をデプロイしてください。 ご利用の RHSM アカウントに Cloud Access による BYOS エンタイトルメントがあることを確認したうえで、VM または仮想マシン スケール セットをデプロイしてください。

JBoss EAP は強力な管理機能を備えると共に、そのアプリケーションに機能と API を提供します。 これらの管理機能は、どの動作モードを使用して JBoss EAP を起動しているかによって異なります。 これは RHEL と Windows Server でサポートされます。 JBoss EAP には、個別のインスタンスを管理するためのスタンドアロン サーバー動作モードが用意されています。 さらに、インスタンスのグループを単一の制御ポイントから管理するためのマネージド ドメイン動作モードも用意されています。 注:Microsoft Azure では、高可用性 (HA) 機能が Azure インフラストラクチャ サービスによって管理されるため、JBoss EAP のマネージド ドメインはサポートされません。 JBoss EAP 環境へのパスは、 *EAP_HOME* という名前の環境変数を使用して表されます。

**JBoss EAP をスタンドアロンのサーバーとして起動する** - EAP サービスをスタンドアロン モードで起動するには、次のコマンドを使用します。

```
$EAP_HOME/bin/standalone.sh
```
    
この起動スクリプトでは、EAP_HOME/bin/standalone.conf ファイルを使用して、いくつかの既定の初期設定 (JVM オプションなど) を行います。 設定は、このファイルでカスタマイズできます。 既定では、standalone.xml 構成ファイルを使用してスタンドアロン モードで JBoss EAP を起動しますが、別のモードを使用して起動することもできます。 使用可能なスタンドアロン構成ファイルとその使用方法について詳しくは、[EAP 7.2 のスタンドアロン サーバー構成ファイル](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)または [EAP 7.3 のスタンドアロン サーバー構成ファイル](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/jboss_eap_management#standalone_server_configuration_files)に関するセクションを参照してください。 別の構成で JBoss EAP を起動するには、--server-config 引数を使用します。 次に例を示します。
    
 ```
 $EAP_HOME/bin/standalone.sh --server-config=standalone-full.xml
 ```
    
起動スクリプトの引数とその目的を網羅した一覧については、--help 引数を使用するか、[EAP 7.2 におけるサーバー実行時引数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)または [EAP 7.3 におけるサーバー実行時引数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuration_guide/reference_material#reference_of_switches_and_arguments_to_pass_at_server_runtime)に関するセクションを参照してください。
    
JBoss EAP は、クラスター モードで動作させることもできます。 詳細については、[EAP 7.2 におけるクラスターの概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.2/html/configuring_messaging/clusters_overview)または [EAP 7.3 におけるクラスターの概要](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html/configuring_messaging/clusters_overview)に関するページを参照してください。 JBoss EAP のクラスター メッセージングを使用すると、JBoss EAP のメッセージング サーバーをグループ化して、メッセージ処理の負荷を分担させることができます。 クラスター内の各アクティブ ノードがアクティブな JBoss EAP メッセージング サーバーとして、それぞれが分担するメッセージを管理し、固有の接続を処理します。

## <a name="support-and-subscription-notes"></a>サポートとサブスクリプションに関する注記
これらのクイックスタート テンプレートの提供形態は次のとおりです。 

- RHEL OS は、Red Hat Gold Image モデルによる BYOS または PAYG として提供されます
- JBoss EAP は BYOS としてのみ提供されます

#### <a name="using-rhel-os-with-payg-model"></a>PAYG モデルでの RHEL OS の使用

これらのクイックスタート テンプレートでは、Azure ギャラリーのオンデマンド RHEL 7.7 または 8.0 PAYG イメージが既定で使用されます。 PAYG イメージでは、コンピューティング、ネットワーク、ストレージの標準コストとは別に、時間単位の RHEL サブスクリプション料が課金されます。 同時に、そのインスタンスは、ご利用の Red Hat サブスクリプションに登録されます。 つまり、自分に付与されたいずれかのエンタイトルメントが使用されます。 この PAYG イメージによって "二重請求" が発生します。 この問題は、独自の RHEL イメージを作成することで回避できます。 詳細については、Red Hat Knowledge Base (KB) で、[Microsoft Azure 向けに RHEL VM をプロビジョニングする方法](https://access.redhat.com/articles/uploading-rhel-image-to-azure)に関する記事を参照してください。 または、[Red Hat Cloud Access](https://access.redhat.com/) の RHEL Gold Image をアクティブにしてください。

PAYG VM 価格の詳細については、[Red Hat Enterprise Linux の価格](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)に関するページを参照してください。 RHEL を PAYG モデルで使用するには、[RHEL 7.7 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux77-ARM) または [RHEL 8.0 Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/RedHat.RedHatEnterpriseLinux80-ARM) の支払方法が指定された Azure サブスクリプションが必要です。 これらのプランの支払方法が Azure サブスクリプションで指定されている必要があります。

#### <a name="using-rhel-os-with-byos-model"></a>BYOS モデルでの RHEL OS の使用

RHEL OS に BYOS を使用するには、Azure で RHEL OS を使用するためのエンタイトルメントが付与された有効な Red Hat サブスクリプションを所有している必要があります。 このクイックスタート テンプレートをデプロイする前に次の前提条件を満たしてください。

1. ご利用の Red Hat サブスクリプションに RHEL OS と JBoss EAP のエンタイトルメントが付与されていることを確認します。
2. ご利用の Azure サブスクリプション ID で RHEL BYOS イメージを使用することを承認します。 [Red Hat Subscription Management (RHSM) のドキュメント](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/con-enable-subs)に従って、これらの手順が含まれているプロセスを実行します。

    2.1 Red Hat Cloud Access ダッシュボードで、Microsoft Azure をプロバイダーとして有効にします。

    2.2 Azure サブスクリプション ID を追加します。

    2.3 Microsoft Azure で新しい製品の Cloud Access を有効にします。
    
    2.4 Azure サブスクリプションの Red Hat Gold Image をアクティブにします。 詳細については、「[Cloud Access 用のサブスクリプションの有効化と管理](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure)」の章を参照してください。

    2.5 Azure サブスクリプションで Red Hat Gold Image が使用できる状態になるまで待ちます。 通常これらの Gold Image は、申請から 3 時間以内に利用できるようになります。
    
3. RHEL BYOS イメージに対する Azure Marketplace の使用条件に同意します。 このプロセスは、以下に示す Azure コマンド ライン インターフェイス (CLI) コマンドで実行できます。 詳細については、[Azure における RHEL BYOS Gold Image](https://docs.microsoft.com/azure/virtual-machines/workloads/redhat/byos) に関するドキュメントを参照してください。 必ず最新バージョンの Azure CLI を実行してください。

    3.1 Azure CLI セッションを起動し、ご利用の Azure アカウントで認証を行います。 詳細については、[Azure CLI でのサインイン](https://docs.microsoft.com/cli/azure/authenticate-azure-cli)に関するページを参照してください。 最新バージョンの Azure CLI が実行されていることを確認してから先に進んでください。

    3.2 次の CLI コマンドを実行して、ご利用のサブスクリプションで RHEL BYOS イメージが使用できることを確認します。 何も結果が返されない場合は、手順 2. を参照して、Azure サブスクリプションの RHEL BYOS イメージがアクティブになっていることを確認してください。
   
   ```
   az vm image list --offer rhel-byos --all
   ```

    3.3 RHEL 7.7 BYOS および RHEL 8.0 BYOS に対する Marketplace の使用条件に同意します。それぞれ次のコマンドを実行してください。
   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm77
   ``` 

   ```
   az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm8
   ``` 
   
4. ご利用のサブスクリプションで、Azure 仮想マシンに RHEL 7.7 または 8.0 BYOS をデプロイする準備はこれで完了です。

#### <a name="using-jboss-eap-with-byos-model"></a>BYOS モデルでの JBoss EAP の使用

Azure では、JBoss EAP が BYOS モデルでのみ提供されます。 このテンプレートをデプロイするときは、有効な EAP エンタイトルメントが付与された RHSM プール ID と共に、RHSM の資格情報を指定する必要があります。 EAP のエンタイトルメントがない場合は、先に進む前に [JBoss EAP 評価サブスクリプション](https://access.redhat.com/products/red-hat-jboss-enterprise-application-platform/evaluation)を入手してください。

## <a name="how-to-consume"></a>使用方法

テンプレートは、次の 3 とおりの方法でデプロイできます。

- PowerShell を使う - 次のコマンドを実行してテンプレートをデプロイします (Azure PowerShell のインストールと構成に関する詳細については、[Azure PowerShell](https://docs.microsoft.com/powershell/azure/) に関するページを参照してください)。

  ```
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri <raw link to the template which can be obtained from github>
  ```
    
- Azure CLI を使う - 次のコマンドを実行してテンプレートをデプロイします (Azure CLI のインストールと構成について詳しくは、[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) に関するページを参照してください)。

  ```
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  ```

  ```
  az group deployment create --resource-group <my-resource-group> --template-uri <raw link to the template which can be obtained from github>
  ```

- Azure portal を使う - 以下のセクションで取り上げる " *Azure クイックスタート テンプレート* " に移動することで、Azure portal へのデプロイを行うことができます。 クイックスタートに移動したら、 **[Deploy to Azure]\(Azure へのデプロイ\)** または **[Browse on GitHub]\(GitHub で参照\)** ボタンをクリックします。

## <a name="azure-quickstart-templates"></a>Azure クイックスタート テンプレート

デプロイの要件を満たす組み合わせの JBoss EAP on RHEL クイックスタート テンプレートを 1 つ使用して開始できます。 提供されているクイックスタート テンプレートは次のとおりです。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/"> JBoss EAP on RHEL (stand-alone VM)</a> - RHEL 7.7 または 8.0 VM 上で実行される JBoss EAP 7.2 または 7.3 に、"JBoss-EAP on Azure" という名前の Web アプリケーションをデプロイします。

* <a href="https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/"> JBoss EAP on RHEL (clustered, multi-VMs)</a> - "n" 個の RHEL 7.7 または 8.0 VM 上で実行される JBoss EAP 7.2 または 7.3 クラスターに、"eap-session-replication" という名前の Web アプリケーションをデプロイします。 "n" の値は、ユーザーが決定します。 すべての VM は、ロード バランサーのバックエンド プールに追加されます。

* <a href="https://azure.microsoft.com/en-us/resources/templates/jboss-eap-clustered-vmss-rhel/"> JBoss EAP on RHEL (clustered, Virtual Machine Scale Set)</a> - RHEL 7.7 または 8.0 の Virtual Machine Scale Sets インスタンス上で実行される JBoss EAP 7.2 または 7.3 クラスターに、"eap-session-replication" という名前の Web アプリケーションをデプロイします。

## <a name="resource-links"></a>リソース リンク:

* [Azure ハイブリッド特典](https://docs.microsoft.com/azure/virtual-machines/windows/hybrid-use-benefit-licensing)
* [Azure App Service 向けの Java アプリを構成する](https://docs.microsoft.com/azure/app-service/configure-language-java)
* [JBoss EAP on Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [JBoss EAP on Azure App Service Linux](https://docs.microsoft.com/azure/app-service/quickstart-java) のクイックスタート
* [JBoss EAP を Azure App Service にデプロイする方法](https://github.com/JasonFreeberg/jboss-on-app-service)のチュートリアル

## <a name="next-steps"></a>次のステップ

* [JBoss EAP 7.2](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.2/) の詳細を確認する
* [JBoss EAP 7.3](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.3/) の詳細を確認する
* [Red Hat Subscription Management](https://access.redhat.com/products/red-hat-subscription-management) の詳細を確認する
* [Red Hat on Azure](https://aka.ms/rhel-docs) に関する Microsoft ドキュメント
* [Azure Marketplace から RHEL の VM または仮想マシン スケール セットに JBoss EAP](https://aka.ms/AMP-JBoss-EAP) をデプロイする
* [Azure クイックスタートから RHEL の VM または仮想マシン スケール セットに JBoss EAP](https://aka.ms/Quickstart-JBoss-EAP) をデプロイする
