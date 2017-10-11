---
title: "Operations Management Suite のセキュリティと監査ソリューションのデータのセキュリティ |Microsoft ドキュメント"
description: "このドキュメントでは、データの管理し、Operations Management Suite のセキュリティと監査ソリューションで保護する方法について説明します。"
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: 
ms.assetid: 9cdf7deb-2a30-4672-b89f-71179ee8326a
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: yurid
ms.openlocfilehash: 3b6327b1f5150f32afd71639f32c55d823f1d1f0
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2017
---
# <a name="operations-management-suite-security-and-audit-solution-data-security"></a>Operations Management Suite のセキュリティと監査ソリューションのデータ セキュリティ
防止、検出、および、脅威への応答を支援する[Operations Management Suite (OMS) のセキュリティと監査ソリューション](operations-management-suite-overview.md)の収集およびが含まれているリソースに関するデータを処理します。

* セキュリティ イベント ログ
* イベント Tracing for Windows (ETW) イベント
* AppLocker イベントの監査
* Windows ファイアウォールのログ
* 高度な Threat Analytics イベント
* 基準の評価の結果
* マルウェアの評価の結果
* 更新プログラム/修正プログラムの評価の結果
* エージェントで明示的に有効になっているシステム ログ ストリーム

このデータのセキュリティとプライバシーを保護する厳密なコミットメントを行う場合。 Microsoft ではコーディングからサービスの運用まで、厳密なコンプライアンスとセキュリティのガイドラインに準拠しています。
この記事では、データの管理し、OMS のセキュリティと監査ソリューションで保護する方法について説明します。

## <a name="data-sources"></a>データ ソース
OMS のセキュリティと監査ソリューションは、仮想マシンと、OMS エージェントがインストールされている物理コンピューターからデータを分析します。 OMS のセキュリティと監査ソリューションは、Windows のイベント、監査ログ、IIS ログおよび syslog メッセージなどのセキュリティ イベントに関する構成情報を収集できます。 このようなデータの例には: オペレーティング システムの種類とバージョン、プロセス、コンピューター名、IP アドレスを実行しているログイン、ユーザーおよびテナントの id。  

## <a name="data-protection"></a>データの保護
**データの分離**: データが、サービスを通じて各コンポーネントで論理的に個別に保存します。 すべてのデータは組織ごとにタグ付けされます。 このタグ付けはデータのライフ サイクルにおいて継続され、サービスの各層で強制されます。 

**データ アクセス**: Microsoft の担当者をセキュリティに関する推奨事項を提供し、潜在的なセキュリティ上の脅威の調査、収集またはサービスによって分析情報にアクセス可能性があります。 従うお、 [Microsoft オンライン サービスの使用条件](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)と[のプライバシーに関する声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)Microsoft がお客様のデータを使用していない、そこからの広告または類似した商用目的で情報を取得またはどのような状態です。 セキュリティに関する推奨事項を提供し、潜在的なセキュリティ上の脅威の調査、Microsoft の担当者に、収集またはサービスによって分析情報がアクセスできます。 おのみ顧客データを使用して必要に応じて、目的でそれらのサービスを提供することとの互換性を含む、Azure のサービスを提供します。 独自のデータに対するすべての権限を保持します。

**データの使用**: Microsoft の防止、検出の機能を強化するためにパターンと複数のテナントに表示される脅威インテリジェンスを使用する以外の場合は、プライバシーのコミットメントを果たすの記載に従ってようお、[のプライバシーに関する声明](https://www.microsoft.com/privacystatement/en-us/OnlineServices/Default.aspx)です。

> [!NOTE]
> データの場所はレベルで構成、OMS ワークスペース、初期の OMS のセキュリティと監査の構成プロセスの一部は、ワークスペースの作成時にします。
> 
> 

## <a name="see-also"></a>関連項目
このドキュメントでは、データが管理され、OMS で保護する方法を説明しました。 OMS のセキュリティと監査ソリューションに関する詳細についてを参照してください。

* [Operations Management Suite (OMS) の概要](operations-management-suite-overview.md)
* [監視と Operations Management Suite のセキュリティと監査ソリューション内でセキュリティの警告に応答して](oms-security-responding-alerts.md)
* [Operations Management Suite のセキュリティと監査ソリューション内のリソースの監視](oms-security-monitoring-resources.md)

