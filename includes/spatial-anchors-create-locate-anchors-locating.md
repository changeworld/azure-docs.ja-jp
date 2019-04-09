---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907740"
---
## <a name="locate-a-cloud-spatial-anchor"></a>クラウド空間アンカーを見つける

クラウド空間アンカーを見つけるには、識別子を知っておく必要があります。 アンカー ID はアプリケーションのバックエンド サービスに格納でき、それを適切に認証できるすべてのデバイスにアクセスできます。 この例のついては、「[チュートリアル:デバイス間で空間アンカーを共有する](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)」をご覧ください。

AnchorLocateCriteria オブジェクトをインスタンス化し、検索する識別子を設定します。次に AnchorLocateCriteria を指定することでセッション上の CreateWatcher メソッドを呼び出します。
