---
title: よくあるご質問 - SAP ソリューション向け Azure Monitor | Microsoft Docs
description: この記事では、Azure Monitor for SAP Solutions に関してよく寄せられる質問 (FAQ) に対する回答を紹介します。
author: rdeltcheva
ms.service: virtual-machines-sap
ms.topic: article
ms.date: 06/30/2020
ms.author: radeltch
ms.openlocfilehash: 4b84f07f637b0a8925dec96c8c609101247ffd64
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377126"
---
# <a name="azure-monitor-for-sap-solutions-faq-preview"></a>Azure Monitor for SAP Solutions に関する FAQ (プレビュー)
## <a name="frequently-asked-questions"></a>よく寄せられる質問

この記事では、Azure Monitor for SAP Solutions に関してよく寄せられる質問 (FAQ) に対する回答を示します。  

 - **SAP ソリューション向け Azure Monitor に対して料金を支払う必要がありますか?**  
SAP ソリューション向け Azure Monitor には、ライセンス料金は発生しません。  
ただし、お客様は管理対象リソース グループ コンポーネントの費用を負担する必要があります。  

 - **このサービスのパブリック プレビューは、どのリージョンで利用できますか?**  
このサービスのパブリック プレビューは、米国東部 2、米国西部 2、米国東部、西ヨーロッパで利用可能になります。  

 - **サブスクリプションで管理対象リソース グループのデプロイを許可するためのアクセス許可を付与する必要がありますか?**  
いいえ。明示的なアクセス許可は必要ありません。  

 - **コレクター VM はどこにありますか?**  
Microsoft では、SAP ソリューション向け Azure Monitor リソースをデプロイする時点で、お客様にご使用の SAP HANA サーバーと同じ VNET を監視リソースに選択することをお勧めします。 そのため、コレクター VM は、SAP HANA サーバーと同じ VNET に配置することをお勧めします。 お客様が HANA 以外のデータベースを使用している場合、コレクター VM は HANA 以外のデータベースと同じ VNET に存在します。  

 - **サポートされている HANA のバージョンを教えてください**  
HANA 1.0 SPS 12 (Rev. 120 以降) および HANA 2.0 SPS03 以降  

 - **サポートされている HANA のデプロイの構成を教えてください**  
次の構成がサポートされています。
   - 単一ノード (スケールアップ) とマルチノード (スケールアウト)  
   - Single Database Container (HANA 1.0 SPS 12) と Multiple Database Containers (HANA 1.0 SPS 12 または HANA 2.0)  
   - 自動ホスト フェールオーバー (n + 1) と HSR  

 - **サポートされている SQL Server のバージョンを教えてください**  
SQL Server 2012 SP4 以降。  

 - **サポートされている SQL Server の構成を教えてください**  
次の構成がサポートされています。
   - 仮想マシンの既定または名前付きスタンドアロン インスタンス  
   - クラスター化されたリソースの仮想名または AlwaysOn リスナー名のいずれかを使用する場合は、クラスター化されたインスタンスまたは AlwaysOn 構成のインスタンス。 現在、クラスターまたは AlwaysOn 固有のメトリックは収集されません    
   - Azure SQL Database (PAAS) は現在サポートされていません  

 - **管理対象リソース グループを誤って削除した場合はどうなりますか?**  
管理対象リソース グループは、既定でロックされています。 したがって、お客様が管理対象リソース グループを誤って削除する可能性はごくわずかです。  
お客様が管理対象リソース グループを削除すると、SAP ソリューション向け Azure Monitor が機能しなくなります。 お客様は、新しい SAP ソリューション向け Azure Monitor リソースをデプロイしてやり直していただく必要があります。  

 - **SAP ソリューション向け Azure Monitor リソースをデプロイするために、Azure サブスクリプションで必要なロールはどれですか?**  
共同作成者ロールです。  

 - **この製品の SLA はどのようなものですか?**  
プレビューは、サービス レベル アグリーメントから除外されています。 SAP ソリューション向け Azure Monitor のマーケットプレース イメージを通じて、完全なライセンス条項をお読みください。  

 - **このソリューションでランドスケープ全体を監視できますか?**  
現在、HANA データベース、基盤となるインフラストラクチャ、高可用性クラスター、Microsoft SQL Server、SAP Netweaver 高可用性、および SAP アプリケーション インスタンス可用性メトリック (パブリック プレビュー) を監視できます。  

 - **このサービスは SAP Solution Manager に代わるものですか?**  
いいえ。 お客様は、引き続き SAP Solution Manager を使用してビジネス プロセスの監視を行うことができます。  

 - **SAP HANA Cockpit/Studio などの従来のソリューションより優れているこのサービスの価値はどのようなものですか?**  
SAP ソリューション向け Azure Monitor は、HANA データベース固有のものではありません。 SAP ソリューション向け Azure Monitor では、AnyDB もサポートされています。  

- **SAP NetWeaver のサポートされているバージョンを教えてください。**  
SAP NetWeaver 7.0 以降。  

- **どの SAP NetWeaver 構成がサポートされていますか?**  
ABAP、Java、およびデュアルスタックの SAP NetWeaver アプリケーション サーバー構成がサポートされています。

- **SAP NetWeaver アプリケーション監視のためにメソッドの保護を解除する必要があるのはなぜですか?**  
SAP リリース 7.3 以降では、多くの Web サービス メソッドは既定で保護されます。 これらのメソッドを呼び出して可用性とパフォーマンスのメトリックを取得するには、GetQueueStatistic、ABAPGetWPTable、GetProcessList、EnqGetStatistic、および GetSystemInstancelist の各メソッドの保護を解除する必要があります。

- **SAPCONTROL webmethod の保護を解除した場合、リスクはありますか?**  
一般に、SAPCONTROL webmethod の保護を解除してもセキュリティ リスク ([こちら](https://launchpad.support.sap.com/#/notes/1439348)を参照) はありませんが、sapstartsrv のサーバー ポート (5XX13 / 5XX14) 経由での、保護が解除された webmethod へのアクセスを制限または禁止したい場合は、SAP のアクセス制御リスト (ACL) にフィルターを追加することによって実行できます。[OSS ノート](https://service.sap.com/sap/support/notes/1495075)に、これを実現するために必要な構成が説明されています。 

- **SAP NetWeaver プロバイダーを設定するためのシステム構成を実行した後、SAP インスタンスを再起動する必要はありますか?**  
はい。SAP 構成の変更によってメソッドの保護を解除した場合は、それぞれの SAP システムを再起動して、構成の変更が確実に更新されるようにする必要があります。  

## <a name="next-steps"></a>次のステップ

- 最初の SAP ソリューション向け Azure Monitor リソースを作成する。
