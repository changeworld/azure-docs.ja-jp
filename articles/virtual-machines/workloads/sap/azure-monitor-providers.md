---
title: SAP ソリューション向け Azure Monitor のプロバイダー | Microsoft Docs
description: この記事では、SAP ソリューション プロバイダー向け Azure Monitor に関してよく寄せられる質問に対する回答を示します
author: rdeltcheva
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.date: 07/06/2021
ms.author: radeltch
ms.openlocfilehash: 5061ee2131ee708c38a68dca02c18a21085b5430
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616861"
---
# <a name="azure-monitor-for-sap-solutions-providers-preview"></a>Azure Monitor for SAP Solutions プロバイダー (プレビュー)

## <a name="overview"></a>概要

この記事では、Azure Monitor for SAP Solutions で現在使用できるさまざまなプロバイダーについて説明します。

SAP ソリューション向け Azure Monitor のコンテキストにおいて、"*プロバイダーの種類*" は、特定の "*プロバイダー*" を指します。 たとえば、*SAP HANA* は、SAP HANA データベースなど、SAP ランドスケープ内の特定のコンポーネント用に構成されています。 プロバイダーは、対応するコンポーネントの接続情報を含み、そのコンポーネントから利用統計情報を収集するのに役立ちます。 1 つの SAP ソリューション向け Azure Monitor リソース (SAP モニター リソースとも呼ばれます) に対して、プロバイダーの種類が同じ複数のプロバイダーを構成することも、プロバイダーの種類が異なる複数のプロバイダーを構成することもできます。
   
SAP ランドスケープの対応するコンポーネントからのデータ収集を可能にするために、さまざまなプロバイダーの種類を構成することを選択できます。 たとえば、SAP HANA プロバイダーの種類用に 1 つのプロバイダーを構成する一方で、高可用性クラスター プロバイダーの種類用に別のプロバイダーを構成することができます。  

また、同じ SAP モニター リソースと関連するマネージド グループを再利用するために、特定のプロバイダーの種類のプロバイダーを複数構成することもできます。 管理対象リソース グループの詳細については、「[Azure portal を使用じて Azure Resource Manager のリソース グループを管理する](../../../azure-resource-manager/management/manage-resource-groups-portal.md)」を参照してください。

パブリック プレビューでは、次のプロバイダーの種類がサポートされています。   
- SAP NetWeaver
- SAP HANA
- Microsoft SQL Server
- 高可用性クラスター
- オペレーティング システム (OS)

![SAP ソリューション向け Azure Monitor のプロバイダー](https://user-images.githubusercontent.com/75772258/115047655-5a5b2c00-9ef6-11eb-9e0c-073e5e1fcd0e.png)

SAP モニター リソースをデプロイするときに、使用可能なプロバイダーの種類から少なくとも 1 つのプロバイダーを構成することをお勧めします。 プロバイダーを構成することによって、プロバイダーが構成されている対応するコンポーネントからのデータ収集を開始します。   

SAP モニター リソースのデプロイ時にプロバイダーを構成しなかった場合、SAP モニター リソースは正常にデプロイされますが、利用統計情報は収集されません。 Azure portal 内で SAP モニター リソースを介して、デプロイ後にプロバイダーを追加することができます。 プロバイダーの追加または削除は、いつでも SAP モニター リソースから行うことができます。

## <a name="provider-type-sap-netweaver"></a>プロバイダーの種類: SAP NetWeaver

プロバイダーの種類 SAP NetWeaver のプロバイダーを 1 つ以上構成して、SAP NetWeaver レイヤーからのデータ収集を有効にできます。 AMS NetWeaver プロバイダーは、既存の [SAPControl Web サービス](https://www.sap.com/documents/2016/09/0a40e60d-8b7c-0010-82c7-eda71af511fa.html) インターフェイスを利用して、適切な利用統計情報を取得します。

現在のリリースについては、次の SOAP Web メソッドが、AMS によって呼び出される既定の標準メソッドです。

![image1](https://user-images.githubusercontent.com/75772258/114600036-820d8280-9cb1-11eb-9f25-d886ab1d5414.png)

パブリック プレビューでは、SAP NetWeaver プロバイダーを使用して、次のデータを確認できます。 
- システムとインスタンスの可用性
- 作業プロセスの使用
- キューの使用
- エンキュー ロックの統計

![image](https://user-images.githubusercontent.com/75772258/114581825-a9f2eb00-9c9d-11eb-8e6f-79cee7c5093f.png)

## <a name="provider-type-sap-hana"></a>プロバイダーの種類: SAP HANA

プロバイダーの種類 *SAP HANA* のプロバイダーを 1 つ以上構成して、SAP HANA データベースからのデータ収集を有効にすることができます。 SAP HANA プロバイダーは、SQL ポートを介して SAP HANA データベースに接続し、データベースから利用統計情報をプルして、ご自身のサブスクリプションの Log Analytics ワークスペースにプッシュします。 SAP HANA プロバイダーは、SAP HANA データベースから 1 分ごとにデータを収集します。  

パブリック プレビューでは、SAP HANA プロバイダーを使用して、次のデータを確認できます。
- 基盤となるインフラストラクチャの使用
- SAP HANA ホストの状態
- SAP HANA システム レプリケーション
- SAP HANA バックアップの利用統計情報。 

SAP HANA プロバイダーを構成するには、以下が必要です。
- ホストの IP アドレス 
- HANA SQL ポート番号
- SYSTEMDB のユーザー名とパスワード

SYSTEMDB に対しては SAP HANA プロバイダーを構成することをお勧めします。ただし、他のデータベース テナントに対して追加のプロバイダーを構成することもできます。

![SAP ソリューション向け Azure Monitor のプロバイダー - SAP HANA](./media/azure-monitor-sap/azure-monitor-providers-hana.png)

## <a name="provider-type-microsoft-sql-server"></a>プロバイダーの種類: Microsoft SQL Server

プロバイダーの種類 *Microsoft SQL Server* のプロバイダーを 1 つ以上構成して、[SQL Server on Virtual Machines](https://azure.microsoft.com/services/virtual-machines/sql-server/) からのデータ収集を有効にすることができます。 SQL Server プロバイダーは、SQL ポートを介して Microsoft SQL Server に接続し、 データベースから利用統計情報をプルして、ご自身のサブスクリプションの Log Analytics ワークスペースにプッシュします。 SQL Server を SQL 認証用に構成し、SQL Server のユーザー名とパスワードを使ってサインインできるようにします。 プロバイダーの既定のデータベースには、SAP データベースを設定します。 SQL Server プロバイダーは、60 秒から最大 1 時間ごとに SQL サーバーからデータを収集します。  

パブリック プレビューでは、SQL Server プロバイダーを使用して、次のデータを確認できます。
- 基盤となるインフラストラクチャの使用
- T-SQL ステートメント
- 一番大きなテーブル
- SQL Server エラー ログに記録された問題
- プロセス ブロックなど  

Microsoft SQL Server プロバイダーを構成するには、以下が必要です。
- SAP システム ID
- ホストの IP アドレス
- SQL Server のポート番号
- SQL Server のユーザー名とパスワード

![SAP ソリューション向け Azure Monitor のプロバイダー - SQL](./media/azure-monitor-sap/azure-monitor-providers-sql.png)

## <a name="provider-type-high-availability-cluster"></a>プロバイダーの種類: 高可用性クラスター
プロバイダーの種類 "*高可用性クラスター*" のプロバイダーを 1 つ以上構成して、SAP ランドスケープ内の Pacemaker クラスターからのデータ収集を有効にすることができます。 高可用性クラスター プロバイダーが Pacemaker に接続するとき、**SUSE** ベースのクラスターの場合は [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) を、**RHEL** ベースのクラスターの場合は [Performance co-pilot](https://access.redhat.com/articles/6139852) を使用します。 その後、AMS はデータベースから利用統計情報をプルして、ご自身のサブスクリプションの Log Analytics ワークスペースにプッシュします。 高可用性クラスター プロバイダーは、Pacemaker から 60 秒ごとにデータを収集します。  

パブリック プレビューでは、高可用性クラスター プロバイダーを使用して、次のデータを確認できます。   
 - ノードとリソースの状態のロールアップとして表示されるクラスターの状態 
 - 場所の制約
 - Trends
 - [その他](https://github.com/ClusterLabs/ha_cluster_exporter/blob/master/doc/metrics.md) 

![Azure Monitor for SAP Solutions プロバイダー - 高可用性クラスター](./media/azure-monitor-sap/azure-monitor-providers-pacemaker-cluster.png)

高可用性クラスター プロバイダーを構成するには、次の 2 つの主要な手順が必要です。

1. Pacemaker クラスター内の *それぞれの* ノードに [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) をインストールします。

   ha_cluster_exporter をインストールするには、次の 2 つのオプションがあります。
   
   - Azure Automation スクリプトを使用して、高可用性クラスターをデプロイする。 スクリプトを実行すると、各クラスター ノードに [ha_cluster_exporter](https://github.com/ClusterLabs/ha_cluster_exporter) がインストールされます。  
   - [手動でインストール](https://github.com/ClusterLabs/ha_cluster_exporter#manual-clone--build)する。 

2. Pacemaker クラスター内の *それぞれの* ノードで、高可用性クラスター プロバイダーを構成します。

   高可用性クラスター プロバイダーを構成するには、次の情報が必要です。
   
   - **[名前]**。 このプロバイダーの名前です。 この Azure Monitor for SAP Solutions インスタンスで一意である必要があります。
   - **Prometheus エンドポイント**。 `http://<servername or ip address>:9664/metrics`.
   - **SID**。 SAP システムでは、SAP SID を使用します。 その他のシステム (たとえば、NFS クラスターなど) では、そのクラスターを表す 3 文字の名前を使用します。 この SID は、監視されている他のクラスターとは異なっている必要があります。   
   - **クラスター名**。 クラスターの作成時に使用されたクラスター名です。 クラスター名は、クラスター プロパティ `cluster-name` で確認できます。
   - **Hostname**。 仮想マシン (VM) の Linux ホスト名。  

## <a name="provider-type-os-linux"></a>プロバイダーの種類: OS (Linux)
プロバイダーの種類 OS (Linux) のプロバイダーを 1 つ以上構成して、BareMetal または VM ノードからのデータ収集を有効にすることができます。 OS (Linux) プロバイダーは、 [Node_Exporter](https://github.com/prometheus/node_exporter)  エンドポイントを使用して BareMetal または VM ノードに接続し、 ノードから利用統計情報をプルして、ご自身のサブスクリプションの Log Analytics ワークスペースにプッシュします。 OS (Linux) プロバイダーでは、ノードからのほとんどのメトリックに対して 60 秒ごとにデータを収集します。 

パブリック プレビューでは、OS (Linux) プロバイダーを使用して、次のデータを確認できます。 
   - CPU 使用、プロセス別の CPU 使用 
   - ディスク使用、I/O 読み取りと書き込み 
   - メモリ配分、メモリ使用、スワップ メモリ使用 
   - ネットワーク使用、ネットワークの受信および送信トラフィックの詳細 

OS (Linux) プロバイダーを構成するには、次の 2 つの主要な手順が必要です。
1. BareMetal または VM ノードそれぞれに  [Node_Exporter](https://github.com/prometheus/node_exporter)  をインストールします。
   [Node_exporter](https://github.com/prometheus/node_exporter) をインストールするためのオプションは 2 つあります。 
      - Ansible での自動化されたインストールの場合、OS (Linux) プロバイダーをインストールする各 BareMetal または VM ノード上で [Node_Exporter](https://github.com/prometheus/node_exporter) を使用する。  
      -  [手動でインストール](https://prometheus.io/docs/guides/node-exporter/)する。

2. 環境内の各 BareMetal または VM ノード インスタンスに対して OS (Linux) プロバイダーを構成します。 
   OS (Linux) プロバイダーを構成するには、次の情報が必要です。 
      - 名前。 このプロバイダーの名前です。 この Azure Monitor for SAP Solutions インスタンスで一意である必要があります。 
      - Node Exporter エンドポイント。 通常は `http://<servername or ip address>:9100/metrics`。 

> [!NOTE]
> 9100 は Node_Exporter エンドポイント用に公開されているポートです。

> [!Warning]
> ノードが再起動した後に Node Exporter が引き続き実行されていることを確認します。 

## <a name="next-steps"></a>次のステップ

Azure portal から Azure Monitor for SAP Solutions をデプロイする方法を学習します。

> [!div class="nextstepaction"]
> [Azure portal を使用して Azure Monitor for SAP Solutions をデプロイする](./azure-monitor-sap-quickstart.md)
