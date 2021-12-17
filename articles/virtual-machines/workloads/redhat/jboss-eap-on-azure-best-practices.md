---
title: Red Hat JBoss EAP on Azure のベスト プラクティス
description: このガイドでは、Microsoft Azure 内で Red Hat JBoss Enterprise Application Platform を使用するためのベスト プラクティスについて説明します。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 195a0bfa-dff1-429b-b030-19ca95ee6abe
ms.date: 06/08/2021
ms.openlocfilehash: aaaa6fb269178954ade464745c6c640845457612
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289226"
---
# <a name="red-hat-jboss-eap-on-azure-best-practices"></a>Azure での Red Hat JBoss EAP のベスト プラクティス

この Red Hat JBoss EAP on Azure のベスト プラクティス ガイドでは、Microsoft Azure 上で Red Hat JBoss Enterprise Application Platform (EAP) を使用するためのベスト プラクティスについて説明します。 JBoss EAP は、Azure 内で JBoss EAP を実行するためにサポートされている特定の構成内であれば、Microsoft Azure プラットフォームと共に使用することができます。 クラスター化された JBoss EAP 環境を手動で構成する場合は、Azure 内で JBoss EAP クラスタリング機能を使用するために必要な特定の構成を適用します。 このガイドでは、Microsoft Azure 内で JBoss EAP を使用するときのサポートされる構成について詳しく説明します。

JBoss EAP は、Web プロファイルと完全なプラットフォームの両方の仕様に対応する、Jakarta Enterprise Edition (EE) 8 互換実装です。 また、Java EE 8 仕様の認定実装でもあります。 JBoss EAP のメジャー バージョンは、WildFly コミュニティ プロジェクトの機能完全性が必要なレベルに達すると、ある時点でそのコミュニティ プロジェクトからフォークされます。 その後、テストと製品化の期間が長くなり、JBoss EAP が運用環境向けに安定化、認定、強化されます。 JBoss EAP メジャー バージョンの有効期間中、選択した機能がコミュニティ プロジェクトからチェリーピックされ、移植されることがあります。 これらの機能は、同じメジャー バージョン ファミリ内のマイナー リリースを強化する seFries 機能内で使用できます。

## <a name="supported-and-unsupported-configurations-of-jboss-eap-on-azure"></a>Red Hat JBoss EAP on Azure のサポート対象の構成とサポート対象外の構成

EAP を使用できるオペレーティング システム (OS)、Java プラットフォーム、その他のサポートされているプラットフォームの詳細については、[JBoss EAP のサポート対象の構成](https://access.redhat.com/articles/2026253)に関するドキュメントを参照してください。

Red Hat Cloud Access プログラムを使用すると、JBoss EAP サブスクリプションを使用して、お使いの Azure 仮想マシンに JBoss EAP をインストールすることができます。これは、Microsoft Azure Marketplace からのオンデマンド従量課金制 (PAYG) オペレーティング システムです。 仮想マシンのオペレーティング システム サブスクリプション (この場合は Red Hat Enterprise Linux (RHEL)) は、JBoss EAP サブスクリプションとは別のものです。 Red Hat Cloud Access は、Microsoft Azure などの Red Hat 認定クラウド インフラストラクチャ プロバイダー上で JBoss EAP のサポートを提供する、Red Hat サブスクリプション機能です。 Red Hat Cloud Access を使用すると、シンプルかつコスト効率の高い方法で、従来のオンプレミス サーバーとパブリック クラウドベース リソースとの間でサブスクリプションを移行できます。

詳細については、[カスタマー ポータル上の Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) に関するページをご覧ください。 Azure Marketplace 上の PAYG プランには、Red Hat Cloud Access は必要ありません。 

Red Hat JBoss EAP リリースはすべて、市場をリードするさまざまなオペレーティング システム、Java 仮想マシン (JVM)、データベースの組み合わせでテストおよびサポートされています。 Red Hat は、サブスクリプション契約に従って、サポートされている構成とテスト済みの統合に対して、運用と開発の両方のサポートを提供しています。 サポートは、物理環境と仮想環境の両方に適用されます。 [JBoss EAP のサポートされている構成](https://access.redhat.com/articles/2026253)に関するページで、サポートされている Java Development Kit (JDK) ベンダーやバージョンなど、上記のオペレーティング システムの制限以外の情報をご確認ください。 さまざまな JBoss EAP バージョン (7.0、7.1、7.2、7.3 など) のサポートされている構成に関する情報が提供されています。 サポートされている RHEL オペレーティングシステム、VM の最小容量の要件、その他のサポートされている Red Hat 製品の情報については、「[Microsoft Azure の製品/構成マトリックス](https://access.redhat.com/articles/product-configuration-for-azure)」をご確認ください。 Microsoft Azure 内での動作が認証された Red Hat ソフトウェア製品については、[認定クラウド プロバイダー/Microsoft Azure](https://access.redhat.com/ecosystem/cloud-provider/2068823) に関するページをご覧ください。

Microsoft Azure 環境内で JBoss EAP を使用する場合、次のような、サポートされていない機能がいくつかあります。

- **マネージド ドメイン** - 1 つのコントロール ポイントから複数の JBoss EAP インスタンスを管理できるようにします。 JBoss EAP マネージド ドメインは、現在、Microsoft Azure ではサポートされていません。 サポートされているのは、スタンドアロンの JBoss EAP サーバー インスタンスのみです。 Azure 内でのスタンドアロンの JBoss EAP サーバーを使用した JBoss EAP クラスター構成はサポートされています。

- **共有ストアを使用した ActiveMQ Artemis 高可用性 (HA)** - Microsoft Azure 内では、Artemis 共有ストアを使用した JBoss EAP メッセージング HA はサポートされていません。

- **`mod_custer` アドバタイズ** - JBoss EAP を Undertow `mod_cluster` プロキシのロードバランサーとして使用することはできません。mod_cluster アドバタイズ機能は、Azure User Datagram Protocol (UDP) マルチキャストの制限のためサポートされていません。

## <a name="other-features-of-jboss-eap-on-azure"></a>JBoss EAP on Azure のその他の機能

JBoss EAP には、HA クラスタリング、メッセージング、分散キャッシュなどの機能に関して、事前構成済みのオプションが用意されています。 加えて、JBoss EAP が備える各種の API およびサービスを使用して、アプリケーションを作成、デプロイ、実行することもできます。

- **Jakarta EE 互換性** - Jakarta EE 8 は、Web プロファイルと完全なプラットフォームの両方の仕様に対応しています。

- **Java EE 準拠** - Java EE 8 は、Web プロファイルと完全なプラットフォームの両方の仕様に対して認定されています。

- **管理コンソールと管理 CLI** - スタンドアロン サーバー管理インターフェイスを対象としています。 管理 CLI には、管理タスクをスクリプト化および自動化できるバッチ モードも含まれています。 JBoss EAP XML 構成ファイルを直接編集することはお勧めしません。

- **簡略化されたディレクトリ レイアウト** - モジュール ディレクトリには、すべてのアプリケーション サーバー モジュールが含まれています。 スタンドアロン ディレクトリには、スタンドアロン デプロイの成果物と構成ファイルが含まれています。

- **モジュール クラスの読み込みメカニズム** - モジュールの読み込みとアンロードがオンデマンドで行われます。 モジュール クラスの読み込みメカニズムにより、パフォーマンスが向上し、セキュリティ上のメリットを得られるほか、起動時間と再起動時間が短縮されます。

- **効率的なデータソース管理** -他のサービスと同様にデータベース ドライバーが展開されます。 また、管理コンソールと管理 CLI を使用して、データソースが作成および管理されます。

- **統合セキュリティ フレームワーク** - Elytron は、スタンドアロン サーバーのアクセスを管理および構成できる単一の統合フレームワークを提供します。 また、JBoss EAP サーバーにデプロイされたアプリケーションのセキュリティ アクセスの構成にも使用できます。

## <a name="creating-your-microsoft-azure-environment"></a>Microsoft Azure 環境の作成

お使いの Microsoft Azure 環境内でご自身の JBoss EAP インスタンスをホストする VM を作成します。 Standard_A2 以上の Azure VM サイズを使用してください。 Azure オンデマンド PAYG Premium イメージを使用して、ご自身の VM を作成するか、独自の VM を手動で作成することができます。 たとえば、次のように RHEL VM をデプロイできます。

* Azure 内でオンデマンド Marketplace RHEL イメージを使用する - Azure Marketplace 内にオファーが複数あります。ここで、JBoss EAP を設定する RHEL VM を選択できます。 [Azure Marketplace からの RHEL 8 VM のデプロイ](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/deploying_red_hat_enterprise_linux_8_on_public_cloud_platforms/assembly_deploying-a-rhel-image-as-a-virtual-machine-on-microsoft-azure_cloud-content)に関するページをご覧ください。 Azure Marketplace 内で RHEL OS ライセンスを選択するオプションは 2 つあります。 Red Hat Gold Image モデルを使用して、PAYG またはサブスクリプション持ち込み (BYOS) のいずれかを選択します。 PAYG プランを使用して RHEL VM をデプロイした場合、結果として生じるデプロイは、ご自身の JBoss EAP サブスクリプションの詳細のみを使用して、Red Hat サブスクリプションにサブスクライブされることに注意してください。

* [Azure 用に RHEL イメージを手動で作成およびプロビジョニングする](https://access.redhat.com/articles/uploading-rhel-image-to-azure)。 RHEL の各メジャー バージョンのマイナー バージョンの中で最新のもの使用してください。

Microsoft Windows Server VM については、[Microsoft Azure のドキュメント](../../windows/overview.md)で、Azure 内での Windows Server VM の作成に関する説明を参照してください。

## <a name="jboss-eap-installation"></a>JBoss EAP のインストール

> [!NOTE]
>  Azure Marketplace を通して RHEL オファー上で JBoss EAP を使用している場合、JBoss EAP は自動的にインストールされ、Azure 環境用に構成されます。

Microsoft Azure 上にデプロイされている RHEL VM に手動で EAP をデプロイする場合は、以下の手順が適用されます。

お使いの VM が設定されたら、JBoss EAP をインストールできます。 Red Hat のサポート技術情報 (KB) およびサブスクリプション リソース向けの一元化されたプラットフォームである、[Red Hat カスタマー ポータル](https://access.redhat.com)へのアクセスが必要です。 EAP サブスクリプションをお持ちでない場合は、[個人用 Red Hat Developer サブスクリプション](https://developers.redhat.com/register)を使用して、無料の開発者サブスクリプションにサインアップします。 登録したら、[Red Hat カスタマー ポータル](https://access.redhat.com/management/)の [サブスクリプション] セクションで、資格情報 (プール ID) を探します。 このサブスクリプションは運用環境での使用を目的としていないことに注意してください。

JBoss EAP 環境へのパスは、変数 *EAP_HOME* を使用して表されています。 この変数を、お使いの JBoss EAP 環境への実際のパスに置き換えてください。

> [!IMPORTANT]
> JBoss EAP をインストールする方法は複数あります。 それぞれの方法が、状況に応じて最適な方法で使用されます。 Microsoft Azure Marketplace から RHEL オンデマンド VM を使用している場合は、ZIP またはインストーラーを使用して JBoss EAP をインストールします。 ** RHEL オンデマンド仮想マシンを Red Hat Subscription Management (RHSM) に登録しないでください。その仮想マシンには PAYG の課金方法が使用されているため、2 回課金されてしまいます。

* **ZIP インストール** - ZIP アーカイブは、サポートされているすべての OS 上でのインストールに適しています。 インスタンスを手動で抽出する場合は、ZIP インストール方法を使用する必要があります。 ZIP インストールにより、JBoss EAP の既定のインストールと、インストール後に実行されるすべての構成が提供されます。 JBoss Operations Network (ON) サーバーを使用して、JBoss EAP パッチをデプロイおよびインストールする予定がある場合は、ZIP インストール方法を使用して、ターゲット JBoss EAP インスタンスをインストールする必要があります。 詳細については、「[ZIP インストール](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#zip_installation)」を参照してください。

* **JAR インストーラー** - JAR インストーラーは、コンソール内で、またはグラフィカル ウィザードとして実行できます。 どちらのオプションにも、サーバー インスタンスをインストールして構成するための詳細な手順が示されます。 JAR インストーラーは、JAR インストーラーは、サポートされているすべてのプラットフォームに JBoss EAP をインストールするときに推奨される方法です。 詳細については、[JAR インストーラーでのインストール](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#installer_installation)に関する記事をご確認ください。

* **RPM インストール** -RHEL 6、rhel 7、rhel 8 のサポートされている環境上で RPM パッケージを使用して、JBoss EAP をインストールできます。 Azure におけるお使いの RHEL VM 上での EAP のインストールを自動化することを計画している場合は、RPM インストール方法が最適です。 JBoss EAP の RPM インストールにより、JBoss EAP をサービスとして実行するために必要なものすべてがインストールされます。 詳細については、「[RPM インストール](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#rpm_installation)」を参照してください。

## <a name="other-offers-by-azure-and-red-hat"></a>Azure と Red Hat によるその他のオファー

Red Hat と Microsoft は提携して、Azure ソリューション テンプレートのセットを Azure Marketplace に導入し、Azure への移行の確固たる出発点を提供しています。 オファーの一覧が記載されているドキュメントで確認し、適切な環境を選択してください。

### <a name="azure-marketplace-offers"></a>Azure Marketplace オファー

これらのオファーには、[Azure Marketplace](https://aka.ms/AMP-JBoss-EAP) からアクセスできます。

この Marketplace オファーには、JBoss EAP と RHEL のバージョンのさまざまな組み合わせと、柔軟なサポート サブスクリプション モデルが含まれています。 JBoss EAP は常に BYOS ですが、RHEL OS の場合は、BYOS または PAYG を選択できます。 Azure Marketplace オファーには、スタンドアロン VM、クラスター化 VM、クラスター化された仮想マシン スケール セットとしての JBoss EAP on RHEL のプラン オプションが含まれています。 

6 つのプランは次のとおりです。

- JBoss EAP 7.3 on RHEL 8.3 スタンドアロン VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 スタンドアロン VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化 VM (BYOS)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (PAYG)
- JBoss EAP 7.3 on RHEL 8.3 クラスター化仮想マシン スケール セット (BYOS)

:::image type="content" source="./media/red-hat-marketplace-image-1.png" alt-text="GitHub デプロイ リンクを使用して Red Hat イメージをデプロイするオプションを示す画像。":::

### <a name="azure-quickstart-templates"></a>Azure のクイック スタート テンプレート

Azure Marketplace のオファーと共に、EAP on Azure を体験するために使用できるクイックスタート テンプレートがあります。 これらのクイックスタートには、あらかじめ構築された ARM テンプレートとスクリプトが含まれており、さまざまな構成とバージョンの組み合わせで JBoss EAP on Azure をデプロイすることができます。 

ソリューションのアーキテクチャには次のものが含まれます。

* JBoss EAP on RHEL スタンドアロン VM
* JBoss EAP on RHEL クラスター化 VM
* JBoss EAP on RHEL クラスター化仮想マシン スケール セット

    :::image type="content" source="./media/red-hat-marketplace-image.png" alt-text="Azure Marketplace で使用できる Red Hat オファーを示す画像。":::

RHEL 7.7 と 8.0、および JBoss EAP 7.2 と 7.3 から選択できます。 次のいずれかからデプロイの組み合わせを選択することができます。

- JBoss EAP 7.2 on RHEL 7.7
- JBoss EAP 7.2 on RHEL 8.0
- JBoss EAP 7.3 on RHEL 8.0

開始するには、デプロイの目標を満たす JBoss EAP on RHEL の組み合わせのクイックスタート テンプレートを選択します。 提供されているクイックスタート テンプレートは次のとおりです。

* [JBoss EAP on RHEL スタンドアロン VM](https://azure.microsoft.com/resources/templates/jboss-eap-standalone-rhel/) - Azure テンプレートによって、JBoss-EAP on Azure という名前の Web アプリケーションが、RHEL (バージョン 7.7 または 8.0) VM 上で動作している JBoss EAP (バージョン 7.2 または 7.3) 上にデプロイされます。

* [JBoss EAP on RHEL クラスター化 VM](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-multivm-rhel/) - Azure テンプレートによって、eap-session-replication という Web アプリケーションが、"n" 個の RHEL VM 上で動作している JBoss EAP クラスター上にデプロイされます。 "n" は、最初に設定した VM の数です。 すべての VM は、ロード バランサーのバックエンド プールに追加されます。

* [ JBoss EAP on RHEL クラスター化仮想マシン スケール セット](https://azure.microsoft.com/resources/templates/jboss-eap-clustered-vmss-rhel/) - Azure テンプレートによって、eap-session-replication という Web アプリケーションが、RHEL 7.7 または 8.0 仮想マシン スケール セット インスタンス上で動作している JBoss EAP 7.2 または 7.3 クラスター上にデプロイされます。

## <a name="configuring-jboss-eap-to-work-on-cloud-platforms"></a>クラウド プラットフォーム上で動作する JBoss EAP の構成

ご自身の VM 内に JBoss EAP をインストールしたら、サービスとして実行するように JBoss EAP を構成できます。 サービスとして実行する JBoss EAP の構成は、JBoss EAP のインストール方法と VM OS の種類によって異なります。 JBoss EAP の RPM インストールにより、JBoss EAP をサービスとして実行するために必要なものすべてがインストールされることに注意してください。 詳細については、「[サービスとして実行する JBoss EAP の構成](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/installation_guide/index#configuring_jboss_eap_to_run_as_a_service)」を参照してください。

### <a name="starting-and-stopping-jboss-eap"></a>JBoss EAP の起動と停止

#### <a name="starting-jboss-eap"></a>JBoss EAP の起動

JBoss EAP は、RHEL および Windows Server でサポートされ、スタンドアロン サーバーの運用モードでのみ実行されます。 JBoss EAP を起動するための特定のコマンドは、基になるプラットフォームによって異なります。 サーバーは、最初は中断状態で起動され、必要なすべてのサービスが開始するまで要求を受け入れません。 この時点で、サーバーは通常の実行状態になり、要求の受け入れを開始できます。 JBoss EAP をスタンドアロン サーバーとして起動するコマンドを次に示します。

- (ZIP またはインストーラーを使用してインストールされた) JBoss EAP を、RHEL VM 内でスタンドアロン サーバーとして起動するコマンド:
    ```
    $EAP_HOME/bin/standalone.sh
    ```
- Windows Server の場合は、`EAP_HOME\bin\standalone.bat` スクリプトを使用して、JBoss EAP をスタンドアロン サーバーとして起動します。
- JBoss EAP の起動は、RPM インストールの場合、ZIP や JAR インストーラーによるインストールとは異なります。 たとえば、RHEL 7 以降の場合は、次のコマンドを使用します。
    ```
    systemctl start eap7-standalone.service
    ```
(ZIP またはインストーラーを使用してインストールされた) JBoss EAP の起動に使用されるスタートアップ スクリプトによって、一部の既定の設定 (JVM オプションなど) が、`EAP_HOME/bin/standalone.conf` ファイル (Windows Server の場合は `standalone.conf.bat`) を使用して設定されます。 このファイル内の設定をカスタマイズします。 JBoss EAP によって既定で使用されるのは `standalone.xml` 構成ファイルですが、JBoss EAP は、別の構成ファイルを使って起動することもできます。 RPM を使用してインストールされた JBoss EAP の起動に使用される既定の構成ファイルを変更するには、`/etc/opt/rh/eap7/wildfly/eap7-standalone.conf` を使用します。 WildFly バインド アドレスなどの他の構成変更を行うには、同じ eap7-standalone.conf ファイルを使用します。

使用可能なスタンドアロン構成ファイルと、その使用方法の詳細については、「[スタンドアロン サーバー構成ファイル](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#standalone_server_configuration_files)」をご覧ください。

別の構成で JBoss EAP を起動するには、--server-config 引数を使用します。 使用可能なスタートアップ スクリプトの引数の一覧と、その目的を確認するには、--help 引数を使用するか、[サーバー ランタイムの引数](https://access.redhat.com/documentation/en/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#reference_of_switches_and_arguments_to_pass_at_server_runtime)に関する記事をご覧ください

#### <a name="stopping-jboss-eap"></a>JBoss EAP の停止

JBoss EAP を停止する方法は、開始された方法によって異なります。 JBoss EAP が開始されたターミナルで `Ctrl+C` を押して、JBoss EAP の対話型インスタンスを停止します。 JBoss EAP のバックグラウンド インスタンスを停止するには、管理 CLI を使用して、実行中のインスタンスに接続し、サーバーをシャットダウンします。 詳細については、[JBoss EAP の停止](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#stopping_jboss_eap)に関する記事をご覧ください。

JBoss EAP の停止は、RPM インストールの場合、ZIP やインストーラーによるインストールとは異なります。 JBoss EAP の RPM インストールを停止するコマンドは、起動する動作モードと、実行している RHEL バージョンによって異なります。 スタンドアロン モードは、Azure 内でのみサポートされているモードです。 

- たとえば、RHEL 7 以降の場合は、次のコマンドを使用します。
    ```
    systemctl stop eap7-standalone.service
    ```
新しい要求を受け入れずに、JBoss EAP を正常に中断またはシャットダウンして、アクティブな要求を正常に完了することもできます。 中断されたサーバーは、シャットダウンしたり、実行中の状態に戻したりできます。また、メンテナンスを行うために、中断状態のままにしておくこともできます。 サーバーが中断されている間も、管理要求は引き続き処理されます。 サーバーを中断および再開するには、管理コンソールまたは管理 CLI を使用します。

### <a name="configuring-jboss-eap-subsystems-to-work-on-cloud-platforms"></a>クラウド プラットフォーム上で動作する JBoss EAP サブシステムの構成

JBoss EAP にデプロイされたアプリケーションに公開されている API と機能の多くが、サブシステムで構成されています。 これらのサブシステムは、アプリケーションの目標に応じてさまざまな動作を提供するように管理者が構成できます。 サブシステムの詳細については、[JBoss EAP サブシステム](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/configuration_guide/index#jboss_eap_subsystems)に関する記事をご覧ください。

JBoss EAP サブシステムの中には、クラウド プラットフォーム上で正しく動作するための構成が必要なものがあります。 構成が必要なのは、JBoss EAP サーバーが、クラウド VM のプライベート IP アドレスにバインドされているためです。 プライベート IP は、クラウド プラットフォーム内からのみ表示されます。 あるサブシステムについては、プライベート IP アドレスを、クラウドの外部から参照できるサーバーのパブリック IP アドレスにマップする必要があります。 これらのサブシステムを変更する方法の詳細については、「[クラウド プラットフォーム上で動作する JBoss EAP サブシステムの構成](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#configuring_subsystems_for_cloud_platforms)」を参照してください

## <a name="using-jboss-eap-high-availability-in-microsoft-azure"></a>Microsoft Azure 内での JBoss EAP 高可用性の使用

Azure では、UDP マルチキャストに基づく JGroups 検出プロトコルがサポートされていません。 既定では UDP スタックが JGroups によって使用されていますが、UDP は Azure ではサポートされていません。このため、これは必ず TCP に変更してください。 TCPPING、JDBC_PING などの他の JGroups 検出プロトコルを使用できますが、Azure 用に開発された共有ファイル検出プロトコル *Azure_PING* を使用することをお勧めします。

Microsoft Azure ストレージ アカウント内では、*AZURE_PING* によって共通の BLOB コンテナーが使用されます。 AZURE_PING が使用できる BLOB コンテナーがまだない場合は、ご自身の VM がアクセスできるものを作成してください。 詳細については、「[Microsoft Azure 内での JBoss EAP 高可用性を構成](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/using_jboss_eap_in_microsoft_azure/index#using_jboss_eap_high_availability_in_microsoft_azure)」を参照してください。

負荷分散環境で JBoss EAP を構成します。 すべてのバランサーとワーカーが、ご自身の内部 Microsoft Azure Virtual Network (VNet) 内のアクセス可能な IP アドレスにバインドされていることを確認します。 負荷分散構成の詳細については、「[Microsoft Azure で Red Hat Enterprise Linux 7.4 (およびそれ以降) の高可用性クラスターをインストールして構成する](https://access.redhat.com/articles/3252491)」を参照してください。

## <a name="other-best-practices"></a>その他のベスト プラクティス

- VM 上の JBoss EAP セットアップの管理者として、お使いの VM のセキュリティを確保することが重要です。 これにより、ご自身のゲストおよびホストの OS が、悪意のあるソフトウェアに感染するリスクが大幅に軽減されます。 ご自身の VM をセキュリティで保護すると、JBoss EAP に対する攻撃と、JBoss EAP 上でホストされているアプリケーションの誤動作が少なくなります。 Azure VM へのアクセスは、[Azure Policy](https://azure.microsoft.com/services/azure-policy/) や、[Azure ロールベースのアクセス制御 (RBAC)](../../../role-based-access-control/overview.md) の [Azure 組み込みロール](../../../role-based-access-control/built-in-roles.md)などの機能を使用して制御します。 お使いの VM をマルウェアから保護するには、Microsoft Antimalware または Microsoft パートナーのエンドポイント保護ソリューションをインストールし、お使いのマルウェア対策ソリューションを [Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/) と統合して、ご自身の保護の状態を監視します。 RHEL VM では、ポート フォワーディングをブロックし、ルート ログインをブロックすることで保護できます。これは、*/ で無効にすることができます


- 環境変数を使用して、JBoss EAP on Azure VM を簡単かつスムーズに使用できるようにします。 たとえば、EAP_HOME を使用して、JBoss EAP 環境へのパスを表すことができます。これは数回使用されます。 このような場合は、環境変数が便利です。 環境変数は、一般的なサービス構成および Web アプリケーション シークレット処理手段でもあります。 export コマンドを使用してシェルから環境変数が設定されている場合、ユーザーのセッションが終了すると、変数の存在も終了します。 セッション間で変数を保持する必要がある場合は、これが問題になります。 ユーザーの環境に対して環境を永続化するには、ユーザーのプロファイル スクリプトから変数をエクスポートします。 export コマンドは、bash_profile に保持する環境変数ごとに追加します。 VM にアクセスできるすべてのユーザーに対して、永続的なグローバル環境変数を設定する必要がある場合は、それを既定のプロファイルに追加できます。 グローバル環境変数は、`/etc/profile.d` という名前のディレクトリに格納することをお勧めします。 ディレクトリには、システム全体の環境変数を設定するときに使用されるファイルの一覧が含まれています。 set コマンドを使用して Windows Server コマンド プロンプト内でシステム環境変数を設定しても、環境変数は永続的には設定されません。 *setx* コマンド、またはコントロール パネルのシステム インターフェースのいずれかを使用します。

- VM の更新とアップグレードを管理します。 Azure 内にデプロイされた Windows コンピューターと Linux コンピューターに対するオペレーティング システムの更新プログラムを管理するには、Azure Automation 内の [Update Management](../../../automation/update-management/overview.md) ソリューションを使用します。 すべてのエージェント コンピューター上で利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理します。 VM ソフトウェアを更新することで、重要な Microsoft Azure パッチ、ハイパーバイザー ドライバー、ソフトウェア パッケージが最新の状態に確保されます。 マイナー リリースでは、インプレース アップグレードが可能です。 たとえば、RHEL 6.9 から RHEL 6.10、または RHEL 7.3 から RHEL 7.4 にアップグレードできます。 インプレース アップグレードを行うには、*yum update* コマンドを実行します。 Microsoft Azure では、メジャー リリースのインプレース アップグレード (RHEL 6 から RHEL 7 など) はサポートされていません。

- [Azure Monitor](../../../azure-monitor/data-platform.md) を使用してリソースの正常性を把握します。 Azure Monitor の機能としては、[Azure 診断ログ ファイル](../../../azure-monitor/essentials/platform-logs-overview.md)があります。 これは VM リソースの監視に使用され、パフォーマンスと可用性を損なう可能性がある問題を特定します。 [Azure Diagnostics 拡張機能](../../../azure-monitor/agents/diagnostics-extension-overview.md)によって、Windows VM 上で監視機能と診断機能を提供することができます。 この機能を有効にするには、その拡張機能を、Azure Resource Manager テンプレートの一部として追加します。 起動しない VM のトラブルシューティング時に使用する重要なツールであるブート診断を有効にします。 コンソール出力とブート ログは、Red Hat テクニカル サポートがブートに関する問題を解決するときに非常に役立ちます。 ブート診断は、Microsoft Azure portal 内で VM を作成中、または既存の VM 上で有効にします。 有効にすると、VM のコンソール出力を表示し、トラブルシューティングのためにブート ログをダウンロードできます。

- 通信をセキュリティで保護するもう 1 つの方法は、[Virtual Network (VNet)](../../../virtual-network/virtual-networks-overview.md) や[仮想プライベート ネットワーク (VPN)](../../../vpn-gateway/vpn-gateway-about-vpngateways.md) でプライベート エンドポイントを使用することです。 オープン ネットワークは外部からアクセスできるため、悪意のあるユーザーからの攻撃を受けやすくなっています。 VNet および VPN により、特定のユーザーへのアクセスが制限されます。 VNET はプライベート IP を使用して、同じ範囲内のサーバー間に、分離された通信チャネルを確立します。 通信を分離することで、同じアカウント内の複数のサーバーが、情報やデータをパブリック空間に公開することなく交換できます。 これをプライベート ネットワークを介してローカルで行っている場合は、リモート サーバーに接続してください。 アプリケーション サーバーと同じ VNet 内の JumpVM/JumpBox を使用するなど、さまざまな方法があります。または、[Azure Virtual Network Peering](../../../virtual-network/virtual-network-peering-overview.md)、[Azure Application Gateway](../../../application-gateway/overview.md)、[Azure Bastion](https://azure.microsoft.com/services/azure-bastion) などを使用します。 これらの方法すべてによって、完全に安全かつプライベートな接続が実現し、複数のリモート サーバーを接続することができます。

- [Azure ネットワーク セキュリティ グループ](../../../virtual-network/network-security-groups-overview.md) (NSG) を使用して、Azure VNet 内のアプリケーション サーバー間のネットワーク トラフィックをフィルター処理します。 NSG には、数種類の Azure リソースへの受信ネットワーク トラフィック (またはリソースからの送信ネットワーク トラフィック) を許可または拒否するセキュリティ規則が含まれます。 各規則で、送信元と送信先、ポート、およびプロトコルを指定することができます。 これらの NSG 規則を使用して JBoss EAP 上のアプリケーションを保護し、インターネットへのポートをブロックまたは許可します。

- JBoss EAP on Azure で実行されているアプリケーションの機能を強化するために、Azure 内で使用可能な HA 機能を使うことができます。 Azure 内で HA は、Load Balancer、Application Gateway、[仮想マシン スケール セット](../../../virtual-machine-scale-sets/overview.md)などの Azure リソースを使用して実現できます。 これらの HA 方法は、使用可能な別のアプリケーション インスタンスに負荷を分散させることで、冗長性を確保し、パフォーマンスを向上させます。これにより、アプリケーション インスタンスのメンテナンスや更新を容易に行うことができます。 顧客の需要の増加に対応するには、アプリケーションを実行するアプリケーション インスタンスの数を増加させることが必要な場合があります。 また、仮想マシン スケール セットは自動スケール機能も備え、ご自身のアプリケーションを、需要の変化に応じて自動的にスケールアップまたはスケールダウンできます。

## <a name="optimizing-the-jboss-eap-server-configuration"></a>JBoss EAP サーバー構成の最適化

JBoss EAP サーバーをインストールし、管理ユーザーを作成したら、サーバー構成を最適化できます。 サーバー構成を最適化し、運用環境内でアプリケーションをデプロイするときの一般的な問題を回避する方法については、[パフォーマンス チューニング ガイド](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html-single/performance_tuning_guide/index)の情報をご確認ください

## <a name="resource-links-and-support"></a>リソースのリンクとサポート

サポート関連の質問、問題、またはカスタマイズの要件については、[Red Hat サポート](https://access.redhat.com/support)または [Microsoft Azure サポート](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)にお問い合わせください。

* [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.3/html/getting_started_with_jboss_eap_for_openshift_online/index) の詳細を確認する
* Red Hat Subscription Manager (RHSM) [Cloud Access](https://access.redhat.com/documentation/en/red_hat_subscription_management/1/html-single/red_hat_cloud_access_reference_guide/index)
* [Azure Red Hat OpenShift (ARO)](https://azure.microsoft.com/services/openshift/)
* [Red Hat on Azure](./overview.md) 向け Microsoft Docs
* [Azure 内の RHEL BYOS Gold Images](./byos.md)
* JBoss EAP on Azure [クイック スタート ビデオ チュートリアル](https://www.youtube.com/watch?v=3DgpVwnQ3V4) 

## <a name="next-steps"></a>次のステップ

* [JBoss EAP on Azure への移行に関する問い合わせ](https://aka.ms/JavaCloud)
* [Azure App Service](/azure/developer/java/ee/jboss-on-azure) 内での JBoss EAP の実行
* [Azure Marketplace](https://aka.ms/AMP-JBoss-EAP) から JBoss EAP on RHEL VM/VM スケール セットをデプロイする
* [Azure クイック スタート](https://aka.ms/Quickstart-JBoss-EAP)から JBoss EAP on RHEL VM/VM スケール セットをデプロイする
* Azure [App Service Migration Assistance](https://azure.microsoft.com/services/app-service/migration-assistant/) を使用する
* Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta) を使用する
