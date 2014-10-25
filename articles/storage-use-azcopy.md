<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram"></tags>

# AzCopy コマンド ライン ユーティリティの概要

AzCopy は、Microsoft Azure BLOB とファイル ストレージ間でのデータのアップロード、ダウンロード、コピーで高いパフォーマンスを実現するために設計されたコマンド ライン ユーティリティです。このガイドでは、AzCopy の使用に関する概要を説明します。

> [WACOM.NOTE] このガイドでは、AzCopy 2.5 以降がインストール済みであることを前提としています。AzCopy はプレリリースであるため、今後のリリースで、コマンド ラインのオプションとその機能が変更される場合があります。

## 目次

-   [AzCopy のダウンロードとインストール][AzCopy のダウンロードとインストール]
-   [AzCopy コマンド ライン構文の理解][AzCopy コマンド ライン構文の理解]
-   [データのコピー中の同時書き込みの制限][データのコピー中の同時書き込みの制限]
-   [AzCopy を使用した Azure BLOB のコピー][AzCopy を使用した Azure BLOB のコピー]
-   [AzCopy を使用した Azure ファイル共有でのファイル コピー][AzCopy を使用した Azure ファイル共有でのファイル コピー]
-   [AzCopy のバージョン][AzCopy のバージョン]
-   [次のステップ][次のステップ]

## <span id="install"></span></a>AzCopy のダウンロードとインストール

1.  [最新バージョンの AzCopy][最新バージョンの AzCopy] をダウンロードします。
2.  インストールを実行します。既定では、AzCopy をインストールすると、`%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (64 ビット Windows を実行しているマシン) または `%ProgramFiles%\Microsoft SDKs\Azure\` (32 ビット Windows を実行しているマシン) に、`AzCopy` という名前のフォルダーが作成されます。ただし、インストール パスはセットアップ ウィザードで変更できます。
3.  必要に応じて、AzCopy のインストール先をシステム パスに追加できます。

## <span id="syntax"></span></a>AzCopy コマンド ライン構文の理解

次に、コマンド ウィンドウを開き、`AzCopy.exe` 実行可能ファイルが格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。AzCopy コマンドの基本構文は次のとおりです。

    AzCopy <source> <destination> [filepattern] [options]

-   `<source>` パラメーターは、コピー元となるソース データを指定します。ソースには、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、またはストレージ ファイル共有を指定できます。

-   `<destination>` パラメーターでコピー先を指定します。コピー先には、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、またはストレージ ファイル共有を指定できます。

-   オプションの `filepattern` パラメーターの動作は、ソース データの場所と再帰モード オプションの有無で決まります。再帰モードは、オプション `/S` で指定されます。

    指定されたソースがファイル システムのディレクトリの場合、標準のワイルドカードが有効となり、指定されたファイル パターンがディレクトリ内のファイルと照合されます。オプション `/S` が指定されると、AzCopy は、該当するディレクトリの下位にあるすべてのサブフォルダー内の全ファイルについても、指定のパターンと照合します。

    指定されたソースが BLOB コンテナーまたは仮想ディレクトリの場合、ワイルドカードは適用されません。オプション `/S` が指定されると、AzCopy は指定のファイル パターンを BLOB のプレフィックスとして解釈します。オプション `/S` を指定しない場合は、AzCopy はファイル パターンを BLOB 名そのものと照合します。

    指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (`abc.txt`) を指定して単一ファイルをコピーするか、オプション `/S` を指定して共有内の全ファイルを再帰的にコピーする必要があります。ファイル パターンとオプション `/S` の両方を同時に指定しようとすると、エラーになります。

    ファイル パターンを指定しない場合、ファイル システムのディレクトリで使用される既定のファイル パターンは `*.*` です。また、Azure BLOB またはファイル ストレージのリソースの場合は空のプレフィックスです。

    > [WACOM.NOTE] パフォーマンス上の理由で、AzCopy バージョン 2.5 では、1 つのコマンドに複数のファイル パターンを指定することができなくなりました。今後、複数のファイル パターンのシナリオに対応するには、コマンドごとに単一のファイル パターンを指定して、複数のコマンドを発行する必要があります。

-   以下の表に、`options` パラメーターで利用可能なオプションを示します。コマンド ラインに「`AzCopy /?`」と入力すると、オプションのヘルプを表示することができます。

<table>
  <tr>
<th>オプション名</th>
<th>説明</th>
<th>BLOB ストレージへの適用の有無 (Y/N)</th>
<th>ファイル ストレージへの適用の有無 (Y/N)</th>
  </tr>
  <tr>
<td><b>/DestKey:&lt;storage-key&gt;</b></td>
<td>宛先リソースのストレージのアカウント キーを指定します。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/DestSAS:&lt;container-SAS&gt;</b></td>
<td>宛先コンテナーの共有アクセス署名 (SAS) を指定します (適用可能な場合)。特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。<br />
宛先リソースが BLOB の場合は、このオプションと直後の SAS トークンを指定するか、または、このオプションを指定せずに BLOB の URI の一部として SAS を指定することができます。<br />
このオプションは、BLOB ストレージのみに適用され、同一ストレージ アカウントで BLOB をアップロードまたはコピーする場合のみ有効です。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/SourceKey:&lt;storage-key&gt;</b></td>
<td>ソース リソースのストレージのアカウント キーを指定します。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/SourceSAS:&lt;container-SAS&gt;</b></td>
<td>ソース コンテナーの共有アクセス署名を指定します (適用される場合)。特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。 
        <br />
キーまたは SAS のどちらも指定しない場合、コンテナーは匿名のアクセスで読み取られます。 
        <br />
このオプションは BLOB ストレージのみに適用されます。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/S</b></td>
<td>コピー操作の再帰モードを指定します。再帰モードでは、AzCopy は指定のファイル パターンに一致するすべての BLOB またはファイル (サブフォルダー内を含む) をコピーします。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/BlobType:&lt;block | page&gt;</b></td>
<td>宛先としてブロック BLOB またはページ BLOB を指定します。このオプションは宛先が BLOB の場合のみに適用され、それ以外の場合はエラーが生成されます。宛先が BLOB でこのオプションを指定しない場合、既定で AzCopy は、ブロック BLOB が必要であると見なします。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/CheckMd5</b></td>
<td>ダウンロードするデータの MD5 ハッシュを計算し、BLOB に格納されている MD5 ハッシュまたはファイルの Content-MD5 プロパティが計算したハッシュに一致するかどうかを検証します。MD5 チェックは既定では無効になっているため、MD5 チェックを実行するにはデータのダウンロード時にこのオプションを指定する必要があります。
    <br />
Azure ストレージでは、BLOB またはファイルに対して格納される MD5 ハッシュが最新であることは保証されません。BLOB またはファイルが変更された場合、MD5 の更新はクライアントの責任で行う必要があります。
    <br />
AzCopy は、Azure BLOB またはファイルをサービスにアップロードした後、常にその Content-MD5 プロパティを設定します。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Snapshot</b></td>
<td>スナップショットを転送するかどうかを示します。このオプションはソースが BLOB の場合のみに有効です。 
        <br />
転送する BLOB のスナップショットは、[blob-name] (snapshot-time) [extension] の形式で名前が変更されます。 
        <br />
スナップショットは既定ではコピーされません。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/V:[verbose log-file]</b></td>
<td>詳細な状態メッセージをログ ファイルに出力します。既定では、詳細なログ ファイルは、<code data-inline="1">AzCopyVerbose.log</code> という名前で、<code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code> に格納されます。このオプションで既存のファイルの場所を指定すると、そのファイルに詳細なログが追加されます。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Z:[journal-file-folder]</b></td>
<td>操作を再開するジャーナル ファイル フォルダーを指定します。<br />
操作に割り込みが発生した場合、AzCopy は常に再開をサポートします。<br />
このオプションを指定しないか、フォルダー パスなしで指定した場合、AzCopy は既定の場所である <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code> にジャーナル ファイルを作成します。<br />
AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。
        <br />
既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。 
        <br />
操作が正常に完了すると、ジャーナル ファイルは削除されます。
        <br />
以前のバージョンの AzCopy で作成されたジャーナル ファイルによる操作の再開はサポートされていません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/@:response-file</b></td>
<td>パラメーターを含むファイルを指定します。AzCopy は、このファイルのパラメーターを、コマンド ラインで指定された場合と同様に処理します。<br /> 
応答ファイルでは、複数のパラメーターを単一行に指定することも、各パラメーターをそれぞれの行に指定することもできます。個々のパラメーターを複数行にまたがって指定することはできません。 
        <br />
応答ファイルには、シンボル <code data-inline="1">#</code> で始まるコメント行を含めることができます。 
        <br />
複数の応答ファイルを指定できます。ただし、入れ子になった応答ファイルはサポートされません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Y</b></td>
<td>AzCopy のすべての確認プロンプトを表示しません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/L</b></td>
<td>一覧の操作のみ指定します。データはコピーされません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/MT</b></td>
<td>ダウンロードしたファイルの最後の変更時刻をソースの BLOB またはファイルと同じに設定します。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XN</b></td>
<td>新しいソース リソースを除外します。ソースが宛先より新しい場合、リソースはコピーされません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XO</b></td>
<td>古いソース リソースを除外します。ソース リソースが宛先リソースより古い場合、リソースはコピーされません。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/A</b></td>
<td>Archive 属性が設定済みのファイルのみアップロードします。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/IA:[RASHCNETOI]</b></td>
<td>指定の属性のいずれかが設定されているファイルのみアップロードします。<br />
以下の属性が利用可能です。  
        <br />
R: 読み取り専用ファイル
        <br />
A: アーカイブの準備が完了したファイル
        <br />
S: システム ファイル
        <br />
H: 隠しファイル
        <br />
C: 圧縮ファイル
        <br />
N: 通常ファイル
        <br />
E: 暗号化ファイル
        <br />
T: 一時ファイル
        <br />
O: オフラインのファイル
        <br />
I: インデックス付きでないファイル</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/XA:[RASHCNETOI]</b></td>
<td>指定の属性のいずれかが設定されているファイルを除外します。<br />
以下の属性が利用可能です。  
        <br />
R: 読み取り専用ファイル  
        <br />
A: アーカイブの準備が完了したファイル  
        <br />
S: システム ファイル  
        <br />
H: 隠しファイル  
        <br />
C: 圧縮ファイル  
        <br />
N: 通常ファイル  
        <br />
E: 暗号化ファイル  
        <br />
T: 一時ファイル  
        <br />
O: オフラインのファイル  
        <br />
I: インデックス付きでないファイル</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/Delimiter:&lt;delimiter&gt;</b></td>
<td>BLOB 名で仮想ディレクトリを区切るために使用する区切り文字を示します。<br />
既定では、AzCopy は区切り文字として / を使用します。ただし、AzCopy は、一般的な文字である @、#、または % の区切り文字としての使用もサポートします。この特殊文字のいずれかをコマンド ラインに含める必要がある場合は、ファイル名を二重引用符で囲みます。 
        <br />
このオプションは BLOB のダウンロードのみに適用されます。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/NC</b></td>
<td>同時操作の数を指定します。 
        <br />
既定の数は、実行しているコア プロセッサの数に 8 を乗算したものになります。したがって、8 つのコアを持つコンピューターの場合、AzCopy は、既定で 64 の同時操作を開始します。<br />
低帯域幅の環境で大量に同時操作を実行すると、接続するネットワークに過剰な負荷がかかり、操作を完了できなくなる場合があります。実際に利用可能なネットワーク帯域幅に適した同時操作数になるように調整してください。</td>
<td>Y</td>
<td>Y</td>
  </tr>
  <tr>
<td><b>/SourceType:Blob</b></td>
<td>ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB を <code data-inline="1">source</code> リソースに指定します。</td>
<td>Y</td>
<td>N</td>
  </tr>
  <tr>
<td><b>/DestType:Blob</b></td>
<td>ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB を <code data-inline="1">destination</code> リソースに指定します。</td>
<td>Y</td>
<td>N</td>
  </tr>
</table>

## <span id="limit-writes"></span></a>データのコピー中の同時書き込みの制限

AzCopy を使用して BLOB またはファイルをコピーする場合は、コピー中に別のアプリケーションがそのデータを変更している可能性があることに注意してください。可能な場合は、コピーしているデータがコピー操作中に変更されないようにしてください。たとえば、Azure 仮想マシンに関連付けられている VHD をコピーしている場合は、その VHD に別のアプリケーションが書き込み中でないことを確認します。または、最初に VHD のスナップショットを作成してからそのスナップショットをコピーします。

コピーしている BLOB またはファイルに対する他のアプリケーションによる書き込みを回避できない場合は、ジョブが終了した時点で、コピー対象のリソースがソース リソースとの間に完全なパリティを保持していない可能性があることを覚えておいてください。

## <span id="copy-blobs"></span></a>AzCopy を使用した Azure BLOB のコピー

以下の例は、AzCopy を使用して BLOB をコピーするさまざまなシナリオを示したものです。

### 単一の BLOB のコピー

**ファイル システムのファイルを BLOB ストレージにアップロードする場合:**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**BLOB ストレージの BLOB をファイル システムにダウンロードする場合:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### サーバー側コピーを通じた BLOB のコピー

BLOB を 1 つのストレージ アカウント内または複数のストレージ アカウント間でコピーする場合は、サーバー側でコピー操作が実行されます。サーバー側のコピー操作の詳細については、「[Introducing Asynchronous Cross-Account Copy Blob (非同期アカウント間での BLOB のコピーについて)][Introducing Asynchronous Cross-Account Copy Blob (非同期アカウント間での BLOB のコピーについて)]」を参照してください。

**1 つのストレージ アカウント内で BLOB をコピーする場合:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**複数のストレージ アカウント間で BLOB をコピーする場合:**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### セカンダリ リージョンの BLOB のコピー

ストレージ アカウントで地理冗長ストレージの読み取りアクセスが有効に設定されている場合は、セカンダリ リージョンのデータをコピーできます。

**セカンダリの BLOB をプライマリ アカウントにコピーする場合:**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**セカンダリの BLOB をファイル システムのファイルにダウンロードする場合:**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### 新しい BLOB コンテナーまたは仮想ディレクトリへのファイルのアップロード

**新しい BLOB コンテナーにファイルをアップロードする**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされることに注意してください。

**新しい BLOB 仮想ディレクトリにファイルをアップロードする**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

存在しない仮想ディレクトリを指定すると、アップロードされるファイルの名前に仮想ディレクトリが含められることに注意してください (この例では `vd/abc.txt`)。

### 新しいフォルダーへの BLOB のダウンロード

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

フォルダー `C:\myfolder` が存在しない場合、AzCopy によってファイル システム内にフォルダーが作成され、この新しいフォルダーに `abc.txt` がダウンロードされます。

### ディレクトリ内のファイルおよびサブフォルダーをコンテナーに再帰的にアップロードする

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

オプション `/S` を指定すると、指定したディレクトリの内容が再帰的に、つまり、ファイルがそのすべてのサブフォルダーのファイルと共に、BLOB ストレージにコピーされます。たとえば、フォルダー `C:\myfolder` に以下のファイルがあるとします。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

コピー操作後にコンテナーに含まれるのは以下のファイルです。

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### ディレクトリからコンテナーにファイルを非再帰的にアップロードする

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

オプション `/S` をコマンド ラインに指定しない場合は、AzCopy は再帰的にはコピーを実行しません。指定されたディレクトリ内のファイルのみコピーされ、サブフォルダーとその中のファイルはコピーされません。たとえば、フォルダー `C:\myfolder` に以下のファイルがあるとします。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

コピー操作後にコンテナーに含まれるのは以下のファイルです。

    abc.txt
    abc1.txt
    abc2.txt

### コンテナー内のすべての BLOB をファイル システムのディレクトリに再帰的にダウンロードする

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

以下の BLOB が指定されたコンテナーにあるとします。

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

コピー操作後にディレクトリ `C:\myfolder` に含まれるのは以下のファイルです。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### 仮想 BLOB の内の BLOB をファイル システムのディレクトリに再帰的にダウンロードする

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

以下の BLOB が指定されたコンテナーにあるとします。

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

コピー操作後にディレクトリ `C:\myfolder` に含まれるのは以下のファイルです。仮想ディレクトリ内の BLOB のみがコピーされることに注意してください。

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### 指定されたファイル パターンに一致するファイルをコンテナーに再帰的にアップロードする

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

フォルダー `C:\myfolder` に以下のファイルがあるとします。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

コピー操作後にコンテナーに含まれるのは以下のファイルです。

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### 指定されたプレフィックスを付けた BLOB をファイル システムに再帰的にダウンロードする

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

以下の BLOB が指定されたコンテナーにあるとします。プレフィックス `a` で始まるすべての BLOB がコピーされます。

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

コピー操作後にフォルダー `C:\myfolder` に含まれるのは以下のファイルです。

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

プレフィックスは仮想ディレクトリに適用され、BLOB 名の最初の部分を構成することに注意してください。前の例では、仮想ディレクトリは指定されたプレフィックスに一致しないため、コピーされません。

### BLOB とそのスナップショットの別のストレージ アカウントへのコピー

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

コピー操作後、ターゲット コンテナーには BLOB とそのスナップショットが含まれます。前の例の BLOB に 2 つのスナップショットが含まれる場合、コンテナーには以下の BLOB とスナップショットが含まれます。

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### コマンド ライン パラメーターを指定するための応答ファイルの使用

    AzCopy /@:"C:\myfolder\abc.txt"

応答ファイルには、任意の AzCopy コマンド ライン パラメーターを含めることができます。AzCopy は、ファイルの内容に直接置換することで、ファイルのパラメーターをコマンド ラインで指定されているかのように処理します。

**1 つ以上の単一行の応答ファイルを指定する**

`source.txt` という名前の応答ファイルが、ソース コンテナーを次のように指定するとします。

    http://myaccount.blob.core.windows.net/mycontainer

さらに、`dest.txt` という名前の応答ファイルが、ファイル システムの宛先フォルダーを指定します。

    C:\myfolder

また、`options.txt` という名前の応答ファイルは、AzCopy のオプションを次のように指定します。

    /S /Y

すべてディレクトリ `C:\responsefiles` に格納されているこれらの応答ファイルを使用して AzCopy を呼び出すには、次のコマンドを使用します。

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy は次のように、個々のパラメーターがすべてコマンド ラインで指定されたかのようにこのコマンドを処理します。

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**複数行の応答ファイルを指定する**

`copyoperation.txt` という名前のファイルに、以下の行が含まれているとします。AzCopy の各パラメーターは次のように、それぞれ個別の行に指定されます。

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

この応答ファイルを使用して AzCopy を呼び出すには、次のコマンドを使用します。

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy は次のように、個々のパラメーターがすべてコマンド ラインで指定されたかのようにこのコマンドを処理します。

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

各 AzCopy パラメーターはそれぞれ 1 行で指定する必要があります。たとえば、次の `/sourcekey` パラメーターで示すように、パラメーターが 2 行に分かれていると、AzCopy は失敗します。

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### 共有アクセス署名 (SAS) の指定

**/sourceSAS オプションを使用してソース コンテナーに SAS を指定する**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**ソース コンテナーの URI でソース コンテナーに SAS を指定する**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**/destSAS オプションを使用して宛先コンテナーに SAS を指定する**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**ソースと宛先のコンテナーに SAS を指定する**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### ジャーナル ファイル フォルダーの指定

AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。

既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。

**既定の場所をジャーナル ファイルに使用する**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

前に示したように、オプション `/Z` を省略するか、フォルダー パスなしでオプション `/Z` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にジャーナル ファイルを作成します。既存のジャーナル ファイルがある場合、このジャーナル ファイルに基づいて AzCopy が操作を再開します。

**ジャーナル ファイルにカスタムの場所を指定する**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

この例では、既存のジャーナル ファイルがない場合に、これを作成します。既存のファイルがある場合、AzCopy はこのジャーナル ファイルに基づいて操作を再開します。

**AzCopy の操作を再開する**

    AzCopy /Z:C:\journalfolder\

この例では、完了できなかった可能性がある直前の操作を再開します。

### ログ ファイルの生成

**詳細ログ ファイルをデフォルトの場所に書き込む**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

冗長ログへのファイル パスを指定せずにオプション `/V` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にログ ファイルを作成します。

**詳細ログ ファイルをカスタムの場所に書き込む**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

`/V:test/azcopy1.log` のように、オプション `/V` に続けて相対パスを指定すると、現在の作業ディレクトリの `test` という名前のサブフォルダー内に詳細ログが作成されます。

### ダウンロードしたファイルの最終変更時刻をソース BLOB と同時刻に設定する

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### 最終変更時刻に基づいてコピー操作の対象から BLOB を除外する

`/MT` オプションを指定して、ソース BLOB と宛先ファイルの最終変更時刻を比較します。

**宛先ファイルより新しい BLOB を除外する**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**宛先ファイルより古い BLOB を除外する**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### 開始する同時操作の数の指定

オプション `/NC` を使用して同時コピー操作の数を指定します。既定では、AzCopy は、環境で使用されているコア プロセッサ数の 8 倍の数の同時操作を開始します。低帯域幅のネットワークで AzCopy を実行している場合は、このオプションに少ない数を指定することで、リソースの競合で生じる失敗を回避できます。

### ストレージ エミュレーターの BLOB リソースに対する AzCopy の実行

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a>AzCopy を使用した Azure ファイル共有でのファイル コピー

以下の例は、AzCopy を使用して Azure ファイルをコピーするさまざまなシナリオを示したものです。

### Azure ファイル共有のファイルをファイル システムにダウンロードする

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (`abc.txt`) を指定して単一ファイルをコピーするか、オプション `/S` を指定して共有内の全ファイルを再帰的にコピーする必要があることに注意してください。ファイル パターンとオプション `/S` の両方を同時に指定しようとすると、エラーになります。

### Azure ファイル共有のファイルおよびフォルダーをファイル システムに再帰的にダウンロードする

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

空のフォルダーはコピーされないことに注意してください。

### ファイル システムのファイルおよびフォルダーを Azure ファイル共有に再帰的にアップロードする

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

空のフォルダーはコピーされないことに注意してください。

### 指定のファイル パターンに一致するファイルを Azure ファイル共有に再帰的にアップロードする

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a>AzCopy のバージョン

| バージョン | 新機能                                                                                                                                   |
|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **最新バージョン。大規模なコピーのシナリオでのパフォーマンスが最大化され、使いやすさの点でいくつかの重要な改善が加えられました。**       |
| V2.4.1     | インストール ウィザードでの宛先フォルダーの指定をサポートします。                                                                        |
| V2.4.0     | Azure ファイル ストレージに対するファイルのアップロードとダウンロードをサポートします。                                                  |
| V2.3.0     | Geo 冗長ストレージ アカウントの読み取りアクセスをサポートします。                                                                        |
| V2.2.2     | Azure ストレージ クライアント ライブラリのバージョン 3.0.3 を使用できるようにアップグレードされました。                                  |
| V2.2.1     | 同一ストレージ アカウント内での大量のファイル コピーで発生していたパフォーマンスの問題が解決されました。                                 |
| V2.2       | BLOB 名に対して仮想ディレクトリの区切り記号を設定する操作がサポートされています。ジャーナル ファイルのパスの指定がサポートされています。 |
| V2.1       | 効果的な BLOB のアップロード、ダウンロード、およびコピーのために 20 個を超えるオプションが提供されています。                             |

## <span id="next-steps"></span></a>次のステップ

Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

### Azure Storage のドキュメント:

-   [Microsoft Azure ストレージの概要][Microsoft Azure ストレージの概要]
-   [How to use Blob Storage from .NET (.NET での BLOB ストレージの使用方法)][How to use Blob Storage from .NET (.NET での BLOB ストレージの使用方法)]
-   [How to Use Azure File storage (Azure ファイル ストレージの使用方法)][How to Use Azure File storage (Azure ファイル ストレージの使用方法)]

### Azure Storage に関するブログの投稿:

-   [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)][Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)]
-   [大量のコピーのシナリオでの最適化が実現した AzCopy 2.5 に関する記事][大量のコピーのシナリオでの最適化が実現した AzCopy 2.5 に関する記事]
-   [AzCopy - Support for read-access geo-redundant storage (AzCopy - Geo 冗長ストレージの読み取りアクセスのサポート)][AzCopy - Support for read-access geo-redundant storage (AzCopy - Geo 冗長ストレージの読み取りアクセスのサポート)]
-   [AzCopy - Transfer data with re-startable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)][AzCopy - Transfer data with re-startable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)]
-   [AzCopy - Using cross-account Copy Blob (AzCopy - アカウント間での BLOB のコピー)][AzCopy - Using cross-account Copy Blob (AzCopy - アカウント間での BLOB のコピー)]
-   [AzCopy - Uploading/downloading files for Windows Azure Blobs (AzCopy - Windows Azure BLOB に対するファイルのアップロードおよびダウンロード)][AzCopy - Uploading/downloading files for Windows Azure Blobs (AzCopy - Windows Azure BLOB に対するファイルのアップロードおよびダウンロード)]

  [AzCopy のダウンロードとインストール]: #install
  [AzCopy コマンド ライン構文の理解]: #syntax
  [データのコピー中の同時書き込みの制限]: #limit-writes
  [AzCopy を使用した Azure BLOB のコピー]: #copy-blobs
  [AzCopy を使用した Azure ファイル共有でのファイル コピー]: #copy-files
  [AzCopy のバージョン]: #versions
  [次のステップ]: #next-steps
  [最新バージョンの AzCopy]: http://aka.ms/downloadazcopy
  [Introducing Asynchronous Cross-Account Copy Blob (非同期アカウント間での BLOB のコピーについて)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Microsoft Azure ストレージの概要]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-introduction/
  [How to use Blob Storage from .NET (.NET での BLOB ストレージの使用方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/
  [How to Use Azure File storage (Azure ファイル ストレージの使用方法)]: http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-files/
  [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [大量のコピーのシナリオでの最適化が実現した AzCopy 2.5 に関する記事]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy - Support for read-access geo-redundant storage (AzCopy - Geo 冗長ストレージの読み取りアクセスのサポート)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy - Transfer data with re-startable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy - Using cross-account Copy Blob (AzCopy - アカウント間での BLOB のコピー)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy - Uploading/downloading files for Windows Azure Blobs (AzCopy - Windows Azure BLOB に対するファイルのアップロードおよびダウンロード)]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
