---
title: Azure API for FHIR のディザスター リカバリー
description: この記事では、Azure API for FHIR のディザスター リカバリー機能を有効にする方法について説明します。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 08/03/2021
ms.author: zxue
ms.openlocfilehash: c060581bb25e8708893ac9c3e48e694a0b86c50d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780934"
---
# <a name="disaster-recovery-for-azure-api-for-fhir"></a>Azure API for FHIR のディザスター リカバリー

Azure API for FHIR® は、高速ヘルスケア相互運用性リソース (FHIR®) に基づいた、フル マネージド サービスです。 ビジネス要件とコンプライアンス要件を満たすために、Azure API for FHIR のディザスター リカバリー (DR) 機能を使用できます。

DR 機能では、15 分の目標復旧時点 (RPO) と 60 分の目標復旧時間 (RTO) が提供されます。

 ## <a name="how-to-enable-dr"></a>DR を有効にする方法 
  
DR 機能を有効にするには、1 回限りのサポート チケットを作成します。 Azure とペアになっているリージョン、または Azure API for FHIR がサポートされている別のリージョンを選択できます。 Microsoft サポート チームは、サポートの優先度に基づいて DR 機能を有効にします。

## <a name="how-the-dr-process-works"></a>DR プロセスのしくみ

DR プロセスには以下の手順が含まれます。 
* データのレプリケーション
* 自動フェールオーバー
* 影響を受けるリージョンの回復
* 手動フェールバック

### <a name="data-replication-in-the-secondary-region"></a>セカンダリ リージョンのデータのレプリケーション

既定では、Azure API for FHIR はバックアップと復元によってデータ保護を提供します。 ディザスター リカバリー機能が有効になっていると、データ レプリケーションが開始されます。 データ レプリカが自動的に作成され、セカンダリ Azure リージョンで同期されます。 最初のデータ レプリケーションには、データ量に応じて、数分から数時間、またはそれ以上かかる場合があります。 セカンダリ データ レプリカは、プライマリ データのレプリケーションです。 これはサービスを回復するために直接使用され、回復プロセスを高速化するのに役立ちます。

スループット RU/秒は、プライマリ リージョンとセカンダリ リージョンで同じ値でなければならないことに注意してください。

[ ![Azure の Traffic Manager。](media/disaster-recovery/azure-traffic-manager.png) ](media/disaster-recovery/azure-traffic-manager.png#lightbox)

### <a name="automatic-failover"></a>自動フェールオーバー

プライマリ リージョンの停止時に、Azure API for FHIR がセカンダリ リージョンに自動的にフェールオーバーし、同じサービス エンドポイントが使用されます。 サービスは 1 時間以内に再開されると予想され、データ損失の可能性は最大 15 分に相当するデータです。 その他の構成変更が必要になる場合があります。 詳細については、「[DR における構成変更](#configuration-changes-in-dr)」を参照してください。

[ ![ディザスター リカバリーにおけるフェールオーバー。](media/disaster-recovery/failover-in-disaster-recovery.png) ](media/disaster-recovery/failover-in-disaster-recovery.png#lightbox)

### <a name="affected-region-recovery"></a>影響を受けるリージョンの回復

影響を受けるリージョンが回復すると、セカンダリ リージョンとして自動的に使用できるようになり、データ レプリケーションが再開されます。 データ回復プロセスを開始するか、フェールバックのステップが完了するまで待機することができます。

[ ![ディザスター リカバリーにおけるレプリケーション。](media/disaster-recovery/replication-in-disaster-recovery.png) ](media/disaster-recovery/replication-in-disaster-recovery.png#lightbox)

回復されたリージョンにコンピューティングがフェールバックし、データがフェールバックされなかった場合、ネットワーク待機時間が発生する可能性があります。 主な理由は、コンピューティングとデータが 2 つの異なるリージョンに配置されているということです。 手動トリガーによって回復されたリージョンにデータがフェールバックされるとすぐに、ネットワーク待機時間は自動的に表示されなくなります。

[ ![ネットワーク待機時間。](media/disaster-recovery/network-latency.png) ](media/disaster-recovery/network-latency.png#lightbox)


### <a name="manual-failback"></a>手動フェールバック

コンピューティングは、回復されたリージョンに自動的にフェールバックします。 データは、スクリプトを使用して Microsoft サポート チームによって手動で回復されたリージョンに切り替えられます。 

[ ![ディザスター リカバリーにおけるフェールバック。](media/disaster-recovery/failback-in-disaster-recovery.png) ](media/disaster-recovery/failback-in-disaster-recovery.png#lightbox)

## <a name="configuration-changes-in-dr"></a>DR での構成の変更

Private Link、カスタマー マネージド キー (CMK)、IoMT FHIR コネクタ (医療分野の IoT)、$export が使用される場合は、その他の構成の変更が必要になる場合があります。

### <a name="private-link"></a>プライベート リンク

Azure API for FHIR がプロビジョニングされる前または後に、プライベート リンク機能を有効にすることができます。 DR 機能を有効にする前または後にプライベート リンクをプロビジョニングすることもできます。 DR 用に Private Link を構成する準備ができたら、以下の一覧を参照してください。

* プライマリ リージョンで Azure Private Link を構成します。 セカンダリ リージョンでは、このステップは必要ありません。 詳細については、「[プライベート リンクの構成](/azure/healthcare-apis/fhir/configure-private-link)」を参照してください

* プライマリ リージョンに 1 つの Azure VNet を作成し、セカンダリ リージョンにもう 1 つの VNet を作成します。 詳細については、「[Azure portal を使用した仮想ネットワークの作成](../../virtual-network/quick-create-portal.md)」をご覧ください。

* プライマリ リージョンでは、VNet はセカンダリ リージョン VNet への VNet ピアリングを作成します。 詳細については、「[仮想ネットワーク ピアリング](../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。

* 既定の設定を使用するか、必要に応じて構成を調整することができます。 重要なのは、2 つの仮想ネットワーク間でトラフィックが流れることができるということです。

* プライベート DNS が設定されている場合は、セカンダリ リージョンの VNet を「仮想ネットワーク リンク」 として手動で設定する必要があります。 プライマリ VNet は、Private Link エンドポイント作成フローの一部として既に追加されている必要があります。 詳細については、「[仮想ネットワーク リンク](../../dns/private-dns-virtual-network-links.md)」を参照してください。

* 必要に応じて、プライマリ リージョン VNet に 1 つの VM を設定し、セカンダリ リージョン VNet に 1 つの VM を設定します。 Azure API for FHIR には、両方の VM からアクセスできます。

プライベート リンク機能は、リージョンでの障害発生時およびフェールバックの完了後も動作し続けます。 詳細については、「[プライベート リンクの構成](/azure/healthcare-apis/fhir/configure-private-link)」を参照してください。

> [!NOTE]
> 仮想ネットワークと VNet ピアリングを構成しても、データのレプリケーションには影響しません。

### <a name="cmk"></a>CMK

サブスクリプションでマネージド キーをホストしているキー コンテナーにアクセスできる場合、Azure API for FHIR へのアクセスは維持されます。 Key Vault が接続を再確立するまでに最大 20 分かかる場合があるため、一時的なダウンタイムが発生する可能性があります。 詳細については、「[Azure Key Vault の可用性と冗長性](../../key-vault/general/disaster-recovery-guidance.md)」を参照してください。  

### <a name="export"></a>$export

エクスポート ジョブは、10 分後に別のリージョンから取得されます。ジョブの状態は更新されません。 リージョンで障害が発生した場合にストレージ アカウントを復旧するには、Azure Storage のガイダンスに従ってください。 詳細については、「[ディザスター リカバリーとストレージ アカウントのフェールオーバー](../../storage/common/storage-disaster-recovery-guidance.md)」を参照してください。 

Azure API for FHIR のシステム ID に同じアクセス許可を付与していることを確認します。 また、選択したネットワークでストレージ アカウントが構成されている場合は、「[FHIR データをエクスポートする方法](/azure/healthcare-apis/fhir/export-data)」を参照してください。

### <a name="iomt-fhir-connector"></a>IoMT FHIR コネクタ

障害が発生したリージョンが復元されるまで、既存の接続は機能しません。 フェールオーバーが完了し、FHIR サーバーにアクセスできるようになったら、新しい接続を作成できます。 この新しい接続は、フェールバックが発生しても機能し続けます。

> [!NOTE]
> IoMT コネクタはプレビュー機能であり、ディザスター リカバリーはサポートされていません。 

## <a name="how-to-test-dr"></a>DR のテスト方法

必須ではありませんが、非運用環境で DR 機能をテストすることができます。 DR テストでは、データのみが含まれ、コンピューティングは含まれません。 

DR テストでは、次の手順を検討してください。

* テスト データを使用してテスト環境を準備します。 データをレプリケートする時間を短縮するために、少量のデータを含むサービス インスタンスを使用することをお勧めします。
 
* サポート チケットを作成し、Azure サブスクリプションと、テスト環境用の Azure API for FHIR のサービス名を指定します。

* 他の DR テストと同様に、テスト計画を立てます。
 
* Microsoft サポート チームは、DR 機能を有効にし、フェールオーバーが行われたことを確認します。

* DR テストを実施し、テスト結果を記録します。これには、データ損失やネットワーク待機時間の問題が含まれます。 

* フェールバックの場合は、Microsoft サポート チームに問い合わせて、フェールバックの手順を完了してください。
 
* (省略可能) Microsoft サポート チームとフィードバックを共有します。


> [!NOTE]
> DR テストでは、テスト中にテスト環境のコストが 2 倍になります。 DR テストが完了し、DR 機能が無効になった後は、追加コストは発生しません。

## <a name="cost-of-disaster-recovery"></a>ディザスター リカバリーのコスト

ディザスター リカバリー機能では、コンピューティングのデータとデータ レプリカがセカンダリ リージョンの環境で実行されるため、追加のコストが発生します。 価格の詳細については、「[Azure API for FHIR の価格]( https://azure.microsoft.com/pricing/details/azure-api-for-fhir)」の Web ページを参照してください。

> [!NOTE]
> DR オファリングは、[SLA for Azure API for FHIR](https://azure.microsoft.com/support/legal/sla/azure-api-for-fhir/v1_0/) 1.0 の対象となります。


## <a name="next-steps"></a>次のステップ

この記事では、Azure API for FHIR の DR のしくみと、それを有効にする方法について説明しました。 Azure API for FHIR でサポートされている他の機能の詳細については、以下を参照してください。

>[!div class="nextstepaction"]
>[FHIR でサポートされている機能](fhir-features-supported.md)