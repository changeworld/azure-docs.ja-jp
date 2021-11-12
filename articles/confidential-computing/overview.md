---
title: Azure Confidential Computing の概要
description: Azure Confidential Computing (ACC) の概要
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77c65a7c5418ebefd7b2414d04bec614eccdc5ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284362"
---
# <a name="what-is-confidential-computing"></a>コンフィデンシャル コンピューティングとは何ですか。

コンフィデンシャル コンピューティングを使用すると、機密データが処理されている間も、そのデータを隔離することができます。 多くの業界で、そのデータを保護するために、コンフィデンシャル コンピューティングを使用し、次のようなことが行われています。

- 財務データをセキュリティで保護する
- 患者の情報を保護する
- 機密情報に対する機械学習処理を実行する
- 複数のソースからの暗号化されたデータ セットに対してアルゴリズムを実行する


## <a name="overview"></a>概要
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Microsoft は、お客様のクラウド データをセキュリティで保護することの重要性を認識しています。 お客様からの不安の声も届いています。 機密性の高いワークロードをクラウドに移すにあたってお客様が抱く可能性のある疑問を何点かだけ挙げてみます。 

- 暗号化されていないデータに Microsoft がアクセスできないことを確かめるには、どうすればよいか。
- 自社内の特権管理者からのセキュリティ上の脅威を防ぐにはどうすればよいか。
- 機密性の高い顧客データにサードパーティがアクセスできないようにするために、他にどのような方法があるか。

Azure を使用すると、攻撃面を最小限に抑え、より強固なデータ保護を実現できます。 Azure には、クライアント側暗号化やサーバー側暗号化などのモデルを使用して [**保存データ**](../security/fundamentals/encryption-atrest.md)を保護するさまざまなツールが既に用意されています。 さらに、TLS や HTTPS といった安全なプロトコルを通じて、[**転送中のデータ**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit)を暗号化するメカニズムも備わっています。 このページで紹介するのは、第 3 のデータ暗号化、つまり **使用中のデータ** の暗号化です。

## <a name="introduction-to-confidential-computing"></a>コンフィデンシャル コンピューティングの概要 

コンフィデンシャル コンピューティングは、コンフィデンシャル コンピューティングを定義し、その導入を促進することに特化した団体である [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC) によって定義された業界用語です。 CCC では、コンフィデンシャル コンピューティングを次のように定義しています。ハードウェアベースの信頼できる実行環境 (TEE) で計算を実行することによる、使用中のデータの保護。

TEE は、承認されたコードのみが実行されるよう強制する環境です。 TEE 内のデータを、その環境外のコードで読み取ったり改ざんしたりすることはできません。 

### <a name="lessen-the-need-for-trust"></a>信頼の必要性を軽減する
クラウドでワークロードを実行するには、信頼が必要です。 アプリケーションの各種のコンポーネントを有効にするさまざまなプロバイダーに、この信頼を付与します。


**アプリ ソフトウェア ベンダー**: オンプレミスにデプロイするか、オープンソースを使用するか、社内アプリケーション ソフトウェアを構築することで、ソフトウェアを信頼します。

**ハードウェア ベンダー**: オンプレミスのハードウェアまたは社内ハードウェアを使用することで、ハードウェアを信頼します。 

**インフラストラクチャ プロバイダー**: クラウド プロバイダーを信頼するか、独自のオンプレミスのデータ センターを管理します。

Azure のコンフィデンシャル コンピューティングを使用すると、コンピューティング クラウド インフラストラクチャのさまざまな側面にわたる信頼の必要性が軽減され、クラウド プロバイダーを信頼しやすくなります。 Azure のコンフィデンシャル コンピューティングにより、ホスト OS カーネル、ハイパーバイザー、VM 管理者、およびホスト管理者への信頼が最小限に抑えられます。

## <a name="next-steps"></a>次のステップ

Azure のすべてのコンフィデンシャル コンピューティング製品について説明します。

> [!div class="nextstepaction"]
> [Azure コンフィデンシャル コンピューティング サービスの概要](overview-azure-products.md)
