<properties 
   pageTitle="Azure Automation DSC の概要 | Microsoft Azure" 
   description="Azure Automation Desired State Configuration (DSC) の概要、その利用規約、および既知の問題" 
   services="automation" 
   documentationCenter="dev-center-name" 
   authors="coreyp-at-msft" 
   manager="stevenka" 
   editor="tysonn"/>

<tags
   ms.service="automation"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="powershell"
   ms.workload="TBD" 
   ms.date="10/09/2015"
   ms.author="coreyp"/>

# Azure Automation DSC の概要 #

## PowerShell DSC とは ##
Desired State Configuration (DSC) は Windows PowerShell における新しい管理プラットフォームです。これにより、宣言型 PowerShell 構文を使用する物理ホストと仮想マシンの構成管理が可能になります。

DSC は一連の Windows PowerShell 言語拡張機能、新しい Windows PowerShell コマンドレット、およびリソースを提供します。これを使用して、ソフトウェア環境の構成方法を宣言的に指定できます。また、既存の構成を維持および管理する手段も提供します。

### 実際の適用 ###
組み込みの DSC リソースを使用して、自動化された方法で一連のコンピューター (ターゲット ノードともいう) を構成および管理できるシナリオ例を以下にいくつか示します。

- サーバー ロールと機能の有効化または無効化
- レジストリ設定の管理
- ファイルとディレクトリの管理
- プロセスとサービスの開始、停止、および管理
- グループとユーザー アカウントの管理
- 新しいソフトウェアのデプロイ
- 環境変数の管理
- Windows PowerShell スクリプトの実行
- 必要な状態とは異なる構成の修正
- 特定のノードでの実際の構成状態の検出

さらに、カスタム リソースを作成して、すべてのアプリケーションまたはシステム設定の状態を構成することもできます。


PowerShell DSC の詳細については、「[DevOps 領域における構成 - Windows PowerShell Desired State Configuration](http://blogs.msdn.com/b/powershell/archive/2013/11/01/configuration-in-a-devops-world-windows-powershell-desired-state-configuration.aspx)」を参照してください。

##Azure Automation DSC とは##
Azure Automation DSC は、構成管理をより容易にするために PowerShell DSC で導入された基礎をベースとします。Azure Automation DSC は、今日 Azure Automation が PowerShell スクリプト用に提供するものと同じ管理レイヤーを [PowerShell Desired State Configuration](https://technet.microsoft.com/library/dn249912.aspx) <link> に提供します。

Azure Automation DSC を使用することで、[PowerShell Desired State Configuration の作成や管理](https://technet.microsoft.com/library/dn249918.aspx)、[DSC リソース](https://technet.microsoft.com/library/dn282125.aspx)のインポート、および DSC ノード構成 (MOF ドキュメント) の生成がすべてクラウドで可能になります。これらの DSC 項目は、Azure Automation の [DSC プル サーバー](https://technet.microsoft.com/library/dn249913.aspx)に配置されるため、クラウドおよびオンプレミスのターゲット ノード (物理および仮想マシンなど) はそれらを選択し、指定した必要な状態に自動的に準拠して、Azure Automation に必要な状態の準拠に関するレポートを返します。

読むより見る方がよければ、 Azure Automation DSC が初めて発表された 2015年 5 月に公開された次のビデオをご覧ください。**注:** このビデオで解説されている概念とライフサイクルは正しいものですが、このビデオが作成されてから Azure Automation DSC はかなり進歩しています。現在はパブリック プレビュー段階であり、Azure ポータルではさらに広範な UI が提供され、追加機能がサポートされるようになっています。

> [AZURE.VIDEO microsoft-ignite-2015-heterogeneous-configuration-management-using-microsoft-azure-automation]

## Azure Automation DSC の利用規約 ##
### 構成 ###
PowerShell DSC では、構成という新しい概念が導入されました。構成では、PowerShell 構文を使用して、環境の必要な状態を定義することができます。DSC を使用して環境を構成するには、まず、構成キーワードを使用して Windows PowerShell スクリプト ブロックを定義してから、その後に識別子と、ブロックを区切る中かっこ ({}) を付加します。

![alt text](./media/automation-dsc-overview/AADSC_1.png)

構成ブロック内には、まったく同じように構成する必要がある環境内の一連のノード (コンピューター) に必要な構成を指定するノードの構成ブロックを定義できます。このようにして、ノード構成では想定される 1 つ以上のノードの「ロール」を示します。ノードの構成ブロックはノード キーワードで始まります。このキーワードの後にロール名 (変数または式の場合あり) を付加します。ロール名の後に中かっこ {} を使用して、ノードの構成ブロックを区切ります。

![alt text](./media/automation-dsc-overview/AADSC_2.png)
 
ノードの構成ブロック内には、特定の DSC リソースを構成するためのリソース ブロックを定義できます。リソース ブロックはリソース名で始まり、その後にブロックに対して指定する必要がある識別子と、ブロックを区切るための中かっこ {} が続きます。

![alt text](./media/automation-dsc-overview/AADSC_3.png)

構成キーワードの詳細については、「[Desired State Configuration における構成キーワードについて](http://blogs.msdn.com/b/powershell/archive/2013/11/05/understanding-configuration-keyword-in-desired-state-configuration.aspx "Desired State Configuration における構成キーワードについて")」を参照してください。

DSC 構成を実行 (コンパイル) すると、1 つ以上の DSC ノード構成 (MOF ドキュメント) が生成されます。これらは、DSC ノードが必要な状態に準拠するために適用するものです。

Azure Automation DSC では、Azure Automation での DSC 構成のインポート、作成、およびコンパイルが可能です。これは、Azure Automation で Runbook をインポート、作成、および開始する方法に似ています。

>[AZURE.IMPORTANT]Azure Automation DSC では、構成にそれと同じ名前の構成ブロックを 1 つだけ含める必要があります。


###ノード構成###

DSC 構成がコンパイルされると、構成内のノード ブロックに応じて、1 つ以上のノード構成が生成されます。ノード構成は「MOF」、つまり「構成ドキュメント」と同じで (これらの PS DSC 利用規約をよく理解している場合)、Web サーバーや worker などの「ロール」(必要な状態を 1 つ以上のノードが想定する必要がある) を示します。Azure Automation DSC でのノード構成の名前は、“<Configuration-name>.<Node configuration-block-name>” という形式です。

PS DSC ノードは、DSC プッシュまたはプル メソッドのいずれかを使用して制定する必要があるノード構成を認識するようになります。Azure Automation DSC は、Azure Automation DSC プル サーバーから適用する必要があるノード構成をノードが要求する DSC プル方式に依存します。ノードは Azure Automation DSC に対して要求を行うため、ファイアウォールの内側に配置したり、受信ポートをすべて閉じたりすることができます。これらに必要なのは、インターネットへの発信アクセスのみです。


###ノード###

DSC ノードは、その構成が DSC によって管理される任意のコンピューターです。これは、Azure VM またはオンプレミスの VM/物理ホストである場合があります。ノードは、定義した必要な状態になり、その状態の準拠を維持するようにノード構成を制定します。また、構成状態と準拠に関するレポートをレポート サーバーに返すこともできます。

Azure Automation DSC は Azure Automation DSC によって管理されるノードのオンボードを容易にし、ノード サーバー側にそれぞれ割り当てられているノード構成を変更できるようにするため、ノードは次回サーバーの指示を確認する際に、別のロールを想定し、一致するように構成方法を変更します。また、ノードはその状態と構成の準拠について、Azure Automation DSC にレポートします。


###リソース###
DSC リソースは、Windows PowerShell Desired State Configuration (DSC) 構成を定義するために使用できる構成要素です。DSC には、ファイルやフォルダー、サーバー機能やロール、レジストリ設定、環境変数、およびサービスやプロセスなどのリソースを構成するための一連の組み込み機能が用意されています。組み込みの DSC リソースの完全な一覧と使用方法については、「[組み込みの Windows PowerShell Desired State Configuration リソース](https://technet.microsoft.com/library/dn249921.aspx)」を参照してください。

DSC リソースは、一連の組み込み DSC リソースを拡張するために PowerShell モジュールの一部としてインポートすることもできます。ノードが制定する必要があるノード構成に既定以外のリソースへの参照が含まれている場合、DSC プル サーバーから DSC ノードによってそのリソースがプルダウンされます。カスタム リソースの作成方法については、「[カスタムの Windows PowerShell Desired State Configuration リソースの作成](https://technet.microsoft.com/library/dn249927.aspx)」を参照してください。

Azure Automation DSC には、PS DSC と同じ組み込み DSC リソースがすべて付属しています。Azure Automation にリソースを含む PowerShell モジュールをインポートすることで、Azure Automation DSC にリソースをさらに追加することができます。


###コンパイル ジョブ###
Azure Automation DSC のコンパイル ジョブは、1 つ以上のノード構成を作成するための構成のコンパイルのインスタンスです。Azure Automation Runbook ジョブに似ていますが、ノード構成を作成する場合を除き、タスクを実際に実行しないという点が異なります。コンパイル ジョブによって作成されたノード構成は、Azure Automation DSC プル サーバーに自動的に配置され、以前のバージョンのノード構成は、この構成のために存在している場合、上書きされます。コンパイル ジョブによって生成されたノード構成の名前には、"<Configuration-name>.<Node configuration-block-name>" という形式を使用します。たとえば、以下の構成をコンパイルすると、"MyConfiguration.webserver" という単一のノード構成が生成されます。


![alt text](./media/automation-dsc-overview/AADSC_5.png)


>[AZURE.NOTE]Runbook と同じように、構成を発行することができます。これは、Azure Automation DSC プル サーバーへの DSC 項目の配置とは関係ありません。コンパイル ジョブにより、DSC 項目は Azure Automation DSC プル サーバーに配置されます。Azure Automation での「発行」の詳細については、「[Runbook の公開](https://msdn.microsoft.com/library/dn903765.aspx)」を参照してください。

現在、Azure Automation DSC では、コンパイル ジョブを管理するために [Azure リソース マネージャーの PowerShell モジュール](https://msdn.microsoft.com/library/mt244122.aspx)で以下のコマンドレットが提供されます。

-	`Get-AzureRmAutomationDscCompilationJob`
-	`Get-AzureRmAutomationDscCompilationJobOutput`
-	`Start-AzureRmAutomationDscCompilationJob`

##Azure Automation DSC のライフサイクル##
空のオートメーション アカウントから適切に構成されたノードの管理対象セットを作成するには、構成を定義し、定義した構成をノード構成に変更して、ノードを Azure Automation DSC とこれらのノード構成にオンボードする一連のプロセスを行います。次の図に、Azure Automation DSC のライフサイクルを示します。

![alt text](./media/automation-dsc-overview/DSCLifecycle.png)


##認識されている既知の問題##

- 現時点では、Azure Automation DSC では部分的または複合 DSC 構成はサポートされていません。

- Windows 用 PowerShell DSC エージェントを Azure Automation と通信できるようにするには、最新バージョンの WMF 5 をインストールする必要があります。現時点では、Linux 用 PowerShell DSC エージェントでは Azure Automation との通信はサポートされていません。at this time.これはすぐに更新する必要があります。

- Azure Automation では、複数の PowerShell モジュールを同時に使用することはできません。つまり、Automation アカウント内のすべての構成では、その Automation アカウントにインポートされた最新バージョンの PowerShell モジュールと、構成で使用されるモジュールに含まれる PowerShell DSC リソースを使用する必要があります。

- 従来の PowerShell DSC プル サーバーでは、**ModuleName\_Version.zip"** という形式のモジュール zip をプル サーバーに配置する必要があります。Azure Automation では、**ModuleName.zip** という形式の名前の PowerShell モジュールをインポートする必要があります。Azure Automation にモジュールをインポートするために必要な統合モジュールの詳細については、「[このブログの投稿](http://azure.microsoft.com/blog/2014/12/15/authoring-integration-modules-for-azure-automation/)」を参照してください。

- 「フォルダーごとのバージョン」形式を使用して、モジュール内に複数の DSC リソースを同時に指定する PowerShell モジュールは、現在 Azure Automation では機能しません。

- Azure Automation にインポートした PowerShell モジュールには、.doc ファイルや .docx ファイルを含めることはできません。DSC リソースを含むいくつかの PowerShell モジュールには、参照用にこれらのファイルが含まれます。Azure Automation にインポートする前に、モジュールからこれらのファイルを削除してください。

- ノードが初めて Azure Automation アカウントに登録されたとき、またはノードがサーバー側の異なるノード構成にマップされるように変更されたときには、ノードの状態が現在マップされているノード構成に実際には準拠していない場合であっても、ノードの状態は "準拠" になります。ノードが登録後またはノード構成のマッピング変更後に最初のプルを実行して最初のレポートを送信した後であれば、ノードの状態を信頼できます。

- Azure Automation DSC VM で `Register-AzureAutomationDscNode`、`Set-AzureVMExtension`、または Azure プレビュー ポータルの Azure Automation DSC VM 拡張機能を使用して、管理対象の Azure VM をオンボードする際に、「**コンピューター名が指定されておらず、構成ディレクトリに構成ファイルが存在しません**」と表示され、登録に失敗する場合がありますが、これは誤報であり、VM 登録は実際には正常に行われています。登録が正常に行われたかどうかは、`Get-AzureAutomationDscNode` コマンドレットを使用して確認できます。

- Azure プレビュー ポータルで `Register-AzureAutomationDscNode`、`Set-AzureVMExtension`、または Azure Automation DSC VM 拡張機能を使用して Azure Automation DSC での管理用に Azure VM をオンボードするときは、VM が DSC ノードとして Azure Automation に表示されるまでに最大で 1 時間かかることがあります。これは、VM を Azure Automation DSC にオンボードするために必要な Windows Management Framework 5.0 を、Azure VM DSC 拡張機能が VM にインストールするためです。

- 登録すると、各ノードは、1 年後に有効期限が切れる認証用の一意の証明書を自動的にネゴシエートします。現時点では、PowerShell DSC 登録プロトコルは、有効期限が近づいたときに証明書を自動的に更新することはできないため、1 年後にノードを再登録する必要があります。再登録する前に、各ノードで Windows Management Framework 5.0 RTM が実行されていることを確認します。ノードの認証証明書の有効期限が切れるときにノードが再登録されない場合、ノードは Azure Automation と通信できなくなり、[反応なし] とマークされます。 再登録は、ノードを初めて登録したのと同じ方法で実行されます。証明書の有効期限が切れる 90 日以内または証明書の有効期限が切れた後で再登録を実行すると、新しい証明書が生成されて使用されます。

##関連記事##

- [Azure Automation DSC cmdlets (Azure Automation DSC コマンドレット)](https://msdn.microsoft.com/library/mt244122.aspx)
- [Azure Automation DSC cmdlets (Azure Automation DSC の価格)](http://azure.microsoft.com/pricing/details/automation/)
- [Azure Automation DSC と Chocolatey を使用した IaaS VM への継続的なデプロイ](automation-dsc-cd-chocolatey.md)

<!---HONumber=Oct15_HO3-->