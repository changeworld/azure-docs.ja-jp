---
title: よく寄せられる質問
description: Azure Container Instances サービスに関連するよく寄せられる質問の回答
author: dkkapur
ms.topic: article
ms.date: 01/07/2020
ms.openlocfilehash: 4a3fb4c1818d86f7fe2913790fd9e573c630cbfd
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888024"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Azure Container Instances についてよく寄せられる質問

この記事では、Azure Container Instances についてよく寄せられる質問に回答します。

## <a name="deployment"></a>デプロイ

### <a name="how-large-can-my-container-image-be"></a>コンテナー イメージのサイズはどのくらいになる可能性がありますか。

Azure Container Instances のデプロイ可能なコンテナー イメージの最大サイズは 15 GB です。 デプロイ時の可用性によっては、より大きなイメージをデプロイできる場合がありますが、これは保証されません。

コンテナー イメージのサイズはデプロイにかかる時間に影響するため、一般的にはコンテナー イメージをできるだけ小さくすることをお勧めします。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>コンテナーのデプロイを高速にするにはどうすればよいですか。

デプロイ時間の主な決定要因の 1 つはイメージのサイズなので、サイズを小さくする方法を探します。 不要なレイヤーを削除したり、イメージ内のレイヤーのサイズを小さくします (より軽いベース OS イメージを選択します)。 たとえば、Linux コンテナーを実行している場合は、完全な Ubuntu Server ではなく Alpine をベース イメージとして使用することを検討します。 同様に、Windows コンテナーの場合は、可能であれば Nano Server のベース イメージを使用します。 

また、[List Cached Images](/rest/api/container-instances/listcachedimages) API から入手できる Azure Container Images の事前にキャッシュされたイメージの一覧も確認することもお勧めします。 事前にキャッシュされたいずれかのイメージのためにイメージ レイヤーを切り替えることもできます。 

コンテナーの起動時間の短縮に関する[詳細なガイダンス](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)を参照してください。

### <a name="what-windows-base-os-images-are-supported"></a>どの Windows ベース OS イメージがサポートされていますか。

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 ベース イメージ

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `10.0.14393.x`、`sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`、`10.0.14393.x`

> [!NOTE]
> Semi-Annual Channel 1709 または 1803 に基づく Windows イメージはサポートされていません。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 とクライアント ベース イメージ (プレビュー)

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809`、`10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019`、`1809`、`10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809`、`10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>どの .NET または .NET Core イメージ レイヤーをコンテナーに使用すればよいですか。 

お客様の要件を満たす最小のイメージを使用します。 Linux の場合は、.NET Core 2.1 のリリース以降のサポートされている *runtime-alpine* .NET Core イメージを使用できます。 Windows で、完全な .NET Framework を使用している場合は、Windows Server Core イメージ (*4.7.2-windowsservercore-ltsc2016* などのランタイム専用イメージ) を使用する必要があります。 ランタイム専用イメージの方が小さいのですが、.NET SDK を必要とするワークロードをサポートしていません。

## <a name="availability-and-quotas"></a>可用性とクォータ

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>コンテナーまたはコンテナー グループに割り当てる必要があるコアとメモリはどのくらいですか。

実際のところ、これはワークロードによって異なります。 小規模から始めてパフォーマンスをテストし、コンテナーの動作を確認します。 [CPU およびメモリ リソースの使用状況を監視します](container-instances-monitor.md)。次に、コンテナーにデプロイするプロセスの種類に基づいてコアまたはメモリを追加します。 

コンテナー グループごとに使用できる CPU コアとメモリの上限について、デプロイしているリージョンの[リソースの可用性](container-instances-region-availability.md#availability---general)も必ず確認します。 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI はどのような基盤インフラストラクチャ上で動作しますか。

Azure Container Instances は、サーバーレスのコンテナー オンデマンド サービスであることを目的としているため、コンテナーの開発に集中してください。インフラストラクチャについて心配する必要はありません。 パフォーマンスの比較に関心がある場合、または比較する場合、ACI は主に F シリーズと D シリーズのさまざまな SKU の Azure VM セット上で動作します。 サービスの開発と最適化に伴い、今後これは変わると予想されます。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>ACI に数千個のコアをデプロイしたいのですが、クォータを増やすことはできますか。
 
はい (場合によります)。 現在のクォータと、要求によって上げることができる上限については、[クォータと制限](container-instances-quotas.md)に関する記事を参照してください。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>4 コア、16 GB を超える RAM は展開できますか。

まだありません。 現在、これらはコンテナー グループの最大値です。 具体的な要件や要求については Azure サポートにお問い合わせください。 

### <a name="when-will-aci-be-in-a-specific-region"></a>特定のリージョンで、ACI はいつ使用できるようになりますか。

現在の利用可能なリージョンについては、[こちら](container-instances-region-availability.md#availability---general)で公開されています。 特定のリージョンの要件がある場合は、Azure サポートにお問い合わせください。

## <a name="features-and-scenarios"></a>機能とシナリオ

### <a name="how-do-i-scale-a-container-group"></a>コンテナー グループをスケールするにはどうすればよいですか。

現在、スケールはコンテナーまたはコンテナー グループには使用できません。 より多くのインスタンスを実行する必要がある場合は、API を使用して、サービスへのコンテナー グループ作成のより多くの要求を自動化および作成します。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>カスタム VNet で実行されているインスタンスにはどのような機能を使用できますか。

選択した [Azure 仮想ネットワークにコンテナー グループをデプロイ](container-instances-vnet.md)し、プライベート IP をコンテナー グループに委任して、Azure リソース全体で VNet 内のトラフィックをルーティングすることができます。 仮想ネットワークへのコンテナー グループのデプロイは、現在、Azure リージョンのサブセット内にある運用環境ワークロードで使用できます。

## <a name="pricing"></a>価格

### <a name="when-does-the-meter-start-running"></a>測定の実行はいつ開始されますか。

コンテナー グループの期間は、最初のコンテナーのイメージのプルを開始した時点 (新しいデプロイの場合)、またはコンテナー グループが再開された時点 (既にデプロイされている場合) から、コンテナー グループが停止するまでの時間です。 詳細については、「[Container Instances の価格](https://azure.microsoft.com/pricing/details/container-instances/)」を参照してください。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>コンテナーを停止するときに課金を停止することはできますか。

コンテナー グループ全体が停止すると、測定は停止します。 コンテナー グループのコンテナーが実行されている限り、コンテナーを再起動する場合に備えてリソースは保持されます。 

## <a name="next-steps"></a>次のステップ

* Azure Container Service の[詳細情報](container-instances-overview.md)。
* Azure Container Instances の[一般的な問題のトラブルシューティング](container-instances-troubleshooting.md)。