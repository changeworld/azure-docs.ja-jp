<properties 
   pageTitle="Aure Automation の Runbook の概念"
   description="Azure Automation の Runbook を作成する上で理解する必要のある基本的な概念を説明します。"
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/13/2015"
   ms.author="bwren" />

# Azure Automation の Runbook の概念

Azure Automation の Runbook は Windows PowerShell ワークフローとして実装されています。このセクションでは、Automation の Runbook に共通のワークフローの重要な特徴に関する簡単な概要を説明します。ワークフローの詳細については、「[Windows PowerShell ワークフローについて](http://technet.microsoft.com/library/jj134242.aspx)」を参照してください。


## Windows PowerShell ワークフロー

ワークフローとは、時間のかかるタスクを実行する手順や、複数のデバイスまたは管理されたノード間で複数の手順の調整を必要とする手順をプログラミングで連結した手順のシーケンスです。通常のスクリプトに比べて、ワークフローの利点には、複数のデバイスに対して同時に操作を実行でき、エラーから自動的に回復できることが含まれます。Windows PowerShell ワークフローは、Windows Workflow Foundation を活用する Windows PowerShell スクリプトです。ワークフローは Windows PowerShell の構文で記述され、Windows PowerShell によって起動されますが、Windows Workflow Foundation によって処理されます。

### 基本構造

Windows PowerShell ワークフローは、**Workflow** キーワードで始まり、中かっこで囲まれたスクリプトの本文が後に続きます。次の構文に示すように、ワークフローの名前は **Workflow** キーワードの後に続きます。ワークフローの名前は、Automation の Runbook の名前と一致します。

    Workflow Test-Runbook
    {
       <Commands>
    }

次の構文に示すように、ワークフローにパラメーターを追加するには、**Param** キーワードを使用します。管理ポータルは、Runbook 開始時に、これらのパラメーターの値の入力をユーザーに求めます。このサンプルでは、パラメーターが必須かどうかを指定するオプションのパラメーター属性を使用します。

    Workflow Test-Runbook
    {
      Param
      (
       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>,

       [Parameter(Mandatory=<$True | $False>]
       [Type]$<ParameterName>
      )
      <Commands>
    }

### 名前を付ける

ワークフローの名前は、Windows PowerShell での標準である、動詞と名詞を組み合わせた形式に従う必要があります。使用する承認された動詞の一覧については、「[Windows PowerShell コマンドの承認された動詞](http://msdn.microsoft.com/library/windows/desktop/ms714428(v=vs.85).aspx)」を参照してください。ワークフローの名前は、Automation の Runbook の名前と一致する必要があります。Runbook がインポートされている場合、ファイル名はワークフロー名と一致していなければならず、末尾は .ps1 でなければなりません。

### 制限事項

Windows PowerShell ワークフローと Windows PowerShell の制限事項と構文の違いの詳細については、「[スクリプト ワークフローとスクリプトの構文上の違い](http://technet.microsoft.com/library/jj574140.aspx)」を参照してください。

## アクティビティ

アクティビティとは、ワークフロー内の特定のタスクです。スクリプトが 1 つまたは複数のコマンドで構成されるのと同様、ワークフローはシーケンスで実行される 1 つまたは複数のアクティビティで構成されます。Windows PowerShell ワークフローは、ワークフローの実行時に多くの Windows PowerShell コマンドレットをアクティビティに自動的に変換します。Runbook でこれらのコマンドレットのいずれかを指定すると、対応するアクティビティは、実際には Windows Workflow Foundation で実行されます。対応するアクティビティがないコマンドレットの場合、Windows PowerShell ワークフローは [InlineScript](#inlinescript) アクティビティ内のコマンドレットを自動的に実行します。InlineScript ブロックに明示的に含めないと除外され、ワークフローでは使用できない一連のコマンドレットがあります。これらの概念の詳細については、「[スクリプト ワークフローでのアクティビティの使用](http://technet.microsoft.com/library/jj574194.aspx)」を参照してください。

ワークフロー アクティビティは、操作を構成するための一連の共通パラメーターを共有します。ワークフローの共通パラメーターの詳細については、「[about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx)」を参照してください。

## 統合モジュール

*統合モジュール*は、Windows PowerShell モジュールを含むパッケージであり、Azure Automation にインポートできます。Azure Automation にインポートされた統合モジュールのコマンドレットは、同じ Automation アカウントのすべての Runbook から自動的に利用可能です。Azure Automation は Windows PowerShell 4.0 に基づいているため、モジュールの自動読み込みをサポートします。これはつまり、[Import-Module](http://technet.microsoft.com/library/hh849725.aspx) が含まれるスクリプトにインポートせずに、インストールされたモジュールからのコマンドレットを使用できるということです。

## 並列実行

Windows PowerShell ワークフローの利点の 1 つは、一般的なスクリプトのように順番に実行するのでなく、一連のコマンドを並行して実行できることです。Runbook では、完了するのに長時間かかる複数の操作を実行する場合があるので特に便利です。たとえば、Runbook では一連の仮想マシンをプロビジョニングする場合があります。各プロビジョニング プロセスを順番に実行するのではなく、操作を同時に実行することによって全体の効率性が向上します。すべてが完了しないと Runbook は続行されません。

**Parallel** キーワードを使用して、同時に実行される複数のコマンドを含むスクリプト ブロックを作成できます。これには、次に示す構文を使用します。この場合は、Activity1 と Activity2 が同時に開始されます。Activity3 は、Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    Parallel
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

**ForEach -Parallel** の構文を使用することにより、コレクション内の各項目のコマンドを同時に処理できます。コレクション内の項目は並行して処理され、スクリプト ブロック内のコマンドは順番に実行されます。これには、次に示す構文を使用します。この場合、Activity1 はコレクション内のすべての項目と同時に開始されます。各項目について、Activity2 は Activity1 が完了した後に開始されます。Activity3 は、すべての項目における Activity1 と Activity2 の両方が完了した後にのみ開始されます。

    ForEach -Parallel ($<item> in $<collection>)
    {
      <Activity1>
      <Activity2>
    }
    <Activity3>

**Sequence** キーワードは、**Parallel** スクリプト ブロック内のコマンドを順番に実行するために使用されます。**Sequence** スクリプト ブロックは他のコマンドと並行して実行されますが、ブロック内のコマンドは順番に実行されます。これには、次に示す構文を使用します。この場合、Activity1、Activity2、および Activity3 は同時に開始されます。Activity4 は、Activity3 が完了した後にのみ開始されます。Activity5 は、他のすべてのアクティビティが完了した後にのみ開始されます。

    Parallel
    {
      <Activity1>
      <Activity2>

      Sequence 
      { 
        <Activity3>  
        <Activity4>
      }

    }
    <Activity5>

## チェックポイント

*チェックポイント* は、変数の現在の値と、そのポイントに生成された出力を含むワークフローの現在の状態のスナップショットです。Runbook で最後に完了するチェックポイントは、Automation データベースに保存され、実行時にコンピューターの停止などの問題が発生した場合にワークフローによって再開できるようにします。チェックポイントがないと、ワークフローは最初から開始されます。Runbook ジョブが完了したら、チェックポイントのデータが削除されます。

**Checkpoint-Workflow** アクティビティを使用してワークフローにチェックポイントを設定できます。Runbook にこのアクティビティを追加すると、チェックポイントがすぐに作成されます。例外によって Runbook が中断された場合、ジョブが再開されるときには、最後のチェックポイント セットから再開されます。

次のサンプル コードでは、Activity2 の後に例外が発生し、Runbook が中断されます。これは、設定されている最後のチェックポイントのすぐ後だったため、ジョブを再開する際、Activity2 を実行することによって開始します。

    <Activity1>
    Checkpoint-Workflow
    <Activity2>
    <Exception>
    <Activity3>

例外を引き起こす可能性のあるアクティビティの後にチェックポイントを Runbook に設定する必要があり、Runbook が再開された場合は繰り返すべきではありません。たとえば、Runbook によって仮想マシンが作成される場合があります。チェックポイントをコマンドの前後に設定して、仮想マシンを作成できます。作成が失敗すると、Runbook が再開するときにコマンドが繰り返されます。作成に成功したものの Runbook が後で失敗すると、Runbook が再開したときに仮想マシンは作成されません。

チェックポイントの詳細については、「[スクリプト ワークフローへのチェックポイントの追加](http://technet.microsoft.com/library/jj574114.aspx)」を参照してください。

## ワークフローの中断

**Suspend-Workflow** アクティビティによって、Runbook がそれ自体を中断できるよう強制できます。このアクティビティはチェックポイントを設定し、ワークフローがすぐに中断されるようにします。ワークフローの中断は、別のアクティビティのセットが実行される前に実行される可能性のある手動の手順を必要とする Runbook に便利です。

ワークフローの中断の詳細については、「[それ自体を中断できるようにワークフローを強制する](http://technet.microsoft.com/library/jj574175.aspx)」を参照してください。

## InlineScript

**InlineScript** アクティビティは、PowerShell ワークフローではなく、従来の PowerShell スクリプトでコマンドのブロックを実行し、その出力をワークフローに返します。ワークフロー内のコマンドは Windows Workflow Foundation に送信されて処理され、InlineScript ブロック内のコマンドは Windows PowerShell によって処理されます。このアクティビティは、コード ブロックを代替の資格情報を使って実行するよう指定できる **PSCredential** を含む標準的なワークフローの共通パラメーターを使用します。

InlineScript は、次に示す構文を使用します。

    InlineScript
    {
      <Script Block>
    } <Common Parameters>

InlineScript アクティビティは特定の Runbook にとって重要ですが、ワークフロー コンストラクトをサポートせず、次の理由により、必要な場合にのみ使用する必要があります。

- InlineScript ブロック内からチェックポイントを使用することはできません。ブロック内でエラーが発生した場合は、ブロックの先頭から再開する必要があります。
- InlineScript は、InlineScript ブロックの長さ全体について Windows PowerShell セッションを保持するため、Runbook の拡張性に影響します。
- Get-AutomationVariable や Get-AutomationPSCredential などのアクティビティは、InlineScript ブロックでは使用できません。  

InlineScript を使用する必要がある場合は、そのスコープを最小化しなければなりません。たとえば、同じ操作の各項目への適用中に Runbook がコレクションを反復処理すると、InlineScript の外部でループが発生します。これにより、次の利点が提供されます。

- 各反復処理の後にワークフローに[チェックポイント](#checkpoints)を設定できます。ジョブが中断または一時停止した後に再開された場合、ループは再開できます。
- **ForEach –Parallel** を使用して、コレクション項目を同時に処理できます。

InlineScript を Runbook で使用する場合は、次の推奨事項に留意してください。

- **$Using** スコープ修飾子を使用してスクリプトに値を渡すことができます。たとえば、InlineScript の外部に設定された $abc という変数は、InlineScript 内の $using:abc になります。

- InlineScript からの出力を返すには、その出力を変数に割り当て、返されるデータを出力ストリームに出力します。次の例では、文字列 “hi” を $output という変数に割り当てます。

	<pre><code>$output = InlineScript { Write-Output "hi" }</code></pre>

- InlineScript スコープ内ではワークフローを定義しないようにします。一部のワークフローが正しく動作しているようであったとしても、これはテストされたシナリオではありません。その結果、複雑なエラー メッセージまたは予期しない動作が発生する可能性があります。

InlineScript の使用の詳細については、「[ワークフローでの Windows PowerShell コマンドの実行](http://technet.microsoft.com/library/jj574197.aspx)」および「[about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx)」を参照してください。


## 関連記事:

- [Runbook の作成またはインポート](http://technet.microsoft.com/library/dn919921.aspx)

<!---HONumber=58-->