<properties
   pageTitle="Reliable Actors のタイマーとアラーム | Microsoft Azure"
   description="Service Fabric Reliable Actors のタイマーとアラームの概要"
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="amanbha"/>


# アクターのタイマー
アクターのタイマーは、アクターのランタイムが提供するターンごとの同時実行の保証をコールバック メソッドが考慮するように、.NET タイマーの単純なラッパーを提供します。

アクターは、`RegisterTimer` と `UnregisterTimer` のメソッドをその基本クラスに使用して、タイマーを登録/登録解除できます。次の例はタイマー API の使用を示します。API は、.NET タイマーによく似ています。この例では、タイマーが期限を迎えると、アクターのランタイムが `MoveObject` メソッドを呼び出します。このメソッドは、ターンごとの同時実行を優先することを保証します。つまり、このコールバックの実行が完了するまで、他のアクター メソッドやタイマーとアラームのコールバックは進行しません。

```csharp
class VisualObjectActor : StatefulActor<VisualObject>, IVisualObject
{
    private IActorTimer _updateTimer;

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            state,                          // State to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```

タイマーの次の期間は、コールバックが実行を完了した後に開始します。これは、コールバックを実行している間はタイマーが停止し、コールバックが完了したときにタイマーが開始することを意味します。

アクターのランタイムは、アクターが上記の例のようにステートフルなアクターの場合、コールバックが完了したときのアクターの状態を保存します。状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。アクターの状態を変更しないコールバック メソッドは、タイマーのコールバックで読み取り専用の属性を指定することで、読み取り専用のタイマーのコールバックとして登録することができます。このことは、「[読み取り専用メソッド](service-fabric-reliable-actors-introduction.md#readonly-methods)」のセクションで説明されています。

ガベージ コレクションの一部としてアクターが非アクティブ化されると、すべてのタイマーが停止します。その後は、タイマーのコールバックは呼び出されません。また、アクターのランタイムは、非アクティブ化の前に実行されていたタイマーについての情報は保持しません。その後、再アクティブ化したときに必要なタイマーを登録するかどうかはアクターによって異なります。詳細については、「[アクターのガベージ コレクション](service-fabric-reliable-actors-lifecycle.md)」のセクションを参照してください。

## アクターのアラーム
アラームは、指定した時間にアクターに永続的なコールバックをトリガーするメカニズムです。タイマーと似ていますが、アラームの場合は、アクターが明示的に登録を解除するまであらゆる状況下でトリガーされます。具体的には、アラームはアクターの無効化およびフェールオーバーによりトリガーされます。これは、アクターのランタイムがアクターのアラームに関する情報を永続化するためです。

アラームは、ステートフル アクターのみでサポートされます。ステートレス アクターは、アラームを使用できません。アクターの状態プロバイダーは、アクターが登録したアラームに関する情報を保存する役割を担います。

アラームを登録するため、アクターは次の例に示すように、基本クラスで提供されている `RegisterReminder` メソッドを呼び出します。

```csharp
string task = "Pay cell phone bill";
int amountInDollars = 100;
Task<IActorReminder> reminderRegistration = RegisterReminder(
                                                task,
                                                BitConverter.GetBytes(amountInDollars),
                                                TimeSpan.FromDays(3),
                                                TimeSpan.FromDays(1),
                                                ActorReminderAttributes.None);
```

上記の例で、`"Pay cell phone bill"` はアラーム名であり、アクターがアラームを一意に識別するために使用する文字列です。`BitConverter.GetBytes(amountInDollars)` は、そのアラームに関連付けられているコンテキストです。これは、アラームのコールバックの引数、つまり `IRemindable.ReceiveReminderAsync` としてアクターに戻ります。

アラームを使用するアクターは、次の例に示すように `IRemindable` インターフェイスを実装する必要があります。

```csharp
public class ToDoListActor : StatefulActor<ToDoList>, IToDoListActor, IRemindable
{
    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```

アラームがトリガーされると、ファブリックのアクターのランタイムがアクターの `ReceiveReminderAsync` メソッドを呼び出します。アクターは複数のアラームを登録でき、`ReceiveReminderAsync` メソッドは、そのいずれかのアラームがトリガーされると呼び出されます。アクターは `ReceiveReminderAsync` メソッドに渡されるアラーム名を使用して、どのアラームがトリガーされたかを判別できます。

アクターのランタイムは、`ReceiveReminderAsync` の呼び出しが完了するとアクターの状態を保存します。状態の保存中にエラーが発生した場合、そのアクターのオブジェクトは非アクティブ化し、新しいインスタンスがアクティブ化されます。アラームのコールバックの完了時に状態を保存する必要がないことを指定するため、 `RegisterReminder` メソッドを呼び出してアラームを作成するときに、 `attributes` パラメーターに `ActorReminderAttributes.ReadOnly` フラグを設定することができます。

アラームの登録を解除するには、次の例に示すように、アクターが `UnregisterReminder` メソッドを呼び出す必要があります。

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminder(reminder);
```

上記の例のように、 `UnregisterReminder` メソッドは、`IActorReminder` インターフェイスを受け入れます。アクターの基本クラスは、アラーム名で渡すことで、`IActorReminder` インターフェイスを取得できる `GetReminder` メソッドをサポートします。これにより、アクターが `RegisterReminder` メソッドの呼び出しから返された `IActorReminder` インターフェイスを永続化する必要がなくなるため便利です。

<!---HONumber=AcomDC_0121_2016-->