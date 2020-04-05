---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "76887909"
---
## <a name="locate-a-cloud-spatial-anchor"></a>クラウド空間アンカーを見つける

Azure Spatial Anchors を使用する主な理由の 1 つに、以前に保存したクラウド空間アンカーを検索できることが挙げられます。 クラウド空間アンカーは、さまざまな方法で検索できます。 Watcher では一度に 1 つの方法を使用することができます。
- ID でアンカーを検索します。
- 以前検索したアンカーに接続されているアンカーを検索します。 アンカーの関係については、[こちら](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)をご覧ください。
- [粗い再局在化](/azure/spatial-anchors/concepts/coarse-reloc/)を使用してアンカーを検索します。

クラウド空間アンカーを ID で検索する場合、そのクラウド空間アンカー ID をアプリケーションのバックエンド サービスに格納しておけば、そのバックエンド サービスに対して適切に認証を行うことができるあらゆるデバイスからアクセスさせることができます。 この例のついては、「[チュートリアル: デバイス間で空間アンカーを共有する](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)」をご覧ください。

`AnchorLocateCriteria` オブジェクトをインスタンス化し、検索する識別子を設定します。次に `AnchorLocateCriteria` を指定することでセッション上の `CreateWatcher` メソッドを呼び出します。