<properties
	pageTitle="増分スナップショットを使用した Azure Virtual Machines のバックアップと復元 | Microsoft Azure"
	description="増分スナップショットを使用して Azure 仮想マシン ディスクのバックアップと復元を行うカスタム ソリューションを作成します。"
	services="storage"
	documentationCenter="na"
	authors="ms-prkhad"
	manager=""
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/21/2016"
	ms.author="tamram;prkhad"/>


# Azure 仮想マシン ディスクを増分スナップショットでバックアップする

## 概要 

Azure Storage には、BLOB のスナップショットを作成する機能があります。スナップショットは、特定の時点における BLOB の状態をキャプチャします。この記事では、スナップショットを使って仮想マシン ディスクのバックアップを保存する方法について説明します。この手法は、Azure Backup と Recovery Service を使用せず、仮想マシン ディスク用に独自のバックアップ方法を構築する必要がある場合にご利用ください。

Azure Storage には、Azure 仮想マシン ディスクがページ BLOB として格納されます。この記事のテーマは仮想マシン ディスクのバックアップ方法であるため、スナップショットという言葉は、ページ BLOB のコンテキストにおける意味合いを指しています。スナップショットの詳細については、「[BLOB のスナップショットの作成](https://msdn.microsoft.com/library/azure/hh488361.aspx)」を参照してください。

## スナップショットとは

BLOB のスナップショットとは、ある時点でキャプチャされた BLOB の読み取り専用版です。作成されたスナップショットは、読み取り、コピー、削除はできますが、変更はできません。スナップショットを使用すると、BLOB をある時点での表示内容のままバックアップできます。REST バージョン XX までは、スナップショット全体をコピーすることができました。REST バージョン YY 以降では、増分スナップショットをコピーすることができます。

## スナップショット全体のコピー

スナップショットは別のストレージ アカウントに BLOB としてコピーすることができます。こうすることでスナップショットの作成元となった BLOB (ベース BLOB) のバックアップを保存できます。逆に、スナップショットをそのベース BLOB に上書きコピーして、以前のバージョンの BLOB を復元することもできます。2 つのストレージ アカウント間でスナップショットをコピーすると、そのスナップショットの作成元となったページ BLOB と同じだけの記憶域がコピー先のストレージ アカウントで消費されます。したがって 2 つのストレージ アカウント間でスナップショット全体をコピーした場合、時間がかかるうえに、コピー先のストレージ アカウントで大量の領域が消費されることになります。

>[AZURE.NOTE] ベース BLOB を別の場所にコピーしても、その BLOB のスナップショットまではコピーされません。同様に、ベース BLOB に上書きコピーしても、そのベース BLOB に関連付けられているスナップショットは影響を受けず、元のベース BLOB 由来として維持されます。

### スナップショットを使用したディスクのバックアップ

仮想マシン ディスクのバックアップを行う一つの方法として、ディスクやページ BLOB のスナップショットを定期的に作成し、[Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) 操作や [AzCopy](storage-use-azcopy.md) などのツールを使ってそれらを別のストレージ アカウントにコピーする手法が考えられます。コピー先のページ BLOB には、スナップショットを別の名前でコピーできます。このときコピー先に生成されるのは、書き込み可能なページ BLOB であってスナップショットではありません。スナップショットを使って仮想マシン ディスクのバックアップを作成する手順については、この記事の後半で説明します。

### スナップショットを使用したディスクの復元

以前バックアップ スナップショットとしてキャプチャした安定した状態のディスクを復元する必要が生じた場合は、スナップショットをそのベースとなったページ BLOB に上書きコピーします。ベース ページ BLOB に昇格された後も、そのスナップショットは同じ状態で保たれますが、その作成元は、読み取りと書き込みが可能なコピーによって上書きされます。以前のバージョンのディスクをそのスナップショットから復元する手順については、この記事の後半で説明します。

### スナップショット全体のコピーの実行

スナップショット全体のコピーは、次の手順で実行できます。

-   まず、[Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) 操作を使用してベース BLOB のスナップショットを作成します。

-   次に、[Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) を使って目的のストレージ アカウントにスナップショットをコピーします。

&lt; create snapshot と copy blob の使用例を示すコード スニペット&gt;

## 増分スナップショットのコピー

新しい Diff Page Ranges API では、従来よりも効率よくページ BLOB やディスクのスナップショットをバックアップできるようになりました。この API は、ベース BLOB とスナップショットとを比較して得られる変更のリストを返します。これによってバックアップ アカウントで使用される記憶域の量を減らすことができます。この API は、Premium Storage だけでなく Standard Storage 上のページ BLOB もサポートします。この API を使用することで、従来よりも高速に効率よく Azure VM をバックアップできるソリューションを構築できます。この API が利用できるのは、REST バージョン xx 以上となります。

Incremental Snapshot Copy を使用すると、以下の差分をストレージ アカウント間でコピーできます。

-   ベース BLOB とそのスナップショット

-   同じベース BLOB から作成された 2 つのスナップショット

**注**: この機能は、Premium と Standard の Azure ページ BLOB に対して利用できます。

スナップショットを使用した独自のバックアップ方法を利用している場合、スナップショットを 2 つのストレージ アカウント間でコピーすると、ひどく時間がかかるうえに記憶領域が大量に消費されます。バックアップ ページ BLOB には、差分だけを書き込むことができます。そうすれば、バックアップ ストレージ アカウントにスナップショット全体をコピーする必要はありません。そうすることでコピーにかかる時間とバックアップで消費される記憶域を大幅に減らすことができます。

### 増分スナップショットのコピーの実行

増分スナップショットのコピーは、次の手順で実行できます。

-   [Snapshot Blob](https://msdn.microsoft.com/library/azure/ee691971.aspx) を使用してベース BLOB のスナップショットを作成します。

-   [Copy Blob](https://msdn.microsoft.com/library/azure/dd894037.aspx) を使って目的のストレージ アカウントにベース BLOB をコピーします。

-   ベース BLOB の [GetPageRanges](https://msdn.microsoft.com/library/azure/ee691973.aspx) を使用し、スナップショットとの差分を取得します。新しいパラメーター **prevsnapshot** を使用して、差分の取得対象となるスナップショットの DateTime 値を指定します。このパラメーターが存在する場合、REST 応答には、対象の BLOB と以前のスナップショットとの間で変更 (ページの消去を含む) されたページだけが格納されます。

-   最後に、[PutPage](https://msdn.microsoft.com/library/azure/ee691975.aspx) を使用してこれらの変更をバックアップ ページ BLOB に適用します。

&lt; get page ranges と put page の使用例を示すコード スニペット&gt;

Incremental Snapshot Copy を使用してディスクのバックアップを保持する方法については、次のセクションで詳しく説明します。

## シナリオ

このセクションでは、スナップショットを使った独自の方法で仮想マシン ディスクをバックアップするシナリオを取り上げます。

Premium Storage P30 ディスクが接続された DS シリーズの Azure VM について考えてみましょう。この P30 ディスク (mypremiumdisk) の格納先は Premium Storage アカウント (mypremiumaccount) です。mypremiumdisk のバックアップには、Standard Storage アカウント (mybackupstdaccount) が使用されます。12 時間ごとの mypremiumdisk のスナップショットを維持したいと考えています。

ストレージ アカウントとディスクの作成について詳しくは、「[Azure ストレージ アカウントについて](storage-create-storage-account.md)」を参照してください。

Azure VM のバックアップについて詳しくは、「[Azure における VM バックアップ インフラストラクチャの計画を立てる](../backup/backup-azure-vms-introduction.md)」を参照してください。

## 増分スナップショットを使用してディスクのバックアップを保存する手順

以下の手順は、mypremiumdisk のスナップショットを作成して、mybackupstdaccount にバックアップを保存する方法を説明しています。バックアップは、mybackupstdpageblob という標準ページ BLOB となります。このページ BLOB には常時、直近に作成された mypremiumdisk のスナップショットと同じ状態が反映されます。

1.  バックアップの Standard ストレージ アカウントに、Premium ストレージ ディスクのコピーを作成します。mypremiumaccount の mypremiumdisk を、mybackupstdpageblob という名前の新しいページ BLOB として mybackupstdaccount にコピーします。

2.  Snapshot Blob を使用し、mybackupstdpageblob のスナップショットを mybackupstdpageblob\_ss1 という名前で作成し、mybackupstdaccount に保存します。

3.  バックアップの時間帯に、mypremiumdisk のスナップショット (例: mypremiumdisk\_ss1) を作成して mypremiumaccount に保存します。

4.  GetPageRanges を使用し、mypremiumdisk とその初回スナップショット mypremiumdisk\_ss1 との間で変更の増分を取得します。これらの変更の増分を mybackupstdaccount のバックアップ ページ BLOB (mybackupstdpageblob) に書き込みます。削除された範囲が増分に含まれている場合、バックアップ ページ BLOB からそれらを消去する必要があります。Put Page を使用して、変更の増分をバックアップ ページ BLOB に書き込みます。

5.  バックアップ ページ BLOB (mybackupstdpageblob) のスナップショットを mybackupstdpageblob\_ss2 という名前で作成し、mybackupstdaccount に保存します。Premium Storage アカウントからスナップショット mypremiumdisk\_ss1 を削除します。

6.  バックアップの時間帯に毎回、この手順 3. ～ 手順 5. を繰り返します。このようにして、mypremiumdisk のバックアップを Standard Storage アカウントに保存することができます。

![Backup disk using incremental snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-1.png)

&lt; Copy Blob、Get Page Ranges、Put Page の使用例を示すコード スニペット&gt;

## スナップショットからディスクを復元する手順

以下の手順は、Premium ディスク mypremiumdisk を、バックアップ ストレージ アカウント mybackupstdaccount に保存されている以前のスナップショットに復元する方法を説明しています。

1.  Premium ディスクをどの時点まで復元するかを決めます。たとえば mybackupstdpageblob\_ss1 を復元するとしましょう。このスナップショットは、バックアップ ストレージ アカウント mybackupstdaccount に保存されています。

2.  mybackupstdaccount で、スナップショット mybackupstdpageblob\_ss1 を mybackupstdpageblobrestored という名前の新しいバックアップ ベース ページ BLOB として昇格させます。

3.  mybackupstdaccount から、バックアップ ベース ページ BLOB (mybackupstdpageblobrestored) を新しい Premium ディスク mypremiumdiskrestored として mypremiumaccount にコピーします。

4.  DS シリーズ VM の接続先ディスクを mypremiumdiskrestored (復元済みのディスク) に切り替えて、以前の mypremiumdisk は VM から切断します。

5.  復元済みのディスク mypremiumdiskrestored に対して前セクションで説明したバックアップ プロセスを開始します。バックアップ ページ BLOB には mybackupstdpageblobrestored を使用します。

![Restore disk from snapshots](./media/storage-incremental-snapshots/storage-incremental-snapshots-2.png)

&lt; Copy Blob、Attach disk、Detach disk を示すコード スニペット&gt;

## 関連項目

- [BLOB のスナップショットの作成](https://msdn.microsoft.com/library/azure/hh488361.aspx)
- [VM のバックアップ インフラストラクチャの計画](../backup/backup-azure-vms-introduction.md)

<!---HONumber=AcomDC_0330_2016------>