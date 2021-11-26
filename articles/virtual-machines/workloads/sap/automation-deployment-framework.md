---
title: Azure 上の SAP デプロイ自動化フレームワークの概要
description: Azure 上の SAP デプロイ自動化フレームワークのフレームワークとツールの概要。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.service: virtual-machines-sap
ms.topic: conceptual
ms.openlocfilehash: 4b3db0835c1710317672aca717a0faa59626e19f
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725222"
---
# <a name="sap-deployment-automation-framework-on-azure"></a>Azure 上の SAP デプロイ自動化フレームワーク

[Azure 上の SAP デプロイ自動化フレームワーク](https://github.com/Azure/sap-hana)は、SAP 環境をデプロイ、インストール、保守するためのオープンソース オーケストレーション ツールです。 SAP でサポートされている任意のバージョンのオペレーティング システム上で SAP HANA と NetWeaver、AnyDB に基づいて SAP ランドスケープのインフラストラクチャを作成し、任意の Azure リージョンにデプロイできます。

[オートメーション フレームワーク](https://github.com/Azure/sap-hana)には、次の 2 つの主要なコンポーネントがあります。
-   デプロイ インフラストラクチャ (コントロール プレーン) 
-   SAP インフラストラクチャ (SAP ワークロード)

コントロール プレーンとアプリケーション プレーン間の依存関係を次の図に示します。

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="コントロール プレーンとアプリケーション プレーンの間の SAP デプロイ自動化フレームワークの依存関係を示す図。":::

次の図は、コントロール プレーンとワークロード ゾーンの主なコンポーネントを示しています。

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="SAP デプロイ オートメーション フレームワーク環境を示す図。":::

フレームワークでは、インフラストラクチャのデプロイに [Terraform](https://www.terraform.io/) を使用し、オペレーティング システムとアプリケーションの構成に [Ansible](https://www.ansible.com/) を使用します。

> [!NOTE]
> この自動化フレームワークは、Microsoft のベスト プラクティスと SAP on Azure の原則に基づいています。 [SAP on Azure 仮想マシン (Azure VM) の使用開始ガイド](get-started.md)を参照して、安定性、信頼性、パフォーマンスのために認定済みの仮想マシンとストレージ ソリューションを使用する方法を理解してください。
> 
> この自動化フレームワークは、[Azure 用の Microsoft クラウド導入フレームワーク](/azure/cloud-adoption-framework/)にも従います。

SAP デプロイ自動化フレームワークのコントロール プレーンを使用して、SAP インフラストラクチャと SAP アプリケーション インフラストラクチャをデプロイします。 このデプロイでは、Terraform テンプレートを使用して、[サービスとしてのインフラストラクチャ (IaaS)](https://azure.microsoft.com/overview/what-is-iaas) で定義されるインフラストラクチャを作成し、SAP アプリケーションをホストします。

アプリケーション構成は、事前定義済みのプレイブックのセットを使用して、コントロール プレーンの Ansible コントローラーから実行されます。 プレイブックは以下を行います。

- ベース オペレーティング システム設定を構成する
- SAP 固有のオペレーティング システム設定を構成する
- システムでインストール メディアを使用可能にする
- SAP システムをインストールする
- SAP データベースをインストールする (SAP HANA、AnyDB)
- Pacemaker を使用して高可用性 (HA) を構成する
- SAP データベースの高可用性 (HA) を構成する


## <a name="about-the-control-plane"></a>コントロール プレーンの概要

コントロール プレーンには、他の環境のデプロイ元となるインフラストラクチャが格納されています。 コントロール プレーンは 1 度デプロイした後は、再デプロイする必要はほとんどありません。

コントロール プレーンには、次のサービスが用意されています
-   Terraform デプロイ インフラストラクチャ
-   Ansible コントローラー
-   Terraform 状態ファイルの永続ストレージ
-   ダウンロードした SAP ソフトウェアの永続的ストレージ
-   デプロイ資格情報のセキュリティで保護されたストレージ
-   プライベート DNS ゾーン (省略可能)

コントロール プレーンは、通常、[ハブ アンド スポーク アーキテクチャ](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)のハブ サブスクリプションにデプロイされるリージョン リソースです。 

コントロール プレーンの主なコンポーネントは次のとおりです。
- デプロイ仮想マシン 
- Terraform 状態ファイルのストレージ アカウント
- SAP インストール メディア用ストレージ アカウント
- デプロイ資格情報の Keyvault


## <a name="about-the-sap-workload"></a>SAP ワークロードの概要

SAP ワークロードには、SAP デプロイ用のすべての Azure インフラストラクチャ リソースが含まれています。 これらのリソースは、コントロール プレーンからデプロイされます。 SAP ワークロードには、次の 2 つの主要なコンポーネントがあります。
-   SAP ワークロード ゾーン
-   SAP システム

## <a name="about-the-sap-workload-zone"></a>SAP ワークロード ゾーンの概要

ワークロード ゾーンを使用すると、デプロイをさまざまな環境 (開発、テスト、実稼働) にパーティション分割できます。SAP ワークロード ゾーンは、SAP システムに次のサービスを提供します
-   仮想ネットワーク インフラストラクチャ
-   システム資格情報のセキュリティで保護されたストレージ (仮想マシンと SAP)
-   共有ストレージ (省略可能)


## <a name="about-the-sap-system"></a>SAP システムの概要

システムのデプロイは、Web、アプリ、データベースのレベルなどを含む、SAP アプリケーションを実行する仮想マシンで構成されます。

SAP システムでは、次のサービスが提供されます
-   SAP アプリケーションをホストするための仮想マシン、ストレージ、サポート インフラストラクチャ。

## <a name="glossary"></a>用語集

次の用語は、オートメーション フレームワークを理解するための重要な概念です。

### <a name="sap-concepts"></a>SAP の概念

| 項目 | 説明 |
| ---- | ----------- |
| システム | アプリケーションが実行する必要があるリソースを含む SAP アプリケーションのインスタンス。 一意の 3 文字の識別子 **SID** によって定義されます。
| 横 | SAP アプリケーション内のさまざまな環境にあるシステムの集合です。 例えば、SAP ERP Central Component (ECC)、SAP カスタマー リレーションシップ マネジメント (CRM)、SAP Business Warehouse (BW) です。 |
| ワークロード ゾーン | SAP アプリケーションを、非実稼働環境やおよび稼働環境、または開発環境、品質保証環境、実稼働環境などの環境にパーティション分割します。 仮想ネットワークやキー コンテナーなどの共有リソースを、内部のすべてのシステムに提供します。 |

次の図は、SAP システム、ワークロード ゾーン (環境)、ランドスケープ間の関係を示しています。 このセットアップ例では、顧客には ECC、CRM、BW という 3 つの SAP ランドスケープがあります。 各ランドスケープには、3 つのワークロード ゾーン (実稼働、品質保証、開発) が含まれています。 各ワークロード ゾーンには、1 つまたは複数のシステムが含まれています。

:::image type="content" source="./media/automation-deployment-framework/sap-terms.png" alt-text="ランドスケープ、ワークフロー ゾーン、システムを含む SAP 構成の図。":::

### <a name="deployment-components"></a>デプロイ コンポーネント

| 項目 | 説明 | Scope |
| ---- | ----------- | ----- |
| Deployer | Terraform と Ansible コマンドを実行できる仮想マシン。 SAP 仮想ネットワークにピアリングされている新規または既存の仮想ネットワークにデプロイされます。 | リージョン |
| ライブラリ | Terraform 状態ファイルと SAP インストール メディア用のストレージを提供します。 | リージョン |
| ワークロード ゾーン | SAP システムのデプロイ先の仮想ネットワークが含まれます。 また、環境内のシステムの資格情報を保持するキー コンテナーも含まれます。 | ワークロード ゾーン |
| システム | SAP アプリケーション (SID) のデプロイ ユニット。 ロード バランサーや可用性セットなどの仮想マシンとサポート インフラストラクチャ成果物が含まれます。 | ワークロード ゾーン |


## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [デプロイ オートメーション フレームワークの概要](automation-get-started.md)
