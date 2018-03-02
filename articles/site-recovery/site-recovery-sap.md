---
title: "Azure Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する | Microsoft Docs"
description: "この記事では、Azure Site Recovery を使用して SAP NetWeaver アプリケーションのデプロイを保護する方法について説明します。"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
<<<<<<< HEAD
ms.openlocfilehash: b6ab734186f23d51d60e51bd0946329d5209097b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
=======
ms.openlocfilehash: 1ee472498bdefc4eeb9863670e5480326b5ba6d8
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/22/2018
>>>>>>> f6e60491ca10793f438340e0ef568521bf62f91f
---
# <a name="protect-a-multi-tier-sap-netweaver-application-deployment-by-using-site-recovery"></a>Site Recovery を使用して多層 SAP NetWeaver アプリケーションのデプロイを保護する

多くの場合、大規模および中規模サイズの SAP デプロイには、何らかの形式のディザスター リカバリー ソリューションを使用します。 SAP のようなアプリケーションに移行するコア ビジネス プロセスが増えているため、堅牢でテスト可能なディザスター リカバリー ソリューションの重要性は高まっています。 Azure Site Recovery はテストされ、SAP アプリケーションと統合済みです。 また、Site Recovery はほとんどのオンプレミス ディザスター リカバリー ソリューションよりも優れた機能で、競合するソリューションよりも低い総保有コスト (TCO) です。

Site Recovery を使用すると、次のことができます。
* **コンポーネントを Azure にレプリケートすることによって、**オンプレミスで実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* **コンポーネントを他の Azure データセンターにレプリケートすることによって、Azure** で実行される SAP NetWeaver および非 NetWeaver 運用アプリケーションの保護を有効にします。
* Site Recovery を使用して SAP デプロイを Azure に移行することで、**クラウド移行を簡略化**します。
* SAP アプリケーションをテストするためのオンデマンドの運用クローンを作成することによって、**SAP プロジェクトのアップグレード、テスト、および、プロトタイプ作成を簡略化**します。

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して SAP NetWeaver アプリケーションのデプロイを保護する方法について説明します。 この記事では、Site Recovery を使用して別の Azure データセンターにレプリケートすることで、Azure 上で 3 層の SAP NetWeaver デプロイを保護するためのベスト プラクティスを取り上げています。 サポートされるシナリオと構成、およびテスト フェールオーバー (ディザスター リカバリーの訓練) と実際のフェイル オーバーを実行する方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事の内容を学習するには、次のタスクの実行方法を知っている必要があります。

* [仮想マシンを Azure にレプリケートする](azure-to-azure-walkthrough-enable-replication.md)
* [復旧ネットワークを設計する](site-recovery-azure-to-azure-networking-guidance.md)
* [Azure へのテスト フェールオーバーを実行する](azure-to-azure-walkthrough-test-failover.md)
* [Azure へのフェールオーバーを実行する](site-recovery-failover.md)
* [ドメイン コントローラーをレプリケートする](site-recovery-active-directory.md)
* [SQL Server のレプリケート](site-recovery-sql.md)

## <a name="supported-scenarios"></a>サポートされるシナリオ
Site Recovery を使用して、次のシナリオのディザスター リカバリー ソリューションを実装できます。
* ある Azure データセンターで実行されている SAP システムを、別の Azure データセンターにレプリケートします (Azure 間の ディザスター リカバリー)。 詳細については、「[Azure から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-a2a-architecture)」をご覧ください。
* VMware (または物理) サーバーのオンプレミス上で実行されている SAP システムを、Azure データセンターのディザスター リカバリー サイトにレプリケートします (VMware と Azure 間のディザスター リカバリー)。 このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[VMware から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-v2a-architecture)」をご覧ください。
* Hyper-V のオンプレミス上で実行されている SAP システムを、Azure データセンターのディザスター リカバリー サイトにレプリケートします (Hyper-V と Azure 間のディザスター リカバリー)。 このシナリオでは、いくつかの追加コンポーネントが必要です。 詳細については、「[Hyper-V から Azure へのレプリケーション アーキテクチャ](https://aka.ms/asr-h2a-architecture)」をご覧ください。

この記事では、Azure 間のディザスター リカバリー シナリオを使用して Site Recovery の SAP ディザスター リカバリー機能を示します。 Site Recovery レプリケーションはアプリケーション固有ではないため、説明されているプロセスの他のシナリオへの適用も期待できます。

### <a name="required-foundation-services"></a>必要な基礎サービス
この記事で説明するシナリオでは、次の基盤サービスがデプロイされます。
* Azure ExpressRoute または Azure VPN Gateway
* Azure で実行されている少なくとも 1 つの Active Directory ドメイン コントローラーと DNS サーバー

Site Recovery をデプロイする前に、このインフラストラクチャを確立することをお勧めします。

## <a name="typical-sap-application-deployment"></a>一般的な SAP アプリケーションの展開
大規模な SAP 環境の場合、通常は 6 から 20 個の SAP アプリケーションをデプロイします。 このようなアプリケーションのほとんどは、SAP NetWeaver ABAP または Java エンジンに基づいています。 小規模な固有の NetWeaver SAP ではない多数のスタンドアロン エンジンと、通常は SAP ではないいくつかのアプリケーションで、これらのコア NetWeaver がサポートされます。  

お使いの環境で実行されるすべての SAP アプリケーションの一覧を作成することが重要です。 次に、配置モード (2 層または 3 層)、バージョン、パッチ、サイズ、チャーン レート、およびディスクの永続性に関する要件を決定します。

![一般的な SAP デプロイ パターンの図](./media/site-recovery-sap/sap-typical-deployment.png)

SAP データベースの永続化層は、SQL Server AlwaysOn、Oracle Data Guard、または SAP HANA システム レプリケーションなどのネイティブ DBMS ツールを使用して保護します。 SAP データベース層と同様に、クライアント層は Site Recovery では保護されません。 この層に影響を及ぼす要因を考慮することが重要です。 要因には、DNS 伝達の遅延、セキュリティ、およびディザスター リカバリー センターへのリモート アクセスなどがあります。

Site Recovery は、SAP SCS および ASCS などのアプリケーション層に推奨されるソリューションです。 NetWeaver ではない SAP アプリケーションや、SAP ではないアプリケーションなど、他のアプリケーションが SAP デプロイ環境全体の一部を構成しています。 それらを Site Recovery で保護する必要があります。

## <a name="replicate-virtual-machines"></a>仮想マシンのレプリケート
Azure ディザスター リカバリー データ センターへのすべての SAP アプリケーション仮想マシンのレプリケートを開始するには、「[仮想マシンを Azure にレプリケートする](azure-to-azure-walkthrough-enable-replication.md)」セクションの手順に従います。

静的 IP アドレスを使用する場合は、仮想マシンに割り当てる IP アドレスを指定できます。 IP アドレスを設定するには、**[コンピューティングとネットワーク] の設定** > **[ネットワーク インターフェイス カード]** に移動します。

![Site Recovery のネットワーク インターフェイス カードのウィンドウで、プライベート IP アドレスを設定する方法を示したスクリーンショット](./media/site-recovery-sap/sap-static-ip.png)

## <a name="create-a-recovery-plan"></a>復旧計画の作成
復旧計画では、フェールオーバー時における多層アプリケーション内の各種階層の順序付けがサポートされます。 順序付けは、アプリケーションの一貫性の保守に役立ちます。 多層 Web アプリケーションの復旧計画を作成する際には、「[復旧計画の作成](site-recovery-create-recovery-plans.md)」で説明されている手順を完了します。

### <a name="add-scripts-to-the-recovery-plan"></a>復旧計画へのスクリプトの追加
お使いのアプリケーションを正常に機能させるには、フェールオーバー後、またはテスト フェールオーバー時に、Azure の仮想マシンに対して一定の操作を実行することが必要な場合があります。 フェールオーバー後の操作は一部自動化することもできます。 たとえば、対応するスクリプトを復旧計画に追加することで、DNS エントリを更新したり、バインドと接続を変更したりできます。

### <a name="dns-update"></a>DNS の更新
DNS が動的 DNS 更新用に構成されている場合、仮想マシンは通常、その起動後に新しい IP アドレスで DNS を更新します。 仮想マシンの新しい IP アドレスで DNS を更新するための明示的な手順を追加する場合は、[DNS の IP アドレスを更新するためのスクリプト](https://aka.ms/asr-dns-update)を、復旧計画グループのフェールオーバー後のアクションとして追加します。  

## <a name="example-azure-to-azure-deployment"></a>Azure 間のデプロイ例
次の図は、Site Recovery の Azure 間のディザスター リカバリー シナリオを示しています。

![Azure 間のレプリケーション シナリオの図](./media/site-recovery-sap/sap-replication-scenario.png)

* プライマリ データセンターはシンガポール (Azure 東南アジア) にあります。 ディザスター リカバリー データセンターは香港 (Azure 東アジア) にあります。 このシナリオでは、シンガポールで同期モードの SQL Server AlwaysOn を実行している 2 つの VM によって、ローカルの高可用性を実現しています。
* ファイル共有 SAP ASCS は、SAP の単一障害点に対する高可用性を実現します。 ファイル共有 ASCS では、クラスター共有ディスクを必要としません。 SIOS のようなアプリケーションは必要ありません。
* DBMS 層のディザスター リカバリー保護は、非同期レプリケーションを使用して達成されます。
* このシナリオは、"対照的なディザスター リカバリー" を示しています。 この用語は、運用環境の完全なレプリカであるディザスター リカバリー ソリューションを表します。 ディザスター リカバリーの SQL Server ソリューションは、ローカルの高可用性を備えています。 対称的なディザスター リカバリーは、データベース層には必須ではありません。 多くの顧客は、クラウド デプロイの柔軟性を活用して、ディザスター リカバリー イベント後に迅速にローカルの高可用性ノードを作成します。
* 次の図は、Site Recovery でレプリケートされる SAP NetWeaver ASCS およびアプリケーション サーバー層を示しています。

## <a name="run-a-test-failover"></a>テスト フェールオーバーの実行

1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  SAP アプリケーション用に作成した復旧計画を選択します。
3.  **[テスト フェールオーバー]** を選択します。
4.  テスト フェールオーバー プロセスを開始するには、復旧ポイントと Azure 仮想ネットワークを選択します。
5.  セカンダリ環境が立ち上がったら、検証を実行します。
6.  検証が完了したら、フェールオーバー環境をクリーニングするために、**[テスト フェールオーバーのクリーンアップ]** を選択します。

詳しくは、「[Site Recovery での Azure へのフェールオーバーをテストする](site-recovery-test-failover-to-azure.md)」をご覧ください。

## <a name="run-a-failover"></a>フェールオーバーの実行

1.  Azure Portal で､Recovery Service コンテナーを選択します。
2.  SAP アプリケーション用に作成した復旧計画を選択します。
3.  **[フェールオーバー]** を選択します。
4.  フェールオーバー プロセスを開始するには、復旧ポイントを選択します。

詳しくは、「[Site Recovery でのフェールオーバー](site-recovery-failover.md)」をご覧ください。

## <a name="next-steps"></a>次の手順
* Site Recovery を使用して SAP NetWeaver デプロイ用のディザスター リカバリー ソリューションの構築に関する詳細を確認するために、ダウンロード可能なホワイト ペーパー「[SAP NetWeaver: Building a Disaster Recovery Solution with Azure Site Recovery](http://aka.ms/asr-sap)」 (SAP NetWeaver: Azure Site Recovery でディザスターリカバリー ソリューションを構築する) をご覧ください。 このホワイトペーパーでは、さまざまな SAP アプリケーションに関する推奨事項、Azure 上の SAP でサポートされるアプリケーションと VM の種類、ディザスター リカバリー ソリューションのテスト計画のオプションについて説明しています。
* Site Recovery を使用した[他のワークロードのレプリケート](site-recovery-workload.md)に関する記事をご覧ください。
