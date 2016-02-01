<properties
   pageTitle="DMZ の例 – NSG を使用して単純な DMZ を構築する | Microsoft Azure"
   description="ネットワーク セキュリティ グループ (NSG) を使用して DMZ を構築する"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/20/2016"
   ms.author="jonor;sivae"/>

# 例 1: NSG を使用して単純な DMZ を構築する

[セキュリティ境界のベスト プラクティス ページに戻る][HOME]

この例では、4 つの Windows サーバーおよびネットワーク セキュリティ グループを使用して単純な DMZ を作成します。また、各手順をより深く理解できるように、関連するコマンドを順に説明します。さらに、「トラフィックに関するシナリオ」セクションでは、DMZ の防御層におけるトラフィックの進行過程を詳しく説明しています。最後の「参照」セクションでは、さまざまなシナリオでテストおよび実験ができるように、この環境を構築するための完全なコードと手順を紹介します。

![NSG での受信 DMZ][1]

## 環境の説明
この例で使用するサブスクリプションには、以下のものが含まれています。

- 2 つのクラウド サービス: "FrontEnd001" と "BackEnd001"
- 2 つのサブネット ("FrontEnd" と "BackEnd") を含む仮想ネットワーク "CorpNetwork"
- 両方のサブネットに適用されるネットワーク セキュリティ グループ
- アプリケーション Web サーバーを表す Windows サーバー ("IIS01")
- アプリケーション バックエンド サーバーを表す 2 つの Windows サーバー ("AppVM01"、"AppVM02")
- DNS サーバーを表す Windows サーバー ("DNS01")

以上に示した環境の大部分は、このページの「参照」セクションで紹介している PowerShell スクリプトで構築します。VM と仮想ネットワークの構築については、スクリプト例には含まれていますが、このドキュメントでは詳細な説明を省略します。

環境を構築するには

  1.	「参照」セクションに示しているネットワーク構成用 xml ファイルを保存します (特定のシナリオに一致するように、名前、ロケーション情報、IP アドレスを更新します)。
  2.	スクリプトを実行する環境に合わせてスクリプト内のユーザー変数を更新します (サブスクリプション、サービス名など)。
  3.	PowerShell でスクリプトを実行します。

**注**: PowerShell スクリプトで示されたリージョンは、ネットワーク構成用 xml ファイルで示されたリージョンと一致する必要があります。

スクリプトが正常に実行されたら、オプションの手順を追加してもかまいません。「参照」セクションに対象となる 2 つのスクリプトを示します。単純なWeb アプリケーションを使用して Web サーバーとアプリケーション サーバーをセットアップして、この DMZ 構成でテストができるようにするスクリプトです。

次のセクションでは、ネットワーク セキュリティ グループについて、また、この例でネットワーク セキュリティ グループがどのように機能しているのかを、PowerShell スクリプトの主要な行を追いながら詳しく説明します。

## ネットワーク セキュリティ グループ (NSG)
この例では、NSG グループを作成し、そこに 6 つのルールを設定します。

>[AZURE.TIP]一般的には、具体的な "Allow" ルールを作成してから、包括的な "Deny" ルールを作成してください。どのルールが先に評価されるかは、割り当てる優先度によって決まります。具体的なルールが一度適用されたトラフィックに対しては、他のルールは評価されません。NSG ルールは、(サブネットから見て) 受信方向または送信方向のどちらか一方に適用できます。

ここでは、受信トラフィックに対して次の内容のルールを作成しています。

1.	内部的な DNS トラフィック (ポート 53) を許可する。
2.	インターネットから任意の VM への RDP トラフィック (ポート 3389) を許可する。
3.	インターネットから Web サーバー (IIS01) への HTTP トラフィック (ポート 80) を許可する。
4.	IIS01 から AppVM1 への任意のトラフィック (すべてのポート) を許可する。
5.	インターネットから VNet 全体 (両方のサブネット) への任意のトラフィック (すべてのポート) を拒否する。
6.	フロントエンド サブネットからバックエンド サブネットへの任意のトラフィック (すべてのポート) を拒否する。

これらのルールが各サブネットにバインドされている状態で、インターネットから Web サーバーへの HTTP 要求が受信された場合、ルール 3 (Allow) とルール 5 (Deny) の両方が該当しますが、ルール 3 の方が優先度が高いので、ルール 3 のみが適用され、ルール 5 は作用しません。したがって、Web サーバーへの HTTP 要求は許可されます。同じトラフィックが DNS01 サーバーに到達しようとしている場合は、ルール 5 (Deny) が最初に適用されるので、トラフィックはサーバーに到達できません。フロントエンド サブネットからバックエンド サブネットへの通信は、ルール 6 (Deny) によってブロックされます (ルール 1 とルール 4 で許可されたトラフィックを除く)。これによって、フロントエンドにある Web アプリケーションのセキュリティが攻撃者によって侵害されたとしてもバックエンド ネットワークは保護されるため、バックエンドの "保護された" ネットワークに対する攻撃者のアクセスは (AppVM01 サーバー上で公開されているリソースに) 限定されます。

インターネットへ向かうトラフィックは、既定の送信ルールによって許可されています。この例では、送信トラフィックを許可していますが、送信ルールに対する変更は一切行っていません。両方向のトラフィックをロックダウンするには、ユーザー定義のルーティングが必要です。これについては、以下の「例 3」で説明します。

各ルールの詳細を以下に説明します (以下のリストにおいてドル記号で始まる項目 (たとえば、$NSGName) はいずれも、ユーザー定義の変数であり、このドキュメントの「参照」セクションのスクリプトで使用されています)。

1. まず、ルールを保持するネットワーク セキュリティ グループを構築する必要があります。　

	    New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.	この例の最初のルールは、すべての内部ネットワーク間で、バックエンド サブネット上の DNS サーバーへの DNS トラフィックを許可します。ルールには、いくつかの重要なパラメーターがあります。
  - “Type” は、サブネットまたは仮想マシンから見て、このルールがどの方向のトラフィックに適用されるかを示します (この NSG がバインドされている場所に応じて異なる)。したがって、Type が “Inbound” である場合、サブネットに入るトラフィックにはルールが適用され、サブネットを出るトラフィックにはルールが適用されません。
  - "Priority" では、トラフィック フローを評価する順序を設定します。数値が低いほど、優先度は高くなります。ルールが特定のトラフィック フローに適用されると直ちに、それ以降のルールの処理は行われなくなります。したがって、優先度 1 のルールがトラフィックを許可し、優先度 2 のルールがトラフィックを拒否する場合、両方のルールをトラフィックに適用すると、トラフィックのフローは許可されます (ルール 1 の方が優先度が高く、これが有効になると、それ以降、ルールは適用されないからです)。
  - “Action” は、このルールの影響を受けるトラフィックがブロックされるか、それとも許可されるかを示します。

			Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.	このルールは、インターネットからの RDP トラフィックが、VNET 内のいずれかのサブネットにある任意のサーバー上の RDP ポートに流れるのを許可します。このルールでは、特種なタイプのアドレス プレフィックス ("VIRTUAL\_NETWORK" と "INTERNET") を使用します。これを使用すれば、アドレス プレフィックスのカテゴリが大きくなっても容易に対処することができます。

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
	    	Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
	    	-Type Inbound -Priority 110 -Action Allow `
	    	-SourceAddressPrefix INTERNET -SourcePortRange '*' `
	    	-DestinationAddressPrefix VIRTUAL_NETWORK `
	    	-DestinationPortRange '3389' `
	    	-Protocol *

4.	このルールは、受信インターネット トラフィックが Web サーバーに到達するのを許可します。これによって、ルーティング動作が変更されることはありません。IIS01 宛てのトラフィックが通過するのを許可するだけです。したがって、インターネットからのトラフィックの宛先が Web サーバーである場合、このルールによってそのトラフィックは許可され、その他のルールの処理は停止されます(優先度 140 のルールでは、他の受信インターネット トラフィックはすべてブロックされます)。処理するのが HTTP トラフィックのみである場合は、宛先ポート 80 のみが許可されるように、このルールをさらに制限することが可能です。

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
    		-Type Inbound -Priority 120 -Action Allow `
    		-SourceAddressPrefix Internet -SourcePortRange '*' `
    		-DestinationAddressPrefix $VMIP[0] `
    		-DestinationPortRange '*' `
    		-Protocol *

5.	このルールは IIS01 サーバーから AppVM01 サーバーに渡されるトラフィックを許可します。フロントエンドからバックエンドに向かう他のトラフィックはすべて、後のルールによってブロックされます。このルールを改善するには、ポートがわかっている場合、それを追加します。たとえば、IIS サーバーからのトラフィックが AppVM01 上の SQL Server のみに到達する場合は、宛先ポート範囲を "*"(任意) から 1433 (SQL ポート) に変更する必要があります。これにより、Web アプリケーションが侵害されても、AppVM01 に対する受信攻撃の可能性は低くなります。

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
		    -Type Inbound -Priority 130 -Action Allow `
	    -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
	    -DestinationAddressPrefix $VMIP[2] `
	    -DestinationPortRange '*' `
	    -Protocol *
 
6.	このルールは、インターネットから、ネットワーク上の任意のサーバーに向かうトラフィックを拒否します。優先度 110 および 120 のルールとの組み合わせで、他のサーバーとつながっているファイアウォールおよび RDP ポートへの受信インターネット トラフィックのみを許可し、他はすべてブロックします。

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
		    Set-AzureNetworkSecurityRule `
		    -Name "Isolate the $VNetName VNet from the Internet" `
		    -Type Inbound -Priority 140 -Action Deny `
		    -SourceAddressPrefix INTERNET -SourcePortRange '*' `
		    -DestinationAddressPrefix VIRTUAL_NETWORK `
		    -DestinationPortRange '*' `
		    -Protocol *
 
7.	最後のルールでは、フロントエンド サブネットからバックエンド サブネットへのトラフィックを拒否します。これは受信専用のルールであるため、逆方向のトラフィック (バックエンドからフロントエンドへのトラフィック) は許可されます。

		Get-AzureNetworkSecurityGroup -Name $NSGName | `
    		Set-AzureNetworkSecurityRule `
    		-Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
    		-Type Inbound -Priority 150 -Action Deny `
    		-SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
    		-DestinationAddressPrefix $BEPrefix `
    		-DestinationPortRange '*' `
    		-Protocol * 

## トラフィックのシナリオ
#### (許可) Web から Web サーバーへ
1.	インターネット ユーザーが FrontEnd001.CloudApp.Net (インターネットに直接つながっているクラウド サービス) に HTTP ページを要求します。
2.	クラウド サービスが、ポート 80 上の開いているエンドポイントを経由して IIS01 (Web サーバー) にトラフィックを渡します。
3.	フロントエンド サブネットは、以下に示す受信ルールの処理を開始します。
  1.	NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
  2.	NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
  3.	NSG ルール 3 (インターネットと IIS01 の間) は適用されません。トラフィックは許可されます。ルールの処理を停止します。
4.	トラフィックは Web サーバー IIS01 の内部 IP アドレス (10.0.1.5) に到達します。
5.	IIS01 は Web トラフィックをリッスンしています。この要求を受け取ると、要求の処理を開始します。
6.	IIS01 が AppVM01 上の SQL Server に情報を求めます。
7.	フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
8.	バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
  1.	NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
  2.	NSG ルール 2 (RDP) は該当しません。次のルールに進みます。
  3.	NSG ルール 3 (インターネットからファイアウォール) は該当しません。次のルールに進みます。
  4.	NSG ルール 4 (IIS01 から AppVM01) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
9.	AppVM01 は SQL クエリを受信し、応答します。
10.	バックエンド サブネットに送信ルールは存在しないので、応答は許可されます。
11.	フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
  1.	バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
  2.	サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
12.	IIS サーバーは SQL 応答を受信すると、HTTP 応答を完了して、要求元に送信します。
13.	フロントエンド サブネットに対する送信ルールは存在しないので、応答は許可され、インターネット ユーザーは要求した Web ページを受信します。

#### (許可) RDP とバックエンドの間
1.	インターネット上のサーバー管理者が BackEnd001.CloudApp.Net:xxxxx 上の AppVM01 に対する RDP セッションを要求します。ここで、xxxxx は AppVM01 に対する RDP のポート番号で、ランダムに割り当てられます (割り当てられたポートは、Azure ポータル上で、または PowerShell を使用して見つけることができます)。
2.	バックエンド サブネットは、以下に示す受信ルールの処理を開始します。
  1.	NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
  2.	NSG ルール 2 (RDP) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
3.	送信ルールがない場合は、既定のルールが適用され、戻りトラフィックが許可されます。
4.	RDP セッションが可能な状態になります。
5.	AppVM01 はユーザー名とパスワードを求めるメッセージを表示します。

#### (許可) DNS サーバー上での Web サーバー DNS の参照
1.	Web サーバーである IIS01 は、www.data.gov でのデータ フィードを必要としますが、アドレスを解決する必要があります。
2.	VNet 用のネットワーク構成にはプライマリ DNS サーバーとして、DNS01 (バックエンド サブネット上の 10.0.2.4) がリストされており、IIS01 は DNS 要求を DNS01 に送信します。
3.	フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
4.	バックエンド サブネットは、以下に示す受信ルールの処理を開始します。
  1.	 NSG ルール 1 (DNS) が該当し、トラフィックが許可され、ルールの処理はここで終了します。
5.	DNS サーバーは要求を受信します。
6.	DNS サーバーは、アドレスがキャッシュされていないことがわかると、インターネット上のルート DNS サーバーに要求します。
7.	バックエンド サブネットに送信ルールはないので、トラフィックは許可されます。
8.	インターネット DNS サーバーが応答します。このセッションは内部から開始されたため、応答は許可されます。
9.	DNS サーバーは応答をキャッシュし、最初の要求に応答して IIS01 に返します。
10.	バックエンド サブネットに送信ルールはないので、トラフィックは許可されます。
11.	フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
  1.	バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
  2.	サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
12.	IIS01 は DNS01 から応答を受信します。

#### (許可) Web サーバーが AppVM01 上のファイルにアクセスする
1.	IIS01 が AppVM01 上のファイルを要求します。
2.	フロントエンド サブネットに送信ルールはないので、トラフィックは許可されます。
3.	バックエンド サブネットが、以下に示す受信ルールの処理を開始します。
  1.	NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
  2.	NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
  3.	NSG ルール 3 (インターネットと IIS01 の間) は適用されません。次のルールに進みます。
  4.	NSG ルール 4 (IIS01 と AppVM01 の間) は適用されません。トラフィックは許可されます。ルールの処理を停止します。
4.	AppVM01 が要求を受信し、ファイルを返します (アクセスが許可されている場合)。
5.	バックエンド サブネットに送信ルールは存在しないので、応答は許可されます。
6.	フロントエンド サブネットが、以下に示す受信ルールの処理を開始します。
  1.	バックエンド サブネットからフロントエンド サブネットへの受信トラフィックに適用される NSG ルールは存在しません。つまり、該当する NSG ルールはありません。
  2.	サブネット間のトラフィックを許可する既定のシステム ルールであれば、このトラフィックを許可するので、トラフィックは許可されます。
7.	IIS サーバーはファイルを受信します。

#### (拒否) Web とバックエンド サーバーの間
1.	インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して AppVM01 上のファイルに対してアクセスを試みます。
2.	ファイル共有用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3.	何らかの理由でエンドポイントが開いていたとしても、NSG ルール 5 (インターネットと VNet の間) によってこのトラフィックはブロックされます。

#### (拒否) DNS サーバー上での Web DNS の参照
1.	インターネット ユーザーが、BackEnd001.CloudApp.Net サービスを介して DNS01 上の内部 DNS レコードを参照しようとしています。
2.	DNS 用に開放されたエンドポイントがないので、このアクセスはクラウド サービスを通過できず、サーバーに到達しません。
3.	何らかの理由でエンドポイントが開いていたとしても、NSG ルール 5 (インターネットと VNet の間) によってこのトラフィックはブロックされます (ルール 1 (DNS) は次の 2 つの理由で適用されません。1 つ目はソース アドレスがインターネットであるということ。このルールはソースとしてのローカル VNet にしか適用されません。2 つ目は Allow ルールであり、トラフィックを拒否しないということです)。

#### (拒否) ファイアウォールを経由した Web と SQL 間のアクセス
1.	インターネット ユーザーが FrontEnd001.CloudApp.Net (インターネットに直接つながっているクラウド サービス) に SQL データを要求します。
2.	SQL 用に開放されているエンドポイントがないので、このアクセスはクラウド サービスを通過できず、ファイアウォールに到達しません。
3.	何らかの理由でエンドポイントが開放されていた場合、フロントエンド サブネットは、受信ルールの処理を開始します。
  1.	NSG ルール 1 (DNS) は該当しません。次のルールに進みます。
  2.	NSG ルール 2 (RDP) は適用されず、次のルールに進みます。
  3.	NSG ルール 3 (インターネットと IIS01 の間) は適用されません。トラフィックは許可されます。ルールの処理を停止します。
4.	トラフィックは IIS01 の内部 IP アドレス (10.0.1.5) に到達します。
5.	IIS01 は、ポート 1433でリッスンしていないので、要求に対する応答はありません。

## まとめ
これは、バックエンド サブネットを受信トラフィックから分離する方法としては比較的に単純な例です。

その他の例およびネットワーク セキュリティ境界の概要については、[こちら][HOME]を参照してください。

## 参照
### 主要なスクリプトとネットワーク構成
PowerShell スクリプト ファイルに完全なスクリプトを保存します。"NetworkConf1.xml" という名前のファイルにネットワーク構成を保存します。必要に応じて、ユーザー定義の変数を変更します。スクリプトを実行し、前述の「例 1」セクションに含まれているファイアウォール ルールのセットアップ手順に従ってください。

#### 完全なスクリプト
このスクリプトは、ユーザー定義の変数に基づいています。

1.	Azure サブスクリプションに接続する
2.	新しいストレージ アカウントの作成
3.	ネットワーク構成ファイルの定義に従って VNet を 1 つ、サブネットを 2 つ新規に作成する
4.	Windows サーバーの VM を 4 つ作成する
5.	NSG を構成する
  -	NSG を作成
  -	NSG にルールを設定
  -	適切なサブネットに NSG をバインド

この PowerShell スクリプトは、インターネットに接続されている PC またはサーバー上でローカルに実行する必要があります。

>[AZURE.IMPORTANT]このスクリプトを実行すると、PowerShell に警告またはその他の情報メッセージが表示される場合があります。赤色のエラー メッセージのみが問題の原因となります。


	<# 
	 .SYNOPSIS
	  Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
	
	 .DESCRIPTION
	  This script will build out a sample DMZ setup containing:
	   - A default storage account for VM disks
	   - Two new cloud services
	   - Two Subnets (FrontEnd and BackEnd subnets)
	   - One server on the FrontEnd Subnet
	   - Three Servers on the BackEnd Subnet
	   - Network Security Groups to allow/deny traffic patterns as declared
	  
	  Before running script, ensure the network configuration file is created in
	  the directory referenced by $NetworkConfigFile variable (or update the
	  variable to reflect the path and file name of the config file being used).
	
	 .Notes
	  Security requirements are different for each use case and can be addressed in a
	  myriad of ways. Please be sure that any sensitive data or applications are behind
	  the appropriate layer(s) of protection. This script serves as an example of some
	  of the techniques that can be used, but should not be used for all scenarios. You
	  are responsible to assess your security needs and the appropriate protections
	  needed, and then effectively implement those protections.
	
	  FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
	   IIS01      - 10.0.1.5
	 
	  BackEnd Service (BackEnd subnet 10.0.2.0/24)
	   DNS01      - 10.0.2.4
	   AppVM01    - 10.0.2.5
	   AppVM02    - 10.0.2.6
	
	#>
	
	# Fixed Variables
	    $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
	    $VMName = @()
	    $ServiceName = @()
	    $VMFamily = @()
	    $img = @()
	    $size = @()
	    $SubnetName = @()
	    $VMIP = @()
	
	# User Defined Global Variables
	  # These should be changes to reflect your subscription and services
	  # Invalid options will fail in the validation section
	
	  # Subscription Access Details
	    $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	
	  # VM Account, Location, and Storage Details
	    $LocalAdmin = "theAdmin"
	    $DeploymentLocation = "Central US"
	    $StorageAccountName = "vmstore02"
	
	  # Service Details
	    $FrontEndService = "FrontEnd001"
	    $BackEndService = "BackEnd001"
	
	  # Network Details
	    $VNetName = "CorpNetwork"
	    $FESubnet = "FrontEnd"
	    $FEPrefix = "10.0.1.0/24"
	    $BESubnet = "BackEnd"
	    $BEPrefix = "10.0.2.0/24"
	    $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
	
	  # VM Base Disk Image Details
	    $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
	  
	  # NSG Details
	    $NSGName = "MyVNetSG"
	
	# User Defined VM Specific Config
	    # Note: To ensure proper NSG Rule creation later in this script:
	    #       - The Web Server must be VM 0
	    #       - The AppVM1 Server must be VM 1
	    #       - The DNS server must be VM 3
	    #
	    #       Otherwise the NSG rules in the last section of this
	    #       script will need to be changed to match the modified
	    #       VM array numbers ($i) so the NSG Rule IP addresses
	    #       are aligned to the associated VM IP addresses.
	
	    # VM 0 - The Web Server
	      $VMName += "IIS01"
	      $ServiceName += $FrontEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $FESubnet
	      $VMIP += "10.0.1.5"
	
	    # VM 1 - The First Application Server
	      $VMName += "AppVM01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.5"
	
	    # VM 2 - The Second Application Server
	      $VMName += "AppVM02"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.6"
	
	    # VM 3 - The DNS Server
	      $VMName += "DNS01"
	      $ServiceName += $BackEndService
	      $VMFamily += "Windows"
	      $img += $SrvImg
	      $size += "Standard_D3"
	      $SubnetName += $BESubnet
	      $VMIP += "10.0.2.4"

	# ----------------------------- #
	# No User Defined Varibles or   #
	# Configuration past this point #
	# ----------------------------- #	

	  # Get your Azure accounts
	    Add-AzureAccount
	    Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
	    Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
	
	  # Create Storage Account
	    If (Test-AzureName -Storage -Name $StorageAccountName) { 
	        Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
	        Return}
	    Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
	          New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
	
	  # Update Subscription Pointer to New Storage Account
	    Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
	    Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
	
	# Validation
	$FatalError = $false
	
	If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
	     Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
	     $FatalError = $true}
	
	If (Test-AzureName -Service -Name $FrontEndService) { 
	    Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
	
	If (Test-AzureName -Service -Name $BackEndService) { 
	    Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
	
	If (-Not (Test-Path $NetworkConfigFile)) { 
	    Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
	    $FatalError = $true}
	Else { Write-Host "The network config file was found" -ForegroundColor Green
	        If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
	            Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
	            $FatalError = $true}
	        Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
	
	If ($FatalError) {
	    Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
	    Return}
	Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
	
	# Create VNET
	    Write-Host "Creating VNET" -ForegroundColor Cyan 
	    Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
	
	# Create Services
	    Write-Host "Creating Services" -ForegroundColor Cyan
	    New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
	    New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
	
	# Build VMs
	    $i=0
	    $VMName | Foreach {
	        Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
	        New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
	            Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
	            Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
	            Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
	            Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
	            Remove-AzureEndpoint -Name "PowerShell" | `
	            New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
	            # Note: A Remote Desktop endpoint is automatically created when each VM is created.
	        $i++
	    }
	    # Add HTTP Endpoint for IIS01
	    Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
	
	# Configure NSG
	    Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
	    
	  # Build the NSG
	    Write-Host "Building the NSG" -ForegroundColor Cyan
	    New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
	
	  # Add NSG Rules
	    Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
	        -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
	        -SourceAddressPrefix Internet -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
	        -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
	        -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
	        -Protocol *
	    
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
	        -SourceAddressPrefix INTERNET -SourcePortRange '*' `
	        -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
	        -Protocol *
	
	    Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
	        -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
	        -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
	        -Protocol *
	
	    # Assign the NSG to the Subnets
	        Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
	        Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
	
	# Optional Post-script Manual Configuration
	  # Install Test Web App (Run Post-Build Script on the IIS Server)
	  # Install Backend resource (Run Post-Build Script on the AppVM01)
	  Write-Host
	  Write-Host "Build Complete!" -ForegroundColor Green
	  Write-Host
	  Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
	  Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
	  Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
	  Write-Host
	  

#### ネットワーク構成ファイル
この xml ファイルに実際のロケーション情報に反映して保存し、このファイルへのリンクを上記スクリプト内の $NetworkConfigFile 変数に追加します。
	
	<NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
	  <VirtualNetworkConfiguration>
	    <Dns>
	      <DnsServers>
	        <DnsServer name="DNS01" IPAddress="10.0.2.4" />
	        <DnsServer name="Level3" IPAddress="209.244.0.3" />
	      </DnsServers>
	    </Dns>
	    <VirtualNetworkSites>
	      <VirtualNetworkSite name="CorpNetwork" Location="Central US">
	        <AddressSpace>
	          <AddressPrefix>10.0.0.0/16</AddressPrefix>
	        </AddressSpace>
	        <Subnets>
	          <Subnet name="FrontEnd">
	            <AddressPrefix>10.0.1.0/24</AddressPrefix>
	          </Subnet>
	          <Subnet name="BackEnd">
	            <AddressPrefix>10.0.2.0/24</AddressPrefix>
	          </Subnet>
	        </Subnets>
	        <DnsServersRef>
	          <DnsServerRef name="DNS01" />
	          <DnsServerRef name="Level3" />
	        </DnsServersRef>
	      </VirtualNetworkSite>
	    </VirtualNetworkSites>
	  </VirtualNetworkConfiguration>
	</NetworkConfiguration>

#### サンプル アプリケーション スクリプト
これに対応するサンプル アプリケーション、およびその他の DMZ の例をインストールしたい場合は、[サンプル アプリケーション スクリプト][SampleApp]をご利用ください。

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "NSG での受信 DMZ"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

<!---HONumber=AcomDC_0121_2016-->