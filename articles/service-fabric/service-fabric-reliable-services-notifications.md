<properties
   pageTitle="Reliable Services の通知 | Microsoft Azure"
   description="Service Fabric の Reliable Services の通知の概念をまとめたドキュメント"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Reliable Services の通知

通知により、クライアントは対象となるオブジェクトの変更を追跡できます。通知をサポートするオブジェクトには、**Reliable State Manager** と **Reliable Dictionary** の 2 種類があります。

通知を使用する一般的な理由は次のとおりです。

- セカンダリ インデックスなどの具体化されたビューや、レプリカの状態の集計フィルター ビューを構築するため。たとえば、Reliable Dictionary のすべてのキーの並べ替えられたインデックスなどがあります。
- 監視データ (過去 1 時間に追加されたユーザーの数など) を送信するため。

通知は操作の適用の一環として発生します。操作の適用の一環であるため、通知の処理をできるだけ速やかに完了し、負荷のかかる操作が同期イベントで実行されないように注意する必要があります。

## Reliable State Manager の通知

Reliable State Manager は、次のイベントの通知を提供します。

- トランザクション
    - コミット
- 状態マネージャー
    - 再構築
    - Reliable State の追加
    - Reliable State の削除

Reliable State Manager は、現在処理中のトランザクションを追跡します。通知を発生させるトランザクションの状態の変化は、トランザクションのコミットだけです。

Reliable State Manager は、Reliable Dictionary や Reliable Queue などの Reliable State のコレクションを保持します。このコレクションが変更されると (Reliable State が追加または削除されたときや、コレクション全体が再構築されたとき)、Reliable State Manager は通知を発生させます。Reliable State Manager のコレクションは、次の 3 つの状況で再構築されます。

- 回復: レプリカは、起動時にディスクから以前の状態を回復します。回復の最後に、回復された **IReliableState** のセットを含む **NotifyStateManagerChangedEventArgs** でイベントを発生させます。
- 完全コピー: レプリカが構成セットに参加できるようにするには、レプリカを構築しておく必要があります。場合によっては、プライマリ レプリカの Reliable State Manager の状態の完全コピーを、アイドル状態のセカンダリ レプリカに適用する必要があります。セカンダリの Reliable State Manager は、プライマリから取得した **IReliableState** のセットを含む **NotifyStateManagerChangedEventArgs** でイベントを発生させます。
- 復元: 障害復旧シナリオでは、**RestoreAsync** を使用して、レプリカの状態をバックアップから復元できます。この場合、プライマリの Reliable State Manager は、バックアップから復元した **IReliableState** のセットを含む **NotifyStateManagerChangedEventArgs** でイベントを発生させます。

### Reliable State Manager の通知の使用方法
トランザクション通知または State Manager 通知を登録するには、ユーザーが Reliable State Manager でそれぞれ **TransactionChanged** イベントまたは **StateManagerChanged** イベントに登録する必要があります。これらのイベント ハンドラーに登録する一般的な場所は、StatefulService のコンストラクターです。これは、コンストラクターで登録することによって、**IReliableStateManager** の有効期間中に変更によって発生する通知をクライアントが見落とすことがなくなるためです。

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

**TransactionChanged** イベント ハンドラーでは、**NotifyTransactionChangedEventArgs** を使用してイベントの詳細を提供します。これには、変更の種類を示す Action プロパティ (例: **NotifyTransactionChangedAction.Commit**) と、変更されたトランザクションへの参照を提供する Transaction プロパティが含まれます。

>[AZURE.NOTE] 現在、TransactionChanged イベントはトランザクションがコミットされた場合にのみ発生するため、Action は NotifyTransactionChangedAction.Commit になります。ただし、今後の更新でさらに多くの Action が追加される可能性があります。そのため、Action を確認し、Action が予想していたものである場合にのみ、そのイベントを処理することをお勧めします。

**TransactionChanged** イベント ハンドラーの例を次に示します。

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

**StateManagerChanged** イベント ハンドラーでは、**NotifyStateManagerChangedEventArgs** を使用してイベントの詳細を提供します。**NotifyStateManagerChangedEventArgs** には、NotifyStateManagerRebuildEventArgs と NotifyStateManagerSingleEntityChangedEventArgs の 2 つのサブクラスがあります。**NotifyStateManagerChangedEventArgs** の Action プロパティを使用して、**NotifyStateManagerChangedEventArgs** を適切なサブクラスにキャストする必要があります。

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs
- NotifyStateManagerChangedAction.Add および Remove: NotifyStateManagerSingleEntityChangedEventArgs

**StateManagerChanged** 通知ハンドラーの例を次に示します。

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Reliable Dictionary の通知

Reliable Dictionary は、次のイベントの通知を提供します。

- Rebuild: **ReliableDictionary** がローカル ディスクから状態を回復したときや、プライマリまたはバックアップから状態をコピーしたときに呼び出されます。
- Clear: **ClearAsync** メソッドを使用して、**ReliableDictionary** の状態がクリアされたときに呼び出されます。
- Add: **ReliableDictionary** に項目が追加されたときに呼び出されます。
- Update: **IReliableDictionary** の項目が更新されたときに呼び出されます。
- Remove: **IReliableDictionary** の項目が削除されたときに呼び出されます。

### Reliable Dictionary の通知の使用方法
Reliable Dictionary の通知を登録するには、ユーザーが **IReliableDictionary** で **DictionaryChanaged** イベント ハンドラーに登録する必要があります。これらのイベント ハンドラーに登録する一般的な場所は、**ReliableStateManager.StateManagerChanged** の追加通知内です。これは、**IReliableDictionary** を **IReliableStateManager** に追加したときに登録すると、通知を見落とすことがなくなるためです。

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification は、前の OnStateManagerChangedHandler の例で呼び出されたサンプル メソッドです。

上記のコードでは、IReliableNotificationAsyncCallback と **DictionaryChanged** を設定しています。**NotifyDictionaryRebuildEventArgs** には、非同期で列挙する必要がある **IAsyncEnumerable** が含まれているので、再構築通知は **OnDictionaryChangedHandler** ではなく、**RebuildNotificationAsyncCallback** によって発生します。

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] 上記のコードでは、再構築通知の処理の一環として、まず、保持されている集計された状態がクリアされます。これは、Reliable Collection は新しい状態で再構築されるので、以前の通知はすべて無関係になるためです。

**DictionaryChanged** イベント ハンドラーでは、**NotifyDictionaryChangedEventArgs** を使用してイベントの詳細を提供します。**NotifyDictionaryChangedEventArgs** には 5 つのサブクラスがあります。**NotifyDictionaryChangedEventArgs** の Action プロパティを使用して、**NotifyDictionaryChangedEventArgs** を適切なサブクラスにキャストする必要があります。

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add および Remove: NotifyDictionaryItemAddedEventArgs
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## 推奨事項

- 通知イベントはできるだけ速やかに完了します。
- 同期イベントの一部として、負荷のかかる操作 (IO 操作など) を実行しないでください。
- イベントを処理する前に、Action の種類を確認します。今後、新しい Action の種類が追加される可能性があります。

次の点に注意してください。
- 通知は操作の実行の一環として発生します。たとえば、復元通知は復元の最後の手順として発生するので、通知イベントが処理されるまで復元は完了しません。
- 通知は操作の適用の一環として発生するので、クライアントにはローカルでコミットされた操作の通知だけが表示されます。操作はローカルでコミットされる (ログに記録される) ことだけが保証されるので、後で元に戻される場合もあれば、戻されない場合もあります。
- やり直しパスでは、適用された操作ごとに 1 つの通知が発生します。つまり、トランザクション T1 に Create(X)、Delete(X)、Create(X) が含まれている場合、X の作成の通知、削除の通知、再度作成の通知をこの順序で受け取ることになります。
- トランザクションに複数の操作が含まれている場合、操作はプライマリでユーザーから受信した順序で適用されます。
- 誤った進行の処理の一環として、一部の操作が元に戻されることがあります。レプリカの状態を安定したポイントにロールバックするこのような元に戻す操作では、通知が発生します。元に戻す通知の重要な違いの 1 つは、重複するキーを持つイベントが集約されることです。たとえば、前述の T1 を元に戻すと、ユーザーには Delete(X) の通知だけが表示されます。

## 次のステップ

- [Reliable Service の概要](service-fabric-reliable-services-quick-start.md)
- [Reliable Service のバックアップと復元 (障害復旧)](service-fabric-reliable-services-backup-restore.md)
- [Reliable Collection の開発者向けリファレンス](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->