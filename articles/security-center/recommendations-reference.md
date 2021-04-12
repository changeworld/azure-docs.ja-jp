---
title: Azure Security Center のすべての推奨事項の参照テーブル
description: この記事では、お使いのリソースを堅牢化および保護するのに役立つ Azure Security Center のセキュリティに関する推奨事項の一覧を示します。
author: memildin
ms.service: security-center
ms.topic: reference
ms.date: 04/06/2021
ms.author: memildin
ms.custom: generated
ms.openlocfilehash: a44a5e4e715238c42e51e65dfe16d4f70dfcf54d
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2021
ms.locfileid: "106504816"
---
# <a name="security-recommendations---a-reference-guide"></a>セキュリティの推奨事項 - リファレンス ガイド

この記事では、Azure Security Center に表示される可能性のある推奨事項の一覧を示します。 お使いの環境に示される推奨事項は、保護しているリソースとご自分でカスタマイズした構成によって異なります。

Security Center の推奨事項は、[Azure セキュリティ ベンチマーク](../security/benchmarks/introduction.md)に基づいています。 Azure セキュリティ ベンチマークは Microsoft が作成したもので、一般的なコンプライアンス フレームワークに基づくセキュリティとコンプライアンスのベスト プラクティスに関する Azure 固有のガイドラインのセットです。 この広く遵守されているベンチマークは、クラウド中心のセキュリティに重点を置いた [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/azure/) と [National Institute of Standards and Technology (NIST)](https://www.nist.gov/) の統制に基づいています。

これらの推奨事項に対応する方法については、[Azure Security Center での推奨事項の修復](security-center-remediate-recommendations.md)に関するページを参照してください。

ご自身のセキュア スコアは、完了した Security Center の推奨事項の数に基づきます。 最初に解決すべき推奨事項を決定するには、それぞれの重大度と、セキュア スコアに対する潜在的な影響を確認します。

> [!TIP]
> 推奨事項の説明に "関連ポリシーなし" と表示されている場合、その理由は通常、その推奨事項が別の推奨事項と _その_ ポリシーに依存しているためです。 たとえば、「…で Endpoint Protection の正常性エラーを修復する必要があります」という推奨事項は、エンドポイント保護ソリューションが _インストール済み_ であるかどうかを確認する推奨事項 (「エンドポイント保護ソリューションを...インストールする必要がある」) に依存しています。 基になる推奨事項にはポリシーが _存在します_。
> ポリシーを基本推奨事項のみに制限すると、ポリシー管理が簡単になります。

## <a name="appservices-recommendations"></a><a name='recs-appservices'></a>AppServices の推奨事項

[!INCLUDE [asc-recs-appservices](../../includes/asc-recs-appservices.md)]

## <a name="compute-recommendations"></a><a name='recs-compute'></a>コンピューティングの推奨事項

[!INCLUDE [asc-recs-compute](../../includes/asc-recs-compute.md)]

## <a name="container-recommendations"></a><a name='recs-container'></a>コンテナーの推奨事項

[!INCLUDE [asc-recs-container](../../includes/asc-recs-container.md)]

## <a name="data-recommendations"></a><a name='recs-data'></a>データの推奨事項

[!INCLUDE [asc-recs-data](../../includes/asc-recs-data.md)]

## <a name="identityandaccess-recommendations"></a><a name='recs-identityandaccess'></a>IdentityAndAccess の推奨事項

[!INCLUDE [asc-recs-identityandaccess](../../includes/asc-recs-identityandaccess.md)]

## <a name="iot-recommendations"></a><a name='recs-iot'></a>IoT の推奨事項

[!INCLUDE [asc-recs-iot](../../includes/asc-recs-iot.md)]

## <a name="networking-recommendations"></a><a name='recs-networking'></a>ネットワークの推奨事項

[!INCLUDE [asc-recs-networking](../../includes/asc-recs-networking.md)]

## <a name="deprecated-recommendations"></a>非推奨のレコメンデーション

|推奨|説明および関連するポリシー|重大度|
|----|----|----|
|App Services へのアクセスを制限する必要がある|ネットワーク構成を変更することによって App Services へのアクセスを制限し、広すぎる範囲からの受信トラフィックを拒否します。<br>(関連ポリシー: [プレビュー]App Services へのアクセスを制限する必要がある)|高|
|IaaS NSG 上の Web アプリケーションに対する規則を強化する必要がある|Web アプリケーションを実行していて、Web アプリケーション ポートに関する NSG 規則の制限が緩すぎる仮想マシンのネットワーク セキュリティ グループ (NSG) を強化します。<br>(関連ポリシー:IaaS 上の Web アプリケーションに対する NSG 規則を強化する必要がある)|高|
|Pod Security Policies should be defined to reduce the attack vector by removing unnecessary application privileges\(不要なアプリケーション特権を削除してポッドのセキュリティ ポリシーを定義し攻撃ベクトルを減らす必要がある\) (プレビュー)|不要なアプリケーション特権を削除してポッドのセキュリティ ポリシーを定義し、攻撃ベクトルを減らす必要があります。 アクセスが許可されているリソースにのみポッドがアクセスできるようポッドのセキュリティ ポリシーを定義し、構成することが推奨されます。<br>(関連ポリシー: [プレビュー]Kubernetes Services ではポッドのセキュリティ ポリシーを定義する必要がある)|Medium|
|IoT デバイスの可視性を向上させるために、IoT セキュリティ モジュールの Azure Security Center をインストールする|IoT デバイスの可視性を向上させるために、IoT セキュリティ モジュールの Azure Security Center をインストールします。|低|
|システムの更新プログラムを適用するには、マシンを再起動する必要があります|マシンを再起動してシステムの更新プログラムを適用し、マシンを脆弱性からセキュリティで保護します。 (関連ポリシー:システム更新プログラムをマシンにインストールする必要がある)|Medium|
| お使いのマシンに監視エージェントをインストールする必要があります|この操作により、選択した仮想マシンに監視エージェントがインストールされます。 エージェントが報告する先のワークスペースを選択します。 (関連ポリシーはありません)|高|
||||

## <a name="next-steps"></a>次のステップ

推奨事項の詳細については、以下を参照してください。

- [セキュリティ ポリシー、イニシアティブ、および推奨事項とは](security-policy-concept.md)
- [セキュリティの推奨事項を確認する](security-center-recommendations.md)
