---
title: Azure VM に SAP S/4HANA または BW/4HANA をデプロイする | Microsoft Docs
description: Azure VM に SAP S/4HANA または BW/4HANA をデプロイします
services: virtual-machines-linux
documentationcenter: ''
author: hobru
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/26/2021
ms.author: hobruche
ms.openlocfilehash: 0fff7d57e8a7dfa312b16accd15ef5990cdedf15
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131853626"
---
# <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library

[SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) を使用すると、SAP システムが完全に事前構成されたデモ環境を迅速に作成できます。 わずか数回のクリックで、自分の SAP システムを稼働させることができます。 以下のリンクでは、Azure にすばやくデプロイできる、いくつかのソリューションを取り上げています。 [インスタンスの作成] リンクを選択してください。 

ご自分の S ユーザーまたは P ユーザーで認証する必要があります。 P ユーザーは [SAP Community](https://community.sap.com/) を介して無料で作成できます。  詳細については、以下の概説をご覧ください。

| 解決策 | リンク |
| -------------- | :--------- | 
| **SAP S/4HANA 2020 FPS02, Fully-Activated Appliance**  2021 年 7 月 27 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|このアプライアンスには SAP S/4HANA 2020 (FPS02) が含まれていて、SAP S/4HANA コア機能向けの SAP Best Practices のほか、サービス、マスター データ管理 (MDG)、輸送管理 (TM)、ポートフォリオ管理 (PPM)、人材管理 (HCM)、分析、移行コックピットなど、その他のシナリオが事前にアクティブ化されています。 ユーザー アクセスは、SAP Fiori、SAP GUI、SAP HANA Studio、Windows リモート デスクトップ、または完全な管理者アクセス用のバックエンド オペレーティング システムを介して行われます。 |  [詳細]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) |
| **SAP S/4HANA 2020 FPS01, Fully-Activated Appliance** 2021 年 4 月 20 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=a0b63a18-0fd3-4d88-bbb9-4f02c13dc343&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|このアプライアンスには SAP S/4HANA 2020 (FPS01) が含まれていて、SAP S/4HANA コア機能向けの SAP Best Practices のほか、サービス、マスター データ管理 (MDG)、輸送管理 (TM)、ポートフォリオ管理 (PPM)、人材管理 (HCM)、分析、移行コックピットなど、その他のシナリオが事前にアクティブ化されています。 ユーザー アクセスは、SAP Fiori、SAP GUI、SAP HANA Studio、Windows リモート デスクトップ、または完全な管理者アクセス用のバックエンド オペレーティング システムを介して行われます。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/a0b63a18-0fd3-4d88-bbb9-4f02c13dc343) | 
| **SAP S/4HANA 2020 FPS02** 2021 年 6 月 10 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=c7cff775-cbf7-4cd1-a907-6eeca95a0946&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
| このソリューションは、フロントエンド アクセスを容易にするリモート デスクトップが含まれた標準の S/4HANA システム インストールとして提供されます。 これには、クライアント 100 で事前構成およびアクティブ化された SAP S/4HANA Fiori UI が含まれています。必須コンポーネントは SAP ノート 3045635「SAP S/4HANA 2020 FPS02 での SAP Fiori の高速アクティブ化」に従ってアクティブ化されています。 詳細については、リンクを参照してください。 | [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/c7cff775-cbf7-4cd1-a907-6eeca95a0946) | 
| **IDES EHP8 FOR SAP ERP 6.0 on SAP ASE、2021 年 6 月** 2021 年 6 月 10 日  | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=ed55a454-0b10-47c5-8644-475ecb8988a0&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|IDES システムは、SAP 内部デモ システムのコピーであり、カスタマイズとテストのためのプレイグラウンドとして使用されます。 この IDES システムは、SAP S/4HANA Fully-Activated Appliance (2020 FPS01 以上) のデータ移行シナリオでソース システムとして特に使用できます。 その他に、事前定義済みのマスター データとトランザクション データに基づく標準的なビジネス シナリオが含まれます。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/ed55a454-0b10-47c5-8644-475ecb8988a0) |
| **SAP BW/4HANA 2.0 SP07 including BW/4HANA Content 2.0 SP06** 2020 年 2 月 24 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=0f2f20f4-d012-4f76-81af-6ff15063db66&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|このソリューションは、SAP BW/4HANA の分析情報を提供します。 SAP BW/4HANA は、HANA 向けに最適化された次世代のデータ ウェアハウスです。 このソリューションは基本の BW/4HANA オプションに加え、HANA に対して最適化された一連の BW/4HANA コンテンツと、SAP Data Warehouse Cloud による次段階のハイブリッド シナリオを提供します。 システムが事前構成済みのため、シナリオの実装を直接開始できます。| [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/0f2f20f4-d012-4f76-81af-6ff15063db66) | 
| **SAP Business One 10.0 PL02, version for SAP HANA** 2020 年 8 月 4 日  | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=371edc8c-56c6-4d21-acb4-2d734722c712&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Business One は、SAP HANA の機能を活用した柔軟で手頃な拡張性の高い ERP ソリューションで、170 か国以上の 7 万を超える小規模および中規模企業に信頼されています。 このソリューションは、31 日間の試用版ライセンスを使用して事前構成され、お客様が選択したデモ データベースがプレインストールされています。 ソリューションのスコープと、新しいデモ データベースを簡単に追加する方法については、ファースト ステップ ガイドを参照してください。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/371edc8c-56c6-4d21-acb4-2d734722c712) |
| **Information Detector for SAP Data Custodian v2106** 2021 年 8 月 30 日  | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=db44680c-8a2a-405d-8963-838db38fa7dd&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|Information Detector for SAP Data Custodian を使用すると、クラウド リソースのデータのラベル付けを自動化できます。 Information Detector では、ご使用のインフラストラクチャ リソースを検索し、特定の種類の情報が含まれているかどうかを判断します。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/db44680c-8a2a-405d-8963-838db38fa7dd) |
| **SAP Yard Logistics 2009 for SAP S/4HANA** 2021 年 7 月 28 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=9cdf4f13-73a5-4743-a213-82e0d1a68742&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Yard Logistics アプリケーションを使用して、より効率的で収益性の高いサプライ チェーン ロジスティクスを実行します。 さまざまな視覚化およびレポート ツールを使用して、すべてのヤード プロセスを最大限に可視化し、計画されたワークロードをプレビューします。これにより、単一のシステムを使用してリソースの利用を最適化し、計画、実行、課金をサポートできます。|  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/9cdf4f13-73a5-4743-a213-82e0d1a68742) | 
| **SAP S/4HANA 2020 FPS02, Fully-Activated Appliance** 2021 年 7 月 27 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=d48af08b-e2c6-4409-82f8-e42d5610e918&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) | 
|このソリューションは、フロントエンド アクセスを容易にするリモート デスクトップが含まれた標準の S/4HANA システム インストールとして提供されます。 これには、クライアント 100 で事前構成およびアクティブ化された SAP S/4HANA Fiori UI が含まれています。必須コンポーネントは SAP ノート 3045635「SAP S/4HANA 2020 FPS02 での SAP Fiori の高速アクティブ化」に従ってアクティブ化されています。 詳細については、リンクを参照してください。| [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/d48af08b-e2c6-4409-82f8-e42d5610e918) | 
| **SAP Focused Run 3.0 FP01, unconfigured** 2021 年 7 月 21 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=82bdb96e-3578-41aa-a3e1-a6d9a8335ae1&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Focused Run は、大規模なシステムとアプリケーションの監視、アラート、分析を必要とするビジネスのために設計されています。 これは、一元化および自動化されたスケーラブルで安全な 1 つの環境ですべての顧客をホストしたいサービス プロバイダーにとって強力なソリューションです。 これはまた、システム管理、ユーザー監視、統合監視、構成およびセキュリティ分析に関する高度なニーズを抱えるお客様にも対応します。|  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/82bdb96e-3578-41aa-a3e1-a6d9a8335ae1) | 
| **SAP S/4HANA 2020 FPS01 Utilities Trial** 2021 年 7 月 21 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=68785eeb-a228-4aa8-8273-b4c30775590c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|このソリューションを使用すると、独自の SAP S/4HANA 2020 ユーティリティ システムを作成して、全領域の完全な管理者アクセスなど、ハンズオン エクスペリエンスを実現できます。 厳選されたガイド ツアーは、メータリング データの最適な処理、ロールベースの Fiori ユーザー インターフェイスを介した合理的な請求プロセス、顧客エンゲージメントにおける業界固有のカスタマー サービス管理を理解するのに役立ちます。|  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/68785eeb-a228-4aa8-8273-b4c30775590c)| 
| **SAP Product Lifecycle Costing 4.0 SP3 Hotfix 2** 2021 年 8 月 1 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=f2bf191a-7efc-48a2-b8ac-51756eb225bc&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP Product Lifecycle Costing は、製品ライフサイクルの初期段階において新製品のコストとその他のディメンション、または製品に関する見積もりを計算するためのソリューションです。コスト要因をすばやく特定し、選択肢を容易にシミュレートして比較します。|  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f2bf191a-7efc-48a2-b8ac-51756eb225bc)|
| **SAP ABAP Platform 1909, Developer Edition** 2021 年 6 月 21 日  | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=7bd4548f-a95b-4ee9-910a-08c74b4f6c37&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP ABAP Platform on SAP HANA では、SAP ABAP Platform 1909 Developer Edition on SAP HANA にアクセスできます。 このソリューションには、多数の追加要素 (SAP ABAP RESTful Application Programming Model、SAP Fiori Launchpad、SAP gCTS、SAP ABAP Test Cockpit、事前構成済みのフロントエンド/バックエンド接続など) が事前構成されています。また、すべての標準 ABAP AS インフラストラクチャ (トランザクション管理、データベース操作/永続化、Change and Transport System、SAP ゲートウェイ、ABAP Development Toolkit との相互運用性、SAP WebIDE など) も含まれます。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/7bd4548f-a95b-4ee9-910a-08c74b4f6c37) |
| **1: SAP ERP ソース システム (openSAP)** 2021 年 9 月 17 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=1a3556c0-0ee1-4a4c-8a5a-db08173df293&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|SAP ERP から SAP S/4HANA 初期状態へのシステム変換を実行するためのソリューション 1。 これは、SAP EHP6 for SAP ERP 6.0 SPS13 から SAP S/4HANA 2020 FPS00 への変換のために、テストされ準備されています。 |  [詳細]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/1a3556c0-0ee1-4a4c-8a5a-db08173df293) |
| **2: 準備手順の後、ソフトウェア更新マネージャー (openSAP) を実行する前の SAP ERP ソース システム** 2021 年 10 月 17 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=5eb92a4d-a704-48b8-b060-0647c63b667c&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|準備手順の後、ソフトウェア更新マネージャーを実行する前に SAP ERP から SAP S/4HANA へのシステム変換を実行するためのソリューション 2。 これは、SAP EHP6 for SAP ERP 6.0 SPS13 から SAP S/4HANA 2020 FPS00 への変換のために、テストされ準備されています。 |  [詳細]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/5eb92a4d-a704-48b8-b060-0647c63b667c) |
| **3. 技術変換の後、追加構成の前の SAP S/4HANA ターゲット システム** 2021 年 9 月 22 日  | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=4336a3fb-2fc9-4a93-9500-c65101ffc9d7&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|追加構成の前、SAP ERP から SAP S/4HANA への技術システム変換を実行した後のソリューション 3。 これは、SAP EHP6 for SAP ERP 6.0 SPS13 から SAP S/4HANA 2020 FPS00 への変換のために、テストされ準備されています。 |  [詳細](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/4336a3fb-2fc9-4a93-9500-c65101ffc9d7) |
| **4: 追加構成を含む SAP S/4HANA ターゲット システム (openSAP)** 2021 年 10 月 17 日 | [Create Instance]\(基本モード: インスタンスの作成\)](https://cal.sap.com/registration?sguid=f48f2b77-389f-488b-be2b-1c14a86b2e69&provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) |
|追加構成を含む SAP ERP から SAP S/4HANA への技術システム変換を実行した後のソリューション 4。 これは、SAP EHP6 for SAP ERP 6.0 SPS13 から SAP S/4HANA 2020 FPS00 への変換のために、テストされ準備されています。 |  [詳細]( https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8#/solutions/f48f2b77-389f-488b-be2b-1c14a86b2e69) |
 




---







## <a name="setup-and-get-started-with-sap-cloud-appliance-library"></a>SAP Cloud Appliance Library の設定と使用開始

> [!NOTE]
> SAP CAL について詳しくは、[SAP Cloud Appliance Library](https://cal.sap.com/catalog?provider=208b780d-282b-40ca-9590-5dd5ad1e52e8) の Web サイトにアクセスします。 SAP には [SAP Cloud Appliance Library 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience) に関するブログも用意されています。

> [!NOTE]
> 2017 年 5 月 29 日より、SAP CAL のデプロイに、クラシック デプロイ モデルに加えて Azure Resource Manager デプロイ モデルを使用できるようになりました。 クラシック デプロイ モデルの代わりに新しい Resource Manager デプロイ モデルを使用することをお勧めします。

## <a name="create-an-account-in-the-sap-cal"></a>SAP CAL にアカウントを作成する
1. 初めて SAP CAL にサインインするには、SAP S-User または SAP に登録された他のユーザーを使用します。 次に、SAP CAL で使用される SAP CAL アカウントを定義し、アプライアンスを Azure にデプロイします。 アカウントの定義では、次を行う必要があります。

    a. Azure のデプロイメント モデルを選択します (Resource Manager またはクラシック)。

    b. Azure サブスクリプションを入力します。 SAP CAL アカウントは 1 つのサブスクリプションにのみ割り当てることができます。 複数のサブスクリプションが必要な場合は、別の SAP CAL アカウントを作成する必要があります。

    c. Azure サブスクリプションにデプロイするために、SAP CAL にアクセス許可を付与します。

    > [!NOTE]
    > 次の手順では、Resource Manager デプロイメント用の SAP CAL アカウントを作成する方法を示します。 クラシック デプロイ モデルにリンクされている SAP CAL アカウントが既にある場合は、次の手順に従って新しい SAP CAL アカウントを作成する "*必要があります*"。 新しい SAP CAL アカウントは、Resource Manager モデルにデプロイする必要があります。

2. 新しい SAP CAL アカウントを作成します。 **[Accounts]\(アカウント\)** ページには、Azure の 3 つの選択肢が用意されています。 

    a. **[Microsoft Azure (classic)]\(Microsoft Azure (クラシック)\)** はクラシック デプロイ モデルで、推奨されなくなりました。

    b. **[Microsoft Azure]** は新しい Resource Manager デプロイ モデルです。

    c. **[Windows Azure operated by 21Vianet]\(21Vianet が運用する Windows Azure\)** は中国向けのオプションで、クラシック デプロイ モデルを使用します。

    Resource Manager モデルでデプロイするには、 **[Microsoft Azure]** を選択します。

    ![SAP CAL アカウントの詳細](./media/cal-s4h/s4h-pic-2a.png)

3. Azure Portal で見つかった Azure の **サブスクリプション ID** を入力します。

   ![SAP CAL アカウント](./media/cal-s4h/s4h-pic3c.png)

4. SAP CAL を承認し、定義した Azure サブスクリプションにデプロイするには、 **[Authorize]\(承認\)** をクリックします。 次のページがブラウザー タブに表示されます。

   ![Internet Explorer のクラウド サービスのサインイン](./media/cal-s4h/s4h-pic4c.png)

5. 複数のユーザーが列挙されている場合は、選択した Azure サブスクリプションの共同管理者としてリンクされている Microsoft アカウントを選択します。 次のページがブラウザー タブに表示されます。

   ![Internet Explorer のクラウド サービスの確認](./media/cal-s4h/s4h-pic5a.png)

6. **[Accept]\(受け入れる\)** をクリックします。 承認が成功した場合は、SAP CAL アカウントの定義が再び表示されます。 短い時間が経過すると、承認プロセスが成功したことを示すメッセージが表示されます。

7. ユーザーに新しく作成した SAP CAL アカウントを割り当てるには、右のテキスト ボックスに **ユーザー ID** を入力し、 **[Add]\(追加\)** をクリックします。

   ![アカウントからユーザーへの関連付け](./media/cal-s4h/s4h-pic8a.png)

8. アカウントを SAP CAL へのサインインに使用するユーザーに関連付けるには、 **[Review]\(レビュー\)** をクリックします。 
 
9. ユーザーと新しく作成した SAP CAL アカウントの間の関連付けを作成するには、 **[Create]\(作成\)** をクリックします。

   ![ユーザーから SAP CAL アカウントへの関連付け](./media/cal-s4h/s4h-pic9b.png)

正しく作成した SAP CAL アカウントでは、次のことが可能です。

- Resource Manager デプロイ モデルを使用する。
- SAP システムを Azure サブスクリプションにデプロイする。

これで Azure のユーザー サブスクリプションに S/4HANA のデプロイを開始できます。

> [!NOTE]
> 先に進む前に、必要な Azure コア クォータがあるかどうかを確認してください。 SAP CAL のソリューションによっては、Azure の M シリーズ VM を使用して、SAP HANA ベースのソリューションの一部をデプロイします。 お使いの Azure サブスクリプションに M シリーズ コア クォータがない場合があります。 その場合は、必要なクォータを得るために Azure サポートへの問い合わせが必要な可能性があります。

> [!NOTE]
> SAP CAL で Azure にソリューションをデプロイするときに、1 つの Azure リージョンのみを選択できることがわかる場合があります。 SAP CAL で提案されたリージョン以外の Azure リージョンにデプロイするには、SAP から CAL のサブスクリプションを購入する必要があります。 また、最初に提案された Azure リージョン以外に送るには、SAP でメッセージを開き、CAL アカウントを有効にする必要がある場合があります。

## <a name="deploy-a-solution"></a>ソリューションのデプロイ

SAP CAL の **[Solutions]\(ソリューション\)** ページからソリューションをデプロイしてみましょう。 SAP CAL では、次の 2 つのシーケンスでデプロイできます。

- 1 ページでデプロイされるシステムを定義する基本シーケンス
- VM のサイズをある程度選択できる高度なシーケンス 

ここでは、基本のパスを使用してデプロイします。

1. **[Account Details]\(アカウントの詳細\)** ページで、次のことを行う必要があります。

    a. SAP CAL アカウントを選択します (Resource Manager デプロイ モデルを使用したデプロイに関連付けられているアカウントを使用します)。

    b. インスタンスの **名前** を入力します。

    c. Azure の **リージョン** を選択します。 SAP CAL がリージョンを提案します。 別の Azure リージョンを使用する必要があるものの、SAP CAL のサブスクリプションがない場合は、SAP で CAL のサブスクリプションを注文する必要があります。

    d. ソリューションのマスター **パスワード** を 8 ～ 9 文字で入力します。 パスワードは別のコンポーネントの管理者に対して使用されます。

   ![SAP CAL の [Basic Mode: Create Instance]\(基本モード: インスタンスの作成\)](./media/cal-s4h/s4h-pic10a.png)

2. **[Create]\(作成\)** をクリックし、表示されたメッセージ ボックスで **[OK]** をクリックします。

   ![SAP CAL でサポートされる VM のサイズ](./media/cal-s4h/s4h-pic10b.png)

3. **[Private Key]\(秘密キー\)** ダイアログ ボックスで、 **[Store]\(格納\)** をクリックしてSAP CAL に秘密キーを格納します。 秘密キーに対してパスワード保護を使用するには、 **[Download]\(ダウンロード\)** をクリックします。 

   ![SAP CAL の秘密キー](./media/cal-s4h/s4h-pic10c.png)

4. SAP CAL の **警告** メッセージに目を通し、 **[OK]** をクリックします。

   ![SAP CAL の警告](./media/cal-s4h/s4h-pic10d.png)

    これで、デプロイが行われます。 ソリューションのサイズと複雑さに応じた一定の時間が経過すると (推定時間が SAP CAL によって示されます)、状態がアクティブであることが示され、使用する準備が整います。

5. あるリソース グループの他の関連リソースで収集された仮想マシンを見つけるには、Azure Portal にアクセスします。 

   ![新しいポータルにデプロイされている SAP CAL オブジェクト](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. SAP CAL ポータルで、状態が **[Active]\(アクティブ\)** として表示されます。 ソリューションに接続するには、 **[Connect]\(接続\)** をクリックします。 別のコンポーネントに接続するための別のオプションはこのソリューション内にデプロイされています。

   ![SAP CAL のインスタンス](./media/cal-s4h/active_solution.png)

7. いずれかのオプションを使用してデプロイ済みのシステムに接続する前に、 **[Getting Started Guide]\(ファースト ステップ ガイド\)** をクリックします。 

   ![インスタンスに接続する](./media/cal-s4h/connect_to_solution.png)

    ドキュメントによって各接続メソッドのユーザーが命名されます。 これらのユーザーのパスワードはデプロイ プロセスの最初に定義したマスター パスワードに設定されます。 ドキュメントにはより高機能なユーザーがパスワードとともに列挙されており、これを使用してデプロイ済みのシステムにサインインできます。 

    たとえば、Windows リモート デスクトップ マシンにプレインストールされている SAP GUI を使用している場合、S/4 システムは次のように表示されることがあります。

   ![プレインストールされた SAP GUI の SM50](./media/cal-s4h/gui_sm50.png)

    また、DBACockpit を使用している場合、インスタンスは次のように表示されることがあります。
 

   ![DBACockpit SAP GUI の SM50](./media/cal-s4h/dbacockpit.png)

数時間のうちに、正常な SAP S/4 アプライアンスが Azure にデプロイされます。

SAP CAP サブスクリプションを購入した場合、SAP が SAP CAL を介した Azure へのデプロイを全面的にサポートします。 サポート キューは BC-VCM-CAL です。







