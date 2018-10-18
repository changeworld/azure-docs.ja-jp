---
title: Azure Security Center でのリソース セキュリティの検疫 | Microsoft Docs
description: この記事は、Azure Security Center でリソース セキュリティの検疫の監視を開始する際に役立ちます。
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 3bd5b122-1695-495f-ad9a-7c2a4cd1c808
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 9/21/2018
ms.author: rkarlin
ms.openlocfilehash: 361fc2c6931dcfaed062bd39b75be7bef059b467
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2018
ms.locfileid: "46122068"
---
# <a name="monitoring-resource-security-hygiene-in-azure-security-center"></a>Azure Security Center でリソース セキュリティの検疫を監視する
この記事は、Azure Security Center で監視機能を使用してリソース セキュリティを可能な限り厳しくし、ポリシーに対するコンプライアンスを監視するうえで役立ちます。

## <a name="what-is-resource-security-hygiene"></a>リソース セキュリティの検疫とは。
私たちは監視について、状況に対処できるように、イベントが発生するのを監視して待機することだと考えがちです。 リソース セキュリティの検疫は、組織の標準やベスト プラクティスを満たしていないシステムを識別するために、リソースを監査するプロアクティブな戦略を持つことを意味します。

## <a name="resource-security-hygiene"></a>リソース セキュリティの検疫
サブスクリプションのリソースに対して[セキュリティ ポリシー](security-center-policies.md)を有効にすると、Security Center は、リソースのセキュリティを分析して潜在的な脆弱性を特定します。 ネットワークの構成に関する情報は、すぐに利用可能になります。 VM とコンピューターの構成に関する情報 (セキュリティ更新プログラムの状態、オペレーティング システムの構成など) については、エージェントがインストールされている VM とコンピューターの台数によっては、収集して利用可能になるまでに 1 時間以上かかる場合があります。 **[推奨事項]** タイルでは、すべての問題とネットワークを強化し、リスクを軽減するための手法が一覧になっています。

リソースのセキュリティ状態とリソースの種類別の問題を確認できます。

- コンピューター リソースとアプリの正常性を監視し、セキュリティを改善するための推奨事項を受信する方法については、「[Azure Security Center でのマシンとアプリケーションの保護](security-center-virtual-machine-protection.md)」を参照してください。
- 仮想マシン、ネットワーク セキュリティ グループ、エンドポイントなど、ネットワーク リソースを監視し、セキュリティを改善するための推奨事項を受信する方法については、「[Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)」を参照してください。 
- SQL サーバーやストレージ アカウントなど、データとストレージ リソースを監視し、セキュリティを改善するための推奨事項を受信する方法については、「[Azure Security Center での Azure SQL サービスとデータの保護](security-center-sql-service-recommendations.md)」を参照してください。 
- MFA やアカウント アクセス許可など、ID やアクセスのリソースを監視し、セキュリティを改善するための推奨事項を受信する方法については、「[Azure Security Center での ID とアクセスの監視](security-center-identity-access.md)」を参照してください。 
- リソースへの Just in Time アクセスを監視する方法については、「[Just in Time を使用した仮想マシン アクセスの管理](security-center-just-in-time.md)」を参照してください。 


推奨事項の適用方法の詳細については、「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」を参照してください。



![[Resources security health (リソース セキュリティの正常性)] タイル](./media/security-center-monitoring/security-center-monitoring-fig1-newUI-2017.png)



## <a name="see-also"></a>関連項目
この記事では、Azure Security Center での監視機能の使用方法について説明しました。 Azure セキュリティ センターの詳細については、次を参照してください。

* 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」: Azure Security Center でセキュリティ設定を構成する方法について説明します。
* 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」: セキュリティの警告の管理と対応の方法について説明しています。
* 「[Azure Security Center を使用したパートナー ソリューションの監視](security-center-partner-solutions.md)」: パートナー ソリューションの正常性状態を監視する方法について説明しています。
* 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」: このサービスの使用に関してよく寄せられる質問が記載されています。
* [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/): Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。
