---
title: Azure Image Builder の概要
description: Azure の仮想マシンの Azure Image Builder についての詳細を参照してください。
author: sumit-kalra
ms.author: sukalra
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 702a4b624041a20c925bfc65d92542788f381800
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132551314"
---
# <a name="azure-image-builder-overview"></a>Azure Image Builder の概要

**適用対象:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: フレキシブル スケール セット :heavy_check_mark: ユニフォーム スケール セット

組織で標準化された仮想マシン (VM) イメージを使用すると、クラウドに移行し、デプロイの一貫性を保つことができます。 通常、イメージには、事前に定義されたセキュリティと構成設定と、必要なソフトウェアが含まれています。 独自のイメージング パイプラインを設定するには、時間、インフラストラクチャ、およびセットアップが必要です。 Azure VM Image Builder (Image Builder) では、イメージを記述した構成を作成し、イメージを構築して配布するサービスに送信するだけで済みます。

Image Builder では、既存のスクリプト、コマンド、およびプロセスを使用してイメージをカスタマイズしながら、既存のイメージ カスタマイズ パイプラインを Azure に移行することができます。 Image Builder を使用すると、コア アプリケーションを VM イメージに統合し、VM で作成後にワークロードを一度に取得できるようになります。 また、構成を追加して、Azure Virtual Desktop のイメージを構築したり、Azure Stack で使用する、またはエクスポートを容易にするために VHD としてビルドしたりすることもできます。

Image Builder では、Windows または Linux イメージから始めることができます。また、Azure Marketplace または既存のカスタムイメージから開始し、独自のカスタマイズを追加することもできます。 [Azure Compute Gallery](shared-image-galleries.md) (旧称 Shared Image Gallery) では、イメージのホスト先として、生成されるマネージド イメージまたは VHD を指定することもできます。

## <a name="features"></a>特徴

カスタム VM イメージは手動または他のツールで作成できますが、プロセスは煩雑で信頼性が低いことがあります。 Azure VM Image Builder は [HashiCorp Packer](https://www.packer.io/) に基づいて構築されているため、マネージド サービス固有のメリットがあります。

### <a name="simplicity"></a>簡便性

- VM イメージを作成するための複雑なツール、プロセス、手動の手順を使用する必要がなくなります。 Image Builder は、これらの詳細をすべて抽出し、イメージ (sysprep) を一般化する必要があるなど Azure 固有の要件を隠蔽しつつ、より高度なユーザーには上書きも許可します。
- Image Builder を既存のイメージ ビルド パイプラインと統合すれば、クリックアンドゴー操作を行うことができます。 パイプラインから Image Builder を呼び出すか、[Azure Image Builder Service DevOps Task (プレビュー)](./linux/image-builder-devops-task.md)を使用することができます。
- Image Builder では、さまざまなソースからカスタマイズ データをフェッチできるため、VM イメージを構築するために 1 か所でまとめて収集する必要がありません。
- Image Builder と Azure Compute Gallery を統合することで、グローバルにイメージを配布、複製、バージョン管理、拡張できるイメージ管理システムが実現します。 また、同じ結果のイメージを VHD として、または最初から再構築せずに 1 つ以上の管理対象イメージとして配布することもできます。

### <a name="infrastructure-as-code"></a>コードとしてのインフラストラクチャ

- 長期的なインフラストラクチャ (*カスタマイズ データを保持するストレージ アカウントなど*) や一時的なインフラストラクチャ (*イメージを構築するための一時的な仮想マシンなど*) を管理する必要はありません。 
- イメージビルダーは、VM イメージのビルド仕様とカスタマイズ アイテムを Azure リソースとして格納します。そのため、オフライン定義を維持する必要がなくなり、誤って削除または更新されたことによる環境誤差のリスクが排除されます。

### <a name="security"></a>セキュリティ

- Image Builder では、ベースライン イメージ (*お客様の最低限のセキュリティや企業の設定を含むことができる*) を作成でき、それを部門ごとにさらにカスタマイズできます。 これらのイメージは、Image Builder を使用して、最新のパッチが適用されたバージョンのソースイメージを使用してゴールデン イメージをすばやく再構築することにより、安全にコンプライアンスを維持できます。 Image Builder では、Azure Windows のベースラインを満たすイメージも簡単に作成できます。 詳細については、「[Image Builder - Windows ベースライン テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/imagebuilder-windowsbaseline)」を参照してください。
- Image Builder が、カスタマイズの成果物を取得するためにパブリック アクセスを許可する必要はありません。 Image Builder は、[Azure Managed Identity](../active-directory/managed-identities-azure-resources/overview.md)を使用してこれらのリソースを取得でき、Azure-RBAC を使用してこの ID の権限を必要に応じて厳しく制限することができます。 これで、アーティファクトのシークレットを保持できるだけでなく、承認されていないアクターによって改ざんされることもなくなります。
- カスタマイズ アーティファクトのコピー、一時的なコンピューティングおよびストレージ リソース、結果のイメージはすべて、Azure RBAC によって制御されるアクセス権を持つサブスクリプション内に安全に格納されます。 これには、カスタマイズされたイメージを作成するために使用するビルド VM が含まれ、カスタマイズ スクリプトとファイルが不明なサブスクリプションの不明な VM にコピーされないようにします。 さらに、ビルド VM に[分離された VM のプラン](./isolation.md)を使用することで、他のお客様のワークロードからの高度な分離を実現することもできます。
- Image Builder は既存の仮想ネットワークに接続できます。それにより、既存の構成サーバー (DSC、Chef、Puppet など)、ファイル共有、またはその他のルーティング可能なサーバー/サービスと通信できます。

## <a name="regions"></a>リージョン

Azure Image Builder サービスは、次のリージョンで利用できます。 

>[!NOTE]
> イメージは、これらのリージョン以外にも引き続き配布できます。
> 
- 米国東部
- 米国東部 2
- 米国中西部
- 米国西部
- 米国西部 2
- 米国中南部
- 北ヨーロッパ
- 西ヨーロッパ
- 東南アジア
- オーストラリア南東部
- オーストラリア東部
- 英国南部
- 英国西部

## <a name="os-support"></a>OS のサポート
Azure Image Builder は Azure Marketplace のベース OS イメージをサポートします。
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise マルチセッション/Professional
- Windows 2016
- Windows 2019

>[!IMPORTANT]
> 一覧表示されたオペレーティング システムはテスト済みであり、Azure Image Builder で動作します。 ただし、Azure Image Builder は、マーケットプレースにあるすべての Linux または Windows イメージで動作します。

## <a name="how-it-works"></a>しくみ

Azure VM Image Builder は、Azure リソース プロバイダーからアクセスできるフル マネージド Azure サービスです。 ソース イメージ、実行するカスタマイズ、および新しいイメージの配布先を指定する構成をサービスに提供します。下の図は、大まかなのワークフローを示しています。

![ソース (Windows/Linux)、カスタマイズ (シェル、PowerShell、Windows の [再起動して更新]、ファイルの追加)、および Azure Compute Gallery を使用したグローバル分散を示す、Azure Image Builder のプロセスの概念図](./media/image-builder-overview/image-builder-flow.png)

テンプレート構成は、PowerShell、Azure CLI、Azure Resource Manager テンプレートを使用して、および Azure VM Image Builder DevOps タスクを使用して渡すことができ、それをサービスに送信すると、イメージ テンプレートのリソースが作成されます。 イメージ テンプレートのリソースが作成されると、サブスクリプションに作成されたステージング リソース グループが `IT_\<DestinationResourceGroup>_\<TemplateName>_\(GUID)` という形式で表示されます。 ステージング リソース グループには、ScriptURI プロパティの File、Shell、PowerShell カスタマイズで参照されるファイルとスクリプトが含まれています。

イメージ テンプレートのリソースで `Run` を呼び出すビルドを実行するために、サービスにより、ビルドのために追加のリソース (VM、ネットワーク、ディスク、ネットワーク アダプターなど) がデプロイされます。既存の VNET Image Builder を使用せずにイメージをビルドすると、パブリック IP と NSG もデプロイされ、このサービスは SSH または WinRM を使用してビルド VM に接続します。 既存の VNET を選択した場合、サービスは Azure Private Link を使用してデプロイされ、パブリック IP アドレスは必要ありません。Image Builder のネットワークの詳細については、「[Image Builder ネットワークの概要](./linux/image-builder-networking.md)」を確認してください。

ビルドが完了すると、すべてのリソースが削除されますが、ステージング リソース グループとストレージ アカウントを除きます。これらを削除するには、イメージ テンプレートのリソースを削除するか、そのままにしてビルドを再度実します。

このドキュメントには、[Azure Image Builder GitHub リポジトリ](https://github.com/azure/azvmimagebuilder)の構成テンプレートとソリューションを参照する複数の例とステップバイステップ ガイドがあります。

### <a name="move-support"></a>移動のサポート
イメージ テンプレートのリソースは変更不可であり、リソースとステージング リソース グループへのリンクが含まれているため、リソースの種類は移動に対応していません。 イメージ テンプレートのリソースを移動する場合は、構成テンプレートのコピーがあることを確認し (ない場合は、リソースから既存の構成を抽出し)、新しい名前で新しいリソース グループに新しいイメージ テンプレートのリソースを作成して、以前のイメージ テンプレートのリソースを削除します。 

## <a name="permissions"></a>アクセス許可
(AIB) に登録すると、ステージング リソース グループ `(IT_*)` を作成、管理、削除するためのアクセス許可が AIB サービスに付与され、イメージのビルドに必要なリソースを追加する権限が与えられます。 これは、登録が成功したときに、AIB サービス プリンシパル名 (SPN) がサブスクリプションで使用可能になることによって行われます。

Azure VM Image Builder で、マネージド イメージまたは Azure Compute Gallery にイメージを配布できるようにするには、イメージの読み取りと書き込みのアクセス許可を持つ Azure ユーザー割り当て ID を作成する必要があります。 Azure Storage にアクセスする場合は、プライベートおよびパブリック コンテナーを読み取るためのアクセス許可が必要です。

アクセス許可の詳細については、[PowerShell](./linux/image-builder-permissions-powershell.md) および [AZ CLI](./linux/image-builder-permissions-cli.md) に関するページを参照してください。

## <a name="costs"></a>コスト
Azure Image Builder を使用してイメージの作成、構築、保存を行うと、コンピューティング、ネットワーク、ストレージのコストがかかります。 これらのコストは、手動でカスタム イメージを作成する際に発生するコストと同様です。 リソースについては、Azure の料金で課金されます。 

イメージ作成プロセス中に、ファイルがダウンロードされ、`IT_<DestinationResourceGroup>_<TemplateName>` リソース グループに格納されます。これにより、小額のストレージ コストが発生します。 これらを維持しない場合は、イメージの構築後に **イメージ テンプレート** を削除してください。
 
イメージ ビルダーは、VM に必要なストレージ、ネットワークとともに、Gen1 イメージには既定の D1v2 VM サイズ、Gen2 イメージには D2ds V4 を使用して VM を作成します。 これらのリソースは、構築プロセスの間は存続し、Image Builder のイメージ作成が終了すると削除されます。 
 
Azure Image Builder によって、選択したリージョンにイメージが配布されます。これにより、ネットワークのエグレス料金が発生する可能性があります。

## <a name="hyper-v-generation"></a>Hyper-V の世代
現在 Image Builder は、Azure Compute Gallery 内で Hyper-V ジェネレーション 1 (Gen1) およびジェネレーション 2 (Gen2) のイメージの作成をサポートしています。 
 
## <a name="next-steps"></a>次のステップ 
 
Azure Image Builder を試すには、[Linux](./linux/image-builder.md) または [Windows](./windows/image-builder.md) イメージの構築に関する記事を参照してください。
