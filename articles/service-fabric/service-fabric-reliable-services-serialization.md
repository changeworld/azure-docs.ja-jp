<properties
   pageTitle="Reliable Service のシリアル化 |Microsoft Azure"
   description="Service Fabric Reliable Service のシリアル化の概念をまとめた文書"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson,tyadam"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="11/18/2015"
   ms.author="mcoskun"/>

# Reliable Service でのシリアル化
Reliable State Manager は、複数の Reliable Object を保持できます。これらの Reliable Object の中には、既製の Reliable Dictionary や Reliable Queue のような汎用データ構造にできるものもあります。これらは信頼性の高い汎用データ構造であるため、これらをレプリケートしてディスクに保持できるようにするには、これらが持つ汎用オブジェクトをシリアル化する必要があります。

Reliable State Manager は、* カスタム シリアライザー、* タイプの数が限定された組み込みのシリアライザー、* DataContractSerilizer の 3 種類のシリアライザーをサポートしています。

Reliable Object は、オブジェクトをシリアル化する必要がある場合に、所定のタイプのシリアライザーを Reliable State Manager に照会します。Reliable State Manager は、最初に入力タイプに対して登録されているカスタムのシリアライザーがあるかどうかをチェックします。ない場合は、いずれかの組み込みのシリアライザーでそのタイプをシリアル化できるかどうかをチェックします。Reliable State Manager には、次のタイプの組み込みのシリアライザーがあります。guid、bool、byte、sbyte、char、decimal、float、int、uint、double、long、ulong、short、ushort、string。できない場合は、DataContractSerializer を返します。

この記事は、カスタムのシリアル化を使用するタイミングと方法について説明します。

## カスタムのシリアル化を使用する場合
カスタムのシリアル化を使用する一般的な理由には、* パフォーマンスと * 暗号化の 2 つがあります。

組み込みのシリアライザーが対応していないタイプには、DataContractSerializer の代わりにカスタム シリアライザーを使用することで、大幅なパフォーマンスの向上が実現できます。その理由は、カスタム シリアライザーがタイプ情報をシリアル化する必要がないからです。Service Fabric は、指定されたタイプの状態シリアライザーだけが、そのタイプのシリアル化と逆シリアル化を行うことを保証します。

シリアライザーによって生成されたシリアル化データは、レプリケートされ、ディスク上にそのまま保持されます。機密情報のデータについては、ネットワーク上およびディスク上でファイルが暗号化されていることを確認することをお勧めします。

## カスタムのシリアル化の使用方法
所定のタイプにカスタム シリアライザーを使用するには、* カスタムの状態シリアライザーを構築し、* そのカスタムの状態シリアライザーを Reliable Service に登録する必要があります。

### カスタム シリアライザーの実装方法
カスタム シリアライザーは、IStateSerializer<T> インターフェイスを実装する必要があります。このインターフェイスの 2 つの主要なメソッドは、* T Read(BinaryReader binaryReader) と * void Write(T value, BinaryWriter binaryWriter) です。

最初のメソッドは、BinaryReader を使用してストリームからシリアル化されたオブジェクトを読み取るために ReliableObject によって使用されます。2 番目のメソッドは、逆の操作、つまり BinaryWriter を使用してオブジェクトをストリームに書き込むために使用されます。

サンプルのカスタム クラスとそのシリアライザーを以下に示します。

```C#
public class OrderKey : IComparable<OrderKey>, IEquatable<OrderKey>
{
    public byte Warehouse { get; set; }
    public short District { get; set; }
    public int Customer { get; set; }
    public long Order { get; set; }
```

```C#
public class OrderKeySerializer : IStateSerializer<OrderKey>
{
    void IStateSerializer<OrderKey>.Write(OrderKey value, BinaryWriter writer)
    {
        writer.Write(value.Warehouse);
        writer.Write(value.District);
        writer.Write(value.Customer);
        writer.Write(value.Order);
    }

    OrderKey IStateSerializer<OrderKey>.Read(BinaryReader reader)
    {
        OrderKey value = new OrderKey();
        value.Warehouse = reader.ReadByte();
        value.District = reader.ReadInt16();
        value.Customer = reader.ReadInt32();
        value.Order = reader.ReadInt64();

        return value;
    }

    void IStateSerializer<OrderKey>.Write(OrderKey currentValue, OrderKey newValue, BinaryWriter writer)
    {
        ((IStateSerializer<OrderKey>)this).Write(newValue, writer);
    }

    OrderKey IStateSerializer<OrderKey>.Read(OrderKey baseValue, BinaryReader reader)
    {
        return ((IStateSerializer<OrderKey>)this).Read(reader);
    }
}
```
>[AZURE.NOTE]上記のサンプルでは、読み取りと書き込みのオーバーロードの実装だけで、対応するオーバーロードを呼び出します。これは、次の 2 つのメソッドが、まだ利用できない機能のために使用されるからです。

### カスタム シリアライザーの登録方法
カスタム シリアライザーを登録するには、最初にすべてのカスタム シリアライザーを登録できるメソッドが必要です。このメソッドでは引数を取る必要がなく、タスクを返すことができます。このメソッドでは、IReliableStateManager.TryAddStateSerializer<T> を使用してすべてのカスタム シリアライザーを Reliable Service に登録する必要があります。

```C#
protected Task InitializeStateSerializers()
{
    this.StateManager.TryAddStateSerializer(new OrderKeySerializer());
    return Task.FromResult(false);
}
```

次の手順では、すべてのカスタム状態シリアライザーを登録するときに、Reliable State Manager によって呼び出されるデリゲートとして上記のメソッドを登録します。このメソッドは、シリアル化されたデータをディスクから読み取るためにシリアライザーが必要になる場合があるため、ローカル回復を開始する前の、Reliable Service レプリカの開始時にのみ呼び出されます。シリアライザーが登録されると、すべての Reliable Object の関連するタイプがこのシリアライザーを使用して、オブジェクトのシリアル化と逆シリアル化を行います。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(
        new ReliableStateManagerConfiguration(
            onInitializeStateSerializersEvent : this.InitializeStateSerializers));
}
```
### バージョン管理
Service Fabric では、シリアライザーに制限のない上位互換性と下位互換性があることを想定しています。組み込みのシリアライザーを使用しているタイプの場合、Service Fabric が上位互換性と下位互換性を保証します。DataContractSerializer またはカスタム シリアライザーを使用しているタイプの場合、ユーザーが互換性を破る変更を行わない必要があります。DataContract のバージョン管理の詳細は、「[データ コントラクトのバージョン管理](https://msdn.microsoft.com/library/ms731138.aspx)」を参照してください。互換性を破る変更が必要な場合、アプリケーション レベルで状態を古いデータ バージョンのサービス インスタンスから新しいデータ バージョンのサービスに移行する必要があります。

### シリアライザーを使用する場合
 * Reliable Object での書き込み操作では、シリアル化、レプリケート、およびログへの格納が発生します。
 * 操作が十分にログに記録されると、メモリから最新のデータがシリアル化され、ディスクにチェックポイントが設定されます。
 * レプリカを再構築するため、ディスクにチェックポイントが設定されたファイルとログからの最新データが、プライマリ レプリカから直接バイト単位で送信されます (つまり、データは再シリアル化されていない)。これらのバイトは、セカンダリ レプリカで C# オブジェクトに逆シリアル化されます。
 * 回復時に、チェックポイント ファイルと最新のデータのログが逆シリアル化されます。
 * バックアップ時に、チェックポイント ファイルと最新のデータのログがバイト単位でコピーされます。
 * 復元時に、以前にバックアップしたチェックポイント ファイルとログ データが元の場所にコピーされ、逆シリアル化されます。

## 次のステップ
 * [Reliable Service プログラミング モデルの詳細な使用方法](service-fabric-reliable-services-advanced-usage.md)

<!---HONumber=AcomDC_1203_2015-->