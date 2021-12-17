---
title: SAP デプロイ自動化フレームワークのサポート マトリックス
description: Azure 上の SAP デプロイ自動化フレームワークのサポートされているプラットフォーム、トポロジ、機能。
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.topic: conceptual
ms.service: virtual-machines-sap
ms.openlocfilehash: b9295fdffc215d842bf53f1a7dcfbf984b764a07
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725209"
---
# <a name="supportability-matrix-for-the-sap-automation-framework"></a>SAP 自動化フレームワークのサポート マトリックス

[Azure 上の SAP デプロイ自動化フレームワーク](automation-deployment-framework.md)は、サポートされているすべての SAP on Azure トポロジのデプロイをサポートしています。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

### <a name="control-plane"></a>コントロール プレーン

Ansible コントローラーは Linux 上でのみ機能するため、コントロール プレーンの Deployer 仮想マシンは Linux 上にデプロイする必要があります。

### <a name="sap-application"></a>SAP アプリケーション

オートメーション フレームワークでは、Linux または Windows 仮想マシンの両方での SAP on Azure インフラストラクチャのデプロイがサポートされています。 

## <a name="supported-topologies"></a>サポートされているトポロジ

既定では、オートメーション フレームワークはデータベース層とアプリケーション層を使用してデプロイを行います。 アプリケーション層は、アプリケーション、セントラル サービス、 web ディスパッチャーというさらに 3 つの層に分割されます。 

アプリケーション層を使用せずに構成を指定することによって、オートメーション フレームワークをスタンドアロン サーバーにデプロイすることもできます。

## <a name="supported-deployment-topologies"></a>サポートされているデプロイ トポロジ

オートメーション フレームワークでは、グリーン フィールドとブラウン フィールドの両方のデプロイがサポートされています。 

### <a name="greenfield-deployments"></a>グリーンフィールド デプロイ
グリーン フィールド デプロイでは、必要なすべてのリソースがオートメーション フレームワークによって作成されます。

このシナリオでは、環境を構成するときに、関連するデータ (ネットワークとサブネットのアドレス空間) を指定します。 その他の例については、[ワークロード ゾーンの構成](automation-configure-workload-zone.md)に関するページを参照してください。

### <a name="brownfield-deployments"></a>ブラウンフィールド デプロイ
ブラウンフィールド デプロイでは、既存の Azure リソースはデプロイの一部として使用できます。

このシナリオでは、環境を構成するときに、既存のリソースの Azure リソース識別子を指定します。 その他の例については、[ワークロード ゾーンの構成](automation-configure-workload-zone.md)に関するページを参照してください。

## <a name="supported-azure-features"></a>サポートされる Azure の機能

オートメーション フレームワークでは、次の Azure サービス、特徴、機能を使用しているか、使用することができます。

- Azure Virtual Machines (VM)
    - 高速ネットワーク
    - アンカー VM (省略可能)
    - SSH 認証
    - ユーザー名とパスワードの認証
    - SKU 構成
    - カスタム イメージ
    - 新しい、または既存の近接配置グループ
- Azure Virtual Network (VNet)
    - SAP ネットワークにピアリングされるネットワークでのデプロイ
    - 顧客が指定した IP アドレス指定
    - Azure によって提供される IP アドレス指定
    - 新しい、または既存のネットワーク セキュリティ グループ
    - 新規または既存の仮想ネットワーク
    - 新規または既存のサブネット
- Azure 可用性ゾーン
    - 高可用性 (HA)
- Azure Firewall
- Azure Load Balancer
    - 標準ロード バランサー
- Azure Storage
    - ブート診断ストレージ
    - SAP インストール メディアのストレージ
    - Terraform 状態ファイルのストレージ
    - HA シナリオ用のクラウド監視ストレージ
- Azure Key Vault
    - 新規または既存のキー コンテナー
    - ディスクの暗号化用の顧客管理キー
- Azure アプリケーションのセキュリティ グループ (ASG)
- Azure NetApp Files (ANF)
    - 共有ファイルの場合

## <a name="unsupported-azure-features"></a>サポートされていない Azure の機能

現時点では、オートメーション フレームワークでは、次の Azure サービス、特徴、または機能は **サポートされません**。

- NFS 用 Azure Files
- Azure NetApp Files (ANF)
    - データベース ファイルの場合

## <a name="next-steps"></a>次のステップ


> [!div class="nextstepaction"]
> [オートメーション フレームワークの概要](automation-get-started.md)
