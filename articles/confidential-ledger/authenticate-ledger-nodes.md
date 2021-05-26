---
title: Azure Confidential Ledger ノードの認証
description: Azure Confidential Ledger ノードの認証
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 0a8f033aa540d2a469141c4c529981a0cbeef3ec
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110386654"
---
# <a name="authenticating-azure-confidential-ledger-nodes"></a>Azure Confidential Ledger ノードの認証

Azure Confidential Ledger ノードは、コード サンプルとユーザーによって認証できます。

## <a name="code-samples"></a>コード サンプル

初期化時に、コード サンプルでは、ID Service にクエリを実行してノード証明書を取得します。 ノード証明書を取得した後、コード サンプルは台帳ネットワークにクエリを実行して見積もりを取得し、ホスト検証バイナリを使用して検証します。 検証が成功すると、コード サンプルは台帳操作に進みます。

## <a name="users"></a>ユーザー

ユーザーは、Confidential Ledger ノードの信頼性を検証して、実際に台帳のエンクレーブとのインターフェイスを持っている必要があります。 Confidential Ledger ノードにはいくつかの方法で信頼性を構築できます。これは、信頼の全体的なレベルを高める目的で積み重ねることができます。 そのため、手順 1 から 2 は、機能ワークフロー内の最初の TLS ハンドシェイクと認証の一部として、その Confidential Ledger エンクレーブに対する信頼度を高めるのに役立ちます。 その他に、ユーザーのクライアントと Confidential Ledger の間に永続的なクライアント接続が維持されます。

- **Confidential Ledgerノードの検証**: これは、Microsoft によってホストされている ID サービスに対してクエリを実行することで実現されます。これにより、ネットワーク証明書が提供され、台帳ノードが、その特定のインスタンスのネットワーク証明書によって保証または署名された証明書を提示しているのを確認できます。 PKI ベースの HTTPS と同様に、サーバーの証明書は、既知の証明機関 (CA) または中間 CA によって署名されます。 Confidential Ledger の場合、CA 証明書は、ネットワーク証明書の形式で ID サービスによって返されます。これは、Confidential Ledger のユーザーにとって重要な信頼度の高い構築手段です。 返されたネットワーク証明書によってこのノード証明書が署名されていない場合、クライアント接続は失敗します (サンプル コードに実装されています)。
- **Confidential Ledger エンクレーブを検証する**: Confidential Ledger は、そのエンクレーブ内で生成されたデータ BLOB である Quote で表される Intel® SGX エンクレーブで実行されます。 これは、他のエンティティが、Intel® SGX 保護を使用して実行されているアプリケーションからその見積もりが生成されたことを確認するために使用できます。 見積もりは、簡単に確認できるように構造化されています。 これには、エンクレーブとそれが実行されているアプリケーションのさまざまなプロパティを識別するためのクレームが含まれています。 これは、Confidential Ledger ユーザーにとって重要な信頼を構築するメカニズムです。 これは、関数ワークフロー API を呼び出してエンクレーブ見積もりを取得することによって実現できます。 見積もりが無効であるとクライアント接続は失敗します。 取得した見積もりは、open_enclaves Host_Verify ツールで検証できます。 この詳細については、こちらをご覧ください。

## <a name="next-steps"></a>次の手順

- [Microsoft Azure Confidential Ledger の概要](overview.md)
- [Azure Confidential Ledger のアーキテクチャ](architecture.md)
