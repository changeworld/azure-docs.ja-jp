---
title: Azure Stack 用の Azure Marketplace アイテム | Microsoft Docs
description: Azure Stack ではここで説明する Azure Marektplace アイテムを使うことができます。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: ae269c532773862e99884d84a05235cb904d13d4
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2018
---
# <a name="azure-marketplace-items-available-for-azure-stack"></a>Azure Stack 用の Azure Marketplace アイテム

*適用先: Azure Stack 統合システムと Azure Stack Development Kit*


## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能
ご使用の仮想マシン (VM) の拡張機能の更新プログラムがあるときは、必ずダウンロードする必要があります。 製品に付属している拡張機能は、通常の修正プログラムや更新プロセスでは更新されません。更新プログラムは頻繁に確認するようにしてください。 他の拡張機能は、Marketplace Management からのみ入手できます。

|  | 項目名 | [説明] | 発行元 | [OS Type]\(OS の種類\) |
| --- | --- | --- | --- | --- |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [ SQL IaaS 拡張機能](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)| <b>この拡張機能は、Windows Marketplace アイテムに SQL Server をデプロイするためにダウンロードします (これは必須です)。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/cse.png) | [ カスタム スクリプト拡張機能 ](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-customscript)| この更新プログラムは、<b>Windows でのカスタムのスクリプト拡張機能のインボックス バージョンにダウンロードします。</b> | Microsoft | Windows |
|![](media/azure-stack-marketplace-azure-items/dsc.png) | [ PowerShell DSC 拡張機能 ](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-dsc-overview)| この更新プログラムは、<b>PowerShell DSC 拡張機能のインボックス バージョンにダウンロードします。</b> | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [ Microsoft マルウェア対策拡張機能 ](https://docs.microsoft.com/en-us/azure/security/azure-security-antimalware)| Azure 向け Microsoft マルウェア対策は、アプリケーションおよびテナント環境のための単一エージェント ソリューションであり、ユーザーの介入なしにバック グラウンドで実行するように作られています。 | Microsoft | Windows |
| ![](media/azure-stack-marketplace-azure-items/dockerextension.png) | [Docker](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.docker-arm) | Linux 仮想マシン用の Docker 拡張機能。 | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/cse.png) | [Linux 用 VM Access](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/)| <b>この更新プログラムを、Linux 用 VMAccess 拡張機能のインボックス バージョンにダウンロードします。この更新プログラムは、Debian Linux VM を使う予定の場合に重要です。</b> | Microsoft | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [ Linux 用の Acronis Backup 拡張機能 ](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-lin-arm) | Microsoft Azure 用の Acronis Backup 拡張機能は、データ保護製品のAcronis Backup ファミリーの一部です。 | Acronis International GmbH。 | Linux |
| ![](media/azure-stack-marketplace-azure-items/acronis.png) | [ Windows 用の Acronis Backup 拡張機能 ](https://azuremarketplace.microsoft.com/marketplace/apps/Acronis.acronis-backup-win-arm) | Microsoft Azure 用の Acronis Backup 拡張機能は、データ保護製品のAcronis Backup ファミリーの一部です。 | Acronis International GmbH。 | Windows |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ CloudLink SecureVM Extension for Linux ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 簡単かつ確実に VM を制御、監視、暗号化します。 | Dell EMC | Linux |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ CloudLink SecureVM Extension for Windows ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 簡単かつ確実に VM を制御、監視、暗号化します。 | Dell EMC | Windows |

## <a name="microsoft-virtual-machine-images-and-solution-templates"></a>Microsoft 仮想マシン イメージおよびソリューション テンプレート

Microsoft Azure Stack では、次の Azure Marketplace 仮想マシンおよびソリューション テンプレートがサポートされています。 依存関係がある場合は、前述のように、それらすべてを個別にダウンロードします。 SQL Server や Machine Learning Server などのアプリケーションには、適切なライセンスが必要です (無償または試用版としてマークされている場合を除く)。

|  | 項目名 | [説明] | 発行元 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/windowsserver.png) | [Windows Server](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer) | アプリケーションに焦点を当てた、ユーザー指向のエンタープライズ クラス ソリューションで、容易にデプロイできるほか、コスト効率に優れています。 これらのイメージは、最新の修正プログラムで定期的に更新されます。 <b>重要な情報: 2018 年 1 月 18 日より前にダウンロードしたイメージは、削除し、最新のバージョンに置き換える必要があります。</b> | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/remotedesktopservicesdeployment.png) | [リモート デスクトップ サービス (RDS) デプロイ](https://azuremarketplace.microsoft.com/marketplace/apps/rds.remote-desktop-services-basic-deployment) | 基本的なリモート デスクトップ サービス (RDS) デプロイを作成します。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2013 評価版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2013Trial) | Windows Server 2012 Datacenter および Visual Studio 2017 Community エディションの Microsoft SharePoint Server 2013 評価版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sharepoint.png) | [SharePoint Server 2016 評価版](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SharePointServer2016Trial) | Windows Server 2016 Datacenter の Microsoft SharePoint Server 2016 評価版。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2012 R2 の SQL Server 2014 SP2](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2014SP2-WS2012R2) | SQL Server 2014 Service Pack 2。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2016 SP1 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2016 SP1 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016) | トランザクション、データ ウェアハウス、ビジネス インテリジェンス、分析の各ワークロード用の SQL Server 2016 SP1 の無償 Developer バージョン。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2016 SP1 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016) | SQL Server 2016 SP1 の無償 Express バージョン。 **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SQL Server 2016 SP1 Enterprise on Windows Server 2016](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2016 SP1 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQL2016SP1-WS2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonWindowsServer2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) | トランザクション、データ ウェアハウス、ビジネス インテリジェンス、分析の各ワークロード用の SQL Server 2016 SP1 の無償 Developer バージョン。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) | SQL Server 2016 SP1 の無償 Express バージョン。 **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseWindowsServer2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Windows Server 2016 の SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonWindowsServer2016) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。  **必要なダウンロード:** SQL IaaS 拡張機能。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS の SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS の SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) | トランザクション、データ ウェアハウス、ビジネス インテリジェンス、分析の各ワークロード用の SQL Server 2016 SP1 の無償 Developer バージョン。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS の SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) | SQL Server 2016 SP1 の無償 Express バージョン。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS の SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017EnterpriseUbuntuServer1604LTS) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [Ubuntu Server 16.04 LTS の SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SQLServer2017WebonUbuntuServer1604LTS) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 の SQL Server 2017 Standard](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017StandardonSLES12SP2) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 の SQL Server 2017 Developer](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP) | トランザクション、データ ウェアハウス、ビジネス インテリジェンス、分析の各ワークロード用の SQL Server 2016 SP1 の無償 Developer バージョン。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 の SQL Server 2017 Express](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) | SQL Server 2016 SP1 の無償 Express バージョン。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 の SQL Server 2017 Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017EnterpriseonSLES12SP2) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/sql.png) | [SUSE Linux Enterprise Server (SLES) 12 SP2 の SQL Server 2017 Web](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.Microsoft.SQLServer2017WebonSLES12SP2) | インテリジェントでミッション クリティカルなアプリケーション用のデータベース プラットフォーム。 | Microsoft + SUSE |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Windows Server 2016 の Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onWindowsServer2016) | Windows Server 2016 の Microsoft Machine Learning Server 9.3.0。 | Microsoft |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [Ubuntu 16.04 の Microsoft Machine Learning Server 9.3.0](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onUbuntu1604) | Ubuntu 16.04 の Microsoft Machine Learning Server 9.3.0。 | Microsoft + Canonical |
| ![](media/azure-stack-marketplace-azure-items/microsoft.png) | [CentOS Linux 7.2 の Microsoft Machine Learning Server 9.3.0 ](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.MicrosoftMachineLearningServer930onCentOSLinux72) | CentOS Linux 7.2 の Microsoft Machine Learning Server 9.3.0。 | Microsoft + Rogue Wave |


## <a name="linux-distributions"></a>Linux ディストリビューション
|  | 項目名 | [説明] | 発行元 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/coreos.png) | [CoreOS の Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps/CoreOS.CoreOS) | Container Linux は、最新で最小の Linux ディストリビューションであり、コンテナーの実行、クラスターの管理、サーバーのシームレスな更新の簡単な方法を提供します。どのコンポーネントも、ウェアハウス規模のコンピューティングを可能にするものです。 | CoreOS |
| ![](media/azure-stack-marketplace-azure-items/ubuntu.png) | [Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer) | Ubuntu Server は、クラウド環境の世界で最も人気のある Linux です。 | Canonical |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian) | Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/debian8.png) | [Debian 9 "Stretch"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian9) | Debian GNU/Linux は最も人気のある Linux ディストリビューションの 1 つです。 | credativ |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS-based 6.9](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased69) | この Linux のディストリビューションは CentOS に基づいており、Rogue Wave Software によって提供されます。 | Rogue Wave Software (以前の OpenLogic)  |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS-based 7.4](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74) | この Linux のディストリビューションは CentOS に基づいており、Rogue Wave Software によって提供されます。 | Rogue Wave Software (以前の OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/roguewave.png) | [CentOS-based 7.4-LVM](https://azuremarketplace.microsoft.com/marketplace/apps/RogueWave.CentOSbased74LVM) | この Linux のディストリビューションは CentOS に基づいており、Rogue Wave Software によって提供されます。 | Rogue Wave Software (以前の OpenLogic) |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SLES 12 SP3 (BYOS) ](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSELinuxEnterpriseServer12SP3BringYourOwnSubscription) | SUSE Linux Enterprise Server 12 SP3。 | SUSE |

## <a name="third-party-byol-free-and-trial-images-and-solution-templates"></a>サードパーティの BYOL、無料、および試用版のイメージとソリューション テンプレート

|  | 項目名 | [説明] | 発行元 |
| --- | --- | --- | --- |
| ![](media/azure-stack-marketplace-azure-items/abantecart.png) | [AbanteCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.abantecart) | オープン ソースの e コマース ショッピング カート。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/activemq.png) | [ActiveMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.activemq) | Java のオープン ソースのメッセージ ブローカー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/akeneo.png) | [Akeneo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.akeneo) | 管理プロセスを簡略化するために設計された強力な PIM。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/alfrescocommunity.png) | [Alfresco Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.alfrescocommunity) | ドキュメント管理を容易にする ECM システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/apachesolr.png) | [Apache Solr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.apachesolr) | 信頼性の高いオープン ソースのエンタープライズ検索プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/canvaslms.png) | [Canvas LMS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.canvaslms) | オープン ソースのラーニング管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cassandra.png) | [Cassandra](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cassandra) | 高可用性を備えたスケーラブルなオープン ソースのデータベース。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/civicrm.png) | [CiviCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.civicrm) | シンプルな Web ベースのリレーションシップ管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/cmsmadesimple.png) | [CMS Made Simple](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.cmsmadesimple) | Web サイトのすばやく簡単な作成および管理方法。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codedx.png) | [Code Dx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codedx) | コード分析および脆弱性管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/codiad.png) | [Codiad](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.codiad) | 最小フット プリントのオープン ソースのクラウド IDE。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/concrete5.png) | [Concrete5](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.concrete5) | Web アプリケーション、Web サイト、ストア、フォーラムを簡単にデプロイできます。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/coppermine.png) | [Coppermine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.coppermine) | 多目的の全機能を備えた Web ギャラリー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/couchdb.png) | [CouchDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.couchdb) | 使いやすいオープン ソースのデータベース システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/diaspora.png) | [Diaspora](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.diaspora) | よく使用されているパーソナル Web サーバー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/discourse.png) | [Discourse](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.discourse) | 高解像度のオープン ソースのディスカッション プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/django.png) | [Django](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.djangostack) | 高レベルの Python Web フレームワーク。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dolibarr.png) | [Dolibarr](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dolibarr) | 無料のオープン ソースのソフトウェア パッケージ。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dokuwiki.png) | [DokuWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dokuwiki) | 汎用的なオープン ソースの Wiki ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/dreamfactory.png) | [DreamFactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.dreamfactory) | SQL、NoSQL、BLOB などのサービスを含むオープン ソースの REST API。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elasticsearch.png) | [Elasticsearch](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elastic-search) | 柔軟性が高く強力なオープン ソースの分析エンジン。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/elk.png) | [ELK](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.elk) | Elasticsearch、Kibana、Logstash で構成されるビッグ データ スイート。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/erpnext.png) | [ERPNext](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.erpnext) | オープン ソースのエンタープライズ リソース プランニング (ERP) プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/espocrm.png) | [EspoCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.espocrm) | 顧客関係の管理に役立つシンプルな CRM システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/exoplatform.png) | [eXo Platform](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.exoplatform) | 企業向けに設計されているオープン ソースのソーシャル ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/fatfreecrm.png) | [Fat Free CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.fatfreecrm) | Rails ベースの CRM のオープン ソースの Ruby。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/bitnami.png) | [GitLab Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.gitlab) | 高速でセキュリティ保護された Git 管理ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ghost.png) | [Ghost](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.ghost) | パブリッシングに特化したプラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hadoop.png) | [Hadoop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hadoop) | 信頼性の高いスケーラブルな分散型コンピューティング用のフレームワーク。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hhvm.png) | [HHVM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hhvmstack) | 完全に統合された、すぐに実行できる開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/hordegroupwarewebmail.png) | [Horde Groupware Webmail](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.hordegroupwarewebmail) | 無料でエンタープライズ対応の、ブラウザー ベースの通信スイート。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jenkins.png) | [Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jenkins) | SCM ツール (CVS、Subversion、および Git) をサポートする統合サーバー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jfrogartifactory.png) | [Bitnami によって認定された JFrog Artifactory](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.artifactory) | 第一線の発行元からのバイナリ リポジトリ ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/joomla.png) | [Joomla!](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.joomla) | Web サイトを簡単に作成できるわかりやすい CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/jruby.png) | [JRuby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.jrubystack) | Ruby の高パフォーマンスの Java 実装。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/kafka.png) | [Kafka](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.kafka) | 強力な分散型パブリッシュ/サブスクライブ メッセージング システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lamp.png) | [LAMP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lampstack) | 完全に統合された、すぐに実行できる開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/lapp.png) | [LAPP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.lappstack) | PHP、PostgreSQL、Apache の完全な開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/letschat.png) | [Let's Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.letschat) | オープン ソースの永続的なメッセージング アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/limesurvey.png) | [LimeSurvey](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.limesurvey) | アンケート調査管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/livehelperchat.png) | [Live Helper Chat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.livehelperchat) | オープン ソースのライブ チャット サポート。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mahara.png) | [Mahara](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mahara) | よく使用されているオープン ソースの e ポートフォリオおよびソーシャル ネットワーク Web アプリ。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/magento.png) | [Magento](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.magento) | よく使用されている e コマース ソフトウェアおよびプラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mantis.png) | [Mantis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mantis) | 高度なバグ追跡システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mattermostteamedition.png) | [Mattermost Team Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mattermost) | オープン ソースの社内メッセージング ソリューション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mautic.png) | [Mautic](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mautic) | オープン ソースのエンタープライズ マーケティング オートメーション プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mean.png) | [MEAN](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mean) | よく使用されている mongoDB および Node.js 用の開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mediawiki.png) | [MediaWiki](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mediawiki) | 強力でスケーラブルな Wiki の実装。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/memcached.png) | [Memcached](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.memcached) | 高パフォーマンスの分散型メモリ オブジェクト キャッシュ システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/modx.png) | [MODX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.modx) | 直感的な Web CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mongodb.png) | [MongoDB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mongodb) | C++ で記述された高パフォーマンスのオープン ソースの NoSQL データベース。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/moodle.png) | [Moodle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.moodle) | オンライン学習コミュニティ用に設計された有効な CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/multicraft.png) | [Multicraft](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.multicraft) | Minecraft サーバーのホスティング ソリューションおよびコントロール パネル。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mybb.png) | [MyBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mybb) | 無料のオープン ソースのフォーラム ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/mysql.png) | [MySQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.mysql) | 最もよく使用されているデータベース システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/neos.png) | [Neos](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.neos) | 汎用的なオープン ソースのコンテンツ管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nginx.png) | [Nginx](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nginxstack) | PHP、MySQL、および Nginx の完全な開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/noalyss.png) | [Noalyss](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.noalyss) | 強力な複式簿記システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/nodejs.png) | [node.js](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.nodejs) | ビルドを容易にする、Javascript で記述されたオープン ソースの環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/odoo.png) | [Odoo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.odoo) | ビジネス プロセスを効果的に接続する ERP および CRM システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openatrium.png) | [Open Atrium](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openatrium) | 柔軟性の高い、多面的なイントラネット プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/opencart.png) | [OpenCart](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.opencart) | オンライン ショップ向けの無料の e コマース プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openedx.png) | [Bitnami によって認定されたオープン edX](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openedx) | 第一線の発行元からの E ラーニング ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openfire.png) | [OpenFire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openfire) | XMPP を使用したオープン ソースのリアル タイム コラボレーション サーバー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/openproject.png) | [OpenProject](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.openproject) | よく使用されているオープン ソースのプロジェクト管理ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orangehrm.png) | [OrangeHRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orangehrm) | モジュールが豊富な人事管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/orocrm.png) | [OroCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.orocrm) | 柔軟性の高いオープン ソースの CRM アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/osclass.png) | [Osclass](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.osclass) | 技術的なノウハウなしに無料の案内広告を作成および管理できます。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/owncloud.png) | [ownCloud](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.owncloud) | よく使用されているオープン ソースのファイル同期および共有ソリューション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/oxideshop.png) | [OXID eShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.oxid-eshop) | 信頼できるオープン ソースの e コマース システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phpbb.png) | [phpBB](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phpbb) | カスタマイズ可能な掲示板ソリューション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/phplist.png) | [phpList](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.phplist) | 一方向のメール通知配信システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pimcore.png) | [Pimcore](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pimcore) | 強力な契約管理プラットフォーム (CEM/CXM)。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/piwik.png) | [Piwik](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.piwik) | リアルタイムの Web 分析ソフトウェア プログラム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/plone.png) | [Plone](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.plone) | 無料のオープン ソースの仮想アプライアンス。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/pootle.png) | [Pootle](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.pootle) | 翻訳プロジェクト向けの使いやすい Web ポータル。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/postgresql.png) | [PostgreSQL](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.postgresql) | 極めて高度なオープン ソースのデータベース。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/prestashop.png) | [PrestaShop](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.prestashop) | オープン ソースの e コマース Web サイト ビルダー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakeropensourceedition) | ビジネス プロセス管理およびワークフロー自動化プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processmaker.png) | [Process Maker Enterprise Edition](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processmakerenterprise) | オープン ソースのワークフローおよびビジネス プロセス管理ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/processwire.png) | [ProcessWire](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.processwire) | よく使用されている PHP5 のオープン ソース CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/publify.png) | [Publify](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.publify) | Ruby on Rails ベースのブログ プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/rabbitmq.png) | [RabbitMQ](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rabbitmq) | 共通のプラットフォームを提供する効率的なメッセージング ブローカー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redash.png) | [Re:dash](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redash) | オープン ソースのデータ可視化およびコラボレーション プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redis.png) | [Redis](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redis) | 強力なオープン ソースのキー値ストア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmine.png) | [Redmine](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmine) | 強力なプロジェクト管理 Web アプリ | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/redmineagile.png) | [Redmine+Agile](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.redmineplusagile) | アジャイル プラグインで事前に構成済みのプロジェクト管理アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserver.png) | [ReportServer Community](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserver) | オープン ソースのビジネス インテリジェンス プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/reportserverenterprise.png) | [ReportServer Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.reportserverenterprise) | エンタープライズ ビジネス インテリジェンス プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/resourcespace.png) | [ResourceSpace](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.resourcespace) | コラボレーション向上のためのデジタル資産管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/roundcube.png) | [Roundcube](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.roundcube) | MIME サポートなどの機能を持つブラウザー ベースの IMAP クライアント。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/ruby.png) | [Ruby](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.rubystack) | Rails の Ruby 用の使いやすい開発環境。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/seopanel.png) | [SEO Panel](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.seopanel) | 複数の Web サイトを追跡するためのオープン ソースの SEO 管理アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/shopware.png) | [Shopware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.shopware) | オープン ソースの e コマース プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/simplemachinesforum.png) | [Simple Machines Forum](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.simplemachinesforum) | 独自のオンライン コミュニティを構築するためのシンプルなフォーラム ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/spree.png) | [Spree](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.spree) | 使いやすい e コマース プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/subversion.png) | [Subversion](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.subversion) | オープン ソースのバージョン管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/sugarcrm.png) | [SugarCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.sugarcrm) | 柔軟性の高い顧客関係管理ソリューション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/suitecrm.png) | [SuiteCRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.suitecrm) | よく使用されているエンタープライズ レベルの CRM アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/testlink.png) | [TestLink](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.testlink) | 品質保証を円滑化するテスト管理ソフトウェア。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tikiwikicmsgroupware.png) | [Tiki Wiki CMS Groupware](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tikiwikicmsgroupware) | 完全な機能を備えた Wiki プラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tinytinyrss.png) | [Tiny Tiny RSS](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tinytinyrss) | 柔軟性に優れたオープン ソースで Web ベースのニュース フィードおよびアグリゲーター。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/tomcat.png) | [Tomcat](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.tom-cat) | Java コミュニティからの仕様を実装する、よく使用されているプラットフォーム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/trac.png) | [Trac](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.trac) | 強化された Wiki および問題追跡システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/typo3.png) | [Typo3](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.typo3) | 完全な柔軟性を備えた CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/weblate.png) | [Weblate](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.weblate) | Web ベースの翻訳管理システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/webmailprophp.png) | [WebMail Pro PHP](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.webmailpro) | エンタープライズ機能を持つ Web メール システム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wildfly.png) | [WildFly](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wildfly) | Apache、WildFly、MySQL、および Java を含むアプリケーション サーバー。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/wordpress.png) | [WordPress](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.wordpress) | 最もよく使用されていて、すぐに使用できる CMS。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/x2enginesalescrm.png) | [X2Engine Sales CRM](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.x2enginesalescrm) | コンパクトなマーケティング、販売、および顧客サービス アプリケーション。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/xoops.png) | [Xoops](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.xoops) | CMS および動的な Web サイトを作成する Web ポータル プログラム。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/zurmo.png) | [Zurmo](https://azuremarketplace.microsoft.com/marketplace/apps/bitnami.zurmo) | オープン ソースの CRM システム (Mobile、Social、および Gamified)。 | Bitnami |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Application Security Control Center](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-app-sec-control-center) | 複数の Barracuda Web Application Firewalls (WAF) を一元管理します。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Email Security Gateway](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.barracuda-email-security-gateway) | 受信電子メールによる脅威から保護する電子メール セキュリティ ゲートウェイです。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/barracuda.png) | [Barracuda Web Application Firewall (WAF)](https://azuremarketplace.microsoft.com/marketplace/apps/barracudanetworks.waf) | 自動化されたターゲット攻撃に対するセキュリティと DDoS 保護。 | Barracuda Networks, Inc. |
| ![](media/azure-stack-marketplace-azure-items/checkpoint.png) | [Check Point vSEC セキュリティ管理](https://azuremarketplace.microsoft.com/marketplace/apps/checkpoint.vsec) | Check Point vSEC は、Azure 内の顧客の資産をマルウェアや高度な脅威から保護する高性能の多層脅威防止機能を提供します。 <b>両方の Check Point vSEC Marketplace アイテムをダウンロードしてください。</b> | Check Point |
| ![](media/azure-stack-marketplace-azure-items/chefautomate.png) | [Chef Automate](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate) | Chef Automate を使用して Continuous Automation Platform をビルド、デプロイ、管理します。 <b>両方の Chef Marketplace アイテムをダウンロードしてください</b> | Chef Software, Inc |
| ![](media/azure-stack-marketplace-azure-items/cloudlink.png) | [ CloudLink SecureVM ](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudlink.cloudlink-securevm)  | 簡単かつ確実に VM を制御、監視、暗号化します。 <b>すべての CloudLink SecureVM 項目をダウンロードします。<b> | Dell EMC |
| ![](media/azure-stack-marketplace-azure-items/hortonworks.png) | [Hortonworks Data Platform (HDP) サンドボックス](https://azuremarketplace.microsoft.com/marketplace/apps/hortonworks.hortonworks-sandbox) | HDP 2.5 を使用した、100% オープン ソースのプラットフォーム (Hadoop、Spark、Storm、HBase、Kafka、Hive、Ambari に対応)。 | Hortonworks |
| ![](media/azure-stack-marketplace-azure-items/kemp.png) | [LoadMaster ロード バランサー ADC コンテンツ スイッチ](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) | レイヤー 4 ～ 7 のアプリケーション配信コントローラー (ADC) ロード バランサー、コンテンツ スイッチ、および Traffic Manager。 | KEMP Technologies Inc. |
| ![](media/azure-stack-marketplace-azure-items/noobaa.png) | [NooBaa Hybrid AWS S3 互換 - Community Edition](https://azuremarketplace.microsoft.com/marketplace/apps/noobaa.noobaa-hybrid-s3-archive-05) | パブリックとオンプレミスのキャパシティ リソースに対応した、S3 互換のストレージ サービス。 | NooBaa |
| ![](media/azure-stack-marketplace-azure-items/puppet.png) | [Puppet Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2) | Puppet Enterprise では、Azure Stack インフラストラクチャのライフ サイクル全体を自動化することができます。 <b>両方の Puppet Marketplace アイテムをダウンロードしてください。</b> | Puppet |
| ![](media/azure-stack-marketplace-azure-items/suse.png) | [SUSE Manager 3.0 Proxy (BYOS) ](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SUSEManager30ProxyBringYourOwnSubscription) | クラス最高のオープン ソースのインフラストラクチャ管理。 | SUSE |
| ![](media/azure-stack-marketplace-azure-items/zerodown.png) | [ZeroDown Software Tool ](https://azuremarketplace.microsoft.com/marketplace/apps/zerodown_software.zerodown-software-tool-7_1) | ZeroDown Software Tool v7.1 - Migrate + HA。 | ZeroDown Software |
