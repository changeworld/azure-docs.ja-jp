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
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: 62728f5b66825eb6698e37bb7ad3adbad831b465
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615344"
---
# <a name="container-security-in-security-center"></a>Security Center のコンテナーのセキュリティ

Azure Security Center は、コンテナー セキュリティ用の Azure ネイティブ ソリューションです。 また、Security Center は、クラウドのワークロード、VM、サーバー、コンテナーのセキュリティにとって最適な単一ウィンドウ ビューのエクスペリエンスでもあります。

この記事では、コンテナーとそのアプリのセキュリティの改善、監視、保守管理に Security Center がどのように役立つかを説明します。 ここでは、コンテナーのセキュリティのこうした主要な側面について、Security Center がどのように役立つかを説明します。

* 脆弱性の管理
* コンテナーの環境のセキュリティ強化
* 実行時の保護

[![Azure Security Center のコンテナーのセキュリティ タブ](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

これらの機能を使用する方法については、「[コンテナーのセキュリティを監視する](monitor-container-security.md)」を参照してください。

## <a name="vulnerability-management---scanning-container-images-preview"></a>脆弱性管理 - コンテナー イメージのスキャン (プレビュー)
ARM ベースの Azure コンテナー レジストリを監視するには、Security Center の Standard レベルになっていることを確認してください ([価格](/azure/security-center/security-center-pricing)に関するページを参照してください)。 次に、オプションのコンテナー レジストリ バンドルを有効にします。 新しいイメージがプッシュされると、Security Center では、業界最高レベルの脆弱性スキャン ベンダーである Qualys のスキャナーを使用してイメージがスキャンされます。

Qualys または Security Center によって問題が見つかった場合は、Security Center ダッシュボードで通知されます。 Security Center には、すべての脆弱性について、実行可能な推奨事項、重大度の分類、および問題の修正方法に関するガイダンスが表示されます。 コンテナーの Security Center の推奨事項の詳細については、[推奨事項の参照リスト](recommendations-reference.md#recs-containers)を参照してください。

## <a name="environment-hardening"></a>環境のセキュリティ強化

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 構成の継続的な監視
Azure Security Center では、IaaS Linux VM 上、または Docker コンテナーを実行している他の Linux マシン上でホストされているアンマネージド コンテナーが識別されます。 Security Center によって、こうしたコンテナーの構成が継続的に評価されます。 その後、[Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/) と比較されます。

Security Center には CIS Docker Benchmark のルールセット全体が含まれており、コンテナーがいずれかのコントロールを満たしていない場合は警告が表示されます。 不適切な構成が検出されると、Security Center によってセキュリティの推奨事項が生成されます。 **推奨事項ページ**を使用すると、推奨事項を表示したり、問題を修復したりすることができます。 また、Docker でデプロイされたすべての仮想マシンが表示される **[コンテナー]** タブにも推奨事項が表示されます。 

この機能には関連する Security Center 推奨事項が表示されることがありますが、その詳細については、推奨事項参照テーブルの[コンテナー セクション](recommendations-reference.md#recs-containers)をご覧ください。

VM のセキュリティ問題を調査している場合、Security Center にはマシン上のコンテナーに関する追加情報が表示されます。 こうした情報には、Docker のバージョンとホストで実行されているイメージの数が含まれます。 

>[!NOTE]
> これらの CIS ベンチマーク チェックは、AKS マネージド インスタンスまたは Databricks マネージド VM では実行されません。

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Kubernetes クラスターの継続的な監視 (プレビュー)
Security Center は Azure Kubernetes Service (AKS) と連携して機能します。これは、コンテナー化されたアプリケーションを開発、デプロイ、および管理するための Microsoft のマネージド コンテナー オーケストレーション サービスです。

AKS には、クラスターのセキュリティ体制をセキュリティで制御し、把握する機能があります。 Security Center では、次の機能を使用します。
* AKS クラスターの構成を常に監視する
* 業界標準に合わせてセキュリティに関する推奨事項を生成する

この機能には関連する Security Center 推奨事項が表示されることがありますが、その詳細については、推奨事項参照テーブルの[コンテナー セクション](recommendations-reference.md#recs-containers)をご覧ください。

## <a name="run-time-protection---real-time-threat-detection"></a>実行時保護 - リアルタイムの脅威検出

Security Center は、コンテナー化された環境に対するリアルタイムの脅威検出機能を備えており、不審なアクティビティに対してはアラートが生成されます。 ユーザーは、この情報を使用して、迅速にセキュリティの問題を修復し、コンテナーのセキュリティを強化することができます。

脅威は、ホストおよび AKS クラスター レベルで検出されます。 詳細については、[Azure コンテナーに対する脅威の防止](threat-protection.md#azure-containers)に関する記事を参照してください。


## <a name="container-security-faq"></a>コンテナー セキュリティに関してよく寄せられる質問

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Azure Security Center ではどのような種類のイメージをスキャンできますか。
Security Center では、シェル アクセスを与える Linux OS ベースのイメージがスキャンされます。 

Qualys スキャナーでは、[Docker スクラッチ](https://hub.docker.com/_/scratch/)のようなスーパー ミニマリスト イメージがサポートされておらず、また、アプリケーションとそのランタイム依存関係のみが含まれる (パッケージ マネージャー、シェル、OS がない) "Distroless" イメージがサポートされていません。

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Azure Security Center スキャンではどのような方法でイメージをスキャンできますか。
イメージはレジストリから抽出されます。 その後、隔離されたサンドボックスの中で、既知の脆弱性の一覧を抽出する Qualys スキャナーによって実行されます。

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Azure Security Center ではどのくらいの頻度でイメージがスキャンされますか。
イメージ スキャンはプッシュのたびにトリガーされます。

### <a name="can-i-get-the-scan-results-via-rest-api"></a>REST API 経由でスキャン結果を取得できますか。
はい。 結果は [Sub-Assessments Rest API](/rest/api/securitycenter/subassessments/list/) の下にあります。 また、Azure Resource Graph (ARG) を利用できます。これはすべてのリソースを対象とする Kusto のような API であり、1 つのクエリで特定のスキャンをフェッチできます。
 

## <a name="next-steps"></a>次のステップ

Azure Security Center のコンテナー セキュリティの詳細については、次の関連記事を参照してください。

* コンテナー関連リソースのセキュリティ状態を表示する方法については、「[マシンとアプリケーションを保護する](security-center-virtual-machine-protection.md#containers)」の「コンテナー」セクションを参照してください。

* [Azure Kubernetes Service との統合](azure-kubernetes-service-integration.md)の詳細

* [Azure Container Registry との統合](azure-container-registry-integration.md)の詳細