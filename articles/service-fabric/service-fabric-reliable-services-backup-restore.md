<properties
   pageTitle="Reliable Service のバックアップと復元 | Microsoft Azure"
   description="Service Fabric Reliable Service のバックアップと復元の概念をまとめた文書"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/25/2016"
   ms.author="mcoskun"/>

# Reliable Services のバックアップと復元

Azure Service Fabric は高可用性プラットフォームであり、複数のノードに状態を複製し、その高可用性を維持します。つまり、クラスター内の 1 つのノードに障害が発生した場合でも、サービスは引き続き利用できます。このプラットフォームに組み込まれている冗長性で十分と考えられますが、(外部ストアに) サービスのデータをバックアップすることが望ましい場合もあります。

たとえば、次のシナリオではサービスのデータをバックアップすることが望まれます。

* Service Fabric クラスター全体または特定のパーティションを実行しているすべてのノードが永久に失われた場合。これは、状態が geo レプリケーションされない場合、クラスター全体が 1 か所のデータ センターにある場合、データ センター全体が停止した場合などに起こる可能性があります。

* 状態が誤って削除されたり、破損したりするなどの管理エラー。これは、十分な権限を持つ管理者が誤ってサービスを削除した場合に起こる可能性があります。

* データの破損を引き起こすサービスのバグ。これは、サービス コードのアップグレードにより、Reliable Collection に不正なデータが書き込まれ始めると起こる可能性があります。このような場合は、コードとデータの両方を、以前の状態に復元する必要があります。

* オフライン データ処理データを生成するサービスと切り離されて行われるビジネス インテリジェンスのために、データのオフライン処理を用意しておけば便利です。

バックアップ/復元機能では、 Reliable Services API を基盤とするサービスでバックアップを作成し、復元できます。このプラットフォームが提供するバックアップ API では、読み書き操作をブロックせずにパーティションの状態をバックアップできます。復元 API では、選択したバックアップからパーティションのステートを復元できます。


## バックアップ操作

サービスの作成者は、バックアップのタイミングと保管場所を完全に制御できます。

バックアップを開始するには、サービスで **IReliableStateManager.BackupAsync** を呼び出す必要があります。バックアップはプライマリ レプリカ以外からは実行できず、書き込みステータスが付与されている必要があります。

下の図のように、**BackupAsync** の最も単純なオーバーロードはFunc<< BackupInfo  bool >> の形式を取ります。これは「**backupCallback**」と呼ばれています。

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** は、ランタイムがバックアップを保存したフォルダー (**BackupInfo.Directory**) の場所など、バックアップに関する情報を提供します。このコールバック関数は **BackupInfo.Directory** が外部ストアまたは別の場所に移動することを要求します。この関数はまた、バックアップ フォルダーがそのターゲット フォルダーに移動したかどうかを示すブール値を返します。

次のコードは、backupCallback を利用し、バックアップを Azure Storage にアップロードするしくみを示しています。

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

上の例では、**ExternalBackupStore** は Azure BLOB Storage とのインターフェイスとして使用されるサンプル クラスであり、**UploadBackupFolderAsync** はフォルダーを圧縮し、Azure BLOB ストアに配置するメソッドです。

以下の点に注意してください。

- 特定の時点で配信できる **BackupAsync** はレプリカあたり 1 つに限られます。一度に複数の **BackupAsync** 呼び出しを行うと、**FabricBackupInProgressException** がスローされ、配信バックアップが 1 つに制限されます。

- バックアップの途中でレプリカにエラーが発生した場合、バックアップは完了しないことがあります。そのため、フェールオーバーが完了すると、サービスは必要に応じて **BackupAsync** を呼び出し、バックアップを再開する必要があります。

## 復元操作

通常、復元操作が必要になるケースは、次のカテゴリのいずれかに分類されます。


- サービス パーティションのデータが失われた場合。たとえば、パーティションの 3 つのレプリカのうちの 2 つのディスク (プライマリ レプリカを含む) が破損または消去された場合です。新しいプライマリに、バックアップからデータを復元する必要がある場合。

- サービス全体が失われた場合。たとえば、管理者がサービス全体を削除したため、サービスとデータを復元する必要がある場合です。

- サービスが、(たとえば、アプリケーションのバグに起因する) 破損したアプリケーション データを複製した場合。その場合、サービスをアップグレードするか、元の状態に戻し、破損の原因を取り除き、破損していないデータを復元する必要があります。

さまざまな方法が可能ですが、ここでは **RestoreAsync** で上記のシナリオから復元する例を紹介します。

## パーティション データの損失

この場合、ランタイムが自動的にデータ損失を検出し、**OnDataLossAsync** API を呼び出します。

サービス作成者は、次を実行して回復する必要があります。

- **CreateReliableStateManager** をオーバーライドして新しい **ReliableStateManager** を返し、データ損失イベントが発生した場合に呼び出されるコールバック関数を提供します。

- サービスのバックアップを含む外部の場所で最新のバックアップを見つけます。

- 最新のバックアップの状態が新しいプライマリの背後にある場合は、false を返します。これにより、新しいプライマリが古いデータで上書きされなくなります。

- 最新のバックアップをダウンロードします (およびそれが圧縮されている場合は、バックアップ フォルダーにバックアップを解凍します)。

- バックアップ フォルダーのパスで **IReliableStateManager.RestoreAsync** を呼び出します。

- 復元が成功した場合は、true を返します。

次の例では、**IReliableStateManager** のオーバーライドと共に **OnDataLossAsync** メソッドを実装しています。

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] RestorePolicy は既定で「Safe」に設定されます。つまり、バックアップ フォルダーにこのレプリカに含まれている状態と同じか、それより古い状態が含まれていることが検出された場合、**RestoreAsync** API は失敗し、ArgumentException をスローします。**RestorePolicy.Force** を使用して、この安全性チェックを省略できます。

## サービスの削除または損失

サービスが削除された場合、先にサービスを作成し直さないとデータを復元できません。データを途切れなく復元するために、サービスを同じ構成 (パーティショニング スキームなど) で作成することが重要です。サービスが起動したら、そのサービスのあらゆるパーティションで、データを復元する API (上記の **OnDataLossAsync**) を呼び出す必要があります。その方法の 1 つは、すべてのパーティションで **FabricClient.ServiceManager.InvokeDataLossAsync** を使用することです。

この点から、実装は上記のシナリオと同じになります。各パーティションで、外部ストアから最新の関連バックアップを復元する必要があります。ここで注意する点は、ランタイムが動的にパーティション ID を作成するため、パーティション ID が変更されている可能性があることです。そのため、各パーティションで復元する正しい最新のバックアップを識別するために、サービスでパーティション情報とサービス名を保存する必要があります。


## 破損したアプリケーション データの複製

新しくデプロイされたアプリケーションのアップグレードにバグがある場合、データが破損することがあります。たとえば、アプリケーションのアップグレードによって、Reliable Dictionary にあるすべての電話番号レコードが無効な局番で更新されることがあります。そのような場合、Service Fabric は保存されるデータの性質を認識しないため、無効な電話番号が複製されます。

データを破損させるような重大なバグを検出した場合に最初にするべきことは、サービスをアプリケーション レベルで停止し、可能であれば、バグのないアプリケーション コードのバージョンにアップグレードします。しかしながら、サービス コードを修正しても、データは壊れたままであり、復元する必要があります。そのような場合、最新バックアップも壊れている可能性があるので、最新バックアップを復元するだけでは不十分です。そのため、データが破損する前に行われた最後のバックアップを見つける必要があります。

どのバックアップが破損しているかわからない場合は、新しい Service Fabric クラスターをデプロイして、上記の「サービスの削除または損失」のシナリオと同様に、影響を受けたパーティションのバックアップを復元できます。各パーティションで、最新から一番古いものの順でバックアップの復元を開始します。破損していないバックアップを見つけたら、このパーティションの (そのバックアップより) 新しいすべてのバックアップを移動、削除します。各パーティションでこの手順を繰り返します。ここで、運用クラスターのパーティションで **OnDataLossAsync** が呼び出されたら、外部ストアで見つかった最後のバックアップが上記の手順で選択されたバックアップとなります。

ここで、「サービスの削除または損失」の手順を利用し、サービス バックアップの状態を、バグのあるコードが状態を破損させる前の状態に戻すことができます。

以下の点に注意してください。

- 復元の際、復元されるバックアップがデータ損失前のパーティションの状態よりも古くなっている可能性があります。そのため、復元は、可能な限り多くのデータを回復するための最後の手段としてのみ実行すべきです。

- バックアップ フォルダー パスとバックアップ フォルダー内のファイルのパスを表す文字列は、FabricDataRoot パスや Application Type 名の長さによっては 255 文字を超える可能性があります。これにより、**Directory.Move** などの一部の .NET メソッドで **PathTooLongException** 例外がスローされることがあります。回避策の 1 つは、**CopyFile** などの kernel32 API を直接呼び出すことです。


## 内部: バックアップと復元に関する追加情報

### Backup
Reliable State Manager には、読み書き操作を中断することなく、一貫性のあるバックアップを作成する機能があります。そのために、チェックポイントとログ永続化のメカニズムが活用されます。Reliable State Manager は特定の時点でファジー (簡易) チェックポイントを作成し、トランザクション ログからの負荷を軽減し、復元時間を早めます。**IReliableStateManager.BackupAsync** が呼び出されると、Reliable State Manager はすべての Reliable Object に、最新のチェックポイント ファイルをローカル バックアップ フォルダーにコピーするように指示します。次に、Reliable State Manager は "開始ポインター" から最新のログ レコードまですべてのログ レコードをバックアップ フォルダーにコピーします。ログ レコードが、最新のログ レコードまで、すべてバックアップに追加され、Reliable State Manager が先書きログを維持するため、Reliable State Manager では、コミットされた (**CommitAsync** が正常に返っている) すべてのトランザクションがバックアップに含まれることが保証されます。

**BackupAsync** が呼び出された後にコミットされるトランザクションは、バックアップに含まれていることもあれば、含まれていないこともあります。プラットフォームによりローカルのバックアップ フォルダーにデータが入力されると (すなわち、ローカルのバックアップがランタイムにより完了すると)、サービスのバックアップ コールバックが呼び出されます。このコールバックは、Azure Storage などの外部の場所にバックアップ フォルダーを移動する役割を担います。

### 復元

Reliable State Manager には、**IReliableStateManager.RestoreAsync** API を使用してバックアップから復元する機能があります。**RestoreAsync** メソッドは **OnDataLossAsync** メソッドの中でしか呼び出すことができません。**OnDataLossAsync** により返されるブール値は、サービスの状態が外部ソースから復元されたかどうかを示すものです。**OnDataLossAsync** が true を返した場合、Service Fabric はこのプライマリから他のすべてのレプリカを再構築します。Service Fabric により、**OnDataLossAsync** を受信するレプリカは最初にプライマリ ロールに切り替わるものの、読み取り状態または書き込み状態にはなりません。これは、StatefulService の実装機能に関して、**OnDataLossAsync** が正常に完了するまで **RunAsync** が呼び出されないことを意味します。次に、新しいプライマリで **OnDataLossAsync** が呼び出されます。サービスが (true または false を返し) この API を完了し、関連再構成を完了するまで、API は 1 つずつ呼び出され続けます。


**RestoreAsync** は最初、それが呼び出されたプライマリ レプリカの既存状態をすべて削除します。次に、Reliable State Manager はバックアップ フォルダーに存在するすべての Reliable Objects を作成します。次に、Reliable Objects はバックアップ フォルダーのチェックポイントから復元するように指示されます。最後に、Reliable State Manager はバックアップ フォルダー内のログ レコードからそれ自体の状態を復元し、復元を実行します。復元プロセスの一環として、バックアップ フォルダーにコミット ログ レコードがある "開始ポイント" から始まる操作が Reliable Objects に対して再生されます。この手順により、復元したステートに一貫性が与えられます。

<!---HONumber=AcomDC_0128_2016-->