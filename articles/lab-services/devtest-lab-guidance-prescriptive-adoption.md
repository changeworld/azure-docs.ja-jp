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
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: f16bdb8b89c2ce72c2a31a320d2dedbc4df09c81
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2019
ms.locfileid: "56242789"
---
# <a name="set-up-azure-devtest-labs-infrastructure-in-your-enterprise"></a>企業で Azure DevTest Labs インフラストラクチャをセットアップする
クラウドには俊敏性、柔軟性、経済性のメリットがあるため、企業はその導入を急速に進めています。 通常、クラウドへの最初のステップは、開発およびテストのワークロードを移行することです。 これらのワークロードを移行する場合の一般的な懸念としては次のようなものがあります。 

- 開発/テスト用リソースのセキュリティ保護
- 開発/テストと運用の明確な分離
- IT チーム、アプリケーション チーム、プロジェクト チームの間でのリソースの分割
- コストの管理
- セキュリティを損わない開発/テスト インフラストラクチャのセルフサービス セットアップの許可

## <a name="intended-audience"></a>対象ユーザー
このドキュメントの対象者は、全体的なデプロイの確立と確認および運用方法の監視を担当する IT プランナー、アーキテクト、マネージャーです。 そのため、このドキュメントでは、最終的に組織への Azure DevTest Labs の導入を推進する安全で安定した開発環境を促進するための全体的なプロセスと推奨される設計の原則に注目します。

## <a name="next-steps"></a>次の手順
- [Azure DevTest Labs の使用を始める](devtest-lab-guidance-get-started.md)