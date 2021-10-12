---
title: Azure Cloud Services (クラシック) を Azure Cloud Services (延長サポート) に移行する
description: Cloud Services (クラシック) から Cloud Services (延長サポート) への移行の概要
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: hirenshah1
ms.author: hirshah
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 1542a8dd6c36c2a623f11179c0871a9fef2e3619
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665513"
---
# <a name="migrate-azure-cloud-services-classic-to-azure-cloud-services-extended-support"></a>Azure Cloud Services (クラシック) を Azure Cloud Services (延長サポート) に移行する

このドキュメントでは、Cloud Services (クラシック) を Cloud Services (延長サポート) に移行するための概要について説明します。     

[Cloud Services (延長サポート)](overview.md) には、Azure Service Manager を使用してデプロイされた Azure Cloud Services との機能パリティと共に、リージョンの回復性を提供するという主な利点があります。 また、ロールベースのアクセス制御 (RBAC)、タグ、ポリシーなどのいくつかの Azure Resource Manager 機能も提供され、デプロイ テンプレート、プライベート リンクがサポートされています。 デプロイ モデル (延長サポートとクラシック) はどちらも、[同様の価格構造](https://azure.microsoft.com/pricing/details/cloud-services/)で利用できます。

Cloud Services (延長サポート) には、Azure Service Manager から Azure Resource Manager に移行するための 2 つのパスが用意されています。再デプロイとインプレース移行です。 

次の表では、これら 2 つのオプションを比較しています。  


| Redeploy | インプレース移行 | 
|---|---|
| 顧客は、新しいクラウド サービスを Azure Resource Manager に直接デプロイした後、検証を通じて Azure Service Manager で古いクラウド サービスを削除できます。 | インプレース移行ツールを使用すると、既存のクラウド サービス (クラシック) のデプロイを Cloud Services (延長サポート) にシームレスにプラットフォームで調整して移行できます。 | 
| 再デプロイすると、顧客は次の操作を行えるようになります。 <br><br> - リソース名を定義する。 <br><br> - リソースを優先として整理または再利用する。 <br><br> - 最小限の変更でサービス構成ファイルと定義ファイルを再利用する。 | インプレース移行の場合、プラットフォームでは次の操作を行います。 <br><br> - リソース名を定義する。 <br><br> - 各デプロイと関連リソースを個々のリソース グループに整理する。 <br><br> - Azure Resource Manager の既存の構成および定義ファイルを変更する。 | 
| 顧客は、新しいデプロイへのトラフィックを調整する必要があります。 | 移行では IP アドレスが保持され、データ パスは変わりません。 | 
| 顧客は、Azure Resource Manager で古いクラウド サービスを削除する必要があります。 | プラットフォームは、移行後に Cloud Services (クラシック) リソースを削除します。 | 
| これはリフト アンド シフト移行であり、柔軟性は向上しますが、移行にはさらに時間がかかります。 | これは自動移行であり、移行は迅速に行われますが、柔軟性は低くなります。 | 

Cloud Services (クラシック) から Cloud Services (延長サポート) への移行計画を評価する場合は、[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)、[App Service](../app-service/overview.md)、[Azure Kubernetes Service](../aks/intro-kubernetes.md)、[Azure Service Fabric](../service-fabric/overview-managed-cluster.md) などの追加の Azure サービスを調査することもできます。 これらのサービスには引き続き追加機能が含まれますが、Cloud Services (延長サポート) では、主に Cloud Services (クラシック) との機能パリティが保持されます。

アプリケーションによっては、Cloud Services (延長サポート) では、Azure Resource Manager に移行するために必要な労力が他のオプションより大幅に少なくなる場合があります。 アプリケーションが進化中でない場合、Cloud Services (延長サポート) は迅速な移行パスを提供するため、考慮すべき実行可能なオプションになります。 逆に、アプリケーションが継続的に進化しており、より最新の機能セットを必要としている場合は、現在および将来の要件により適切に対処するために他の Azure サービスを調査してください。

## <a name="redeploy-overview"></a>再デプロイの概要

[Cloud Services (拡張サポート)](overview.md) を使用してサービスを再デプロイすることには、次の利点があります。 

- [Cloud Services (クラシック) と同様に、Web ロールと worker ロールがサポートされます。
- Web および worker ロールの設計、アーキテクチャ、コンポーネントに変更はありません。 
- データ プレーンはクラウド サービスと同じであるため、ランタイム コードへの変更は必要ありません。 
- Azure GuestOS のリリースとそれに関連する更新プログラムは Cloud Services (クラシック) と整合されます。 
- 更新ドメインに関する基になる更新プロセス、アップグレードの処理方法、ロールバック、更新中に許可されるサービスの変更は変更されません。

新しい Cloud Service (延長サポート) は、次のクライアント ツールを使用して Azure Resource Manager に直接デプロイできます。

- [クラウド サービスのデプロイ - ポータル](deploy-portal.md)
- [クラウド サービスのデプロイ - PowerShell](deploy-powershell.md)
- [クラウド サービスのデプロイ - テンプレート](deploy-template.md)
- [クラウド サービスのデプロイ - SDK](deploy-sdk.md)
- [クラウド サービスのデプロイ - Visual Studio](/visualstudio/azure/cloud-services-extended-support?context=%2fazure%2fcloud-services-extended-support%2fcontext%2fcontex)


## <a name="migration-tool-overview"></a>移行ツールの概要

プラットフォームによってサポートされた移行には、次のような主な利点があります。

- ほとんどのシナリオで、ダウンタイムがなくシームレスな、プラットフォームで調整された移行が可能です。 [サポートされるシナリオ](in-place-migration-technical-details.md)に関する詳細情報を参照してください。  
- 既存のクラウド サービスを、検証、準備、コミット (または中止) の 3 つの簡単な手順で移行します。 [移行ツールのしくみ](in-place-migration-overview.md#migration-steps)の詳細を確認してください。
- 準備が成功した後、移行されたデプロイをテストする機能を提供します。 中止によって移行がロールバックされる間に、移行をコミットして仕上げます。

移行ツールで利用される API とそのエクスペリエンスは、[仮想マシン (クラシック) の移行](../virtual-machines/migration-classic-resource-manager-overview.md)と同じです。 

## <a name="setup-access-for-migration"></a>移行のためのアクセス権を設定する

この移行を実行するには、自分をサブスクリプションの共同管理者として追加し、プロバイダーに登録する必要があります。 

1. Azure portal にサインインします。
3. [ハブ] メニューで、 [サブスクリプション] を選択します。 表示されない場合は、 [すべてのサービス] を選択します。
3. 適切なサブスクリプションのエントリを探し、 [自分のロール] フィールドを確認します。 共同管理者の場合、この値が [アカウント管理者] でなければなりません。共同管理者を追加できない場合は、サービス管理者またはサブスクリプションの共同管理者に連絡して、追加を依頼します。

4. [ポータル](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)、[PowerShell](../azure-resource-manager/management/resource-providers-and-types.md#azure-powershell)、または [CLI](../azure-resource-manager/management/resource-providers-and-types.md#azure-cli) を使用して、サブスクリプションを Microsoft.ClassicInfrastructureMigrate 名前空間に登録します

    ```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate 
    ```
 
5. 登録の状態を確認します。 登録は完了するまでに数分かかることがあります。 

    ```powershell
    Get-AzProviderFeature -FeatureName CloudServices -ProviderNamespace Microsoft.Compute 
    ```

## <a name="how-is-migration-for-cloud-services-classic-different-from-virtual-machines-classic"></a>Cloud Services (クラシック) の移行と Virtual Machines (クラシック) の違い
Azure Service Manager により、2 つの異なるコンピューティング製品がサポートされています。[Azure Virtual Machines (クラシック)](/previous-versions/azure/virtual-machines/windows/classic/tutorial-classic) と [Azure Cloud Services (クラシック)](../cloud-services/cloud-services-choose-me.md) (Web と worker ロール) です。 2 つの製品は、クラウド サービス内でデプロイの種類が異なります。 Azure Cloud Services (クラシック) には、Web と worker ロールでのデプロイを含むクラウド サービスが使用されます。 Azure Virtual Machines (クラシック) には、IaaS VM でのデプロイを含むクラウド サービスが使用されます。

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

## <a name="supported-configurations--migration-scenarios"></a>サポートされる構成と移行シナリオ
これらは、リソース、機能、Cloud Services の組み合わせが含まれる主なシナリオです。 このリストは全てを網羅しているわけではありません。

| サービス | 構成 | コメント | 
|---|---|---|
| [Azure AD Domain Services](../active-directory-domain-services/migrate-from-classic-vnet.md) | Azure Active Directory Domain Services が含まれる仮想ネットワーク。 | クラウド サービスのデプロイと Azure AD Domain Services の両方が含まれる仮想ネットワークがサポートされています。 お客様は、最初に Azure AD Domain Services を個別に移行してから、クラウド サービスのデプロイだけが含まれる残りの仮想ネットワークを移行する必要があります |
| クラウド サービス | 1 つのスロットにだけデプロイされたクラウド サービス。 | 運用またはステージング スロットのデプロイを含む Cloud Services を移行できます |
| クラウド サービス | 公開された仮想ネットワーク内にないデプロイ (既定の仮想ネットワークのデプロイ) | クラウド サービスは、公開された仮想ネットワークまたは非公開の仮想ネットワークに配置するか、仮想ネットワークに配置しないことができます。  非公開の仮想ネットワーク内と公開された仮想ネットワーク内の Cloud Services は、移行がサポートされています。 お客様は Validate API を使用して、デプロイが既定の仮想ネットワーク内にあるかどうか、したがって移行できるかどうかを判断できます。 |
|クラウド サービス | XML 拡張機能 (BGInfo、Visual Studio デバッガー、Web 配置、リモート デバッグ)。 | すべての xml 拡張機能は、移行がサポートされています 
| Virtual Network | 複数のクラウド サービスを含む仮想ネットワーク。 | 複数のクラウド サービスが含まれる仮想ネットワークは、移行がサポートされています。 仮想ネットワークとその中のすべてのクラウド サービスが、Azure Resource Manager にまとめて移行されます。 |
| Virtual Network | ポータルで作成された仮想ネットワークの移行 (.cscfg ファイルで "Group Resource-group-name VNet-Name" を使用する必要があります)  | 移行の一環として、cscfg 内の仮想ネットワーク名は、仮想ネットワークの Azure Resource Manager ID を使用するように変更されます。 (サブスクリプション/サブスクリプション ID/リソース グループ/リソース グループ名/リソース/VNet 名) <br><br>移行後にデプロイを管理するには、.cscfg ファイルのローカル コピーを更新して、仮想ネットワーク名ではなく Azure Resource Manager ID の使用を開始します。 <br><br>古い名前付けスキームを使用する .cscfg ファイルは、検証に合格しません。 
| Virtual Network | 異なるサブネット内にロールのあるデプロイの移行。 | 異なるサブネット内に異なるロールがあるクラウド サービスは、移行がサポートされています。 |

## <a name="next-steps"></a>次の手順
- [クラシックから Azure Resource Manager への IaaS リソースのプラットフォームでサポートされる移行の概要](../virtual-machines/migration-classic-resource-manager-overview.md)
- [Azure portal](in-place-migration-portal.md) を使用して Cloud Services (延長サポート) に移行する
- [PowerShell](in-place-migration-powershell.md) を使用して Cloud Services (延長サポート) に移行する
