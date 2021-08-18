---
title: Azure プラットフォームの整合性とセキュリティ - Azure のセキュリティ
description: Azure プラットフォームの整合性とセキュリティに関する技術概要。
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 06/24/2021
ms.openlocfilehash: e0522d6dcb02571a1ed197d60734906ee8131ab4
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/25/2021
ms.locfileid: "112893962"
---
# <a name="platform-integrity-and-security-overview"></a>プラットフォームの整合性とセキュリティの概要
Azure フリートは、数百万のサーバー (ホスト) で構成されており、さらに数千が日々追加されています。 また、再起動、オペレーティング システムの更新、または修復によって、毎日数千のホストでメンテナンスが実行されています。 ホストがこのフリートに参加して顧客のワークロードの受け入れを開始する前に、Microsoft では、ホストがセキュリティで保護された信頼できる状態であることを確認します。 この検証により、サプライ チェーンやメンテナンスのワークフロー中に、ブート シーケンス コンポーネントで悪意のある、または不注意による変更が発生していないことが保証されます。

## <a name="securing-azure-hardware-and-firmware"></a>Azure のハードウェアとファームウェアの保護
この一連の記事では、製造から終了まで、ライフサイクルのさまざまな段階を通じてホストの整合性とセキュリティが Microsoft によってどのように確保されているかについて説明します。 これらの記事では、次のことについて説明します。
 
- [ファームウェアのセキュリティ](firmware.md)
- [プラットフォーム コードの整合性](code-integrity.md)
- [UEFI セキュア ブート](secure-boot.md)
- [メジャー ブートとホストの構成証明](measured-boot-host-attestation.md)
- [プロジェクト Cerberus](project-cerberus.md)
- [保存時の暗号化](encryption-atrest.md)
- [ハイパーバイザー セキュリティ](hypervisor.md)
 
## <a name="next-steps"></a>次のステップ

- [ファームウェアのセキュリティ強化](firmware.md)を継続的に推進するために、Microsoft が積極的に行っているクラウド ハードウェア エコシステム内の提携について説明します。

- [クラウドにおける共同責任](shared-responsibility.md)について理解します。