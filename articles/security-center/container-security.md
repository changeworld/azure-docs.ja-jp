---
title: Azure Security Center のコンテナーのセキュリティ | Microsoft Docs
description: Azure Security Center のコンテナーのセキュリティ機能について説明します。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 9cb4f6ec7331a2d0eb03beb2680c882484b69541
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74868492"
---
# <a name="container-security-in-security-center"></a>Security Center のコンテナーのセキュリティ

Azure Security Center は、コンテナー セキュリティ用の Azure ネイティブ ソリューションです。 また、Security Center は、クラウドのワークロード、VM、サーバー、コンテナーのセキュリティにとって最適な単一ウィンドウ ビューのエクスペリエンスでもあります。

この記事では、コンテナーとそのアプリのセキュリティを改善、監視、および保守する方法について説明します。 ここでは、コンテナーのセキュリティのこうした主要な側面について、Security Center がどのように役立つかを説明します。

* 脆弱性の管理
* コンテナーの環境のセキュリティ強化
* 実行時の保護

[![Azure Security Center のコンテナーのセキュリティ タブ](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>脆弱性管理 - コンテナー イメージのスキャン (プレビュー)
Azure Container Registry を監視するには、Security Center の Standard レベルになっていることを確実にしてください ([価格](https://docs.microsoft.com/azure/security-center/security-center-pricing.md)に関するページを参照してください)。 次に、オプションのコンテナー レジストリ バンドルを有効にします。 新しいイメージがプッシュされると、Security Center では、業界最高レベルの脆弱性スキャン ベンダーである Qualys のスキャナーを使用してイメージがスキャンされます。

Qualys または Security Center によって問題が見つかった場合は、Security Center ダッシュボードで通知されます。 Security Center には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、および問題の修正方法に関するガイダンスが表示されます。 Security Center の推奨事項の詳細については、[こちら](recommendations-compute-and-apps.md)で推奨事項の一覧を参照してください。

## <a name="environment-hardening"></a>環境のセキュリティ強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 構成の継続的な監視
Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 また、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) とも比較されます。 

Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 不適切な構成が検出されると、Security Center によってセキュリティの推奨事項が生成されます。 **推奨事項ページ**を使用すると、推奨事項を表示したり、問題を修復したりすることができます。 また、Docker でデプロイされたすべての仮想マシンが表示される **[コンテナー]** タブにも推奨事項が表示されます。 仮想マシンのセキュリティ問題を調査している場合、Security Center にはマシン上のコンテナーに関する追加情報が表示されます。 こうした情報には、Docker のバージョンとホストで実行されているイメージの数が含まれます。 推奨事項の詳細については、[こちら](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection)を参照してください。 

>[!NOTE]
> これらの CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kubernetes クラスターの継続的な監視 (プレビュー)
Security Center は Azure Kubernetes Service (AKS) と連携して機能します。これは、コンテナー化されたアプリケーションを開発、デプロイ、および管理するための Microsoft のマネージド コンテナー オーケストレーション サービスです。

AKS には、クラスターのセキュリティ体制をセキュリティで制御し、把握する機能があります。 Security Center では、次の機能を使用します。
* AKS クラスターの構成を常に監視する
* 業界標準に合わせてセキュリティに関する推奨事項を生成する

Security Center の推奨事項の詳細については、[仮想マシンの保護](security-center-virtual-machine-protection.md)に関するページを参照してください。

## <a name="run-time-protection---real-time-threat-detection"></a>実行時保護 - リアルタイムの脅威検出

Security Center は、コンテナー化された環境に対するリアルタイムの脅威検出機能を備えており、不審なアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

脅威は、ホストおよび AKS クラスター レベルで検出されます。 詳細については、[Azure コンテナーに対する脅威の検出](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-)に関する記事を参照してください。

## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>コンテナー関連のリソースのセキュリティ体制を確認するには
1.  Security Center の **[計算とアプリ]** ページを開きます。
2.  **[コンテナー]** タブをクリックします。AKS クラスター、ACR レジストリ、および Docker を実行している VM の体制が表示されます。

## <a name="next-steps"></a>次の手順

Azure Security Center のコンテナーのセキュリティの詳細については、以下を参照してください。
* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)の詳細

* [Azure Container Registry との統合](azure-container-registry-integration.md)の詳細