---
title: Azure Cloud Services (延長サポート) への移行に関する技術的詳細と要件
description: Azure Cloud Services (クラシック) から Azure Cloud Services (拡張サポート) への移行に関する技術的詳細と要件について説明します。
author: tanmaygore
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
ms.reviwer: mimckitt
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 4ff7d9aa2075b675a7ecd979c08d5621bbdd831a
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286735"
---
# <a name="technical-details-of-migrating-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (延長サポート) への移行に関する技術的詳細   

この記事では、Cloud Services (クラシック) と関係のある移行ツールに関する技術的詳細について説明します。 

> [!IMPORTANT]
> 移行ツールを使用した Cloud Services (クラシック) から Cloud Services (延長サポート) への移行は現在、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="details-about-feature--scenarios-supported-for-migration"></a>移行がサポートされている機能およびシナリオの詳細 

### <a name="extensions-and-plugin-migration"></a>拡張機能とプラグインの移行 
- サポートされているすべての有効な拡張機能が移行されます。 
- 無効になっている拡張機能は移行されません。 
- プラグインはレガシの概念であり、移行前に削除する必要があります。 移行はサポートされていますが、移行後に拡張機能を有効にする必要がある場合は、拡張機能をインストールする前にまずプラグインを削除する必要があります。 リモート デスクトップのプラグインと拡張機能は、これによる影響を最も受けます。   
 
### <a name="certificate-migration"></a>証明書の移行
- Cloud Services (拡張サポート) では、証明書は Key Vault に格納されます。 移行の一環として、クラウド サービスの名前を持つ顧客の Key Vault を作成し、Azure Service Manager から Key Vault にすべての証明書を転送します。 
- この Key Vault への参照は、テンプレートで指定されるか、PowerShell または Azure CLI を通じて渡されます。 

### <a name="service-configuration-and-service-definition-files"></a>サービス構成ファイルとサービス定義ファイル
- .cscfg および .csdef ファイルを Cloud Services (拡張サポート) 用の軽微な変更について更新する必要があります。 
- 仮想ネットワークや VM SKU などのリソースの名前が異なります。 「[移行後のリソースの変換と名前付け規則](#translation-of-resources-and-naming-convention-post-migration)」を参照してください
- お客様は、[PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) と [Rest API](https://docs.microsoft.com/rest/api/compute/cloudservices/get)を使って新しいデプロイを取得することができます。 

### <a name="cloud-service-and-deployments"></a>クラウド サービスとデプロイ
- Cloud Service (延長サポート) のデプロイはそれぞれ独立したクラウド サービスです。 デプロイは、スロットを使用してクラウド サービスにグループ化されなくなりました。
- Cloud Service (クラシック) に 2 つのスロットがある場合は、1 つのスロット (ステージング) を削除し、移行ツールを使用してもう一方の (実稼働) スロットを Azure Resource Manager に移動する必要があります。 
- このクラウド サービスのデプロイのパブリック IP アドレスは Azure Resource Manager への移行後も変わらず、Basic SKU IP (動的または静的) リソースとして公開されます。 
- 移行されたクラウド サービスの DNS 名とドメイン (cloudapp.azure.net) は変わりません。 

### <a name="virtual-network-migration"></a>仮想ネットワークの移行
- Cloud Services のデプロイが仮想ネットワーク内にある場合、移行中に、すべての Cloud Services と、関連付けられた仮想ネットワーク リソースが一緒に移行されます。 
- 移行後、仮想ネットワークはクラウド サービスとは別のリソース グループに配置されます。 
- 複数のクラウド サービスを使用する仮想ネットワークでは、各クラウド サービスが順に移行されます。 

### <a name="migration-of-deployments-not-in-a-virtual-network"></a>仮想ネットワークに配置されていないデプロイの移行
- 2017 年に、Azure は、プラットフォームに作成された "既定の" 仮想ネットワークに、(お客様が指定した仮想ネットワークのない) 新しいデプロイを自動的に作成することを開始しました。 これらの既定の仮想ネットワークは、お客様には表示されません。   
- 移行の一環として、この既定の仮想ネットワークは Azure Resource Manager で一度お客様に公開されます。 Azure Resource Manager でデプロイを管理または更新するには、.cscfg ファイルの NetworkConfiguration セクションにこの仮想ネットワーク情報を追加する必要があります。    
- Azure Resource Manager に移行すると、既定の仮想ネットワークはクラウド サービスと同じリソース グループに配置されます。
- これより前に作成された Cloud Services はどの仮想ネットワークにも配置されず、ツールを使用して移行することはできません。 これらの Cloud Services を Azure Resource Manager に直接再デプロイすることを検討してください。 
- デプロイが移行できるかどうかを確認するには、デプロイで validate API を実行します。 Validate API の結果には、そのデプロイを移行できるかどうかを明確に示すエラー メッセージが含まれます。     

### <a name="load-balancer"></a>Load Balancer   
- パブリック エンドポイントを使用するクラウド サービスの場合、クラウド サービスに関連付けられたプラットフォーム作成のロード バランサーが、Azure Resource Manager の顧客のサブスクリプション内で公開されます。 ロード バランサーは読み取り専用のリソースであり、更新プログラムはサービス構成 (.cscfg) とサービス定義 (.csdef) ファイル経由に制限されています。 

### <a name="key-vault"></a>Key Vault
- 移行の一環として、Azure によって新しい Key Vault が自動的に作成され、すべての証明書がそれに移行されます。 このツールでは、既存の Key Vault を使用することはできません。 
- Cloud Services (拡張サポート) には、同じリージョンとサブスクリプションに配置された Key Vault が必要です。 この Key Vault は、移行の一環として自動的に作成されます。 


## <a name="translation-of-resources-and-naming-convention-post-migration"></a>移行後のリソースの変換と名前付け規則
移行の一環として、リソースの名前が変更され、いくつかの Cloud Services の機能は、Azure Resource Manager のリソースとして公開されます。 この表は、Cloud Services の移行に固有の変更点をまとめたものです。

| Cloud Services (クラシック) <br><br> リソース名 | Cloud Services (クラシック) <br><br> 構文| Cloud Services (延長サポート) <br><br> リソース名| Cloud Services (延長サポート) <br><br> 構文 | 
|---|---|---|---|
| クラウド サービス | `cloudservicename` | 関連付けられていません| 関連付けられていません |
| デプロイ (ポータルで作成) <br><br> デプロイ (ポータル以外で作成)  | `deploymentname` | Cloud Services (延長サポート) | `deploymentname` |  
| Virtual Network | `vnetname` <br><br> `Group resourcegroupname vnetname` <br><br> 関連付けられていません |  仮想ネットワーク (ポータル以外で作成) <br><br> 仮想ネットワーク (ポータルで作成) <br><br> 仮想ネットワーク (既定) | `vnetname` <br><br> `group-resourcegroupname-vnetname` <br><br> `DefaultRdfevirtualnetwork_vnetid`|
| 関連付けられていません | 関連付けられていません | Key Vault | `cloudservicename` | 
| 関連付けられていません | 関連付けられていません | クラウド サービス のデプロイのリソース グループ | `cloudservicename-migrated` | 
| 関連付けられていません | 関連付けられていません | 仮想ネットワークのリソース グループ | `vnetname-migrated` <br><br> `group-resourcegroupname-vnetname-migrated`|
| 関連付けられていません | 関連付けられていません | パブリック IP (動的) | `cloudservicenameContractContract` | 
| 予約済み IP 名 | `reservedipname` | 予約済み IP (ポータル以外で作成) <br><br> 予約済み IP (ポータルで作成) | `reservedipname` <br><br> `group-resourcegroupname-reservedipname` | 
| 関連付けられていません| 関連付けられていません | Load Balancer | `deploymentname-lb`|



## <a name="migration-issues-and-how-to-handle-them"></a>移行に関する問題とその対処方法。 

### <a name="migration-stuck-in-an-operation-for-a-long-time"></a>ある操作で移行が長時間固まっている。 
- デプロイの数に応じて、コミット、準備、中止には、時間がかかる可能性があります。 操作は 24 時間後にタイムアウトになります。   
- コミット、準備、および中止の各操作はべき等です。 ほとんどの問題は、再試行することによって修正できます。 一時的なエラーが発生する場合があります。これは数分で解決する可能性があるため、少し時間をおいて再試行することをお勧めします。 Azure portal を使用して移行し、操作が "進行中" 状態のままになっている場合は、PowerShell を使用して操作を再試行してください。 
- バックエンドからデプロイを移行またはロールバックするには、サポートにお問い合わせください。 

### <a name="migration-failed-in-an-operation"></a>移行の操作が失敗した。 
- 検証に失敗した場合は、デプロイまたは仮想ネットワークにサポートされていないシナリオ、機能、リソースが含まれていることが原因です。 サポートされていないシナリオの一覧を使用して、ドキュメントで回避策を見つけてださい。  
- 準備操作では、まず負荷の高い検証 (検証では説明されていません) を含む検証が行われます。 サポートされていないシナリオが原因で準備が失敗する可能性があります。 公開されているドキュメントでシナリオと回避策を探してください。 元の状態に戻り、更新および削除操作のためにデプロイのロックを解除するには、中止を呼び出す必要があります。
- 中止に失敗した場合は、操作を再試行してください。 再試行に失敗する場合は、サポートにお問い合わせください。
- コミットに失敗した場合は、操作を再試行してください。 再試行に失敗する場合は、サポートにお問い合わせください。 コミットに失敗した場合でも、デプロイにデータ プレーンの問題が発生することはありません。 デプロイでは、問題なくお客様のトラフィックを処理できます。 

### <a name="portal-refreshed-after-prepare-experience-restarted-and-commit-or-abort-not-visible-anymore"></a>準備後にポータルが更新されました。 エクスペリエンスが再開され、コミットまたは中止が表示されなくなりました。 
- ポータルでは、移行情報がローカルに保存されるため、更新後は、クラウド サービスが準備段階にある場合でも検証フェーズから開始されます。  
- ポータルを使用して検証と準備の手順を再度実行すると、[中止] ボタンと [コミット] ボタンを表示できます。 これによりエラーが発生することはありません。
- PowerShell または Rest API を使用して中止またはコミットすることができます。 

### <a name="how-much-time-can-the-operations-takebr"></a>操作にはどれくらいの時間がかかりますか。<br>
検証は短時間で終了するように設計されています。 準備が最も長く、移行されるロール インスタンスの合計数によっては時間がかかります。 中止とコミットにも時間がかかることがありますが、準備よりは短い時間です。 すべての操作は 24 時間後にタイムアウトになります。 
