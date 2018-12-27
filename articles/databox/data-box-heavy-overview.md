---
title: Microsoft Azure Data Box Heavy の概要 | Microsoft Docs in data
description: 大量のデータを Azure に転送できるハイブリッド ソリューションである Azure Data Box について説明します
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2018
ms.locfileid: "51235158"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Azure Data Box Heavy とは (プレビュー)

Microsoft Azure Data Box ハイブリッド ソリューションを使用すると、迅速かつ安価な信頼性の高い方法で、数百テラバイトのデータを Azure に送信できます。 貨物を介して 1 PB のストレージ容量を持つ独自のストレージ デバイスを出荷することにより、セキュリティで保護されたデータ転送を高速化します。 デバイスは堅牢な筐体で保護され、転送中のデータはセキュリティで保護されます。

Data Box Heavy は現在プレビュー段階にあり、Azure portal を介してサインアップしてデバイスを要求することができます。 データセンターでデバイスを受け取ったら、ローカル Web UI を使用して設定できます。 データをサーバーからデバイスにコピーし、デバイスを Azure に返送します。 そのデータは、Azure のデータセンターにてデバイスから Azure へと自動的にアップロードされます。 プロセス全体は、Azure portal の Data Box サービスでエンド ツー エンドで追跡できます。


> [!IMPORTANT]
> - Data Box Heavy はプレビュー段階です。 このソリューションを展開する前に、[プレビューに関する Azure のサービス利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)を確認してください。 
> - デバイスを要求するには、[Preview portal](https://aka.ms/) でサインアップします。
> - プレビュー期間中、Data Box Heavy は、米国および欧州連合のお客様に出荷できます。 詳細については、「[Region availability (利用可能なリージョン)](#region-availability)」をご覧ください。

## <a name="use-cases"></a>ユース ケース

Data Box Heavy は、ネットワーク接続が制限されていないシナリオで、500 TB を超えるデータ サイズを転送する場合に最適です。 データは 1 回だけ移動することも、定期的に移動することもできます。また、初期一括データ転送の後に定期的な転送を行うこともできます。 Data Box Heavy は、以下のようなさまざまなシナリオで、データ転送に使用できます。

 - **1 回限りの移行** - 大量のオンプレミス データを Azure に移動する場合。 
     - オフライン テープのメディア ライブラリを Azure に移動し、オンライン メディア ライブラリを作成する。
     - VM ファーム、SQL Server、アプリケーションを Azure に移行する
     - HDInsight を使用して詳細な分析やレポートの作成を行うために、履歴データを Azure に移動する

 - **初期一括転送** - Data Box Heavy (シード) を使用して初期一括転送が実行された後に、ネットワーク経由で増分転送を行う場合。 
     - Commvault などのバックアップ ソリューション パートナーと Data Box Heavy を使用して、最初に大量の履歴バックアップを Azure に移動する場合など。 完了後のデータの増分は、ネットワーク経由で Azure ストレージに転送する。

 - **定期的なアップロード** - 定期的に生成される大量のデータを Azure に移動する必要がある場合。 たとえば、石油掘削装置や風力発電地帯でビデオ コンテンツが生成されるエネルギー探査でこれを実行します。      

## <a name="benefits"></a>メリット

Data Box Heavy は、ネットワークにほとんどまたはまったく影響を与えずに大量のデータを Azure に移動することを目的としています。 このソリューションには次の利点があります。

- **速度** - Data Box Heavy は、高パフォーマンスの 40 Gbps ネットワーク インターフェイスを使用します。

- **セキュリティ保護** - Data Box Heavy には、デバイス、データ、サービスのセキュリティ保護が組み込まれています。
    - デバイスの筐体は堅牢で、不正開封防止ネジと不正開封防止ステッカーによってセキュリティ保護されています。 
    - デバイスのデータは、AES 256 ビット暗号化によって常にセキュリティ保護されています。
    - デバイスは、Azure portal で提供されるパスワードでのみロックを解除できます。
    - このサービスは、Azure のセキュリティ機能によって保護されています。
    - データが Azure にアップロードされたら、NIST 800-88r1 規格に従って、デバイスのディスクが完全にワイプされます。


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>コンポーネント

Data Box Heavy に含まれるコンポーネントを次に示します。

* **Data Box Heavy デバイス** - データを安全に保存する堅牢な外装を備えた物理的なデバイス。 このデバイスでは、800 TB のストレージ容量を使用できます。 

    
* **Data Box サービス** - さまざまな地理的場所からアクセスできる Web インターフェイスから、Data Box Heavy デバイスを管理できる、Azure portal の拡張機能。 Data Box サービスを使用すると、Data Box Heavy デバイスの日常的な管理を実行できます。 サービス タスクには、注文の作成と管理、警告の表示と管理、共有の管理が含まれます。  

* **ローカル Web ユーザー インターフェイス** - デバイスの構成に使用する Web ベースの UI。この UI を使用して、ローカル ネットワークに接続し、デバイスを Data Box サービスに登録できます。 ローカル Web UI を使用すると、デバイスのシャット ダウンと再起動、コピー ログの表示、Microsoft サポートへの連絡とサービス要求の送信も行うことができます。


## <a name="the-workflow"></a>ワークフロー

一般的なフローには次の手順が含まれます。

1. **注文** - Azure portal で注文を作成し、配送情報とデータのコピー先 Azure ストレージ アカウントを指定します。 デバイスが利用可能な場合は、Azure がデバイスを準備し、出荷追跡 ID が割り当てられたデバイスを出荷します。

2. **受領** - デバイスが到着したら、デバイスをネットワークに接続し、指定のケーブルを電源につなぎます。 電源を入れてデバイスに接続します。 デバイスのネットワークを構成し、データをコピーするホスト コンピューターに共有をマウントします。

3. **データのコピー** - Data Box Heavy の共有にデータをコピーします。

4. **返却** - デバイスを準備し、電源を切って Azure データセンターに返送します。

5. **アップロード** - デバイスから Azure にデータが自動的にコピーされます。 デバイスのディスクは、米国国立標準技術研究所 (NIST) のガイドラインに従って安全に消去されます。

このプロセス全体を通じて、状態のすべての変更について電子メールで通知されます。 

## <a name="region-availability"></a>利用可能なリージョン

Data Box Heavy は、サービスが展開されているリージョン、デバイスが出荷される国、データの転送対象となる Azure ストレージ アカウントに基づいてデータを転送できます。 

- **サービスの可用性** - このリリースでは、Data Box Heavy は次のリージョンで利用できます。
    - 米国のすべてのパブリック クラウド リージョン - 米国中西部、米国西部 2、米国西部、米国中南部、米国中部、米国中北部、米国東部、米国東部 2。
    - 欧州連合 - 西ヨーロッパ、北ヨーロッパ。
    - 英国 - 英国南部、英国西部。
    - フランス - フランス中部、フランス南部。

- **転送先ストレージ アカウント** - データを格納するストレージ アカウントは、サービスが使用可能なすべての Azure リージョンで利用できます。 

## <a name="sign-up"></a>サインアップ

Data Box Heavy はプレビュー段階であり、サインアップする必要があります。 Data Box Heavy サービスにサインアップするには、次の手順を実行します。

1. Azure portal (https://aka.ms/azuredatabox) にサインインします。
2. **+** をクリックして、新しいリソース グループを作成します。 **Azure Data Box** を検索します。 **Azure Data Box** サービスを選択します。

    <!--![The Data Box Heavy sign up 1]()-->

3. **Create** をクリックしてください。

    <!--![The Data Box Heavy sign up 2]()-->

4. Data Box Heavy プレビューに使用するサブスクリプションを選択します。 Data Box Heavy リソースをデプロイするリージョンを選択します。 **[Data Box Heavy]** オプションで、**[サインアップ]** をクリックします。

   <!--![The Data Box Heavy sign up 3]()-->

5. データ所在国、時間枠、ターゲットの Azure サービス (データ転送、ネットワーク帯域幅、データ転送頻度) に関する質問に回答します。 プライバシーと利用規約を確認し、[Microsoft はお客様の電子メール アドレスを使用してお客様にご連絡いたします] のチェックボックスを選択します。

    <!--![The Data Box Heavy sign up 4]()-->

サインアップしてプレビューが有効になると、Data Box Heavy を注文できます。




