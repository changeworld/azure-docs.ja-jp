---
title: 企業に Azure DevTest Labs を導入する
description: この記事では、企業への Azure DevTest Labs の導入に関する規範的ガイダンスを提供します。
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e1d119f3c7c5d6dbdb570d362c53b80dad7886bd
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149004"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>企業で Azure DevTest Labs インフラストラクチャをセットアップする
クラウドには俊敏性、柔軟性、経済性を含む[メリット](/azure/architecture/cloud-adoption/business-strategy/cloud-migration-business-case)があるため、企業ではその導入が急速に進められています。 クラウドを使用する顧客の一般的な最初の手順では、開発およびテスト ワークロードから始めます。  DevTest Labs では、企業にメリットがあり、[主なエンタープライズ開発/テスト シナリオ](devtest-lab-guidance-get-started.md)をサポートする[機能](devtest-lab-concepts.md)が提供されます。

これらのワークロードをクラウドに移行する場合の一般的な懸念としては、次のようなものがあります。

- [開発/テスト用リソースのセキュリティ保護](devtest-lab-guidance-governance-policy-compliance.md)
- [コストの管理と理解](devtest-lab-guidance-governance-cost-ownership.md)
- エンタープライズ セキュリティとコンプライアンスを損なうことなく、開発者のセルフサービスを有効にする
- 追加のシナリオに対応するための DevTest Labs の自動化および拡張
- [数千ものリソースへの DevTest Labs ベース ソリューションのスケーリング](devtest-lab-guidance-scale.md)
- [DevTest Labs の大規模なデプロイ](devtest-lab-guidance-orchestrate-implementation.md)
- [概念実証の使用の開始](devtest-lab-guidance-orchestrate-implementation.md)

## <a name="intended-audience"></a>対象ユーザー
エンタープライズに重点を置いたドキュメントの対象者は、全体的なデプロイの確立と確認および運用方法の監視を担当する IT プランナー、アーキテクト、マネージャーです。 そのため、このドキュメントでは、最終的に組織内への Azure DevTest Labs の導入を推進する、安全で安定した開発/テスト環境を促進するための全体的なプロセスと推奨される設計の原則に注目します。

## <a name="enterprise-customers"></a>企業顧客

DevTest Labs の現行の企業顧客の多くは、自社の組織での開発およびワークロードのテストに DevTest Labs をうまく使用しています。 [詳細情報](https://azure.microsoft.com/en-us/case-studies/?term=DevTest+labs)。

## <a name="next-steps"></a>次の手順
- [エンタープライズ向け参照アーキテクチャ](devtest-lab-reference-architecture.md)
