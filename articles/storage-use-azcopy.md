<properties urlDisplayName="AzCopy" pageTitle="Microsoft Azure Storage で AzCopy を使用する方法" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# AzCopy コマンド ライン ユーティリティの概要

AzCopy は、Microsoft Azure の BLOB、ファイル、およびテーブル ストレージ間のデータのアップロード、ダウンロード、コピーにおいて高いパフォーマンスを実現するために設計されたコマンド ライン ユーティリティです。このガイドでは、AzCopy の使用に関する概要を説明します。

> [WACOM.NOTE] このガイドでは、AzCopy 3.0.0 以降がインストール済みであることを前提としています。現在、AzCopy 3.x が一般公開されています。<br /> 
> また、このガイドでは、AzCopy のプレビュー リリースである AzCopy 4.0.0 についても取り上げます。このガイド内では、プレビュー リリースでのみ提供される機能を*プレビュー*と明示します。<br />
> AzCopy 4.x に関しては、コマンド ラインのオプションとその機能が今後のリリースで変更される可能性があるため、注意してください。

##目次

- [AzCopy のダウンロードとインストール](#install)
- [AzCopy コマンド ライン構文の理解](#syntax)
- [データのコピー中の同時書き込みの制限](#limit-writes)
- [AzCopy を使用した Azure BLOB のコピー](#copy-blobs)
- [AzCopy を使用した Azure ファイル共有でのファイル コピー &#40;プレビュー バージョンのみ&#41;](#copy-files)
- [AzCopy を使用した Azure テーブル エンティティのコピー &#40;プレビュー バージョンのみ&#41;](#copy-entities)
- [AzCopy のバージョン](#versions)
- [次のステップ](#next-steps)

##<a id="install"></a> AzCopy のダウンロードとインストール

1. [AzCopy の最新バージョン](http://aka.ms/downloadazcopy)または[最新のプレビュー バージョン](http://aka.ms/downloadazcopypr)をダウンロードします。
2. インストールを実行します。既定では、AzCopy をインストールすると、`AzCopy` という名前のフォルダーが、`%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (64 ビット Windows を実行しているコンピューターの場合) または `%ProgramFiles%\Microsoft SDKs\Azure\` (32 ビット Windows を実行しているコンピューターの場合) に作成されます。ただし、インストール パスはセットアップ ウィザードで変更できます。
3. 必要に応じて、AzCopy のインストール先をシステム パスに追加できます。

##<a id="syntax"></a> AzCopy コマンド ライン構文の理解

次に、コマンド ウィンドウを開き、`AzCopy.exe` 実行可能ファイルが格納されている、コンピューターの AzCopy インストール ディレクトリに移動します。AzCopy コマンドの基本構文は次のとおりです。

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] AzCopy Version 3.0.0 より、AzCopy コマンド ライン構文では、すべてのパラメーターをパラメーター名と共に指定する必要があります (例: `/ParameterName:ParameterValue`)。

以下の表に、AzCopy 用パラメーターを示します。AzCopy の使用中にコマンド ラインから以下のコマンドのいずれかを入力して、ヘルプを表示することもできます。

- AzCopy に関する詳細なコマンド ライン ヘルプを表示する場合:`AzCopy /?`
- AzCopy パラメーターに関する詳細なヘルプを表示する場合:`AzCopy /?:SourceKey`
- コマンド ラインの例を表示する場合:`AzCopy /?:Samples` 


<table>
  <tr>
    <th>オプション名</th>
    <th>説明</th>
    <th>BLOB ストレージへの適用の有無 (Y/N)</th>
    <th>ファイル ストレージへの適用の有無 (Y/N) (プレビュー リリースのみ)</th>
    <th>テーブル ストレージへの適用の有無 (Y/N) (プレビュー リリースのみ)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source&gt;</b></td>
    <td>コピー元となるソース データを指定します。ソースには、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、ストレージ ファイル共有、ストレージ ファイルのディレクトリ、または Azure テーブルを指定できます。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination&gt;</b></td>
    <td>コピー先を指定します。コピー先には、ファイル システムのディレクトリ、BLOB コンテナー、BLOB 仮想ディレクトリ、ストレージ ファイル共有、ストレージ ファイル ディレクトリ、または Azure テーブルを指定できます。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern&gt;</b></td>
      <td>
          コピーするファイルを示すファイル パターンを指定します。/Pattern パラメーターの動作は、ソース データの場所と再帰モード オプションの有無で決まります。再帰モードは、オプション /S で指定されます。
          <br />
          指定されたソースがファイル システムのディレクトリの場合、標準のワイルドカードが有効となり、指定されたファイル パターンがディレクトリ内のファイルと照合されます。オプション /S が指定されると、AzCopy は、該当するディレクトリの下位にあるすべてのサブフォルダー内の全ファイルについても、指定のパターンと照合します。
          <br />
          指定されたソースが BLOB コンテナーまたは仮想ディレクトリの場合、ワイルドカードは適用されません。オプション&nbsp;/S&nbsp;が指定されると、AzCopy は指定のファイル パターンを BLOB のプレフィックスとして解釈します。オプション&nbsp;/S&nbsp;を指定しない場合は、AzCopy はファイル パターンを BLOB 名そのものと照合します。
          <br />
          指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (abc.txt など) を指定して単一ファイルをコピーするか、オプション&nbsp;/S&nbsp;を指定して共有内の全ファイルを再帰的にコピーする必要があります。ファイル パターンとオプション /S&nbsp;の両方を同時に指定しようとすると、エラーになります。
          <br />
          ファイル パターンを指定しない場合、ファイル システム上の場所で使用される既定のファイル パターンは *.* です。また、Azure Storage 上の場所の場合は空のプレフィックスです。複数のファイル パターンを指定することはサポートされていません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>宛先リソースのストレージのアカウント キーを指定します。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token&gt;</b></td>
    <td class="auto-style1">宛先コンテナーの共有アクセス署名 (SAS) を指定します (適用可能な場合)。特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。<br />
        宛先リソースが BLOB コンテナーまたはテーブルの場合は、このオプションと直後の SAS トークンを指定するか、または、このオプションを指定せずに BLOB の URI の一部として SAS を指定することができます。<br />
        ソースと宛先がどちらも BLOB の場合は、宛先 BLOB がソース BLOB と同じストレージ アカウント内に存在している必要があります。</td>
    <td class="auto-style1">Y</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">Y<br /> (プレビューのみ</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>ソース リソースのストレージのアカウント キーを指定します。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token&gt;</b></td>
    <td>ソース コンテナーの共有アクセス署名を指定します (適用される場合)。特殊なコマンド ライン文字が含まれる可能性があるため、SAS は二重引用符で囲みます。
        <br />
        ソース リソースが BLOB コンテナーで、キーまたは SAS のどちらも指定しない場合、BLOB コンテナーは匿名のアクセスで読み取られます。
        <br />
        ソースがテーブルの場合は、キーまたは SAS を指定する必要があります。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>コピー操作の再帰モードを指定します。再帰モードでは、AzCopy は指定のファイル パターンに一致するすべての BLOB またはファイル (サブフォルダー内を含む) をコピーします。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>宛先 BLOB がブロック BLOB とページ BLOB のどちらであるかを指定します。このオプションは BLOB をアップロードする場合のみに適用され、それ以外の場合はエラーが生成されます。宛先が BLOB でこのオプションを指定しない場合、既定で AzCopy はブロック BLOB を作成します。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>ダウンロードするデータの MD5 ハッシュを計算し、BLOB に格納されている MD5 ハッシュまたはファイルの Content-MD5 プロパティが計算したハッシュに一致するかどうかを検証します。MD5 チェックは既定では無効になっているため、MD5 チェックを実行するにはデータのダウンロード時にこのオプションを指定する必要があります。
	<br />
    Azure ストレージでは、BLOB またはファイルに対して格納される MD5 ハッシュが最新であることは保証されません。BLOB またはファイルが変更された場合、MD5 の更新はクライアントの責任で行う必要があります。
	<br />
    AzCopy は、Azure BLOB またはファイルをサービスにアップロードした後、常にその Content-MD5 プロパティを設定します。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>スナップショットを転送するかどうかを示します。このオプションはソースが BLOB の場合のみに有効です。 
        <br />
        転送する BLOB のスナップショットは、[blob-name] (snapshot-time)[extension] の形式で名前が変更されます。 
        <br />
        スナップショットは既定ではコピーされません。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[verbose log-file]</b></td>
    <td>詳細な状態メッセージをログ ファイルに出力します。既定では、詳細なログ ファイルは、 <code>AzCopyVerbose.log</code>  という名前で、 <code>%LocalAppData%\Microsoft\Azure\AzCopy</code> に格納されます。このオプションで既存のファイルの場所を指定すると、そのファイルに詳細なログが追加されます。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/Z:[journal-file-folder]</b></td>
    <td>操作を再開するジャーナル ファイル フォルダーを指定します。<br />
        操作に割り込みが発生した場合、AzCopy は常に再開をサポートします。<br />
        このオプションを指定しないか、フォルダー パスなしで指定した場合、AzCopy は既定の場所である <code>%LocalAppData%\Microsoft\Azure\AzCopy</code> にジャーナル ファイルを作成します。<br />
        AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。
        <br />
		既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。 
        <br />
        操作が正常に完了すると、ジャーナル ファイルは削除されます。
		<br />
		以前のバージョンの AzCopy で作成されたジャーナル ファイルによる操作の再開はサポートされていません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>パラメーターを含むファイルを指定します。AzCopy は、このファイルのパラメーターを、コマンド ラインで指定された場合と同様に処理します。<br /> 
		応答ファイルでは、複数のパラメーターを単一行に指定することも、各パラメーターをそれぞれの行に指定することもできます。個々のパラメーターを複数行にまたがって指定することはできません。 
        <br />
		応答ファイルには、シンボル  <code>#</code>  で始まるコメント行を含めることができます。 
        <br />
        複数の応答ファイルを指定できます。ただし、入れ子になった応答ファイルはサポートされません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>AzCopy のすべての確認プロンプトを表示しません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>一覧の操作のみ指定します。データはコピーされません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>ダウンロードしたファイルの最後の変更時刻をソースの BLOB またはファイルと同じに設定します。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>新しいソース リソースを除外します。ソースが宛先より新しい場合、リソースはコピーされません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>古いソース リソースを除外します。ソース リソースが宛先リソースより古い場合、リソースはコピーされません。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Archive 属性が設定済みのファイルのみアップロードします。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>指定の属性のいずれかが設定されているファイルのみアップロードします。<br />
        以下の属性が利用可能です。  
        <br />
        R&nbsp;&nbsp;&nbsp;読み取り専用ファイル
        <br />
        A&nbsp;&nbsp;&nbsp;アーカイブ
        <br />
        S&nbsp;&nbsp;&nbsp;システム ファイル
        <br />
        H&nbsp;&nbsp;&nbsp;隠しファイル
        <br />
        C&nbsp;&nbsp;&nbsp;圧縮ファイル
        <br />
        N&nbsp;&nbsp;&nbsp;通常のファイル
        <br />
        E&nbsp;&nbsp;&nbsp;暗号化されたファイル
        <br />
        T&nbsp;&nbsp;&nbsp;一時ファイル
        <br />
        O&nbsp;&nbsp;&nbsp;オフライン ファイル
        <br />
        I&nbsp;&nbsp;&nbsp;非インデックス対象ファイル</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>指定の属性のいずれかが設定されているファイルを除外します。<br />
        以下の属性が利用可能です。  
        <br />
        R&nbsp;&nbsp;&nbsp;読み取り専用ファイル  
        <br />
        A&nbsp;&nbsp;&nbsp;アーカイブ  
        <br />
        S&nbsp;&nbsp;&nbsp;システム ファイル  
        <br />
        H&nbsp;&nbsp;&nbsp;隠しファイル  
        <br />
        C&nbsp;&nbsp;&nbsp;圧縮ファイル  
        <br />
        N&nbsp;&nbsp;&nbsp;通常のファイル  
        <br />
        E&nbsp;&nbsp;&nbsp;暗号化されたファイル  
        <br />
        T&nbsp;&nbsp;&nbsp;一時ファイル  
        <br />
        O&nbsp;&nbsp;&nbsp;オフライン ファイル  
        <br />
        I&nbsp;&nbsp;&nbsp;非インデックス対象ファイル</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>BLOB 名で仮想ディレクトリを区切るために使用する区切り文字を示します。<br />
        既定では、AzCopy は区切り文字として / を使用します。ただし、AzCopy は、一般的な文字である @、#、または % の区切り文字としての使用もサポートします。この特殊文字のいずれかをコマンド ラインに含める必要がある場合は、ファイル名を二重引用符で囲みます。 
        <br />
        このオプションは BLOB のダウンロードのみに適用されます。</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents&gt;</b></td>
    <td>同時操作の数を指定します。
        <br />
        AzCopy は既定で、データ転送のスループットを向上するために一定数の同時操作を開始します。低帯域幅の環境で大量に同時操作を実行すると、接続するネットワークに過剰な負荷がかかり、操作を完了できなくなる場合があります。実際に利用可能なネットワーク帯域幅に適した同時操作数になるように調整してください。
        <br />
		同時操作の上限は 512 です。</td>
    <td>Y</td>
    <td>Y<br /> (プレビューのみ)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB を  <code>source</code>  リソースに指定します。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>ストレージ エミュレーターで実行されている、ローカル開発環境で利用可能な BLOB を <code>destination</code> リソースに指定します。</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;&quot;key1#key2#key3#...&quot;&gt;</strong></td>
    <td>テーブル データを並行してエクスポートできるようにパーティション キーの範囲を分割します。これによって、エクスポート操作の速度が速くなります。
        <br />
        このオプションを指定しない場合、AzCopy はシングル スレッドを使用してテーブル エンティティをエクスポートします。たとえば、ユーザーが /PKRS:&quot;aa#bb&quot;, と指定した場合、AzCopy は 3 つの同時操作を開始します。
        <br />
        以下のとおり、1 つの操作で、3 つのパーティション キー範囲の 1 つがエクスポートされます。 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;&lt;first partition key&gt;, aa&#41; 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;aa, bb&#41;
        <br />
        &nbsp;&nbsp;&nbsp;&#91;bb, &lt;last partition key&gt;&#93; </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size&gt;</strong></td>
    <td>エクスポートされるファイルの分割サイズを MB 単位で指定します。
        <br />
        このオプションを指定しない場合、AzCopy はテーブル データを 1 つのファイルにエクスポートします。
        <br />
        テーブル データを BLOB にエクスポートする場合、エクスポートされるファイルのサイズが BLOB サイズの上限である 200 GB に達していると、このオプションが指定されていなくても、エクスポートされるファイルが分割されます。 </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace&gt;
</b>
</td>
    <td>テーブル データのインポート動作を指定します。
        <br />
        InsertOrSkip - テーブルにエンティティが存在する場合はスキップし、存在しない場合は新しいエンティティを挿入します。
        <br />
        InsertOrMerge - テーブルにエンティティが存在する場合はマージし、存在しない場合は新しいエンティティを挿入します。
        <br />
        InsertOrReplace - テーブルにエンティティが存在する場合は置換し、存在しない場合は新しいエンティティを挿入します。 </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file&gt;</b></td>
    <td>インポート操作用のマニフェスト ファイルを指定します。 <br />
        マニフェスト ファイルはエクスポート操作中に生成されます。</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (プレビューのみ)</td>
  </tr>
</table>

<br/>

##<a id="limit-writes"></a> データのコピー中の同時書き込みの制限

AzCopy を使用して BLOB またはファイルをコピーする場合は、コピー中に別のアプリケーションがそのデータを変更している可能性があることに注意してください。可能な場合は、コピーしているデータがコピー操作中に変更されないようにしてください。たとえば、Azure 仮想マシンに関連付けられている VHD をコピーしている場合は、その VHD に別のアプリケーションが書き込み中でないことを確認します。または、最初に VHD のスナップショットを作成してからそのスナップショットをコピーします。

コピーしている BLOB またはファイルに対する他のアプリケーションによる書き込みを回避できない場合は、ジョブが終了した時点で、コピー対象のリソースがソース リソースとの間に完全なパリティを保持していない可能性があることを覚えておいてください。

##<a id="copy-blobs"></a> AzCopy を使用した Azure BLOB のコピー

以下の例は、AzCopy を使用して BLOB をコピーするさまざまなシナリオを示したものです。

### 単一の BLOB のコピー

**ファイル システムのファイルを BLOB ストレージにアップロードする場合:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**BLOB ストレージの BLOB をファイル システムにダウンロードする場合:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### サーバー側コピーを通じた BLOB のコピー

BLOB を 1 つのストレージ アカウント内または複数のストレージ アカウント間でコピーする場合は、サーバー側でコピー操作が実行されます。サーバー側のコピー操作の詳細については、「[Introducing Asynchronous Cross-Account Copy Blob (非同期アカウント間での BLOB のコピーについて)]」を参照してください(http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx)。

**1 つのストレージ アカウント内で BLOB をコピーする場合:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**複数のストレージ アカウント間で BLOB をコピーする場合:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### セカンダリ リージョンの BLOB のコピー 

ストレージ アカウントで地理冗長ストレージの読み取りアクセスが有効に設定されている場合は、セカンダリ リージョンのデータをコピーできます。 

**セカンダリの BLOB をプライマリ アカウントにコピーする場合:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**セカンダリの BLOB をファイル システムのファイルにダウンロードする場合:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### 新しい BLOB コンテナーまたは仮想ディレクトリへのファイルのアップロード

**新しい BLOB コンテナーにファイルをアップロードする**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

存在しない宛先コンテナーを指定すると、AzCopy によってコンテナーが作成され、そのコンテナーにファイルがアップロードされることに注意してください。

**新しい BLOB 仮想ディレクトリにファイルをアップロードする**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

存在しない仮想ディレクトリを指定すると、アップロードされるファイルの名前に仮想ディレクトリが含められることに注意してください (*例*: 上の例の `vd/abc.txt`)。

### 新しいフォルダーへの BLOB のダウンロード

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

フォルダー `C:\myfolder` が存在しない場合、AzCopy によってファイル システム内にフォルダーが作成され、この新しいフォルダーに `abc.txt ` がダウンロードされます。

### ディレクトリ内のファイルおよびサブフォルダーをコンテナーに再帰的にアップロードする

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

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

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

コピー操作後、ターゲット コンテナーには BLOB とそのスナップショットが含まれます。前の例の BLOB に 2 つのスナップショットが含まれる場合、コンテナーには以下の BLOB とスナップショットが含まれます。

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### コマンド ライン パラメーターを指定するための応答ファイルの使用

	AzCopy /@:"C:\myfolder\abc.txt"

応答ファイルには、任意の AzCopy コマンド ライン パラメーターを含めることができます。AzCopy は、ファイルの内容に直接置換することで、ファイルのパラメーターをコマンド ラインで指定されているかのように処理します。

**1 つ以上の単一行の応答ファイルを指定する**

`source.txt` という名前の応答ファイルが、ソース コンテナーを次のように指定するとします。

	/Source:http://myaccount.blob.core.windows.net/mycontainer

さらに、`dest.txt` という名前の応答ファイルが、ファイル システムの宛先フォルダーを指定します。

	/Dest:C:\myfolder

また、`options.txt` という名前の応答ファイルは、AzCopy のオプションを次のように指定します。

	/S /Y

ディレクトリ `C:\responsefiles` に格納されているこれらの応答ファイルすべてを使用して AzCopy を呼び出すには、次のコマンドを使用します。

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy は次のように、個々のパラメーターがすべてコマンド ラインで指定されたかのようにこのコマンドを処理します。

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**複数行の応答ファイルを指定する**

`copyoperation.txt` という名前のファイルに、以下の行が含まれているとします。AzCopy の各パラメーターは次のように、それぞれ個別の行に指定されます。

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

この応答ファイルを使用して AzCopy を呼び出すには、次のコマンドを使用します。

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy は次のように、個々のパラメーターがすべてコマンド ラインで指定されたかのようにこのコマンドを処理します。	

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

各 AzCopy パラメーターはそれぞれ 1 行で指定する必要があります。たとえば、次の `/sourcekey` パラメーターで示すように、パラメーターが 2 行に分かれていると、AzCopy は失敗します。

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### 共有アクセス署名 (SAS) の指定
	
**/sourceSAS オプションを使用してソース コンテナーに SAS を指定する**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**ソース コンテナーの URI でソース コンテナーに SAS を指定する**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**/destSAS オプションを使用して宛先コンテナーに SAS を指定する**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**ソースと宛先のコンテナーに SAS を指定する**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### ジャーナル ファイル フォルダーの指定

AzCopy にコマンドが発行されるたびに、AzCopy は既定のフォルダーまたはこのオプションで指定されたフォルダーにジャーナル ファイルが存在するかどうかを確認します。どちらの場所にもジャーナル ファイルがない場合は、AzCopy は新しい操作であると認識し、新しいジャーナル ファイルを作成します。

既存のジャーナル ファイルがある場合は、AzCopy は入力されたコマンド ラインがジャーナル ファイルのコマンド ラインと一致するかどうかを確認します。2 つのコマンド ラインが一致する場合、AzCopy は未完了の操作を再開します。一致しない場合は、ジャーナル ファイルを上書きして新しい操作を開始するか、現在の操作を取り消すかをたずねるメッセージが表示されます。 

**既定の場所をジャーナル ファイルに使用する**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

前に示したように、オプション `/Z` を省略するか、フォルダー パスなしでオプション `/Z` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にジャーナル ファイルを作成します。既存のジャーナル ファイルがある場合、このジャーナル ファイルに基づいて AzCopy が操作を再開します。 

**ジャーナル ファイルにカスタムの場所を指定する**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

この例では、既存のジャーナル ファイルがない場合に、これを作成します。既存のファイルがある場合、AzCopy はこのジャーナル ファイルに基づいて操作を再開します。

**AzCopy の操作を再開する**

	AzCopy /Z:C:\journalfolder\

この例では、完了できなかった可能性がある直前の操作を再開します。


### ログ ファイルの生成

**詳細ログ ファイルをデフォルトの場所に書き込む**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

詳細ログへのファイル パスを指定せずにオプション `/V` を指定すると、AzCopy は既定の場所である `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy` にログ ファイルを作成します。

**詳細ログ ファイルをカスタムの場所に書き込む**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

`/V:test/azcopy1.log` のように、オプション `/V` に続けて相対パスを指定すると、現在の作業ディレクトリの `test` という名前のサブフォルダー内に詳細ログが作成されます。


### ダウンロードしたファイルの最終変更時刻をソース BLOB と同時刻に設定する

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### 最終変更時刻に基づいてコピー操作の対象から BLOB を除外する

`/MT` オプションを指定して、ソース BLOB と宛先ファイルの最終変更時刻を比較します。

**宛先ファイルより新しい BLOB を除外する**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**宛先ファイルより古い BLOB を除外する**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### 開始する同時操作の数の指定

オプション `/NC` を使用して同時コピー操作の数を指定します。既定では、AzCopy は、環境で使用されているコア プロセッサ数の 8 倍の数の同時操作を開始します。低帯域幅のネットワークで AzCopy を実行している場合は、このオプションに少ない数を指定することで、リソースの競合で生じる失敗を回避できます。


###	ストレージ エミュレーターの BLOB リソースに対する AzCopy の実行

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S


##<a id="copy-files"></a> AzCopy を使用した Azure ファイル ストレージでのファイル コピー (プレビュー バージョンのみ)

以下の例は、AzCopy を使用して Azure ファイルをコピーするさまざまなシナリオを示したものです。

### Azure ファイル共有のファイルをファイル システムにダウンロードする

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

指定されたソースが Azure ファイル共有の場合は、厳密なファイル名 (*例:* `abc.txt`) を指定して単一ファイルをコピーするか、オプション /S を指定して共有内の全ファイルを再帰的にコピーする必要があることに注意してください。ファイル パターンとオプション `/S` の両方を同時に指定しようとすると、エラーになります。

### Azure ファイル共有のファイルおよびフォルダーをファイル システムに再帰的にダウンロードする

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

空のフォルダーはコピーされないことに注意してください。


### ファイル システムのファイルおよびフォルダーを Azure ファイル共有に再帰的にアップロードする

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

空のフォルダーはコピーされないことに注意してください。


### 指定のファイル パターンに一致するファイルを Azure ファイル共有に再帰的にアップロードする

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

##<a id="copy-entities"></a> AzCopy を使用した Azure テーブルでのエンティティのコピー (プレビュー バージョンのみ)

以下の例は、AzCopy を使用して Azure テーブル エンティティをコピーするさまざまなシナリオを示したものです。

### エンティティをローカル ファイル システムにエクスポートする

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### エンティティを Azure BLOB にエクスポートする

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy は、次の名前付け規則を使用して JSON データ ファイルをローカル フォルダーまたは BLOB コンテナーに生成します。

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

生成される JSON データ ファイルは、最小のメタデータ用のペイロード形式に準じます。このペイロード形式の詳細については、「[テーブル サービス操作のペイロード形式](http://msdn.microsoft.com/library/azure/dn535600.aspx)」を参照してください。

### エクスポート ファイルを分割する

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy では、複数のファイルを区別できるように、分割したデータ ファイル名に*ボリューム インデックス*を含めます。ボリューム インデックスは、*パーティション キー範囲のインデックス*と*分割されたファイルのインデックス*の 2 つの部分で構成されます。どちらのインデックスも 0 から始まります。

パーティション キー範囲のインデックスは、ユーザーがオプション `/PKRS` を指定しなかった場合、0 に設定されます (次のセクションで説明します)。

たとえば、ユーザーがオプション `/SplitSize` を指定した後に、AzCopy によって 2 つのデータ ファイルが生成されたとします。生成されたデータ ファイルの名前は次のようになります。

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

オプション `/SplitSize` の最小の値は 32 MB です。指定された宛先が BLOB ストレージの場合、データ ファイルのサイズが BLOB サイズの上限 (200 GB) に達すると、ユーザーがオプション `/SplitSize` を指定しているかどうかにかかわらず、AzCopy によってデータ ファイルが分割されます。

### エンティティを並列処理でエクスポートする

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

ユーザーがオプション `/PKRS` を指定すると、AzCopy によってエンティティをエクスポートするための同時操作が開始されます。1 つの操作で 1 つのパーティション キー範囲がエクスポートされます。

同時操作の数は、オプション `/NC` にも制御されます。`/NC` が指定されていない場合でも、AzCopy はコア プロセッサ数を `/NC` の既定値として使用してテーブル エンティティをコピーします。ユーザーがオプション `/PKRS` を指定した場合、AzCopy は、パーティション キー範囲と明示的または暗黙的に指定された同時操作数の 2 つの値のうち小さいほうを使用して、開始する同時操作の数を決定します。詳細については、コマンド ラインに「`AzCopy /?:NC`」と入力してください。

### エンティティを並列処理でインポートする

	AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

テーブル エンティティをエクスポートすると、指定した宛先フォルダーまたは BLOB コンテナーにマニフェスト ファイルが作成されます。このマニフェスト ファイルは、インポート処理中に、必要なデータ ファイルを特定し、データの検証を実行するために使用されます。マニフェスト ファイルでは、次の名前付け規則が使用されます。

	<account name>_<table name>_<timestamp>.manifest

オプション `/EntityOperation` は、エンティティをテーブルに挿入する方法を示します。次のいずれかの値になります。

- `InsertOrSkip`: テーブルにエンティティが存在する場合はスキップし、存在しない場合は新しいエンティティを挿入します。
- `InsertOrMerge`: テーブルにエンティティが存在する場合はマージし、存在しない場合は新しいエンティティを挿入します。
- `InsertOrReplace`: テーブルにエンティティが存在する場合は置換し、存在しない場合は新しいエンティティを挿入します。

インポート シナリオでは、オプション `/PKRS` を指定できません。同時操作を開始するためにオプション `/PKRS` を指定する必要があるエクスポート シナリオとは違って、エンティティをインポートすると、既定で同時操作が開始されます。開始される同時操作の既定数は、コア プロセッサの数と同じです。ただし、オプション `/NC` を使用して別の同時操作数を指定できます。詳細については、コマンド ラインに「`AzCopy /?:NC`」と入力してください。


##<a id="versions"></a> AzCopy のバージョン

| バージョン | 新機能                                                                                      				|
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.0.0**  | **現在のプレビュー バージョンV3.0.0 のすべての機能が含まれています。Azure ファイル ストレージとの間のファイルのコピーと Azure テーブル ストレージとの間のエンティティのコピーもサポートしています。**	
| **V3.0.0**  | **現在のリリース バージョンAzCopy コマンド ライン構文が変更され、パラメーター名が必要になりました。また、コマンド ライン ヘルプが再設計されています。このバージョンでは、Azure BLOB ストレージとの間のコピーのみをサポートしています。**	
| V2.5.1  | オプション /xo と /xn を使用したときのパフォーマンスが最適化されました。ソース ファイル名に含まれた特殊文字に関連するバグと、ユーザーが誤ったコマンド ライン構文を入力した後のジャーナル ファイルの損傷が解決されました。	
| V2.5.0  | 大規模なコピーのシナリオでのパフォーマンスが最大化され、使いやすさの点でいくつかの重要な改善が加えられました。	
| V2.4.1  | インストール ウィザードでの宛先フォルダーの指定をサポートします。                     			
| V2.4.0  | Azure ファイル ストレージに対するファイルのアップロードとダウンロードをサポートします。                       				                              
| V2.3.0  | Geo 冗長ストレージ アカウントの読み取りアクセスをサポートします。                                                  			|
| V2.2.2  | Azure ストレージ クライアント ライブラリの Version 3.0.3 を使用できるようにアップグレードされました。                                            				                    
| V2.2.1  | 同一ストレージ アカウント内での大量のファイル コピーで発生していたパフォーマンスの問題が解決されました。            				                                                
| V2.2    | BLOB 名に対して仮想ディレクトリの区切り記号を設定する操作がサポートされています。ジャーナル ファイルのパスの指定がサポートされています。		|
| V2.1    | 効果的な BLOB のアップロード、ダウンロード、およびコピーのために 20 個を超えるオプションが提供されています。		|


##<a id="next-steps"></a> 次のステップ

Azure Storage および AzCopy の詳細については、以下のリソースを参照してください。

### Azure Storage のドキュメント:

- [Microsoft Azure Storage の概要](http://azure.microsoft.com/ja-jp/documentation/articles/storage-introduction/)
- [How to use Blob Storage from .NET (.NET での BLOB ストレージの使用方法)](http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-blobs/)
- [How to Use Azure File storage (Azure ファイル ストレージの使用方法)](http://azure.microsoft.com/ja-jp/documentation/articles/storage-dotnet-how-to-use-files/)

### Azure Storage に関するブログの投稿:

- [AzCopy 3.0:Announcing General Availability of AzCopy 3.0 plus preview release of AzCopy 4.0 with Table and File support (AzCopy 3.0 の一般公開とテーブルおよびファイルのサポートを伴う AzCopy 4.0 のプレビュー リリースのお知らせ)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy 2.5:Optimized for Large-Scale Copy Scenarios (大量のコピーのシナリオでの最適化が実現した AzCopy 2.5)](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Introducing Microsoft Azure File Service (Microsoft Azure File サービスの概要)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy:Support for read-access geo-redundant storage (AzCopy - Geo 冗長ストレージの読み取りアクセスのサポート)](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy:Transfer data with re-startable mode and SAS token (AzCopy - 再起動可能モードまたは SAS トークンを使用したデータの転送)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy:Using cross-account Copy Blob (AzCopy - アカウント間での BLOB のコピー)](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy:Uploading/downloading files for Windows Azure Blobs (AzCopy - Windows Azure BLOB に対するファイルのアップロードおよびダウンロード)](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


<!--HONumber=35_1-->
