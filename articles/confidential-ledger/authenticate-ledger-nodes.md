---
title: Azure Confidential Ledger ノードの認証
description: Azure Confidential Ledger ノードの認証
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 20e5c46ea174807efea21cf4812eef47ee8c0257
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450098"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Azure Confidential Ledger ノードの認証

Azure Confidential Ledger ノードは、コード サンプルとユーザーによって認証できます。

## <a name="code-samples"></a>コード サンプル

初期化時に、コード サンプルでは、ID Service にクエリを実行してノード証明書を取得します。 ノード証明書を取得した後、コード サンプルは台帳ネットワークにクエリを実行して見積もりを取得し、ホスト検証バイナリを使用して検証します。 検証が成功すると、コード サンプルは台帳操作に進みます。

## <a name="users"></a>ユーザー

ユーザーは、Azure Confidential Ledger ノードの信頼性を検証して、それらが実際に台帳のエンクレーブとやり取りしていることを確認できます。 Azure Confidential Ledger ノードの信頼度はいくつかの方法で構築できます。それを相互に積み重ねることで、全体的な信頼度レベルを高めることができます。 そのため、手順 1 から 2 は、機能ワークフロー内の最初の TLS ハンドシェイクと認証の一部として、その Azure Confidential Ledger エンクレーブに対する信頼度を高めるのに役立ちます。 その他に、ユーザーのクライアントと Confidential Ledger の間には永続的なクライアント接続が維持されます。

- **Confidential Ledgerノードの検証**: これは、Microsoft によってホストされている ID サービスに対してクエリを実行することで実現されます。これにより、ネットワーク証明書が提供され、台帳ノードが、その特定のインスタンスのネットワーク証明書によって保証または署名された証明書を提示しているのを確認できます。 PKI ベースの HTTPS と同様に、サーバーの証明書は、既知の証明機関 (CA) または中間 CA によって署名されます。 Azure Confidential Ledger の場合、CA 証明書は、ネットワーク証明書の形式で ID サービスによって返されます。これは、Confidential Ledger のユーザーにとって重要な信頼度の構築手段です。 返されたネットワーク証明書によってこのノード証明書が署名されていない場合、クライアント接続は失敗します (サンプル コードに実装されています)。
- **Confidential Ledger エンクレーブの検証**: Confidential Ledger は、そのエンクレーブ内で生成されたデータ BLOB である Quote で表される Intel® SGX エンクレーブで実行されます。 これは、他のエンティティが、Intel® SGX 保護を使用して実行されているアプリケーションからその見積もりが生成されたことを確認するために使用できます。 見積もりは、簡単に確認できるように構造化されています。 これには、エンクレーブとそれが実行されているアプリケーションのさまざまなプロパティを識別するためのクレームが含まれています。 これは、Confidential Ledger ユーザーにとって重要な信頼度構築メカニズムです。 これは、関数ワークフロー API を呼び出してエンクレーブ見積もりを取得することによって実現できます。 見積もりが無効であるとクライアント接続は失敗します。 取得した見積もりは、open_enclaves Host_Verify ツールで検証できます。 この詳細については、こちらをご覧ください。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)
- [Azure Confidential Ledger のアーキテクチャ](architecture.md)
