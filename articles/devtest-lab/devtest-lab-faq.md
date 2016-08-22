<properties
	pageTitle="DevTest Labs に関する FAQ | Microsoft Azure"
	description="DevTest Labs に関する一般的な質問に対する回答を見つける"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/05/2016"
	ms.author="tarcher"/>

# DevTest Labs に関する FAQ

この記事では、DevTest Labs について特に多く寄せられる質問に回答しています。

## 全般
- [なぜ DevTest Labs を使用する必要があるのですか。](#why-should-i-use-devtest-labs)
- [「worry free, self-service (心配無用のセルフ サービス)」とはどのような意味ですか。](#what-does-quotworry-free-self-servicequot-mean)
- [DevTest Labs を使用するにはどうすればよいですか。](#how-can-i-use-devtest-labs)
- [DevTest Labs に関してどのような請求が行われますか。](#how-will-i-be-charged-for-devtest-labs)
 
## セキュリティ 
- [DevTest Labs の各種セキュリティ レベルはどのようなものですか。](#what-are-the-different-security-levels-in-devtest-labs)
- [1 つのタスクのみの実行をユーザーに許可するように特定のロールを作成するにはどうすればよいですか。](#how-do-i-create-a-specific-role-to-allow-users-to-perform-only-a-single-task)
 
## CI/CD 統合と自動化 
 
- [DevTest Labs は、CI/CD ツールチェーンと統合されますか。](#does-devtest-labs-integrate-with-my-cicd-toolchain)
 
## Virtual Machines 
 
- [DevTest Labs で表示される特定の VM が Azure 仮想マシン ブレードに表示されないのはなぜですか。](#why-cant-i-see-certain-vms-in-the-azure-virtual-machines-blade-that-i-see-within-devtest-labs)
- [カスタム イメージとフォーミュラの違いは何ですか。](#what-is-the-difference-between-custom-images-and-formulas)
- [同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。](#how-do-i-create-multiple-vms-from-the-same-template-at-once)
- [既存の Azure VM を DevTest Labs ラボに移動するにはどうすればよいですか。](#how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab)
- [複数のディスクを VM に接続できますか。](#can-i-attach-multiple-disks-to-my-vms)
- [カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。](#how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images)
 
## アーティファクト 
 
- [アーティファクトとは何ですか。](#what-are-artifacts)
 
## ラボの構成 
 
- [Azure Resource Manager テンプレートからラボを作成するにはどうすればよいですか。](#how-do-i-create-a-lab-from-an-arm-template)
- [別々のリソース グループに作成した VM すべてが任意の名前を持つのはなぜですか。 これらのリソース グループの名前や内容を変更できますか。](#why-are-all-of-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups)
- [同じサブスクリプションにラボをいくつ作成できますか。](#how-many-labs-can-i-create-under-the-same-subscription)
- [ラボごとにいくつの VM を作成できますか。](#how-many-vms-can-i-create-per-lab)
 
## トラブルシューティング 
 
- [VM の作成時にアーティファクトでエラーが発生しました。どのようにトラブルシューティングすればよいですか。](#my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it)
- [既存の仮想ネットワークが正しく保存されないのはなぜですか。](#why-isnt-my-existing-virtual-network-saving-properly)

### なぜ DevTest Labs を使用する必要があるのですか。 
DevTest Labs を使用すると、チームの時間と費用を節約できます。開発者は、多様なベースを使用して各自の環境を作成し、アーティファクトを使用してアプリケーションを迅速に展開および構成できます。カスタム イメージとフォーミュラを使用して、仮想マシンをテンプレートとして保存して簡単に再現できます。さらに、ラボでは、自動シャットダウン、コストのしきい値、ユーザーごとの最大の VM 数、最大 VM サイズなど複数の構成可能なポリシーを提供しており、ラボ管理者が無駄を削減してチームの環境を管理することができます。DevTest Labs の詳細については、[概要](devtest-lab-overview.md)を参照するか[入門ビデオ](/documentation/videos/videos/what-is-azure-devtest-labs)をご覧ください。

### 「worry free, self-service (心配無用のセルフ サービス)」とはどのような意味ですか。
「心配無用のセルフ サービス」とは、開発者およびテスト担当者が必要に応じて各自の環境を作成でき、管理者は DevTest Labs によって無駄を最小化して管理コストを制御できる安心感を得られることを示します。管理者は、許可される VM のサイズ、VM の最大数、VM の起動およびシャットダウン時間を指定できます。DevTest Labs では、ラボ内のリソースがどのように使用されているかを把握するために、コストを監視してアラートを設定することも容易になります。

### DevTest Labs を使用するにはどうすればよいですか。 
DevTest Labs は、開発環境やテスト環境が必要であり、迅速にこれを再現したり、コスト節約ポリシーで管理したりする場合に常に役立ちます。

お客様が DevTest Labs を使用しているシナリオのいくつかを次に示します。

- 開発環境およびテスト環境を 1 か所で管理し、ポリシーを使用してコストを削減し、カスタム イメージを使用してチーム全体でビルドを共有します。
- 開発段階全体にわたってディスクの状態を保存するために、カスタム イメージを使用してアプリケーションを開発します。
- 進行状況に関連してコストを追跡します。
- 品質保証テスト用の大容量テスト環境を作成します。
- さまざまな環境でアプリケーションを容易に構成して再現するために、アーティファクトとフォーミュラを使用します。
- ハッカソン (共同開発またはテスト作業) 用の VM を配布し、イベントの終了時に容易にプロビジョニング解除します。

### DevTest Labs に関してどのような請求が行われますか。 
DevTest Labs は、無料サービスです。つまり、ラボの作成や、ポリシー、テンプレート、アーティファクトの構成は無料です。仮想マシン、ストレージ アカウント、仮想ネットワークなど、ラボ内で使用する Azure リソースに対してのみ課金されます。ラボ リソースのコストの詳細については、「[Azure DevTest Labs の価格](https://azure.microsoft.com/pricing/details/devtest-lab/)」をご覧ください。

### DevTest Labs の各種セキュリティ レベルはどのようなものですか。  
セキュリティ アクセスは、[Azure のロール ベースのアクセス制御 (RBAC)](../active-directory/role-based-access-built-in-roles.md) によって決定されます。アクセスのしくみを理解するには、RBAC によって定義されているアクセス許可、ロール、およびスコープの違いを理解することが有用です。アクセス許可とは、特定のアクションへのアクセスを定義したもの (たとえば、すべての仮想マシンへの読み取りアクセス) です。ロールは、グループ化してユーザーに割り当てることができる一連のアクセス許可です (たとえば、「サブスクリプション所有者」は、サブスクリプション内のすべてのリソースにアクセスできます)。スコープは、Azure リソースの階層内のレベル (単一のリソース グループ、単一のラボ、サブスクリプション全体など) です。
 
DevTest Labs のスコープ内では、ユーザーのアクセス許可を定義する 2 種類のロールがあります。ラボ所有者とラボ ユーザーです。

- ラボ所有者: ラボ所有者は、ラボ内のすべてのリソースにアクセスできます。そのため、ポリシーの変更、任意の VM の読み取りと書き込み、仮想ネットワークの変更などを行うことができます。
- ラボ ユーザー: ラボ ユーザーは VM、ポリシー、仮想ネットワークなど、すべてのラボ リソースを表示できますが、他のユーザーが作成したポリシーまたは VM を変更することはできません。DevTest Labs にカスタム ロールを作成することもできます。実施方法については、記事「[特定のラボ ポリシーに対するアクセス許可をユーザーに付与する](devtest-lab-grant-user-permissions-to-specific-lab-policies.md)」をご覧ください。
 
スコープは階層構造を持つため、ユーザーが特定のスコープのアクセス許可を持つ場合は、含まれているすべての下位のスコープでそのアクセス許可が自動的に付与されます。たとえば、ユーザーにサブスクリプション所有者のロールが割り当てられている場合、ユーザーはサブスクリプションのすべてのリソースにアクセスできます。これには、すべての仮想マシン、すべての仮想ネットワーク、およびすべてのラボが含まれます。このため、サブスクリプション所有者は、ラボ所有者のロールを自動的に継承します。ただし、その逆は真ではありません。ラボ所有者はラボにアクセスできます。これはサブスクリプション レベルよりも下位のスコープです。そのため、ラボ所有者はラボの外にある仮想マシン、仮想ネットワーク、またはいずれのリソースも表示できません。

### 特定の 1 つのタスクの実行をユーザーに許可するようにロールを作成するにはどうすればよいですか。
カスタム ロールを作成し、そのロールにアクセス許可を割り当てる方法についての包括的な記事がこちらにあります。ラボ内のすべての VM を開始および停止するアクセス許可を持つロール "DevTest Labs Advanced User" を作成するスクリプトの例を次に示します。
 
	$policyRoleDef = (Get-AzureRmRoleDefinition "DevTest Labs User") 
	$policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*') 
	$policyRoleDef.Id = $null 
	$policyRoleDef.Name = "DevTest Labs Advance User" 
	$policyRoleDef.IsCustom = $true 
	$policyRoleDef.AssignableScopes.Clear() 
	$policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action") 
	$policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action") 
	$policyRoleDef = (New-AzureRmRoleDefinition -Role $policyRoleDef)  
 
### DevTest Labs は、CI/CD ツールチェーンと統合されますか。 
VSTS を使用している場合は、DevTest Labs でのリリース パイプラインを自動化できる、[Azure DevTest Labs タスク拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)があります。この拡張機能の使用方法には次のようなものがあります。

- VM を自動で作成してデプロイし、Azure File Copy または PowerShell の VSTS タスクを使用して最新のビルドで構成します。
- テストの終了後、詳しい調査を目的として同じ VM 上でバグを再現するために、VM の状態を自動的にキャプチャします。
- 不要になったときに、リリース パイプラインの最後に VM を削除します。

次のブログ投稿では、VSTS 拡張機能の使用に関するガイダンスおよび情報を提供しています。
 
- [Azure DevTest Labs – VSTS extension (Azure DevTest Labs – VSTS 拡張機能)](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
- [Deploying a new VM in an existing AzureDevTestLab from VSTS (VSTS からの既存 AzureDevTestLab での新しい VM のデプロイ)](http://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS)
- [Using VSTS Release Management for Continuous Deployments to AzureDevTestLabs (VSTS のリリース管理を使用した AzureDevTestLabs への継続的なデプロイメント)](http://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs)
 
その他の CI/CD ツールチェーンについては、VSTS のタスク拡張機能によって実施可能な前述のすべてのシナリオを、[Azure PowerShell コマンドレット](../resource-group-template-deploy.md)と [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/) を使用して、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)をデプロイすることによって同様に実施できます。[DevTest Labs 用の REST API](http://aka.ms/dtlrestapis) を使用して、お使いのツール チェーンと統合することもできます。

### DevTest Labs で表示される特定の VM が Azure 仮想マシン ブレードに表示されないのはなぜですか。
DevTest Labs で VM を作成すると、その VM にアクセスするアクセス許可が与えられ、ラボのブレードと **[仮想マシン]** ブレードの両方で表示できます。DevTest Labs ユーザーの場合は、これにより、**[All Virtual Machines (すべての仮想マシン)]** ブレードを通じて、ラボで作成されたすべての仮想マシンを参照できます。ただし、DevTest Labs ユーザーの場合、他のユーザーが作成した VM リソースへの読み取りアクセスが自動的には許可されないため、**[仮想マシン]** ブレードに表示されません。

### カスタム イメージとフォーミュラの違いは何ですか。 
カスタム イメージは VHD (仮想ハード ディスク) である一方、フォーミュラは保存して再現できる追加設定を構成可能なイメージです。同じ基本的な不変のイメージで複数の環境をすばやく作成する場合は、カスタム イメージをお勧めします。フォーミュラは、最新のビット、仮想ネットワーク/サブネット、または特定のサイズで VM の構成を再現する必要がある場合に適しています。詳細については、記事「[Comparing custom images and formulas in DevTest Labs (DevTest Labs のカスタム イメージとフォーミュラの比較)](devtest-lab-comparing-vm-base-image-types.md)」をご覧ください。
 
### 同じテンプレートから複数の VM を一度に作成するにはどうすればよいですか。 
VM の作成中に [VSTS タスク拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)または [Azure Resource Manager テンプレートの生成](devtest-lab-add-vm-with-artifacts.md#save-arm-template)を使用し、[Windows PowerShell から Azure Resource Manager テンプレートをデプロイ](../resource-group-template-deploy.md)することができます。
 
### 既存の Azure VM を DevTest Labs ラボに移動するにはどうすればよいですか。 
当社では、DevTest ラボに VM を直接移動するソリューションを設計中ですが、現時点では、既存の VM DevTest Labs に次のようにコピーすることで対処できます。

1. この [Windows PowerShell スクリプト](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1)を使用して、既存の VM の VHD ファイルをコピーします。
1. DevTest Labs ラボ内に[カスタム イメージを作成](devtest-lab-create-template.md)します。
1. カスタム イメージからラボ内に VM を作成します。
 
### 複数のディスクを VM に接続できますか。 
VM への複数ディスクの接続はサポートされています。
 
### カスタム イメージを作成するために VHD ファイルをアップロードするプロセスを自動化するにはどうすればよいですか。 
2 つのオプションがあります。

- [Azure AzCopy](../storage/storage-use-azcopy.md#blob-upload) を使用して、ラボに関連付けられているストレージ アカウントに VHD ファイルをコピーまたはアップロードできます。
- [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md)は Windows、OSX、Linux で稼働するスタンドアロン アプリです。
 
ラボに関連付けられているコピー先ストレージ アカウントを検索するには、次の手順に従います。

1. [Azure ポータル](http://go.microsoft.com/fwlink/p/?LinkID=525040)にサインインします。
1. 左側にあるパネルから **[リソース グループ]** を選択します。
1. ラボに関連付けられているリソース グループを見つけて選択します。
1. **[概要]** ブレードで、いずれかのストレージ アカウントを選択します。
1. **[BLOB]** を選択します。
1. 一覧内でアップロードを検索します。存在しない場合は、手順 4 に戻り、別のストレージ アカウントを試します。
1. AzCopy コマンドで、コピー先としてこの **URL** を使用します。

### アーティファクトとは何ですか。 
アーティファクトは、最新のビットまたは開発用ツールを VM 上にデプロイするために使用できるカスタマイズ可能な要素です。いくつかの簡単なクリック操作で作成時に VM に接続され、VM がプロビジョニングされると、アーティファクトによって VM がデプロイおよび構成されます。[パブリック GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)には、既存のアーティファクトが多数存在しますが、[独自のアーティファクトの作成](devtest-lab-artifact-author.md)も簡単です。

### Azure Resource Manager テンプレートからラボを作成するにはどうすればよいですか。 
[ラボ Azure Resource Manager テンプレートの GitHub リポジトリ](https://github.com/Azure/azure-devtestlab/tree/master/ARMTemplates)があります。これらの各テンプレートには、クリックすると自身の Azure サブスクリプションの下に DevTest Labs ラボをデプロイできるリンクが用意されています。
 
### 別々のリソース グループに作成した VM すべてが任意の名前を持つのはなぜですか。 これらのリソース グループの名前や内容を変更できますか。 
リソース グループは、DevTest Labs がユーザーのアクセス許可と仮想マシンへのアクセスを管理するため、ご質問で述べられたように作成されます。このエクスペリエンスを向上させて柔軟性を高めるように取り組んでいますが、必要に応じて、これらのリソース グループの名前を変更できます。アクセス許可を意図せず変更しないようにするため、別のリソース グループに VM を移動しないことをお勧めします。
 
### 同じサブスクリプションにラボをいくつ作成できますか。 
サブスクリプションごとに作成できるラボの数に具体的な制限はありませんが、使用するリソースはサブスクリプションごとに制限されます。[Azure サブスクリプションに適用される制限とクォータ](../azure-subscription-service-limits.md)および[これらの制限を増やす方法](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests)に関する記事をご覧ください。
 
### ラボごとにいくつの VM を作成できますか。 
ラボごとに作成できる VM の数に具体的な制限はありませんが、現在、ラボでは、Standard Storage で同時に実行できる VM の数は 40 個程度、Premium Storage で同時に実行できる VM の数は 25 個に限られます。現在、これらの制限値を増やす作業に取り組んでいます。
 
### VM の作成時にアーティファクトでエラーが発生しました。どのようにトラブルシューティングすればよいですか。 
障害が発生したアーティファクトに関するログを取得する方法については、MVP の 1 人によるブログ記事「[How to troubleshoot failing Artifacts in AzureDevTestLabs (AzureDevTestLabs 内のアーティファクトの障害をトラブルシューティングする方法)](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs)」をご覧ください。
 
### 既存の仮想ネットワークが正しく保存されないのはなぜですか。  
仮想ネットワーク名にピリオドが含まれていることが原因となっている可能性があります。その場合は、ピリオドを削除するかハイフンに置き換えてから、仮想ネットワークをもう一度保存してみてください。

<!---HONumber=AcomDC_0810_2016-->