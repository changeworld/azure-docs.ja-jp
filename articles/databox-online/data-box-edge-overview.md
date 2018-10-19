---
title: Microsoft Azure Data Box Edge の概要 | Microsoft Docs
description: Azure へのネットワーク ベースの転送に物理デバイスを使用するストレージ ソリューションである Azure Data Box Edge について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 6422482a31ad8022b795cb357c37ad8560781767
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166595"
---
# <a name="what-is-azure-data-box-edge-preview"></a>Azure Data Box Edge (プレビュー) とは 

Azure Data Box Edge は、データを処理してネットワーク経由で Azure に送信できるストレージ ソリューションです。 この記事では、Data Box Edge ソリューションの概要、利点、主な機能、このデバイスを配置できるシナリオについて説明します。 

Data Box Edge は、Microsoft 製の物理デバイスを使用して、安全なデータ転送を高速化します。 物理デバイスは構内にあり、NFS プロトコルと SMB プロトコルを使用してデータを書き込みます。 

Data Box Edge は、Data Box Gateway のゲートウェイ機能をすべて備えています。 Data Box には AI 対応のエッジ コンピューティング機能が追加装備されており、Azure ブロック ブロブ、ページ ブロブ、または Azure ファイルに転送する際にデータの分析、処理、またはフィルター処理に役立ちます。  

> [!IMPORTANT]
> - Data Box Edge はプレビュー段階です。 このサービスに [サインアップ](#sign-up) してください。
> - このソリューションをデプロイする前に、[プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。


## <a name="use-cases"></a>ユース ケース

Azure Data Box Edge は、ネットワーク データ転送の機能を備えた AI 対応のエッジ コンピューティング デバイスです。 Data Box Edge は、以下のようなさまざまなシナリオで、データ転送に使用できます。

- **データの前処理** - データが生成される場所の近くにいる間に、オンプレミスまたは IoT デバイスからのデータを分析して、すばやく結果を得ることができます。 Data Box Edge は、データ セット全体をクラウドに転送して、より高度な処理やより深い分析を行います。  前処理は以下の目的で使用できます。 

    - データを集計します。
    - 個人を特定できる情報 (PII) を削除するなど、データを変更します。
    - クラウドでのより深い分析に必要なデータをサブセット化して転送します。
    - 分析して、IoT イベントに対応します。 

- **Inference Azure Machine Learning** - Data Box Edge により、Machine Learning (ML) モデルを実行して、データがクラウドに送信される前に対応できるように、迅速な結果を得ることができます。 ML モデルを再調整して改善し続けるために、データ セット全体が転送されます。

- **ネットワーク経由でデータを Azure に転送する** - 計算や分析をさらに可能にするか、アーカイブ目的のために、Data Box Edge を使用して、Azure にデータを簡単かつ迅速に転送します。 

## <a name="benefits"></a>メリット

Data Box Edge には次の利点があります。

- **簡単なデータ転送**- ローカル ネットワーク共有で操作するのと同じくらい簡単に、Azure ストレージとの間でデータを転送できます。  
- **高性能** - Azure との間で高性能な転送を可能にします。 
- **高速アクセス** - オンプレミス ファイルに高速にアクセスするために、最新のファイルをキャッシュします。  
- **制限された帯域幅の使用** - 営業時間のピーク時にネットワークの使用が制限されるように調整されている場合でも、Azure にデータを書き込むことができます。  
- **データ変換** - Azure に転送する際に、データの分析、処理、またはフィルター処理が可能になります。

## <a name="key-capabilities"></a>主な機能

Data Box Edge には次の機能があります。

|機能 |説明  |
|---------|---------|
|高性能     | 完全に自動化され、高度に最適化されたデータ転送と帯域幅。|
|サポートされるプロトコル     | データ インジェストのために、標準の SMB プロトコルと NFS プロトコルをサポートします。 <br> サポート対象のバージョンについては、「[Data Box Edge のシステム要件](http://aka.ms/dbe-docs)」を参照してください。|
|コンピューティング       |データの分析、処理、フィルター処理が可能です。|
|データ アクセス     | クラウドでデータをさらに処理するために、クラウド API を使用して、Azure Storage Blob と Azure ファイルからデータを直接アクセスします。|
|高速アクセス     | 最近使用されたファイルを高速にアクセスするために、デバイス上でローカル キャッシュを行います。|
|オフライン アップロード     | 切断モードでは、オフライン アップロードのシナリオに対応します。|
|データ更新     | ローカル ファイルをクラウドから最新の情報に更新する機能。|
|暗号化    | ローカルでデータを暗号化し、クラウドへのデータ転送を *https* 経由で保護する BitLocker のサポート。       |
|回復性     | 組み込まれたネットワークの回復性。        |


## <a name="features-and-specifications"></a>機能と仕様

Data Box Edge 物理デバイスには次の機能があります。

| 機能/仕様                                          | 説明              |
|---------------------------------------------------------|--------------------------|
| ディメンション   | 幅: 17.25" 奥行: 27.25" 高さ: 1.75"<br>(取っ手と PSU ハンドルを除く)  |            
| ラック スペース|ラックに収納した場合 1U|
| ケーブル| 電源ケーブル x 2<br>1 Gbps RJ45 ケーブル x 2<br>10 Gbps SFP x 2 と銅線ケーブル|
| コンポーネント|組み込み型電源ユニット (PSU) x 2|
| CPU|10 コア搭載 Intel Xeon プロセッサ x 2  |
| メモリ| 64 GB RAM|
| ディスク| NVMe SSD x 8、各ディスクは 1.6 TB <br> 1 つの NVMe SSD に障害が発生すると、システムは機能しなくなります。 |
| ローカル ストレージの容量| 合計容量 12.8 TB|
| ネットワーク インターフェイス| 1 GbE インターフェイス x 2 - 1 つは管理用で、ユーザー構成不可であり、初期セットアップに使用されます。 他のデータ インターフェイスはユーザー構成可能であり、既定では DHCP です。 <br>25 GbE インターフェイス x 2 – これらは 10 GbE インターフェイスとしても動作可能です。 これらのデータ インターフェイスは、DHCP (既定) または静的 としてユーザーが構成できます。 <br> 25 GbE インターフェイス x 2 – これらのデータ インターフェイスは、DHCP (既定) または静的 としてユーザーが構成できます。|

## <a name="components"></a>コンポーネント

Data Box Edge ソリューションは、Data Box Edge リソース、Data Box Edge 物理デバイス、ローカル Web UI で構成されています。

* **Data Box Edge 物理デバイス** - Azure にデータを送信するように構成可能な Microsoft 製の 1U ラックマウント型サーバー。 
    
* **Data Box リソース** - さまざまな地理的な場所からアクセスできる Web インターフェイスから、Data Box デバイスを管理できる、Azure portal でのリソース。 Data Box Edge リソースを使用して、リソースの作成と管理、デバイスとアラートの表示と管理、および共有の管理を行います。  

    <!--![The Data Box Edge service in Azure portal](media/data-box-overview/data-box-Edge-service1.png)-->

    詳細については、「[Data Box Edge サービスを使用した Data Box Edge デバイスの管理](http://aka.ms/dbe-docs)」を参照してください。

* **Data Box ローカル Web UI** - ローカル Web UI を使用して、診断の実行、Data Box Edge デバイスのシャットダウンと再起動し、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の提出を行います。

    <!--![The Data Box Edge local web UI](media/data-box-Edge-overview/data-box-Edge-local-web-ui.png)-->

    Web ベース UI の使用については、「[Web ベース UI を使用した Data Box の管理](http://aka.ms/dbe-docs)」を参照してください。


## <a name="region-availability"></a>利用可能なリージョン

Data Box Edge 物理デバイス、Azure リソース、データ転送先のターゲット ストレージ アカウントは、すべて同じ地域にある必要はありません。

- **リソースの可用性** - このリリースでは、Data Box Edge リソースは次の地域で利用できます。
    - **米国** - 米国西部と米国東部
    - **欧州連合** - 欧州西部
    - **アジア太平洋** - 東南アジア

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、すべての Azure 地域で利用できます。 

    最適なパフォーマンスを得るには、ストレージ アカウントが Data Box データを格納する地域は、デバイスが配置されている場所の近くでなければなりません。 デバイスから離れた場所にあるストレージ アカウントは、待ち時間が長くなり、パフォーマンスが低下します。 


## <a name="sign-up"></a>サインアップ

Data Box Edge はプレビュー段階であり、サインアップする必要があります。 Data Box Gateway にサインアップするには、次の手順を実行します。

1. Azure portal ([https://aka.ms/databox-edge](https://aka.ms/databox-edge)) にサインインします。

2. Data Box Edge プレビューに使用するサブスクリプションを選択します。 Data Box Edge リソースを配置する地域を選択します。 Data Box Edge オプションで、**[サインアップ]** をクリックします。

    ![Data Box Edge のサインアップ 3](media/data-box-edge-overview/data-box-edge-sign-up3.png)

3.  データ所在国、時間枠、ターゲットの Azure サービス (データ転送、ネットワーク帯域幅、データ転送頻度) に関する質問に回答します。 **プライバシーと利用規約**を確認し、**[Microsoft はお客様の電子メール アドレスを使用してお客様にご連絡いたします]** に対してチェックボックスを選択します。

    ![Data Box Edge のサインアップ 4](media/data-box-edge-overview/data-box-edge-sign-up4.png)

4. サインアップしてプレビューが有効になると、Data Box Edge を注文できます。

## <a name="next-steps"></a>次の手順

- [Data Box Edge のシステム要件](http://aka.ms/dbe-docs) を確認する。
- [Data Box Edge の制限事項](http://aka.ms/dbe-docs) を理解する。
- Azure portal で [Azure Data Box Edge](http://aka.ms/dbe-docs) を配置する。




