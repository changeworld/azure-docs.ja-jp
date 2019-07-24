---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67180888"
---
## <a name="update-properties"></a>プロパティを更新する

アンカーでプロパティを更新するには、`UpdateAnchorProperties()` メソッドを使用します。 2 つ以上のデバイスが同じアンカーのプロパティを同時に更新する場合は、オプティミスティック同時実行制御モデルを使用します。 これは、最初の書き込みが優先されることを意味します。  その他のすべての書き込みには、「同時実行」エラーが発生します。再試行する前に、プロパティの更新が必要になります。
