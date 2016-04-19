



環境や選択肢によっては、このスクリプトにより、Azure virtual network、ストレージ アカウント、クラウド サービス、ドメイン コントローラー、リモートまたはローカルの SQL データベース、ヘッド ノード、ブローカー ノード、コンピューティング ノード、Azure クラウド サービス (「バースト」または PaaS) ノードを含む、すべてのクラスター インフラストラクチャを作成できます。あるいは、このスクリプトにより既存の Azure インフラストラクチャを利用し、HPC クラスター ヘッド ノード、ブローカー ノード、コンピューティング ノード、Azure バースト ノードを作成できます。


HPC Pack クラスターの計画に関する背景情報については、HPC Pack TechNet ライブラリの「[製品の評価と計画](https://technet.microsoft.com/library/jj899596.aspx)」と「[はじめに](https://technet.microsoft.com/library/jj899590.aspx)」コンテンツを参照してください。



## 前提条件

* **Azure サブスクリプション** - Azure Global または Azure China サービスのサブスクリプションを利用できます。ご利用のサブスクリプションの制限によりデプロイ可能なクラスター ノードの数と種類が変わります。詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../articles/azure-subscription-service-limits.md)」をご覧ください。


* **Azure PowerShell 0.8.7 以降がインストールされ、構成されている Windows クライアント コンピューター** - 「[Azure PowerShell のインストールおよび構成方法](../articles/powershell-install-configure.md)」を参照してください。このスクリプトは Azure サービス管理 (asm) モードで実行されます。


* **HPC Pack IaaS デプロイ スクリプト** - [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949) から最新版のスクリプトをダウンロードし、解凍します。`New-HPCIaaSCluster.ps1 –Version` を実行して、スクリプトのバージョンを確認します。この記事はバージョン 4.4.0 のスクリプトに基づきます。

* **スクリプトの設定ファイル** - HPC クラスターを設定するためにスクリプトにより使用される XML ファイルを作成する必要があります。情報とサンプルについては、この記事の後半のセクションを参照してください。


## 構文

```
New-HPCIaaSCluster.ps1 [-ConfigFile] <String> [-AdminUserName]<String> [[-AdminPassword] <String>] [[-HPCImageName] <String>] [[-LogFile] <String>] [-Force] [-NoCleanOnFailure] [-PSSessionSkipCACheck] [<CommonParameters>]
```
>[AZURE.NOTE]このスクリプトは管理者として実行する必要があります。

### パラメーター

* **ConfigFile** - HPC クラスターについて説明する構成ファイルのファイル パスを指定します。詳細については、このトピックの「[構成ファイル](#Configuration-file)」か、スクリプトが含まれているフォルダーの Manual.rtf ファイルを参照してください。

* **AdminUserName** - ユーザー名を指定します。ドメイン フォレストがこのスクリプトにより作成される場合、これがすべての VM のローカル管理者ユーザー名となり、また、ドメイン管理者名となります。ドメイン フォレストが既に存在する場合、HPC Pack をインストールするために、ドメイン ユーザーがローカル管理者ユーザー名として指定されます。

* **AdminPassword** - 管理者のパスワードを指定します。コマンド ラインで指定されない場合、パスワードを入力するように求められます。

* **HPCImageName** (省略可能) - HPC クラスターのデプロイに使用される HPC Pack VM イメージ名が指定されます。これは Azure Marketplace から Microsoft が提供する HPC Pack イメージにする必要があります。指定されていない場合 (ほとんどの場合に推奨)、最近公開された HPC Pack イメージが選択されます。

    >[AZURE.NOTE] 有効な HPC Pack イメージを指定しない場合、デプロイは失敗します。

* **LogFile** (省略可能) - デプロイ ログ ファイルのパスを指定します。指定しない場合、スクリプトを実行しているコンピューターの一時ディレクトリにログ ファイルが作成されます。

* **Force** (省略可能) - すべての確認プロンプトを非表示にします。

* **NoCleanOnFailure** (省略可能) - デプロイできなかった Azure VM を削除しません。デプロイできなかった VM を最初に手動で削除してから、スクリプトを再実行し、デプロイを続行します。そうしないと、デプロイは失敗します。

* **PSSessionSkipCACheck** (省略可能) - このスクリプトで VM がデプロイされたすべてのクラウド サービスに対して、Azure によって自己署名証明書が自動的に生成されます。クラウド サービスのすべての VM がこの証明書を既定の Windows リモート管理 (WinRM) 証明書として使用します。これらの Azure VM で HPC 機能をデプロイするために、スクリプトによって、これらの証明書がローカル コンピューター/クライアント コンピューターの信頼されたルート証明機関ストアに既定で一時的にインストールされ、スクリプトの実行中、"信頼できない CA" セキュリティ エラーが抑制されます。スクリプトが完了すると、証明書は削除されます。このパラメーターが指定されている場合、証明書はクライアント コンピューターにインストールされず、セキュリティ警告が非表示になります。

    >[AZURE.IMPORTANT] 本稼働デプロイの場合、このパラメーターは推奨されません。

### 例

次の例では、*MyConfigFile.xml* という構成ファイルを使用して新しい HPC Pack クラスターを作成し、クラスターをインストールするための管理者資格情報を指定します。

```
New-HPCIaaSCluster.ps1 –ConfigFile MyConfigFile.xml -AdminUserName <username> –AdminPassword <password>
```

### 追加の考慮事項

* このスクリプトは Azure Marketplace の HPC Pack VM イメージを使用し、クラスター ヘッド ノードを作成します。最新のイメージは、HPC Pack 2012 R2 Update 3 がインストールされた Windows Server 2012 R2 Datacenter に基づいています。

* このスクリプトでは、任意で、HPC Pack Web ポータルまたは HPC Pack REST API を通してジョブ送信を有効にできます。

* このスクリプトでは、追加ソフトウェアをインストールするか、その他の設定を構成する場合、カスタムの構成前スクリプトと構成後スクリプトを任意で実行できます。


## 構成ファイル

デプロイ スクリプトの構成ファイルは XML ファイルです。「HPCIaaSClusterConfig.xsd」という名前のスキーマ ファイルは HPC Pack IaaS デプロイ スクリプト フォルダーにあります。**IaaSClusterConfig** は構成ファイルのルート要素であり、デプロイ スクリプト フォルダーの Manual.rtf ファイルに詳細がある子要素が含まれています。さまざまなシナリオのサンプル ファイルが必要であれば、この記事の「[サンプル構成ファイル](#Example-configuration-files)」を参照してください。

<!---HONumber=AcomDC_0406_2016-->