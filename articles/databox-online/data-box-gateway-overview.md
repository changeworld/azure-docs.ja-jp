---
title: Microsoft Azure Data Box Gateway の概要 | Microsoft Docs
description: データを Azure に転送できる仮想アプライアンス ストレージ ソリューションである Azure Data Box Gateway について説明します
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 9c12674a66582ede04b4cf9d311238d61816afec
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46969548"
---
# <a name="what-is-azure-data-box-gateway-preview"></a>Azure Data Box Gateway (プレビュー) とは 

Azure Data Box Gateway は、Azure にシームレスにデータを送信できるストレージ ソリューションです。 この記事では、Azure Data Box Gateway ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。 

Data Box Gateway は、仮想化環境またはハイパーバイザーにプロビジョニングされた仮想マシンに基づく仮想デバイスです。 仮想デバイスはオンプレミスに存在し、NFS および SMB プロトコルを使用して仮想デバイスに対するデータを記述します。 デバイスはその後、Azure ブロック blob、ページ blob、または Azure Files にデータを転送します。 

> [!IMPORTANT]
> Data Box Gateway はプレビュー段階にあります。 このソリューションをデプロイする前に、「[プレビューの使用条件に関するページ](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を確認してください。

## <a name="use-cases"></a>ユース ケース

Data Box Gateway は、クラウド アーカイブやディザスター リカバリーなどでクラウドにデータを転送したり、クラウド スケールでデータを処理する必要がある場合に活用できます。 Data Box Gateway は、以下のようなさまざまなシナリオで、データ転送に使用できます。

- **クラウド アーカイブ** - Data Box Gateway を使用して、セキュリティで保護された効率的な方法で、数百 TB のデータを Azure storage にコピーします。 データはアーカイブのシナリオに合わせて 1 回で取り込んだり継続的に取り込んだりすることができます。

- **データ集計** - データ処理と分析のために複数のソースから Azure Storage の 1 つの場所にデータを集計します。  

- **オンプレミスのワークロードとの統合** - オンプレミスのワークロードと統合します。これにはクラウド ストレージを使用し、一般的に使用されるファイルについてはローカル アクセスが必要となるバックアップと復元などがあります。 

## <a name="benefits"></a>メリット

Data Box Gateway には次の利点があります。

- **簡単なデータ転送**- ローカル ネットワーク共有で操作するのと同じくらい簡単に、Azure ストレージとの間でデータを転送できます。  
- **高パフォーマンス** - Azure との間でパフォーマンスの高い転送を実現することにより、ネットワーク データ転送の困難さを解消します。 
- **高速アクセス** - オンプレミス ファイルに高速にアクセスするために、最新のファイルをキャッシュします。  
- **制限された帯域幅の使用** - 営業時間のピーク時にネットワークの使用が制限されるように調整されている場合でも、Azure にデータを書き込むことができます。  

## <a name="key-capabilities"></a>主な機能

Data Box Gateway には次の機能があります。

|機能 |説明  |
|---------|---------|
|速度     | 完全に自動化され、高度に最適化されたデータ転送と帯域幅。|
|サポートされるプロトコル     | データ インジェストのために、標準の SMB プロトコルと NFS プロトコルをサポートします。 <br> サポート対象のバージョンについては、「[Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)」を参照してください。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。|
|高速アクセス     | 最近使用されたファイルを高速にアクセスするために、デバイス上でローカル キャッシュを行います。|
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート       |
|回復性     | 組み込まれたネットワークの回復性        |


## <a name="specifications"></a>仕様

Data Box Gateway 仮想デバイスの仕様は次のとおりです。

| 仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| 仮想プロセッサ (コア)   | 最低 4 |            
| メモリ  | 8 GB 以上|
| 可用性|単一ノード|
| ディスク| OS ディスク: 250 GB  <br> データ ディスク: 最小 2 TB、シン プロビジョニング、SSD を使用する必要があります|
| ネットワーク インターフェイス|1 つ以上の仮想ネットワーク インターフェイス|
| ネイティブのファイル共有プロトコル|SMB および NFS  |
| セキュリティ| デバイスとデータへのアクセスのロックを解除する認証 <br> AES 256 ビット暗号化を使用した移動中のデータの暗号化|
| 管理| ローカル Web UI - 初期セットアップ、診断、およびデバイスの電源管理 <br> Azure portal - Data Box Gateway デバイスの日々 の管理       |


## <a name="components"></a>コンポーネント

Data Box Gateway ソリューションは、Data Box Gateway リソース、Data Box Gateway 仮想デバイス、ローカル Web UI で構成されています。

* **Data Box Gateway 仮想デバイス**: 仮想化環境またはハイパーバイザーにプロビジョニングされた仮想マシンに基づくデバイスで、Azure にデータを送信できます。 
    
* **Data Box Gateway リソース** - さまざまな地理的な場所からアクセスできる Web インターフェイスから、Data Box Gateway デバイスを管理できる、Azure portal でのリソース。 Data Box Gateway リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

    <!--![The Data Box Gateway service in Azure portal](media/data-box-overview/data-box-Gateway-service1.png)-->

    <!--For more information, go to [Use the Data Box Gateway service to administer your Data Box Gateway device](data-box-gateway-portal-ui-admin.md).-->

* **Data Box ローカル Web UI** - ローカル Web UI を使用して、診断の実行、Data Box Gateway デバイスのシャットダウンと再起動、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    <!--![The Data Box Gateway local web UI](media/data-box-gateway-overview/data-box-gateway-local-web-ui.png)-->

    <!-- For information about using the web-based UI, go to [Use the web-based UI to administer your Data Box](data-box-gateway-portal-ui-admin.md).-->


## <a name="region-availability"></a>利用可能なリージョン

Data Box Edge 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じ地域にある必要はありません。

- **リソースの可用性** - このリリースでは、Data Box Edge リソースは次の地域で利用できます。
    - **米国** - 米国西部と米国東部
    - **欧州連合** - 欧州西部
    - **アジア太平洋** - 東南アジア

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 

    最適なパフォーマンスを得るには、ストレージ アカウントが Data Box データを格納する地域は、デバイスが配置されている場所の近くでなければなりません。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。 


## <a name="next-steps"></a>次の手順

- [Data Box Gateway のシステム要件](data-box-gateway-system-requirements.md)を確認する。
- [Data Box Gateway の制限事項](data-box-gateway-limits.md)を理解する。
- Azure portal で [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) を配置する。




