---
title: 要約 - Microsoft Azure におけるコンテナーのセキュリティ
description: ホワイト ペーパー「Container security in Microsoft Azure (Microsoft Azure におけるコンテナーのセキュリティ)」の要約。
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727780"
---
# <a name="container-security-in-microsoft-azure"></a>Microsoft Azure におけるコンテナーのセキュリティ
## <a name="abstract"></a>要約

コンテナー テクノロジは、クラウド コンピューティングの世界に構造的変化をもたらしています。 コンテナーにより、オペレーティング システムの単一のインスタンスでアプリケーションの複数のインスタンスを実行できるようになるため、リソースをより効率的に使用できます。 コンテナーは組織に一貫性と柔軟性を提供します。 コンテナーを使用すると、アプリケーションをデスクトップで開発し、仮想マシンでテストした後、運用のためにクラウドにデプロイできるため、アプリケーションの継続的なデプロイが可能になります。 コンテナーは、リソースの最適化によって、俊敏性、効率化された操作、スケーラビリティ、コスト削減を実現します。

コンテナー テクノロジは比較的新しいため、多くの IT プロフェッショナルが、運用環境での可視性の欠如と使用についてセキュリティ上の懸念を抱いています。 多くの場合、開発チームはセキュリティのベスト プラクティスを認識していません。 このホワイト ペーパーは、セキュリティ運用チームと開発者が、Microsoft Azure プラットフォーム上でのコンテナーの開発とデプロイをセキュリティで保護する方法を選択する際に役立ちます。

このホワイト ペーパーでは、コンテナー、コンテナーのデプロイと管理、ネイティブ プラットフォーム サービスについて説明します。 また、Azure プラットフォーム上でコンテナーを使用する際に発生するランタイム セキュリティの問題についても説明します。 このホワイト ペーパーの図と例では、コンテナー モデルとしての Docker とコンテナー オーケストレーターとしての Kubernetes に注目しています。 セキュリティに関する推奨事項のほとんどは、Azure プラットフォーム上の Microsoft パートナーの他のコンテナー モデルにも適用されます。

[ホワイト ペーパーをダウンロードする](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)