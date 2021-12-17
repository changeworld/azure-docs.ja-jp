---
title: プライベート アクセス (プレビュー) - Hyperscale (Citus) - Azure Database for PostgreSQL
description: この記事では、Azure Database for PostgreSQL - Hyperscale (Citus) のプライベート アクセスについて説明します。
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 8ddefc9ee135b897dc866826208f50f7f9ad21ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084390"
---
# <a name="private-access-preview-in-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL でのプライベート アクセス (プレビュー) - Hyperscale (Citus)

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

このページでは、プライベート アクセス オプションについて説明します。 パブリック アクセスについては、[こちら](concepts-hyperscale-firewall-rules.md)を参照してください。

> [!NOTE]
>
> プライベート アクセスは、[特定のリージョン](concepts-hyperscale-limits.md#regions)でのみプレビューで使用できます。
>
> サーバー グループが許可されたリージョン内にあるにもかかわらず、サーバー グループに対してプライベート アクセス オプションを選択できない場合は、Azure [サポート リクエスト](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)を開き、Azure サブスクリプション ID を含めて、アクセス権を取得してください。

## <a name="definitions"></a>定義

**Virtual network**。 Azure Virtual Network (VNet) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 仮想ネットワークでは、データベース サーバーや Azure Virtual Machine (VM) などのさまざまな種類の Azure リソースが、安全に相互に通信することができます。 仮想ネットワークでは、オンプレミス接続がサポートされ、複数の仮想ネットワーク内のホストがピアリングを介して相互に通信でき、スケール、セキュリティ オプション、分離といった面でもメリットがあります。 Hyperscale (Citus) サーバー グループの各プライベート エンドポイントには、関連付けられた仮想ネットワークが必要です。

**サブネット**。 サブネットは、仮想ネットワークを 1 つ以上のサブネットワークに分割します。
各サブネットワークでアドレス空間の一部が取得され、アドレス割り当ての効率が向上します。  ネットワーク セキュリティ グループを使用して、サブネット内のリソースを保護できます。 詳細については、「ネットワーク セキュリティ グループ」を参照してください。

Hyperscale (Citus) のプライベート エンドポイントにサブネットを選択する場合、現在および将来のニーズを考慮し、そのサブネットで使用できる十分なプライベート IP アドレスが確保できることを確認してください。

**プライベート エンドポイント** プライベート エンドポイントは、仮想ネットワークのプライベート IP アドレスを使用するネットワーク インターフェイスです。 このネットワーク インターフェイスにより、Azure Private Link を利用するサービスにプライベートに、かつ安全に接続できます。 プライベート エンドポイントは、サービスを仮想ネットワークに取り込みます。

Hyperscale (Citus) に対してプライベート アクセスを有効にすると、サーバー グループのコーディネーター ノードのプライベート エンドポイントが作成されます。 このエンドポイントにより、選択した仮想ネットワーク内のホストがコーディネーターにアクセスできるようになります。 必要に応じて、ワーカー ノードのプライベート エンドポイントを作成することもできます。

**プライベート DNS ゾーン** Azure プライベート DNS ゾーンは、リンクされた仮想ネットワーク内、およびピアリングされた仮想ネットワーク内のホスト名を解決します。 Hyperscale (Citus) ノードのドメイン レコードは、そのサーバー グループに対して選択されたプライベート DNS ゾーンで作成されます。  ノードの PostgreSQL 接続文字列には完全修飾ドメイン名 (FQDN) を使用してください。

## <a name="private-link"></a>プライベート リンク

Hyperscale (Citus) サーバー グループの[プライベート エンドポイント](../private-link/private-endpoint-overview.md)を使用すると、仮想ネットワーク (VNet) 上のホストは[プライベート リンク](../private-link/private-link-overview.md)を介してデータに安全にアクセスできるようになります。

サーバー グループのプライベート エンドポイントでは、仮想ネットワークのアドレス空間から IP アドレスを使用します。 仮想ネットワークと Hyperscale (Citus) ノード上のホスト間のトラフィックは、Microsoft バックボーン ネットワーク上のプライベート リンクを経由します。これによって、パブリック インターネットに公開されないようにします。

仮想ネットワーク内のアプリケーションは、プライベート エンドポイント経由で Hyperscale (Citus) ノードにシームレスに接続できます。その際、使用される接続文字列と承認メカニズムは、それを経由しない場合と同じものになります。

Hyperscale (Citus) サーバー グループの作成中にプライベート アクセスを選択できます。また、パブリック アクセスを任意の時点でプライベート アクセスに切り替えることができます。

### <a name="using-a-private-dns-zone"></a>プライベート DNS ゾーンを使用する

Hyperscale (Citus) によって以前に作成されたプライベート DNS ゾーンのいずれかを選択しない限り、プライベート エンドポイントごとに新しいプライベート DNS ゾーンが自動的にプロビジョニングされます。 詳細については、[プライベート DNS ゾーンの概要](../dns/private-dns-overview.md)に関するページを参照してください。

Hyperscale (Citus) サービスは、プライベート エンドポイントを持つ各ノードに対して、選択したプライベート DNS ゾーンに `c.privatelink.mygroup01.postgres.database.azure.com` などの DNS レコードを作成します。 プライベート エンドポイント経由で Azure VM から Hyperscale (Citus) ノードに接続すると、Azure DNS はノードの FQDN をプライベート IP アドレスに解決します。

プライベート DNS ゾーンの設定と、仮想ネットワーク ピアリングはそれぞれ独立しています。 同じリージョンまたは異なるリージョンの別の仮想ネットワークでプロビジョニングされたクライアントからサーバー グループ内のノードに接続する場合は、プライベート DNS ゾーンをその仮想ネットワークにリンクさせる必要があります。 詳細については、「[仮想ネットワークのリンク](../dns/private-dns-getstarted-portal.md#link-the-virtual-network)」を参照してください。

> [!NOTE]
>
> また、サービスは、すべてのノードに対して `c.mygroup01.postgres.database.azure.com` などのパブリック CNAME レコードを常に作成します。 ただし、パブリック インターネット上の選択されたコンピューターは、データベース管理者がサーバー グループへの[パブリック アクセス](concepts-hyperscale-firewall-rules.md)を有効にした場合にのみ、パブリック ホスト名に接続できます。

カスタム DNS サーバーを使用する場合は、DNS フォワーダーを使用して Hyperscale (Citus) ノードの FQDN を解決する必要があります。 フォワーダーの IP アドレスは、168.63.129.16 である必要があります。 カスタム DNS サーバーは、仮想ネットワーク内にあるか、仮想ネットワークの DNS サーバー設定を使用して到達可能である必要があります。 詳細については、「[独自の DNS サーバーを使用する名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)」を参照してください。

### <a name="recommendations"></a>Recommendations

Hyperscale (Citus) サーバー グループのプライベート アクセスを有効にする場合は、次の点を考慮してください。

* **サブネットのサイズ**: Hyperscale (Citus) サーバー グループのサブネットのサイズを選択するときは、コーディネーターの IP アドレスやそのサーバー グループ内のすべてのノードなどの現在のニーズ、および将来のニーズ (サーバー グループの成長など) を考慮してください。 現在および将来のニーズに対応するために十分なプライベート IP アドレスがあることを確認します。 Azure では、各サブネットで 5 つの IP アドレスが予約されています。
  詳細については、こちらの [FAQ](../virtual-network/virtual-networks-faq.md#configuration) を参照してください。
* **プライベート DNS ゾーン**: プライベート IP アドレスを持つ DNS レコードは、Hyperscale (Citus) サービスによって管理されます。 Hyperscale (Citus) サーバー グループに使用されているプライベート DNS ゾーンを削除しないようにしてください。

## <a name="limits-and-limitations"></a>制限と制限事項

詳細については、Hyperscale (Citus) の[制限と制限事項](concepts-hyperscale-limits.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [プライベート アクセス (プレビュー) を有効にし、管理する](howto-hyperscale-private-access.md)方法を学習する
* [チュートリアル](tutorial-hyperscale-private-access.md)に従って、プライベート アクセス (プレビュー) の動作を確認する。
* [プライベート エンドポイント](../private-link/private-endpoint-overview.md)について学習する
* [仮想ネットワーク](../virtual-network/concepts-and-best-practices.md)について学習する
* [プライベート DNS ゾーン](../dns/private-dns-overview.md)について学習する