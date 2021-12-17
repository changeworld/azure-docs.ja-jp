---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128910467"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

Android SDK を利用すると、値が変更されたときに通知を受け取るように、ほとんどのプロパティとコレクションをサブスクライブできます。

## <a name="properties"></a>Properties
`property changed` イベントをサブスクライブするには:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
同じクラス内で定義されているイベント リスナーを使用する場合は、リスナーを変数にバインドします。 リスナーのメソッドを追加および削除するには、この変数を引数として渡します。

リスナーを引数として直接渡すと、そのリスナーへの参照が失われる可能性があります。 Java ではこれらのリスナーの新しいインスタンスが作成され、以前に作成されたリスナーは参照されません。 それらは引き続き正しく動作しますが、参照がなくなったために削除できません。


## <a name="collections"></a>コレクション
`collection updated` イベントをサブスクライブするには:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
