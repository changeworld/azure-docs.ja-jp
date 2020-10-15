---
title: Azure Security Center の対応準備ロードマップ | Microsoft Docs
description: このドキュメントでは、Azure Security Center を導入するための対応準備ロードマップについて取り上げます。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: fece670cc-df70-445d-9773-b32cbaba8d4a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/03/2018
ms.author: memildin
ms.openlocfilehash: 52ea6f862b7ef6190348743a128912131e6a9609
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/09/2020
ms.locfileid: "91314489"
---
# <a name="azure-security-center-readiness-roadmap"></a>Azure Security Center の対応準備ロードマップ
このドキュメントでは、Azure Security Center の導入を支援する対応準備ロードマップについて取り上げます。

## <a name="understanding-security-center"></a>Security Center について
Azure Security Center は、Azure、オンプレミス、他のクラウドで実行されているワークロードの統合セキュリティ管理と高度な脅威保護を実現します。 

Security Center の基本的な事柄については、次のリソースを参照してください。

[アーティクル]
- [Azure Security Center 入門](security-center-introduction.md)
- [Azure Security Center クイック スタート ガイド](security-center-get-started.md)

ビデオ
- [概要紹介ビデオ](https://azure.microsoft.com/resources/videos/introduction-to-azure-security-center/)
- [脅威の回避、検出、対応に関して Security Center が備えている機能の概要](https://azure.microsoft.com/resources/videos/azurecon-2015-new-azure-security-center-helps-you-prevent-detect-and-respond-to-threats/)

## <a name="planning-and-operations"></a>計画と運用

Security Center を最大限に活用するには、安全な運用、監視、ガバナンス、インシデント対応のニーズを満たすために、組織内のさまざまな個人やチームがこのサービスをどのように使用するのかを把握することが重要です。

計画と運用のプロセスでは、次の資料が参考になります。

- [Azure Security Center 計画および運用ガイド](security-center-planning-and-operations-guide.md)


### <a name="onboarding-computers-to-security-center"></a>Security Center へのコンピューターのオンボーディング
Security Center によって、Azure Defender で保護されていない Azure サブスクリプションまたはワークスペースが自動的に検出されます。 これには、Security Center Free を使用する Azure サブスクリプションと、セキュリティ ソリューションが有効になっていないワークスペースが含まれます。

オンボーディング プロセスでは、次の資料が参考になります。

- [Azure 以外のコンピューターのオンボード](quickstart-onboard-machines.md)
- [Azure Security Center Hybrid - Overview (Azure Security Center ハイブリッド - 概要)](https://youtu.be/NMa4L_M597k)

## <a name="mitigating-security-issues-using-security-center"></a>Security Center によるセキュリティの問題の緩和
Security Center は、真の脅威を検出し、偽陽性を減らすために、Azure のリソースやネットワークのほか、接続されているパートナー ソリューション (ファイアウォールやエンドポイント保護ソリューションなど) から、自動的にログ データを収集、分析、統合します。

セキュリティ アラートの管理とリソースの保護については、次のリソースが参考になります。

[アーティクル]    
- [Azure Security Center でのセキュリティ ヘルスの監視](https://docs.microsoft.com/azure/security-center/security-center-monitoring)
- [Azure Security Center でのネットワークの保護](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)
- [Azure Security Center での Azure SQL サービスとデータの保護](https://docs.microsoft.com/azure/security-center/security-center-sql-service-recommendations)


ビデオ    
- [Mitigating Security Issues using Azure Security Center (Azure Security Center を使用してセキュリティの問題を緩和する)](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Mitigating-Security-Issues-using-Azure-Security-Center)

### <a name="security-center-for-incident-response"></a>Security Center によるインシデント対応
コストを削減し、被害を抑えるためには、攻撃を受ける前にインシデント対応計画を整えておくことが重要です。 Azure Security Center は、インシデント対応のさまざまな段階で利用できます。

インシデント対応プロセスへの具体的な Security Center の導入方法については、次の資料が参考になります。

ビデオ    
* [Azure Security Center in Incident Response (Azure Security Center でのインシデント対応)](https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response)
* [Respond quickly to threats with next-generation security operation, and investigation (次世代のセキュリティ運用と調査を通じて脅威にすばやく対応する)](https://youtu.be/e8iFCz5RM4g)

[アーティクル]    
* [Azure Security Center を活用したインシデント対応](https://docs.microsoft.com/azure/security-center/security-center-incident-response)
* [ワークフローの自動化による応答の自動化](workflow-automation.md)

## <a name="advanced-cloud-defense"></a>高度なクラウド防御

Azure VM は、Security Center にある高度なクラウド防御機能を有効活用することができます。 そうした機能の例として、Just-in-Time VM (仮想マシン) アクセスやアダプティブ アプリケーション制御が挙げられます。

これらの Security Center 機能の使い方については、次の資料を参照してください。

ビデオ    
* [Azure Security Center - Just-in-Time VM アクセス](https://youtu.be/UOQb2FcdQnU)
* [Azure Security Center - アダプティブ アプリケーション制御](https://youtu.be/wWWekI1Y9ck)

[アーティクル]    
* [Just-In-Time を使用した仮想マシン アクセスの管理](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
* [Azure Security Center におけるアダプティブ アプリケーション制御](https://docs.microsoft.com/azure/security-center/security-center-adaptive-application)

## <a name="hands-on-activities"></a>実践アクティビティ

* [Security Center ハンズオン ラボ](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=78871abf-6f35-4aa0-840f-d801f5cdbd72)
* [Security Center の Web アプリケーション ファイアウォール (WAF) 推奨プレイブック](https://gallery.technet.microsoft.com/ASC-Playbook-Protect-38bd47ff)
* [Azure Security Center プレイブック:セキュリティのアラート](https://gallery.technet.microsoft.com/Azure-Security-Center-f621a046)

## <a name="additional-resources"></a>その他のリソース
* [Security Center のドキュメント ページ](https://docs.microsoft.com/azure/security-center/)
* [Security Center REST API ドキュメント ページ](https://msdn.microsoft.com/library/mt704034.aspx)
* [Azure Security Center のよく寄せられる質問 (FAQ)](https://docs.microsoft.com/azure/security-center/security-center-faq)
* [Security Center の価格ページ](https://azure.microsoft.com/pricing/details/security-center/)
* [ID に関するセキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/identity-management-best-practices)
* [ネットワーク セキュリティのベスト プラクティス](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices)
* [PaaS に関する推奨情報](https://docs.microsoft.com/azure/security/security-paas-deployments)
* [コンプライアンス](https://www.microsoft.com/trustcenter/compliance/due-diligence-checklist)
* [Log Analytics のお客さまが Azure Security Center を使用してハイブリッド クラウド ワークロードを保護できるようになりました](https://blogs.technet.microsoft.com/msoms/2017/09/25/oms-customers-can-now-use-azure-security-center-to-protect-their-hybrid-cloud-workloads/)

## <a name="community-resources"></a>コミュニティ リソース

* [Security Center UserVoice](https://feedback.azure.com/forums/347535-azure-security-center)
* [Security Center の Q&A ページ](https://docs.microsoft.com/answers/topics/azure-security-center.html)