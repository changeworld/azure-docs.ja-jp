---
title: "Azure Service Fabric Reliable Services のライフサイクルの概要 | Microsoft Docs"
description: "Service Fabric Reliable Services のさまざまなライフサイクル イベントに関する説明"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 6945c3333b43877a62158e2a367b1aa3dd615129
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/12/2018
---
# <a name="reliable-services-lifecycle-overview"></a>Reliable Services のライフサイクルの概要
> [!div class="op_single_selector"]
> * [Windows での C#](service-fabric-reliable-services-lifecycle.md)
> * [Linux での Java](service-fabric-reliable-services-lifecycle-java.md)
>
>

Reliable Services のライフサイクルについて考える際には、ライフサイクルの基本が最も重要です。 一般的には次のとおりです。

* スタートアップ時
  * サービスが構築される
  * サービスは 0 個以上のリスナーを構築して返すことができる
  * 返されたリスナーはすべて開かれて、サービスとの通信が許可される
  * サービスの runAsync メソッドが呼び出され、ロング ランやバックグラウンド作業が可能になる
* シャットダウン時
  * runAsync に渡されるキャンセル トークンは取り消され、リスナーが閉じられる
  * 上記の完了後、サービス オブジェクト自体が破棄される

これらのイベントの順序には、詳細な決まりがあります。 特に、イベントの順序は Reliable Services がステートレスかステートフルであるかによって若干異なる場合があります。 さらに、ステートフル サービスでは、プライマリ スワップのシナリオに対処する必要があります。 このシーケンスでは、サービスがシャットダウンすることなく、プライマリのロールが別のレプリカに転送 (または別のレプリカから復帰) されます。 最後に、エラーまたは失敗の条件を考慮する必要があります。

## <a name="stateless-service-startup"></a>ステートレス サービスのスタートアップ
ステートレス サービスのライフサイクルは、非常にわかりやすいものです。 イベントの順序は次の通りです。

1. サービスが構築されます。
2. 次に、2 つのことが並行して行われます。
    - `StatelessService.createServiceInstanceListeners()` が呼び出され、返されたリスナーはすべて開かれます (`CommunicationListener.openAsync()` が各リスナーに呼び出されます)。
    - サービスの runAsync メソッド (`StatelessService.runAsync()`) が呼び出されます。
3. 存在する場合、サービス独自の onOpenAsync が呼び出されます (具体的には、`StatelessService.onOpenAsync()` が呼び出されます。 このオーバーライドは多くは起こりませんが、利用可能なものです)。

また、リスナーを作成して開く呼び出しと runAsync の順番は調整されないことに注意してください。 runAsync が開始される前にリスナーが開くことがあります。 同様に、通信リスナーが開く前、または構築される前に runAsync が呼び出される場合もあります。 同期が必要な場合、実装者がこれを行います。 一般的な解決策は次のとおりです。

* リスナーは、別の情報が作成されるか作業が完了するまで機能しないことがあります。 ステートレス サービスでは、こうした作業は通常、サービスのコンストラクターで、`createServiceInstanceListeners()` の呼び出し時、またはリスナー自体の構築の一環として行うことができます。
* runAsync のコードをリスナーが開くまで開始しないようにする必要のある場合があります。 この場合、追加の調整が必要です。 1 つの一般的な解決策は、リスナー内にリスナーの完了時期を知らせるフラグを設定することです。このフラグは、実際の作業に進む前に runAsync でチェックされます。

## <a name="stateless-service-shutdown"></a>ステートレス サービスのシャットダウン
ステートレス サービスをシャット ダウンするときは、同じパターンが逆の順序で実行されます。

1. 次のことが並行して行われます。
    - 開いているリスナーはすべて閉じられます (各リスナーで `CommunicationListener.closeAsync()` が呼び出されます)。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます (キャンセル トークンの `isCancelled` プロパティをチェックすると true が返され、呼び出された場合、トークンの `throwIfCancellationRequested` メソッドは `CancellationException` をスローします)。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、存在する場合はサービスの `StatelessService.onCloseAsync()` メソッドが呼び出されます (先ほどと同様に、これも珍しいオーバーライドです)。
3. `StatelessService.onCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-startup"></a>ステートフル サービスのスタートアップ
ステートフル サービスのパターンはステートレス サービスに似ていますが、変更点がいくつかあります。 ステートフル サービスを開始する場合のイベントの順序は次の通りです。

1. サービスが構築されます。
2. `StatefulServiceBase.onOpenAsync()` が呼び出されます この呼び出しがサービスで上書きされることはほとんどありません。
3. 次のことが並行して行われます。
    - `StatefulServiceBase.createServiceReplicaListeners()` が呼び出されます。 
      - サービスがプライマリ サービスである場合、返されるすべてのリスナーは開かれます。 `CommunicationListener.openAsync()` が各リスナーに呼び出されます。
      - サービスがセカンダリ サービスである場合、`listenOnSecondary = true` とマークされているリスナーのみが開かれます。 開いているリスナーがセカンダリにあるのは、あまり一般的ではありません。
    - サービスが現在プライマリである場合、サービスの `StatefulServiceBase.runAsync()` メソッドが呼び出されます。
4. すべてのレプリカ リスナーの `openAsync()` 呼び出しが完了して、`runAsync()` が呼び出されると、`StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスで上書きされることはほとんどありません。

ステートレス サービスと同様に、リスナーが作成され開かれる順序と **runAsync** が呼び出されるタイミングは調整されません。 調整が必要な場合、解決策はほぼ同じです。 ステートフル サービスでは 1 つ追加のケースがあります。 通信リスナーが受信する呼び出しで、[Reliable Collection](service-fabric-reliable-services-reliable-collections.md) に保管されている情報が必要です。 Reliable Collection が読み込み可能または書き込み可能になる前、および **runAsync** が起動する前に通信リスナーが開く可能性があるため、追加の調整が必要となります。 最も簡単で一般的な解決策は、クライアントに要求の再試行を知らせるエラー コードを通信リスナーが返すことです。

## <a name="stateful-service-shutdown"></a>ステートフル サービスのシャットダウン
ステートレス サービスと同様に、シャットダウン中のライフサイクル イベントは起動時と同じですが、順序が逆です。 ステートフル サービスのシャットダウン時には、次のイベントが発生します。

1. 次のことが並行して行われます。
    - 開いているすべてのリスナーが閉じられます。 `CommunicationListener.closeAsync()` が各リスナーに呼び出されます。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `isCancelled()` メソッドへの呼び出しで true が返され、呼び出された場合、トークンの`throwIfCancellationRequested()` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、サービスの `StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスで上書きされることはほとんどありません。

   > [!NOTE]  
   > このレプリカがプライマリ レプリカの場合にのみ、**runAsync** が完了するのを待つ必要があります。

3. `StatefulServiceBase.onChangeRoleAsync()` メソッドが完了すると、`StatefulServiceBase.onCloseAsync()` メソッドが呼び出されます。 この呼び出しの上書きは一般的ではありませんが利用可能です。
3. `StatefulServiceBase.onCloseAsync()` が完了すると、サービス オブジェクトは破棄されます。

## <a name="stateful-service-primary-swaps"></a>ステートフル サービスのプライマリ スワップ
ステートフル サービスの実行中、通信リスナーを開き、**runAsync** メソッドを呼び出すのはそのステートフル サービスのプライマリ レプリカだけです。 セカンダリ レプリカは構築されますが、それ以上の呼び出しは行われません。 ステートフル サービスの実行中に、現在プライマリであるレプリカが変更される場合があります。 レプリカに生じるライフサイクル イベントの観点から見ると、これはどういうことでしょうか。 ステートフルなレプリカに起こる動作は、それがスワップ中に降格されているレプリカであるか、昇格されているものかによって変わります。

### <a name="for-the-primary-thats-demoted"></a>降格されているプライマリの場合
降格されているプライマリ レプリカの場合、Service Fabric では、このレプリカでメッセージの処理を終了して、途中のバックグラウンド作業をすべて終了する必要があります。 その結果、この手順はサービスのシャットダウン時と似たものになります。 1 つ違う点は、サービスがセカンダリのままであるために、サービスが破棄されたり閉じられたりしないということです。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - 開いているすべてのリスナーが閉じられます。 `CommunicationListener.closeAsync()` が各リスナーに呼び出されます。
    - `runAsync()` に渡されたキャンセル トークンが取り消されます。 キャンセル トークンの `isCancelled()` メソッドをチェックすると true が返され、呼び出された場合、トークンの `throwIfCancellationRequested()` メソッドは `OperationCanceledException` をスローします。
2. 各リスナーで `closeAsync()` が完了し、`runAsync()` も完了すると、サービスの `StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスで上書きされることはほとんどありません。

### <a name="for-the-secondary-thats-promoted"></a>昇格されているセカンダリの場合
同様に、Service Fabric では、昇格されているセカンダリ レプリカで送信中のメッセージのリッスンを開始して、完了する必要があるバックグラウンド タスクをすべて開始する必要があります。 その結果、レプリカ自体が既に存在している点以外、この手順はサービスの作成時と似たものになります。 次の API が呼び出されます。

1. 次のことが並行して行われます。
    - `StatefulServiceBase.createServiceReplicaListeners()` が呼び出され、返されたリスナーはすべて開かれます。 `CommunicationListener.openAsync()` が各リスナーに呼び出されます。
    - サービスの `StatefulServiceBase.runAsync()` メソッドが呼び出されます。
2. すべてのレプリカ リスナーの `openAsync()` 呼び出しが完了して、`runAsync()` が呼び出されると、`StatefulServiceBase.onChangeRoleAsync()` が呼び出されます。 この呼び出しがサービスで上書きされることはほとんどありません。


### <a name="common-issues-during-stateful-service-shutdown-and-primary-demotion"></a>ステートフル サービスのシャットダウンとプライマリの降格における一般的な問題
Service Fabric は、さまざまな理由でステートフル サービスのプライマリを変更します。 最も一般的な理由は、[クラスターの再分散](service-fabric-cluster-resource-manager-balancing.md)と[アプリケーションのアップグレード](service-fabric-application-upgrade.md)です。 これらの操作の際 (および通常のサービス シャットダウンの際にサービスが削除されたかどうかを確認する場合) には、サービスが `cancellationToken` に対応することが重要です。 

取り消しを適切に処理しないサービスでは、いくつかの問題が発生する可能性があります。 サービスが正常に停止するのを Service Fabric が待機するため、それらの操作の速度が遅くなります。 最終的には、アップグレードがタイムアウトしてロールバックし、アップグレードに失敗することにつながります。 キャンセル トークンに対応しないことにより、クラスターが不均衡になることもあります。 クラスターが不均衡になる原因は、ノードの負荷が高くなってもサービスの移動に時間がかかるためサービスを分散できないためです。 

サービスがステートフルなため、サービスが [Reliable Collection](service-fabric-reliable-services-reliable-collections.md) を使用する可能性も高くなります。 Service Fabric では、プライマリが降格されたときに最初に発生することの 1 つは、基になる状態への書き込みアクセスの取り消しです。 これは、サービスのライフサイクルに影響を与える第 2 の一連の問題につながります。 レプリカが移動中なのかあるいはシャットダウンされているのかと、そのタイミングに基づいて、コレクションが例外を返します。 これらの例外を正しく処理する必要があります。 Service Fabric によってスローされる例外は、永続的なカテゴリ [(`FabricException`)](https://docs.microsoft.com/java/api/system.fabric.exception) と一時的なカテゴリ [(`FabricTransientException`)](https://docs.microsoft.com/java/api/system.fabric.exception._fabric_transient_exception) に分類されます。 永続的な例外はログに記録してスローするべきですが、一時的な例外は再試行ロジックに基づいて再試行することもできます。

`ReliableCollections` とサービスのライフサイクル イベントを組み合わせて使用した結果の例外を処理することは、Reliable Service のテストと検証の重要な部分です。 運用環境にデプロイする前にアップグレードと[カオス テスト](service-fabric-controlled-chaos.md)を実行するときは、サービスに常に負荷がかかっている状態で行うことをお勧めします。 このような基本の手順を行うことで、サービスが正しく実装され、ライフサイクル イベントを正しく処理できるようになります。

## <a name="notes-on-service-lifecycle"></a>サービスのライフサイクルに関するメモ
* `runAsync()` メソッドおよび `createServiceInstanceListeners/createServiceReplicaListeners` の呼び出しは両方とも省略可能です。 サービスには、これらのいずれかまたは両方を含めることも、どちらも含めないこともできます。 例えば、サービスがユーザーの呼び出しに応答してすべての処理を実行する場合、`runAsync()` を実装する必要はありません。 通信リスナーおよびそれに関連付けられたコードのみが必要です。 同様に、サービスにはバックグラウンドの作業しかない場合があり、この場合 `runAsync()` の実装のみが必要なため、通信リスナーを作成して返す必要はありません。
* サービスは、正常に `runAsync()` を完了して戻ることができます。 これは失敗条件とは見なされず、完了中のサービスのバックグラウンド作業に相当します。 ステートフル リライアブル サービスで、サービスがプライマリから一度降格して再度プライマリに昇格した場合、`runAsync()` が再度呼び出されます。
* サービスが何らかの予期しない例外をスローして `runAsync()` を終了した場合、障害が起きたことになります。その際、サービス オブジェクトがシャットダウンされ、正常性エラー報告がされます。
* これらのメソッドから制御が戻るときに時間制限はありませんが、書き込みはすぐにできなくなるため、実際の処理を完了できません。 取り消し要求を受信したらできるだけ短時間で制御が戻るようにすることをお勧めします。 サービスが適切な時間内にこれらの API 呼び出しに応答しない場合、Service Fabric はサービスを強制的に終了する場合があります。 通常、これはアプリケーションのアップグレード中か、サービスの削除中のみに発生します。 このタイムアウト時間は既定で 15 分です。
* `onCloseAsync()` パスで障害が起きると、`onAbort()` が呼び出されます。これは、サービスが要求したリソースすべてをクリーンアップし解放するための最後のベストエフォートの機会になります。

## <a name="next-steps"></a>次の手順
* [Reliable Services 入門](service-fabric-reliable-services-introduction.md)
* [Reliable Service の概要](service-fabric-reliable-services-quick-start-java.md)
* [Reliable Services の詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)
