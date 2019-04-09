---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57907679"
---
## <a name="update-properties"></a>プロパティを更新する

アンカーでプロパティを更新するには、UpdateAnchorProperties メソッドを使用します。 2 つ以上のデバイスが同じアンカーのプロパティを同時に更新する場合は、オプティミスティック同時実行制御モデルを使用します。 これは、最初の書き込みが優先されることを意味します。  その他のすべての書き込みには、「同時実行」エラーが発生します。再試行する前に、プロパティの更新が必要になります。
