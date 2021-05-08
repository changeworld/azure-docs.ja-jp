---
title: Azure Cloud Services (クラシック) を Azure Cloud Services (延長サポート) に移行する
description: Cloud Services (クラシック) から Cloud Services (延長サポート) への移行の概要
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 96315899f80d0bd02ac3d2108b7cd76876025218
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2021
ms.locfileid: "106286736"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (クラシック) を Azure Cloud Services (延長サポート) に移行する

この記事では、プラットフォームでサポートされる移行ツールの概要と、それを使用して [Azure Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) を [Azure Cloud Services (延長サポート)](overview.md) に移行する方法について説明します。

移行ツールで利用される API とそのエクスペリエンスは、[仮想マシン (クラシック) の移行](https://docs.microsoft.com/azure/virtual-machines/migration-classic-resource-manager-overview)と同じです。 

> [!IMPORTANT]
> 移行ツールを使用した Cloud Services (クラシック) から Cloud Services (延長サポート) への移行は現在、パブリック プレビュー段階にあります。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

移行についてサポートが必要な場合は、次のリソースを参照してください。 

- [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html):移行に対する Microsoft とコミュニティによるサポート。
- [Azure の移行のサポート](https://ms.portal.azure.com/#create/Microsoft.Support/Parameters/%7B%22pesId%22:%22e79dcabe-5f77-3326-2112-74487e1e5f78%22,%22supportTopicId%22:%22fca528d2-48bd-7c9f-5806-ce5d5b1d226f%22%7D):移行中の技術的支援のための専用サポート チーム。 テクニカル サポートを利用していないお客様は、この移行に特化して提供される[無料サポート機能](https://aka.ms/cs-migration-errors)を利用できます。
- 会社または組織がマイクロソフトと提携している場合、またはクラウド ソリューション アーキテクトやテクニカル アカウント マネージャーなどのマイクロソフトの担当者と協力している場合、移行に関する追加のリソースについてはマイクロソフトに問い合わせてください。
- Cloud Services (延長サポート) 製品チームにフィードバックを提供したり、問題を提起したりするには、[こちらのアンケート](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR--AgudUMwJKgRGMO84rHQtUQzZYNklWUk4xOTFXVFBPOFdGOE85RUIwVC4u)を使用してください。 

## <a name="migration-benefits"></a>移行の利点
プラットフォームによってサポートされた移行には、次のような主な利点があります。
- 移行はプラットフォームによって完全に調整され、デプロイ全体と関連リソースが Azure Resource Manager に移動されます。
- ダウンタイムなしでの移行。
- 最小限の手動タスクによる、他の移行パスと比べて簡単かつ迅速な移行。 
- 移行の過程で Cloud Services の IP アドレスと DNS ラベルが保持されます。 

その他の利点および移行が必要な理由については、[Cloud Services (延長サポート)](overview.md) と [Azure Resource Manager](../azure-resource-manager/management/overview.md) に関する記事を参照してください。 

## <a name="setup-access-for-migration"></a>移行のためのアクセス権を設定する

この移行を実行するには、自分をサブスクリプションの共同管理者として追加し、プロバイダーに登録する必要があります。 

1. Azure portal にサインインします。
3. [ハブ] メニューで、 [サブスクリプション] を選択します。 表示されない場合は、 [すべてのサービス] を選択します。
3. 適切なサブスクリプションのエントリを探し、 [自分のロール] フィールドを確認します。 共同管理者の場合、この値が [アカウント管理者] でなければなりません。共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの共同管理者に連絡して、追加を依頼します。

4. [ポータル](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して、サブスクリプションを Microsoft.ClassicInfrastructureMigrate 名前空間に登録します

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. [ポータル](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して、サブスクリプションを Cloud Services 移行プレビュー機能に登録します

    ```powershell
    Register-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

6. 登録の状態を確認します。 登録は完了するまでに数分かかることがあります。 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Cloud Services (クラシック) の移行と Virtual Machines (クラシック) の違い
Azure Service Manager により、2 つの異なるコンピューティング製品がサポートされています。[Azure Virtual Machines (クラシック)](https://docs.microsoft.com/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) と [Azure Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) (Web と worker ロール) です。 2 つの製品は、クラウド サービス内でデプロイの種類が異なります。 Azure Cloud Services (クラシック) には、Web と worker ロールでのデプロイを含むクラウド サービスが使用されます。 Azure Virtual Machines (クラシック) には、IaaS VM でのデプロイを含むクラウド サービスが使用されます。

デプロイの種類が違うため、サポートされているシナリオの一覧は Cloud Services (クラシック) と Virtual Machines (クラシック) で異なります。

## <a name="migration-steps"></a>移行の手順
 
お客様は、Virtual Machines (クラシック) の移行に使用されるのと同じ 4 つの操作を使用して、Cloud Services (クラシック) のデプロイを移行できます。 

1. **移行を検証する** - 一般的なサポートされていないシナリオによって、移行が妨げられないことを検証します。
2. **移行を準備する** - Azure Resource Manager でリソースのメタデータを複製します。 Azure サーバー マネージャーと Azure Resource Manager の間でリソース メタデータが同期されるように、すべてのリソースは作成、更新、削除操作に関してロックされています。 すべての読み取り操作は、Cloud Services (クラシック) API と Cloud Services (延長サポート) API の両方を使用して行われます。
3. **移行を中止する** - Azure Resource Manager からリソース メタデータを削除します。 すべてのリソースの作成、更新、削除操作のロックを解除します。
4. **移行をコミットする** - Azure Service Manager からリソース メタデータを削除します。 リソースの作成、更新、削除操作のロックを解除します。 コミットが試みられた後では、中止は許可されません。

>[!NOTE]
> 準備、中止、コミットはべき等であるため、失敗した場合は、再試行することで問題が解決されるはずです。

:::image type="content" source="media/in-place-migration-1.png" alt-text="移行に関連する手順の図を示す画像。":::

詳細については、[クラシックから Azure Resource Manager への、プラットフォームでサポートされた IaaS リソースの移行の概要](../virtual-machines/migration-classic-resource-manager-overview.md)に関する記事を参照してください

## <a name="supported-resources-and-features-available-for-migration-associated-with-cloud-services-classic"></a>Cloud Services (クラシック) に関連する移行に使用できるサポートされたリソースと機能
-   ストレージ アカウント
-   仮想ネットワーク
-   ネットワーク セキュリティ グループ
-   予約済みパブリック IP アドレス
-   エンドポイント アクセス制御リスト
-   ユーザー定義のルート
-   内部ロード バランサー
-   キー コンテナーへの証明書の移行
-   プラグインと拡張機能 (XML および Json ベース)
-   開始時または停止時のタスク
-   高速ネットワークでのデプロイ
-   1 つまたは複数のロールを使用したデプロイ
-   Basic ロード バランサー
-   入力、インスタンス入力、内部エンドポイント
-   動的パブリック IP アドレス
-   DNS 名 
-   ネットワーク トラフィック規則
-   Hypernet 仮想ネットワーク

## <a name="supported-configurations--migration-scenarios"></a>サポートされる構成と移行シナリオ
これらは、リソース、機能、Cloud Services の組み合わせが含まれる主なシナリオです。 このリストは全てを網羅しているわけではありません。

| サービス | 構成 | コメント | 
|---|---|---|
| [Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet) | Azure Active Directory Domain Services が含まれる仮想ネットワーク。 | クラウド サービスのデプロイと Azure AD Domain Services の両方が含まれる仮想ネットワークがサポートされています。 お客様は、最初に Azure AD Domain Services を個別に移行してから、クラウド サービスのデプロイだけが含まれる残りの仮想ネットワークを移行する必要があります |
| クラウド サービス | 1 つのスロットにだけデプロイされたクラウド サービス。 | 運用またはステージング スロットのデプロイを含む Cloud Services を移行できます |
| クラウド サービス | 公開された仮想ネットワーク内にないデプロイ (既定の仮想ネットワークのデプロイ) | クラウド サービスは、公開された仮想ネットワークまたは非公開の仮想ネットワークに配置するか、仮想ネットワークに配置しないことができます。  非公開の仮想ネットワーク内と公開された仮想ネットワーク内の Cloud Services は、移行がサポートされています。 お客様は Validate API を使用して、デプロイが既定の仮想ネットワーク内にあるかどうか、したがって移行できるかどうかを判断できます。 |
|クラウド サービス | XML 拡張機能 (BGInfo、Visual Studio デバッガー、Web 配置、リモート デバッグ)。 | すべての xml 拡張機能は、移行がサポートされています 
| Virtual Network | 複数のクラウド サービスを含む仮想ネットワーク。 | 複数のクラウド サービスが含まれる仮想ネットワークは、移行がサポートされています。 仮想ネットワークとその中のすべてのクラウド サービスが、Azure Resource Manager にまとめて移行されます。 |
| Virtual Network | ポータルで作成された仮想ネットワークの移行 (.cscfg ファイルで "Group Resource-group-name VNet-Name" を使用する必要があります)  | 移行の一環として、cscfg 内の仮想ネットワーク名は、仮想ネットワークの Azure Resource Manager ID を使用するように変更されます。 (サブスクリプション/サブスクリプション ID/リソース グループ/リソース グループ名/リソース/VNet 名) <br><br>移行後にデプロイを管理するには、.cscfg ファイルのローカル コピーを更新して、仮想ネットワーク名ではなく Azure Resource Manager ID の使用を開始します。 <br><br>古い名前付けスキームを使用する .cscfg ファイルは、検証に合格しません。 
| Virtual Network | 異なるサブネット内にロールのあるデプロイの移行。 | 異なるサブネット内に異なるロールがあるクラウド サービスは、移行がサポートされています。 |
    

## <a name="resources-and-features-not-available-for-migration"></a>移行に使用できないリソースと機能
これらは、リソース、機能、Cloud Services の組み合わせが含まれる主なシナリオです。 このリストは全てを網羅しているわけではありません。 

| リソース | 次の手順、回避策 | 
|---|---|
| 自動スケーリング ルール | 移行は行われますが、ルールは削除されます。 Cloud Services への移行後に[ルールを再作成](https://docs.microsoft.com/azure/cloud-services-extended-support/configure-scaling)します (延長サポート)。 | 
| 警告 | 移行は行われますが、警告は削除されます。 Cloud Services への移行後に[ルールを再作成](https://docs.microsoft.com/azure/cloud-services-extended-support/enable-alerts)します (延長サポート)。 | 
| VPN Gateway | 移行を開始する前に VPN Gateway を削除し、移行の完了後に VPN Gateway を作成し直します。 | 
| ExpressRoute ゲートウェイ (仮想ネットワークと同じサブスクリプション内にある場合のみ) | 移行を開始する前に ExpressRoute ゲートウェイを削除し、移行の完了後にゲートウェイを作成し直します。 | 
| Quota  | クォータは移行されません。 検証を成功させるには、移行の前に Azure Resource Manager で[新しいクォータを要求](https://docs.microsoft.com/azure/azure-resource-manager/templates/error-resource-quota#solution)します。 | 
| アフィニティ グループ | サポートされていません。 移行前にアフィニティ グループを削除します。  | 
| [仮想ネットワーク ピアリング](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)を使用する仮想ネットワーク| ピアリングされた仮想ネットワークを別の仮想ネットワークに移行する前に、ピアリングを削除し、仮想ネットワークを Resource Manager に移行して、ピアリングを作成し直します。 これにより、アーキテクチャによってはダウンタイムが発生する可能性があります。 | 
| App Service 環境を含む仮想ネットワーク | サポートされていません | 
| HDInsight サービスを含む仮想ネットワーク | サポートされていません。 
| Azure API Management デプロイを含む仮想ネットワーク | サポートされていません。 <br><br> 仮想ネットワークを移行するには、API Management のデプロイの仮想ネットワークを変更します。 これは、ダウンタイムのない操作です。 | 
| クラシック Express Route 回線 | サポートされていません。 <br><br>これらの回線は、PaaS の移行を開始する前に、Azure Resource Manager に移行する必要があります。 詳細については、「[クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行](../expressroute/expressroute-howto-move-arm.md)」をご覧ください。 |  
| ロールベースのアクセス制御 | 移行後に、リソースの URI は `Microsoft.ClassicCompute` から `Microsoft.Compute` に変わります。RBAC ポリシーを移行後に更新する必要があります。 | 
| Application Gateway | サポートされていません。 <br><br> 移行を始める前にアプリケーション ゲートウェイを削除し、Azure Resource Manager への移行の完了後にアプリケーション ゲートウェイを作成し直します | 

## <a name="unsupported-configurations--migration-scenarios"></a>サポートされない構成と移行シナリオ

| 構成、シナリオ  | 次の手順、回避策 | 
|---|---|
| 仮想ネットワーク内にない一部の古いデプロイの移行 | 仮想ネットワーク内にない一部のクラウド サービスのデプロイは、移行がサポートされていません。 <br><br> 1. Validate API を使用して、デプロイが移行の対象であるかどうかを確認します。 <br> 2. 対象である場合、デプロイは仮想ネットワークの下の Azure Resource Manager に "DefaultRdfeVnet" というプレフィックスを付けて移動されます | 
| 動的 IP アドレスを使用している運用とステージング両方のスロットのデプロイを含むデプロイの移行 | 2 スロットのクラウド サービスを移行するには、ステージング スロットを削除する必要があります。 ステージング スロットを削除した後、Azure Resource Manager の独立したクラウド サービス (延長サポート) として、運用スロットを移行します。 その後、ステージング環境を新しいクラウド サービス (延長サポート) として再デプロイし、最初のものとスワップできるようにします。 | 
| 予約済み IP アドレスを使用している運用とステージング両方のスロットのデプロイを含むデプロイの移行 | サポートされていません。 | 
| 異なる仮想ネットワーク内にある運用とステージングのデプロイの移行|2 スロットのクラウド サービスを移行するには、ステージング スロットを削除する必要があります。 ステージング スロットを削除した後、Azure Resource Manager の独立したクラウド サービス (延長サポート) として、運用スロットを移行します。 その後、新しい Cloud Services (延長サポート) のデプロイを、スワップ可能なプロパティが有効になっている移行されたデプロイにリンクすることができます。 古いステージング スロットのデプロイのデプロイ ファイルを再利用して、この新しいスワップ可能なデプロイを作成できます。 | 
| 空のクラウド サービス (デプロイのないクラウド サービス) の移行 | サポートされていません。 | 
| リモート デスクトップ プラグインとリモート デスクトップ拡張機能が含まれるデプロイの移行 | オプション 1: 移行前にリモート デスクトップ プラグインを削除します。 そのためには、デプロイ ファイルの変更が必要です。 その後、移行を行います。 <br><br> オプション 2: リモート デスクトップ拡張機能を削除して、デプロイを移行します。 移行後に、プラグインを削除して拡張機能をインストールします。 そのためには、デプロイ ファイルの変更が必要です。 <br><br> 移行の前に、プラグインと拡張機能を削除します。 Cloud Services (延長サポート) での[プラグインの使用は推奨されません](https://docs.microsoft.com/azure/cloud-services-extended-support/deploy-prerequisite#required-service-definition-file-csdef-updates)。| 
| PaaS と IaaS 両方のデプロイが含まれる仮想ネットワーク |サポートされていません <br><br> PaaS または IaaS どちらかデプロイを、別の仮想ネットワークに移動します。 これはダウンタイムの原因になります。 | 
従来のロール サイズ (Small や ExtraLarge など) を使用しているクラウド サービスのデプロイ。 | 移行は完了しますが、ロールのサイズは最新のロール サイズを使用するように更新されます。 コストまたは SKU のプロパティは変更されず、この変更のために仮想マシンは再起動されません。 これらの新しい最新のロール サイズを参照するように、すべてのデプロイ成果物を更新します。 詳細については、[使用可能な VM サイズ](available-sizes.md)に関する記事を参照してください|
| 異なる仮想ネットワークへのクラウド サービスの移行 | サポートされていません <br><br> 1. 移行の前に、別のクラシック仮想ネットワークにデプロイを移動します。 これはダウンタイムの原因になります。 <br> 2. 新しい仮想ネットワークを Azure Resource Manager に移行します。 <br><br> または <br><br> 1. 仮想ネットワークを Azure Resource Manager に移行します <br>2. クラウド サービスを新しい仮想ネットワークに移動します。 これはダウンタイムの原因になります。 | 
| 仮想ネットワーク内にあるが、明示的なサブネットが割り当てられていないクラウド サービス | サポートされていません。 軽減策にはサブネットへのロールの移動が含まれ、ロールの再起動 (ダウンタイム) が必要です | 


## <a name="post-migration-changes"></a>移行後の変更
クラウド サービス (クラシック) のデプロイは、クラウド サービス (延長サポート) のデプロイに変換されます。 詳細については、[Cloud Services (延長サポート) のドキュメント](deploy-prerequisite.md)を参照してください。  

### <a name="changes-to-deployment-files"></a>デプロイ ファイルの変更 

デプロイ ファイルを Azure Resource Manager と Cloud Services (延長サポート) の要件に準拠させるため、お客様の .csdef および .cscfg ファイルが少し変更されます。 移行後に、新しいデプロイ ファイルが取得されるか、既存のファイルが更新されます。 これは、更新または削除操作で必要になります。  

- Virtual Network では、.cscfg ファイルの NetworkConfiguration セクションのリソース名だけでなく、Azure Resource Manager の完全なリソース ID が使用されます。 たとえば、「 `/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Network/virtualNetworks/vnet-name` 」のように入力します。 クラウド サービスと同じリソース グループに属している仮想ネットワークの場合は、仮想ネットワーク名のみを使用するように .cscfg ファイルを更新して戻すことができます。  

- Small、Large、ExtraLarge などの従来のサイズは、新しいサイズの名前 (Standard_A*) に置き換えられます。 サイズの名前を、.csdef ファイルで使用されている新しい名前に変更する必要があります。 詳細については、[Cloud Services (延長サポート) のデプロイの前提条件](deploy-prerequisite.md#required-service-definition-file-csdef-updates)に関する記事を参照してください

- デプロイ ファイルの最新のコピーを取得するには、Get API を使用します。 
    - [ポータル](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)、[PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-powershell#export-resource-groups-to-templates)、[CLI](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resource-groups-cli#export-resource-groups-to-templates)、[Rest API](https://docs.microsoft.com/rest/api/resources/resourcegroups/exporttemplate) を使用して、テンプレートを取得します 
    - [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) または [Rest API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package) を使用して、.csdef ファイルを取得します。 
    - [PowerShell](https://docs.microsoft.com/powershell/module/az.cloudservice/?view=azps-5.4.0#cloudservice&preserve-view=true) または [Rest API](https://docs.microsoft.com/rest/api/compute/cloudservices/rest-get-package) を使用して、.cscfg ファイルを取得します。 
    
 

### <a name="changes-to-customers-automation-cicd-pipeline-custom-scripts-custom-dashboards-custom-tooling-etc"></a>お客様のオートメーション、CI/CD パイプライン、カスタム スクリプト、カスタム ダッシュボード、カスタム ツールなどに対する変更  

お客様は、新しい API とコマンドを使用してデプロイを管理するには、ツールとオートメーションを更新する必要があります。 お客様は、この変更の一環として、Azure Resource Manager や Cloud Services (延長サポート) の新機能を簡単に導入できます。 

- 移行後のリソースとリソース グループの名前の変更
    - 移行の一部として、クラウド サービスやパブリック IP アドレスなど、いくつかのリソースの名前が変更されます。 クラウド サービスを更新する前に、これらの変更をデプロイ ファイルに反映することが必要な場合があります。 [リソースの名前の変更の詳細については、こちらを参照してください](in-place-migration-technical-details.md#translation-of-resources-and-naming-convention-post-migration)。  

- クラウド サービスの管理とスケーリングに必要なルールとポリシーを作成し直します 
    - [自動スケーリング ルール](configure-scaling.md)は移行されません。 移行後に、自動スケーリング ルールを作成し直します。  
    - [アラート](enable-alerts.md)は移行されません。 移行後に、アラートを作成し直します。
    - キー コンテナーは、アクセス ポリシーなしで作成されます。 キー コンテナーで、証明書を表示または管理するための[適切なポリシーを作成](../key-vault/general/assign-access-policy-portal.md)します。 証明書は、[設定] の [シークレット] タブに表示されます。

## <a name="next-steps"></a>次の手順
- [クラシックから Azure Resource Manager への IaaS リソースのプラットフォームでサポートされる移行の概要](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure portal](in-place-migration-portal.md) を使用して Cloud Services (延長サポート) に移行する
- [PowerShell](in-place-migration-powershell.md) を使用して Cloud Services (延長サポート) に移行する
