---
title: Defender for Cloud 対応性ロードマップ |Microsoft Docs
description: このドキュメントでは、Defender for Cloud を導入するための対応準備ロードマップについて取り上げます。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 345b1373371cc8643cb6d9c82e8a98e2eee42def
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457147"
---
# <a name="defender-for-cloud-readiness-roadmap"></a>Defender for Cloud 対応性のロードマップ

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

このドキュメントでは、Defender for Cloud の導入を支援する対応準備ロードマップについて取り上げます。

## <a name="understanding-defender-for-cloud"></a>Defender for Cloud について
Defender for Cloud は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。 

Defender for Cloud の基本的な事柄については、次のリソースを参照してください。

記事
- [Defender for Cloud の概要](defender-for-cloud-introduction.md)
- [Defender for Cloud クイック スタート ガイド](get-started.md)

ビデオ
- [概要紹介ビデオ](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [脅威の回避、検出、対応に関して Defender for Cloud が備えている機能の概要](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>計画と運用

Defender for Cloud を最大限に活用するには、安全な運用、監視、ガバナンス、インシデント対応のニーズを満たすために、組織内のさまざまな個人やチームがこのサービスをどのように使用するのかを把握することが重要です。

計画と運用のプロセスでは、次の資料が参考になります。

- [Defender for Cloud の計画と運用のガイド](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-defender-for-cloud"></a>Defender for Cloud へのコンピューターのオンボード
Defender for Cloud によって、Azure Defender で保護されていない Azure サブスクリプションまたはワークスペースが自動的に検出されます。 これには、Defender for Cloud を使用する Azure サブスクリプションと、セキュリティ ソリューションが有効になっていないワークスペースが含まれます。

オンボーディング プロセスでは、次の資料が参考になります。

- [Azure 以外のコンピューターのオンボード](quickstart-onboard-machines.md)
- [Defender for Cloud ハイブリッド - 概要](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-defender-for-cloud"></a>Defender for Cloud を使用したセキュリティの問題の軽減
Defender for Cloud は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。

セキュリティ アラートの管理とリソースの保護については、次のリソースが参考になります。

[アーティクル]    
- [Defender for Cloud でのネットワークの保護](./protect-network-resources.md)
- [Defender for Cloud での Azure SQL サービスとデータの保護](./implement-security-recommendations.md)


ビデオ    
- [Defender for Cloud を使用したセキュリティの問題の軽減](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="defender-for-cloud-for-incident-response"></a>インシデント対応のための Defender for Cloud
コストを削減し、被害を抑えるためには、攻撃を受ける前にインシデント対応計画を整えておくことが重要です。 インシデント対応の異なるステージで Defender for Cloud を使用できます。

インシデント対応プロセスへの具体的な Defender for Cloud の導入方法については、次の資料が参考になります。

ビデオ    
* [インシデント対応のための Defender for Cloud](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Respond quickly to threats with next-generation security operation, and investigation (次世代のセキュリティ運用と調査を通じて脅威にすばやく対応する)](https://youtu.be/e8iFCz5RM4g)

[アーティクル]    
* [インシデント対応のための Defender for Cloud の使用する](./tutorial-security-incident.md)
* [自動化を使用して Defender for Cloud トリガーに応答する](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>高度なクラウド防御

Azure VM は、Defender for Cloud にある高度なクラウド防御機能を有効活用することができます。 そうした機能の例として、Just-in-Time VM (仮想マシン) アクセスやアダプティブ アプリケーション制御が挙げられます。

これらの Defender for Cloud 機能の使い方については、次の資料を参照してください。

ビデオ    
* [Defender for Cloud – Just-In-Time VM アクセス](https://youtu.be/UOQb2FcdQnU)
* [Defender for Cloud - 適応型アプリケーション制御](https://youtu.be/wWWekI1Y9ck)

[アーティクル]    
* [Just-In-Time を使用した仮想マシン アクセスの管理](./just-in-time-access-usage.md)
* [Defender for Cloud での適応型アプリケーション制御](./adaptive-application-controls.md)

## <a name="hands-on-activities"></a>実践アクティビティ

* [Defender for Cloud ハンズオン ラボ](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Defender for Cloud の Web アプリケーション ファイアウォール (WAF) 推奨プレイブック](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Defender for Cloud プレイブック: セキュリティ アラート](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>その他の技術情報
* [Defender for Cloud のドキュメントページ](./index.yml)
* [Defender for Cloud REST API のドキュメントページ](/previous-versions/azure/reference/mt704034(v=azure.100))
* [Defender for Cloud についてよく寄せられる質問](./faq-general.yml)
* [価格ページ](https://azure.microsoft.com/pricing/details/security-center/)
* [ID に関するセキュリティのベスト プラクティス](../security/fundamentals/identity-management-best-practices.md)
* [ネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)
* [PaaS に関する推奨情報](../security/fundamentals/paas-deployments.md)
* [コンプライアンス](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics のお客さまが Defender for Cloud を使用してハイブリッド クラウド ワークロードを保護できるようになりました](/archive/blogs/msoms/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads)
