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
   ms.date="08/18/2015"
   ms.author="mcoskun"/>

# Reliable Services のバックアップと復元

Service Fabric は高可用性プラットフォームであり、複数のノードにわたりステートを複製し、その高可用性を維持します。つまり、クラスター内の 1 つのノードに障害が発生した場合でも、サービスは引き続き利用できます。このプラットフォームに組み込まれている冗長性で十分な場合もありますが、(外部ストアに) サービスのデータをバックアップすることが望ましくなります。

たとえば、次のシナリオではサービスのデータをバックアップすることが望まれます。

* Service Fabric クラスター全体または特定のパーティションを実行しているすべてのノード (すなわち、クラスター内のすべてのノード) が永久に失われた場合。これは、たとえば、geo レプリケーションされないとき、クラスター全体が 1 か所のデータ センターにあり、データ センター全体が停止する場合などに起こります。
* ステートが誤って削除される/壊れるなどの管理エラー。これは、権限を与えられた管理者が誤ってサービスを削除した場合に起こります。
* サービスのバグが引き起こしたデータの破損。これは、たとえば、誤りのあるデータをコレクションに書き出すサービス アップグレードで、サービスとデータの両方を以前のステートに戻さなければならない場合に起こります。
* オフライン データ処理データを生成するサービスから離れているビジネス インテリジェンスのためにデータのオフライン処理を用意しておけば便利です。

バックアップ/復元機能では、 Reliable Services API を基盤とするサービスでバックアップを作成し、復元できます。このプラットフォームが提供するバックアップ API では、読み書き操作をブロックせずにパーティションのステートをバックアップできます。復元 API では、選択したバックアップからパーティションのステートを復元できます。



### バックアップの方法

サービスの作成者は、バックアップのタイミングと保管場所を完全に制御できます。

バックアップを開始するには、サービスで **IReliableStateManager.BackupAsync** を呼び出す必要があります。バックアップはプライマリ レプリカ以外からは実行できません。書き込みステータスを与える必要があります。

下の図のように、**BackupAsync** の最も単純なオーバーロードはFunc<< BackupInfo  bool >> の形式を取ります。これは「**backupCallback**」と呼ばれています。

        await this.StateManager.BackupAsync(this.BackupCallbackAsync);

**BackupInfo** は、ランタイムがバックアップを保存したフォルダー (BackupInfo.Directory) など、バックアップに関する情報を提供します。このコールバック関数は BackupInfo.Directory が外部ストアまたは別の場所に移動することを要求します。この関数はまた、バックアップ フォルダーがそのターゲット フォルダーに移動したかどうかを示すブール値を返します。

次のコードは、backupCallback を利用し、バックアップを Azure Storage にアップロードするしくみを示しています。

```C#
        private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
        {
            var backupId = Guid.NewGuid();

            await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

            return true;
        }
```

上の例では、**ExternalBackupStore** は Azure BLOB Storage と連動するためのサンプル クラスであり、**UploadBackupFolderAsync** はフォルダーを圧縮し、Azure BLOB ストアに配置するメソッドです。

>[AZURE.NOTE]ある時点で配信できる **BackupAsync** はレプリカあたり 1 つに限られます。一度に複数の **BackupAsync** 呼び出しを配信すると、**FabricBackupInProgressException** がスローされ、配信バックアップが 1 つに制限されます。[AZURE.NOTE]バックアップの途中でレプリカにエラーが発生した場合、バックアップは完了しないことがあります。そのため、フェールオーバーが完了したとき、必要に応じて **BackupAsync** を呼び出し、バックアップを再開する役割をサービスが担います。

### データの復元方法

実行中のサービスでバックアップ ストアからデータを復元するシナリオは次のカテゴリに分類できます。

1. サービス パーティションのデータが失われた場合。たとえば、パーティションの 3 つのレプリカのうちの 2 つのディスク (プライマリ レプリカを含む) が壊れた/消去された場合です。新しいプライマリのデータを復元する必要があります。

2. サービス全体が失われた場合。たとえば、管理者がサービス全体を削除し、サービスとデータを復元する必要がある場合です。

3. (たとえば、アプリケーション バグに起因して) サービスが壊れたアプリケーション データを複製した場合。その場合、サービスをアップグレードするか、元の状態に戻し、データが壊れるの原因を取り除き、壊れていないデータを復元する必要があります。

さまざまな方法が可能ですが、RestoreAsync で上記のシナリオから復元する方法について紹介します。

#### パーティション データの損失

この場合、ランタイムが自動的にデータ損失を検出し、**OnDataLossAsync** API を呼び出します。

サービスの作成者は復元のために以下を実行する必要があります。 - **IReliableStateManager** を上書きし、新しい ReliableStateManager を返し、データが失われた場合はコールバック関数を呼び出します。 - サービスのバックアップが含まれる外部の場所で最新のバックアップを探します。 - 最新のバックアップをダウンロードします (圧縮されている場合、バックアップ フォルダーに解凍します)。 - バックアップ フォルダーのパスを指定し、**IReliableStateManager.RestoreAsync** を呼び出します。 - 復元が完了した場合、true を返します。

以下は **OnDataLossAsync** メソッドと **IReliableStateManager** オーバーライドの実行例です。

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

>[AZURE.NOTE]RestorePolicy は既定で「Safe」に設定されます。つまり、バックアップ フォルダーにこのレプリカに含まれているステートと同じか、それより古いステートが含まれていることが検出された場合、RestoreAsync API は失敗し、ArgumentException をスローします。RestorePolicy.Force でこの安全性チェックを省略できます。

#### サービスの削除または紛失

サービスが削除された場合、最初にサービスを再作成しないとデータを復元できません。データを途切れなく復元するために、サービスを同じ構成 (パーティショニング スキームなど) で作成することが重要です。サービスが起動したら、そのサービスのあらゆるパーティションで、データを復元する API (上記の **OnDataLossAsync**) を呼び出す必要があります。その方法の 1 つは、すべてのパーティションで **FabricClient.ServiceManager.InvokeDataLossAsync** を使用することです。**InvokeDataLossAsync** に関する詳細については、テスト容易性の章を参照してください。

この時点から、実装は上記のシナリオと同じになります。各パーティションで、外部ストアから最新の関連バックアップを復元する必要があります。1 つ注意してことがあります。ランタイムが (動的に) パーティション ID を作成するため、パーティション ID が変更されている可能性があります。そのため、各パーティションで復元する最新バックアップを識別するために、サービスでパーティション情報とサービス名を格納しておく必要があります。


#### 壊れたアプリケーション データの複製

アップグレード後のアプリケーションにバグがある場合、データが壊れることがあります。たとえば、あるアプリケーション アップグレードでは、Reliable Dictionary にあるすべての電話番号レコードが無効な局番で更新されることがあります。そのような場合、Service Fabric は保存されるデータの性質を認識しないため、無効な電話番号が複製されます。

データを壊すこのような重大なバグが検出された場合、最初にするべきことは、サービスをアプリケーション レベルで停止し、可能であれば、バグのないバージョンのアプリケーション コードにアップグレードします。しかしながら、サービス コードを修正しても、データは壊れたままであり、復元する必要があります。そのような場合、最新バックアップも壊れている可能性があるので、最新バックアップを復元するだけでは不十分です。そのため、データが壊れる前に行われた最後のバックアップを探す必要があります。

それを行う方法の 1 つは新しい Service Fabric クラスターをデプロイし、上記の「サービスの削除」シナリオと同様に、影響を受けたパーティションのバックアップを復元することです。データが壊れる前の最後のバックアップが見つかったら、このバックアップ後のこのパーティションのすべてのバックアップを移動/削除できます。本稼働クラスターのパーティションで **OnDataLossAsync** が呼び出されたら、外部ストアで見つけた最後のバックアップが上記のプロセスで選択されたバックアップとなります。

「サービスの削除」の手順を利用し、サービス バックアップのステートをバグのあるコードがステートを壊す前のステートに戻すことができます。


>[AZURE.NOTE]復元するたびに、復元されたバックアップがデータ紛失前のパーティションのステートより古くなるという可能性が生じます。そのため、復元は、可能な限り多くのデータを復元するための最後の手段として使用するべきです。

>[AZURE.NOTE]バックアップ フォルダー パスとバックアップ フォルダー内のファイルのパスを表す文字列は、FabricDataRoot パスや Application Type 名の長さに起因し、255 文字を超えることがあります。その場合、**Directory.Move** のような .Net メソッドは **PathTooLongException** をスローします。回避策として、**CopyFile** のような kernel32 API を直接呼び出すことができます。


## 内部: バックアップと復元に関する追加情報

### Backup

Reliable State Manager には、サービスに必要な読み書き操作を中断することなく、一貫性のあるバックアップを作成する機能があります。そのために、チェックポイントとログ永続化のメカニズムが活用されます。Reliable State Manager は特定の時点でファジー (簡易) チェックポイントを作成し、トランザクション ログからの負荷を軽減し、復元時間を早めます。IReliableStateManager.**BackupAsync** が呼び出されると、Reliable State Manager はすべての Reliable Objects に最新のチェックポイント ファイルをローカルのバックアップ フォルダーにコピーするように指示します。次に、Reliable State Manager は「開始ポインター」から最新のログ レコードまですべてのログ レコードをバックアップ フォルダーにコピーします。ログ レコードが、最新のログ レコードまで、すべてバックアップに追加され、Reliable State Manager が Write Ahead Logging を維持するため、Reliable State Manager では、コミットされた (CommitAsync が正常に返ります) すべてのトランザクションがバックアップに含まれることが保証されます。

**BackupAsync** が呼び出された後にコミットするトランザクションはバックアップに追加されることもあれば、追加されないこともあります。プラットフォームによりローカルのバックアップ フォルダーにデータが入力されると (すなわち、ローカルのバックアップがランタイムにより完了すると)、サービスのバックアップ コールバックが呼び出されます。このコールバックは、Azure Storage などの外部の場所にバックアップ フォルダーを移動する役割を担います。

### 復元

Reliable State Manager には、IReliableStateManager.RestoreAsync API でバックアップから復元する機能があります。RestoreAsync method は OnDataLossAsync メソッドの内部でのみ呼び出すことができます。OnDataLossAsync により返されるブール値は、サービスのステートが外部ソースから復元されたかどうかを示します。OnDataLossAsync が true を返した場合、Service Fabric はこのプライマリから他のすべてのレプリカを再構築します。OnDataLossAsync を受け取ったレプリカは最初にプライマリ ロールに遷移しますが、読み取りステータスや書き込みステータスは与えられません。これは、StatefulService の実装機能にとって、OnDataLossAsync が完了するまで RunAsync が呼び出されないことを意味します。サービスが (true または false を返し) この API を完了し、関連再構成を完了するまで、API は呼び出され続けます。

RestoreAsync は最初、それが呼び出されたプライマリ レプリカの既存ステートをすべて削除します。次に、Reliable State Manager はバックアップ フォルダーに存在するすべての Reliable Objects を作成します。次に、Reliable Objects はバックアップ フォルダーのチェックポイントから復元するように指示されます。最後に、Reliable State Manager はバックアップ フォルダーのログ レコードからそれ自体のステートを復元し、復元を実行します。復元プロセスの一環として、バックアップ フォルダーにコミット ログ レコードがあり、「開始ポイント」から始まる操作が Reliable Objects でリプレイされます。この手順により、復元したステートに一貫性が与えられます。

<!---HONumber=Nov15_HO4-->