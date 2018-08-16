---
title: Ethereum Proof-of-Authority Consortium
description: Etherereum Proof-of-Authority Consortium ソリューションを使用してマルチメンバー コンソーシアム型 Ethereum ネットワークをデプロイおよび構成する
services: azure-blockchain
keywords: ''
author: CodyBorn
ms.author: coborn
ms.date: 8/2/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: vamelech
ms.openlocfilehash: fcb35f23be384b3625e4e17e5dc2285b7eeb341a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2018
ms.locfileid: "39530060"
---
# <a name="ethereum-proof-of-authority-consortium"></a>Ethereum Proof-of-Authority Consortium

[このソリューション](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)は、Azure と Ethereum の最小限の知識で、マルチメンバー コンソーシアム型 Proof-of-Authority Ethereum ネットワークを簡単にデプロイ、構成、および管理できるように設計されています。

Azure Portal でいくつかの項目を入力し、1 回のクリックでデプロイできます。各メンバーは、世界中の Microsoft Azure Compute、ネットワーキング、およびストレージ サービスを使用して、ネットワーク フットプリントをプロビジョニングできます。 各メンバーのネットワーク フットプリントは一連の負荷分散型バリデーター ノードで構成されます。アプリケーションまたはユーザーはそれらのノードと対話して、Ethereum トランザクションを送信できます。

## <a name="concepts"></a>概念

### <a name="terminology"></a>用語集

-   **コンセンサス** - ブロックの検証と作成を通じて分散ネットワーク全体でデータを同期する行為。

-   **コンソーシアム メンバー** - ブロックチェーン ネットワーク上のコンセンサスに参加するエンティティ。

-   **管理者** - 特定のコンソーシアム メンバーの参加を管理するために使用される Ethereum アカウント。

-   **バリデーター** - 管理者に代わってコンセンサスに参加する Ethereum アカウントに関連付けられたマシン。

### <a name="proof-of-authority"></a>Proof-of-Authority

ブロックチェーン コミュニティに参加したばかりなら、このソリューションのリリースをきっかけにしてこの技術を学習してください。Azure 上で簡単に、また構成しながら学習できます。 Proof-of-Work は、計算コストを利用してネットワークを自己規制し、公正な参加を可能にする Sybil 攻撃対策メカニズムです。 これは、暗号通貨を得るための競争によってネットワーク上のセキュリティが推進される、匿名のオープンなブロックチェーン ネットワークでは良好に機能します。 しかし、プライベート/コンソーシアム ネットワークにおいて、基盤となる Ether が何の価値も持ちません。 すべてのコンセンサス参加者が既知であり信頼できる許可型のネットワークには、代替プロトコルの 1 つである Proof-of-Authority のほうが適しています。 マイニングを必要としない Proof-of-Authority では、ビザンチン フォールトトレランスを維持しながら、より高い効率が得られます。

### <a name="consortium-governance"></a>コンソーシアムのガバナンス

Proof-of-Authority は、ネットワークの正常性を維持するために許可されたネットワーク機関のリストに依存するので、この許可リストに変更を加えるための公正なメカニズムを提供することが重要です。 各デプロイには、この許可リストのオンチェーン ガバナンスのための一連のスマート コントラクトとポータルが付属しています。 提案された変更がコンソーシアム メンバーによる投票で過半数に達すると、変更が適用されます。 これにより、新しいコンセンサス参加者の追加や危害を受けた参加者の削除を透明な方法で行うことができ、誠実なネットワークが促進されます。

### <a name="admin-account"></a>管理者アカウント

Proof-of-Authority ノードのデプロイ時には、管理者 Ethereum アドレスの入力を求められます。 この Ethereum アカウントは、いくつかの異なるメカニズムを使用して生成およびセキュリティ保護することができます。 このアドレスがネットワーク上で機関として追加されると、このアカウントを使用してガバナンスに参加できるようになります。 この管理者アカウントは、デプロイの一環として作成されたバリデーター ノードにコンセンサスへの参加を委任するときにも使用されます。 パブリックな Ethereum アドレスのみを使用するため、各管理者が、希望するセキュリティ モデルに準拠した方法で自分の秘密キーを柔軟にセキュリティで保護できます。

### <a name="validator-node"></a>バリデーター ノード

Proof-of-Authority プロトコルでは、バリデーター ノードが従来のマイナー ノードの代わりとなります。 各バリデーターは、スマート コントラクトの許可リストに追加される一意の Ethereum ID を持ちます。 このリストに追加されたバリデーターは、ブロックの作成プロセスに参加できます。 このプロセスの詳細については、Parity の [Authority Round コンセンサス](https://wiki.parity.io/Aura)に関するドキュメントを参照してください。 各コンソーシアム メンバーは、geo 冗長性のために、5 つのリージョンで 2 つ以上のバリデーター ノードをプロビジョニングできます。 バリデーター ノードは他のバリデーター ノードと通信して、基礎となる分散型台帳の状態についてコンセンサスを形成します。
ネットワークでの公正な参加を保証するために、各コンソーシアム メンバーは、ネットワーク上の最初のメンバーよりも多くのバリデーターを使用することを禁止されます (最初のメンバーが 3 つのバリデーターをデプロイした場合、各メンバーが使用できるバリデーターは 3 つまでに制限されます)。

### <a name="identity-store"></a>ID ストア

各メンバーが同時に実行されるバリデーター ノードを持ち、各ノードが許可された ID を持つ必要があるため、バリデーターがネットワーク上でアクティブな一意の ID を安全に取得できることが重要です。 それを容易にするために、各メンバーのサブスクリプションにデプロイされ、生成された Ethereum ID を安全に保持する ID ストアが構築されています。 デプロイ時に、オーケストレーション コンテナーは各バリデーター用の Ethereum 秘密キーを生成し、Azure Key Vault に格納します。 パリティ ノードは起動前に、まず未使用の ID に対してリースを取得し、その ID が別のノードによって取得されないようにします。 その ID は、ブロックの作成を開始する権限をパリティ ノードに与えるクライアントに提供されます。 ホスト VM で障害が発生した場合は、ID のリースがリリースされ、置換ノードが後でその ID を再開できるようになります。

### <a name="bootnode-registrar"></a>ブートノード レジストラー

接続を簡単にするために、各メンバーは、接続情報のセットを[データ API エンドポイント](#data-api)でホストします。 このデータには、参加メンバー用のピアリング ノードとして提供されるブートノードのリストが含まれています。 マイクロソフトでは、このブートノード リストを、このデータ API の一部として最新の状態に維持しています。

### <a name="bring-your-own-operator"></a>Bring Your Own Operator

コンソーシアム メンバーがネットワーク ガバナンスへの参加を希望しているが、インフラストラクチャの運用と管理を行うことは希望していないという場合はよくあります。 従来のシステムとは異なり、ネットワーク全体でオペレーターを 1 人しか置かないことはブロックチェーン システムの分散型モデルに反します。 ネットワークの運用のために 1 人の中央集権的な仲介者を雇う代わりに、各コンソーシアム メンバーが、自分で選択したオペレーターにインフラストラクチャの管理を委任できます。 これにより、各メンバーが自分のインフラストラクチャを自ら運用するか、別のパートナーに操作を委任するかを選択できるハイブリッド モデルが実現します。 委任された運用のワークフローは次のように機能します。

1.  **コンソーシアム メンバー**が Ethereum アドレスを生成します (秘密キーを保持)

2.  **コンソーシアム メンバー**が**オペレーター**にパブリック Ethereum アドレスを提供します

3.  **オペレーター**が Azure Resource Manager ソリューションを使用して PoA バリデーター ノードをデプロイし、構成します

4.  **オペレーター**が**コンソーシアム メンバー**に RPC と管理エンドポイントを提供します

5.  **コンソーシアム メンバー**が自分の秘密キーを使用して要求に署名し、**オペレーター**によってデプロイされたバリデーター ノードが自分に代わって参加することを承認します

![Bring Your Own Operator](./media/ethereum-poa-deployment-guide/bring-you-own-operator.png)

### <a name="azure-monitor"></a>Azure Monitor

また、このソリューションには、ノードとネットワークの統計情報を追跡する Azure Monitor が付属しています。 アプリケーション開発者はこれを使用して基になるブロックチェーンを表示し、ブロック生成の統計情報を追跡できます。 ネットワーク オペレーターは、Azure Monitor のインフラストラクチャ統計情報とクエリ可能なログを使用して、ネットワークの停止を迅速に検出/予防できます。 詳細については、「[サービスの監視](#service-monitoring)」を参照してください。

### <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

#### <a name="description"></a>説明

このソリューションは、単一または複数のリージョンベースのマルチメンバー Ethereum コンソーシアム ネットワークをデプロイできます。 既定では、サブスクリプションとクラウド間の接続を簡単にするために、RPC とピアリング エンドポイントにはパブリック IP を使用してアクセスできます。 アプリケーション レベルのアクセス制御には、[Parity のアクセス許可コントラクト](https://wiki.parity.io/Permissioning)を利用することをお勧めします。 また、サブスクリプション間に VNet ゲートウェイを利用する、VPN の背後にデプロイされたネットワークもサポートされています。 これらのデプロイはより複雑なため、最初はパブリック IP モデルから始めることをお勧めします。

#### <a name="consortium-member-overview"></a>コンソーシアム メンバーの概要

各コンソーシアム メンバーのデプロイには以下が含まれます。

-   PoA バリデーターを実行するための Virtual Machine Scale Sets (VMSS)

-   RPC、ピアリング、およびガバナンス DApp の要求を分散するための Azure Load Balancer

-   バリデーター ID をセキュリティで保護するための Azure Key Vault

-   永続的なネットワーク情報をホストし、リースを調整するための Azure Storage

-   ログとパフォーマンスの統計情報を集約するための Azure Monitor

-   プライベート VNet 間の VPN 接続を可能にするための VNet ゲートウェイ (オプション)

![デプロイ アーキテクチャ](./media/ethereum-poa-deployment-guide/deployment-architecture.png)

信頼性とモジュール性のために、Docker コンテナーを利用します。 バージョン管理されたイメージを各デプロイの一部としてホストし使用するために、Azure Container Registry を使用します。 コンテナー イメージは以下で構成されます。

-   オーケストレーター

    -   デプロイ時に 1 回実行されます

    -   ID とガバナンス コントラクトを生成します

    -   ID ストアに ID を格納します

-   Parity クライアント

    -   ID ストアから ID をリースします

    -   ピアを検出し、接続します

-   EthStats エージェント

    -   RPC 経由でローカル ログと統計情報を収集し、Azure Monitor にプッシュします

-   ガバナンス DApp

    -   ガバナンス コントラクトを操作するための Web インターフェイス

## <a name="governance-dapp"></a>ガバナンス DApp

Proof-of-Authority の中核をなすのは分散型ガバナンスです。 ガバナンス DApp は、ネットワーク上の機関を管理するために使用される、事前にデプロイされたスマート コントラクトのセットと Web アプリケーションです。
機関は管理 ID とバリデーター ノードに分けられます。
管理者は、コンセンサスへの参加を一連のバリデーター ノードに委任する権限を持ちます。 また、管理者は、他の管理者のネットワーク参加またはネットワーク脱退に投票することもできます。

![ガバナンス DApp](./media/ethereum-poa-deployment-guide/governance-dapp.png)

-   **分散型ガバナンス -** ネットワーク機関での変更は、選ばれた管理者によるオンチェーン投票を通じて管理されます。

-   **バリデーターへの委任 -** 機関は、各 PoA デプロイに設定されている自分のバリデーター ノードを管理できます。

-   **監査可能な変更履歴 -** 各変更はブロックチェーンに記録され、透明性と監査機能が提供されます。

## <a name="how-to-guides"></a>ハウツー ガイド

### <a name="deploy-ethereum-proof-of-authority"></a>Ethereum Proof-of-Authority のデプロイ

以下に、マルチパーティー デプロイのフローの例を示します。

1.  3 人のメンバーが、MetaMask を使用してそれぞれ Ethereum アカウントを生成します

2.  *メンバー A* が、3 人のパブリック Ethereum アドレスを入力して Ethereum PoA をデプロイします

3.  *メンバー A* が、*メンバー B* と*メンバー C* にコンソーシアムの URL を提供します

4.  *メンバー B* と*メンバー C* が、自分たちのパブリック Ethereum アドレスと*メンバー A* のコンソーシアムの URL を入力して Ethereum PoA をデプロイします

5.  *メンバー A* が、*メンバー B* を管理者とする投票をします

6.  *メンバー A* と*メンバー B* の両方が、*メンバー C* を管理者とする投票をします

このプロセスでは、複数の仮想マシン スケール セットとマネージド ディスクのデプロイに対応できる Azure サブスクリプションが必要です。 必要に応じて、[無料の Azure アカウントを作成](https://azure.microsoft.com/free/)して始めてください。

サブスクリプションを取得したら、Azure portal を開きます。 [+] を選択し、Marketplace ([すべて表示]) で「Ethereum PoA Consortium」を検索します。

次のセクションでは、ネットワークで最初のメンバーのフットプリントを構成する手順について説明します。 デプロイ フローは、[基本]、[Deployment regions]\(デプロイ リージョン\)、[Network size and performance]\(ネットワークのサイズとパフォーマンス\)、[Ethereum settings]\(Ethereum の設定\)、[Azure Monitor] という 5 つの手順に分かれています。

#### <a name="basics"></a>基本

**[基本]** では、サブスクリプション、リソース グループ、基本仮想マシンのプロパティなど、デプロイの標準パラメーターの値を指定します。

各パラメーターの詳しい説明は次のとおりです。

パラメーター名|説明|使用できる値|既定値    
---|---|---|---
Create a new network or join existing network? (ネットワークの新規作成または既存のネットワークに参加)|新しいネットワークを作成するか、既存のコンソーシアム ネットワークに参加します|[新規作成] または [Join Existing]\(既存に参加\)|Create New
電子メール アドレス (省略可能)|デプロイの完了時に、デプロイに関する情報を含んだ電子メール通知が送信されます。|有効な電子メール アドレス|該当なし
VM ユーザー名|デプロイされた各 VM の管理者ユーザー名 (英数字のみ)|1 - 64 文字|該当なし
認証の種類|仮想マシンに対して認証する方法。|[パスワード] または [SSH 公開キー]|パスワード
パスワード ([認証の種類] = [パスワード])|デプロイされた各仮想マシンの管理者アカウントのパスワード。  パスワードには、小文字、大文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります。 VM にはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます。|12 - 72 文字|該当なし
SSH キー ([認証の種類] = [公開キー])|リモート ログインに使用される Secure Shell キー。||該当なし
サブスクリプション|コンソーシアム ネットワークをデプロイするサブスクリプション||該当なし
リソース グループ|コンソーシアム ネットワークをデプロイするリソース グループ||該当なし
Location|リソース グループの Azure リージョン。||該当なし

デプロイの例を次に示します: ![[基本] ブレード](./media/ethereum-poa-deployment-guide/basic-blade.png)

#### <a name="deployment-regions"></a>Deployment regions (デプロイ リージョン)

次に、[Deployment regions]\(デプロイ リージョン\) で、コンソーシアム ネットワークをデプロイするリージョン数を入力し、指定したリージョン数に基づいて Azure リージョンを選択します。 最大 5 つのリージョンにデプロイできます。 1 番目のリージョンには、[基本] セクションのリソース グループの場所と同じリージョンを選択することをお勧めします。 開発またはテスト用のネットワークでは、メンバーごとに 1 つのリージョンを選択することをお勧めします。 運用環境では、高可用性のために、2 つ以上のリージョンにデプロイすることをお勧めします。

各パラメーターの詳しい説明は次のとおりです。

  パラメーター名|説明|使用できる値|既定値
  ---|---|---|---
  Number of region(s) (リージョン数)|コンソーシアム ネットワークをデプロイするリージョンの数|1、2、3、4、5|1
  First region (リージョン 1)|コンソーシアム ネットワークをデプロイする 1 つ目のリージョン|許可されているすべての Azure リージョン|該当なし
  Second region (リージョン 2)|コンソーシアム ネットワークをデプロイする 2 つ目のリージョン (リージョン数を 2 以上に指定した場合にのみ表示されます)|許可されているすべての Azure リージョン|該当なし
  Third region (リージョン 3)|コンソーシアム ネットワークをデプロイする 3 つ目のリージョン (リージョン数を 3 以上に指定した場合にのみ表示されます)|許可されているすべての Azure リージョン|該当なし
  Fourth region (リージョン 4)|コンソーシアム ネットワークをデプロイする 4 つ目のリージョン (リージョン数を 4 以上に指定した場合にのみ表示されます)|許可されているすべての Azure リージョン|該当なし
  Fifth region (リージョン 5)|コンソーシアム ネットワークをデプロイする 5 つ目のリージョン (リージョン数を 5 に指定した場合にのみ表示されます)|許可されているすべての Azure リージョン|該当なし

デプロイの例を次に示します: ![[deployment regions]\(デプロイ リージョン\)](./media/ethereum-poa-deployment-guide/deployment-regions.png)

#### <a name="network-size-and-performance"></a>Network size and performance (ネットワークのサイズとパフォーマンス) 

次に、[Network Size and Performance]\(ネットワークのサイズとパフォーマンス\) で、バリデーター ノードの数やサイズなど、コンソーシアム ネットワークのサイズを入力します。
バリデーター ノードのストレージ サイズにより、ブロックチェーンの可能なサイズが決まります。 これはデプロイ後に変更できます。

各パラメーターの詳しい説明は次のとおりです。

  パラメーター名|説明|使用できる値|既定値
  ---|---|---|---
  Number of load balanced validator nodes (負荷分散型バリデータ ノードの数)|ネットワークの一部としてプロビジョニングするバリデーター ノードの数。|2 - 15|2
  Validator node storage performance (バリデーター ノード ストレージのパフォーマンス)|デプロイされる各バリデーター ノードをバッキングするマネージド ディスクの種類。|Standard または Premium|標準
  Validator node virtual machine size (バリデーター ノードの仮想マシンのサイズ)|バリデーター ノードに使用される仮想マシンのサイズ。|Standard A、Standard D、Standard D-v2、Standard F シリーズ、Standard DS、Standard FS|Standard D1 v2

デプロイの例を次に示します: ![[network size and performance](./media/ethereum-poa-deployment-guide/network-size-and-performance.png)]\(ネットワークのサイズとパフォーマンス\)

#### <a name="ethereum-settings"></a>Ethereum Settings (Ethereum の設定)

次に、[Ethereum settings]\(Ethereum の設定\) で、ネットワーク ID と Ethereum アカウントのパスワード、ジェネシス ブロックなど、Ethereum 関連の構成設定を指定します。

各パラメーターの詳しい説明は次のとおりです。

  パラメーター名|説明|使用できる値|既定値
  ---|---|---|---
Consortium Member ID (コンソーシアム メンバー ID)|コンソーシアム ネットワークに参加する各メンバーに関連付けられている ID。競合を回避するように IP アドレス 空間を構成するために使用されます。 プライベート ネットワークの場合、メンバー ID は同じネットワーク内のさまざまな組織全体で一意である必要があります。  同じ組織が複数のリージョンにデプロイする場合でも、一意のメンバー ID が必要です。 競合が発生しないように、他の参加メンバーと共有する必要があるため、このパラメーターの値を書き留めておきます。|0 - 255|該当なし
ネットワーク ID|デプロイされているコンソーシアム Ethereum ネットワークのネットワーク ID。  各 Ethereum ネットワークには独自のネットワーク ID があり、1 はパブリック ネットワークの ID を示します。|5 から 999,999,999|10101010
Admin Ethereum Address (管理者 Ethereum アドレス)|PoA ガバナンスに参加するために使用される Ethereum アカウントのアドレス。  Ethereum アドレスの生成には MetaMask を使用することをお勧めします。|0x で始まる 42 文字の英数字|該当なし
詳細オプション|Ethereum 設定の詳細オプション|[Enable]\(有効\) または [Disable]\(無効\)|Disable
[パブリック IP] ([詳細オプション] = [Enable]\(有効\))|VNet ゲートウェイの背後でネットワークをデプロイし、ピアリング アクセスを排除します。 このオプションを選択した場合、接続に互換性を持たせるためにすべてのメンバーが VNet ゲートウェイを使用する必要があります。|パブリック IP またはプライベート VNet|パブリック IP
[Transaction Permission Contract]\(トランザクションのアクセス許可コントラクト\) ([詳細オプション] = [Enable]\(有効\))|トランザクションのアクセス許可コントラクトのバイトコード。 スマート コントラクトのデプロイと実行を、許可された Ethereum アカウントのリストのみに制限します。|コントラクトのバイトコード|該当なし

デプロイの例を次に示します: ![[Ethereum settings]\(Ethereum の設定\)](./media/ethereum-poa-deployment-guide/ethereum-settings.png)

#### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor] ブレードでは、ネットワークの Azure Monitor リソースを構成できます。 Azure Monitor は、ネットワークから役に立つメトリックとログを収集して表示し、ネットワークの正常性やデバッグの問題をすばやく確認できるようにします。

  パラメーター名|説明|使用できる値|既定値
  ---|---|---|---
監視|Azure Monitor を有効にするオプション|[Enable]\(有効\) または [Disable]\(無効\)|有効化
Connect to existing Log Analytics (既存の Log Analytics に接続する)|Azure Monitor の一部として新しい Log Analytics インスタンスを作成するか、既存のインスタンスに参加します|[新規作成] または [Join existing]\(既存に参加\)|新規作成
Azure Monitor Location (Azure Monitor の場所) ([Connect to Existing Azure Monitor]\(既存の Azure Monitor に接続する\) = [新規作成])|新しい Azure Monitor がデプロイされるリージョン|すべての Azure Monitor リージョン|該当なし
Existing Log Analytics Workspace Id (既存の Log Analytics ワークスペース ID) ([Connect to Existing Azure Monitor]\(既存の Azure Monitor に接続する\) = [Join existing]\(既存に参加\))|既存の Log Analytics インスタンスのワークスペース ID||該当なし
Existing Log Analytics Primary Key (既存の Log Analytics 主キー) ([Connect to Existing Azure Monitor]\(既存の Azure Monitor に接続する\) = [Join existing]\(既存に参加\))|既存の Azure Monitor インスタンスへの接続に使用される主キー||該当なし


デプロイの例を次に示します: ![[Azure Monitor]](./media/ethereum-poa-deployment-guide/azure-monitor.png)

#### <a name="summary"></a>まとめ

[概要] ブレードの各項目をクリックして、指定した内容を確認し、基本的なデプロイ前検証を実行します。 デプロイの前に、テンプレートとパラメーターをダウンロードできます。

法律条項とプライバシー条項を確認し、[購入] をクリックしてデプロイします。 デプロイに VNet ゲートウェイが含まれている場合は、デプロイに 45 - 50 分かかります。

#### <a name="post-deployment"></a>デプロイ後

##### <a name="deployment-output"></a>デプロイの出力 

デプロイが完了したら、確認の電子メールまたは Azure portal から必要なパラメーターにアクセスできます。 これらのパラメーターには以下が含まれています。

-   Ethereum RPC エンドポイント

-   ガバナンス ダッシュボードの URL

-   Azure Monitor の URL

-   データの URL

-   VNet ゲートウェイのリソース ID (オプション)

##### <a name="confirmation-email"></a>確認の電子メール 

([[基本] セクション](#basics)で) 電子メール アドレスを入力すると、デプロイの出力情報を含んだ電子メールがそのアドレス宛に送信されます。

![デプロイの電子メール](./media/ethereum-poa-deployment-guide/deployment-email.png)

##### <a name="portal"></a>ポータル

デプロイが正常に完了し、すべてのリソースがプロビジョニングされたら、リソース グループ内の出力パラメーターを表示できます。

1.  ポータルで目的のリソース グループを探します

2.  *[デプロイ]* に移動します

3.  リソース グループと同じ名前の一番上のデプロイを選択します

4.  *[出力]* を選択します

### <a name="growing-the-consortium"></a>コンソーシアムの拡張

コンソーシアムを拡張するには、まず物理ネットワークを接続する必要があります。
パブリック IP ベースのデプロイを使用する場合、この最初の手順はシームレスです。 VPN の背後でデプロイする場合は、「[VNet ゲートウェイの接続](#connecting-vnet-gateways)」セクションを参照し、[手順 2](#step-2-new-admin-deployment) の一部としてネットワーク接続を実行してください。

#### <a name="step-1-add-the-new-admin"></a>手順 1: 新しい管理者の追加

1.  新しい管理者のパブリック Ethereum アドレスを収集します

2.  ガバナンス DApp に移動して、Ethereum アドレスと適切なエイリアスを使用して新しい管理者を作成します

3.  他の既存のメンバーに新しい要求を通知して、他のメンバーがこの新しい管理者の追加に投票できるようにします

4.  投票が 51% に達すると、このメンバーが管理者として追加されます

![管理者の追加](./media/ethereum-poa-deployment-guide/adding-admin.png)

#### <a name="step-2-new-admin-deployment"></a>手順 2: 新しい管理者のデプロイ

1.  次の情報を参加するメンバーと共有します。 この情報は、デプロイ後の電子メールまたはポータルのデプロイの出力で確認できます。

    -  コンソーシアム データの URL

    -  デプロイ済みのノード数

    -  VNet ゲートウェイのリソース ID (VPN を使用する場合)

2.  デプロイするメンバーは、自分のネットワーク プレゼンスをデプロイするときに、次のことを念頭に置いて[同じソリューション](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium)を使用する必要があります。

    -  *[Join existing]\(既存に参加\)* を選択します

    -  公正な代表制を保証するために、残りのメンバーと同数のバリデーター ノードを選択します

    -  [前の手順](#step-1-add-the-new-admin)で提供されたのと同じ Ethereum アドレスを使用します

    -  提供された *[Consortium Data Url]\(コンソーシアム データの URL\)* を、*[Ethereum Settings]\(Ethereum の設定\)* で渡します

    -  ネットワークの残りの部分が VPN の背後にある場合は、詳細セクションで*プライベート VNet* を選択します

#### <a name="step-3-delegate-validators"></a>手順 3: バリデーターへの委任

新しい管理者は、自分の代わりに参加する権限をバリデーターに付与する必要があります。 メンバーのみが自分のキーをコントロールできるため、この操作は自動的には実行できません。\*

_\*ネットワークの 1 番目のメンバー のノードは自動的に追加できます。そのメンバーのバリデーター ノードを、ネットワーク上のジェネシス ブロックにプリコンパイルできるからです。_

新しい管理者は次の手順を実行する必要があります。

1.  *自分の*デプロイの一部としてデプロイされているガバナンス DApp を開きます

2.  [Validators]\(バリデーター\) タブに移動します

3.  デプロイ済みのバリデーターのそれぞれを選択し、**[追加]** をクリックします

![バリデーターの追加](./media/ethereum-poa-deployment-guide/adding-validators.png)

#### <a name="connecting-vnet-gateways"></a>VNet ゲートウェイの接続

プライベート ネットワークの場合は、さまざまなメンバーが VNet ゲートウェイ接続を使用して接続されます。 メンバーがネットワークに参加してトランザクション トラフィックを表示する前に、既存のメンバーが最終的な構成をVPN ゲートウェイで実行し、接続を受け入れる必要があります。 つまり、参加メンバーの Ethereum ノードは接続が確立するまで実行されません。 単一障害点が生じる可能性を減らすために、冗長なネットワーク接続 (メッシュ) をコンソーシアムに作成することをお勧めします。

新しいメンバーのデプロイ後に、既存のメンバーは新しいメンバーへの VNet ゲートウェイ接続を設定して、双方向接続を完成させる必要があります。 そのためには、この既存メンバーに次のものが必要です。

1.  接続するメンバーの VNet ゲートウェイのリソース ID (「デプロイの出力」を参照)

2.  共有の接続キー

既存のメンバーは、次の PowerShell スクリプトを実行して接続を完成させる必要があります。 ポータルの右上のナビゲーション バーにある Azure Cloud Shell を使用することをお勧めします。

![Cloud Shell](./media/ethereum-poa-deployment-guide/cloud-shell.png)

```bash
$MyGatewayResourceId = "<EXISTING_MEMBER_RESOURCEID>"
$OtherGatewayResourceId = "<NEW_MEMBER_RESOURCEID]"
$ConnectionName = "Leader2Member"
$SharedKey = "<NEW_MEMBER_KEY>"

## $myGatewayResourceId tells me what subscription I am in, what ResourceGroup and the VNetGatewayName
$splitValue = $MyGatewayResourceId.Split('/')
$MySubscriptionid = $splitValue[2]
$MyResourceGroup = $splitValue[4]
$MyGatewayName = $splitValue[8]

## $otherGatewayResourceid tells me what the subscription and VNet GatewayName are
$OtherGatewayName = $OtherGatewayResourceId.Split('/')[8]
$Subscription=Select-AzureRmSubscription -SubscriptionId $MySubscriptionid

## create a PSVirtualNetworkGateway instance for the gateway I want to connect to
$OtherGateway=New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
$OtherGateway.Name = $OtherGatewayName
$OtherGateway.Id = $OtherGatewayResourceId
$OtherGateway.GatewayType = "Vpn"
$OtherGateway.VpnType = "RouteBased"

## get a PSVirtualNetworkGateway instance for my gateway
$MyGateway = Get-AzureRmVirtualNetworkGateway -Name $MyGatewayName -ResourceGroupName $MyResourceGroup

## create the connection
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionName -ResourceGroupName $MyResourceGroup -VirtualNetworkGateway1 $MyGateway -VirtualNetworkGateway2 $OtherGateway -Location $MyGateway.Location -ConnectionType Vnet2Vnet -SharedKey $SharedKey -EnableBgp $True
```

### <a name="service-monitoring"></a>サービスの監視

Azure Monitor ポータルを表示するには、デプロイ電子メールにあるリンク先にアクセスするか、デプロイの出力 \[OMS\_PORTAL\_URL\] でパラメーターを見つけます。

ポータルには、まず、高レベルのネットワーク統計情報とノードの概要が表示されます。

![監視のカテゴリ](./media/ethereum-poa-deployment-guide/monitor-categories.png)

**[Node Overview]\(ノードの概要\)** を選択すると、ノードごとのインフラストラクチャ統計情報を確認できるポータルに移動します。

![ノードの統計情報](./media/ethereum-poa-deployment-guide/node-stats.png)

**[Network Stats]\(ネットワークの統計\)** を選択すると、Ethereum ネットワークの統計情報を確認できます。

![ネットワークの統計](./media/ethereum-poa-deployment-guide/network-stats.png)

#### <a name="sample-log-analytics-queries"></a>Log Analytics クエリの例

これらのダッシュボードの背後には、一連のクエリ可能な生ログがあります。 これらの生ログを、ダッシュボードのカスタマイズ、エラーの調査、アラートのしきい値の設定に使用できます。 ログ検索ツールで実行できるいくつかのクエリの例を以下に示します。

##### <a name="lists-blocks-that-have-been-reported-by-more-than-one-validator-useful-to-help-find-chain-forks"></a>複数のバリデーターでレポートされたブロックを一覧表示する。 チェーンのフォークを見つけるのに役立ちます。

```sql
MinedBlock_CL
| summarize DistinctMiners = dcount(BlockMiner_s) by BlockNumber_d, BlockMiner_s
| where DistinctMiners > 1
```

##### <a name="get-average-peer-count-for-a-specified-validator-node-averaged-over-5-minute-buckets"></a>指定したバリデーター ノードの、5 分間のバケットで平均された平均ピア数を取得する。

```sql
let PeerCountRegex = @"Syncing with peers: (\d+) active, (\d+) confirmed, (\d+)";
ParityLog_CL
| where Computer == "vl-devn3lgdm-reg1000001"
| project RawData, TimeGenerated
| where RawData matches regex PeerCountRegex
| extend ActivePeers = extract(PeerCountRegex, 1, RawData, typeof(int)) 
| summarize avg(ActivePeers) by bin(TimeGenerated, 5m)
```

### <a name="ssh-access"></a>SSH アクセス

セキュリティ上の理由から、既定では、SSH ポートのアクセスはネットワーク グループ セキュリティ ルールによって 拒否されます。 PoA ネットワークで仮想マシン スケール セット インスタンスにアクセスするには、このルールを \"[許可]\" に変更する必要があります

1.  まず、Azure portal で、デプロイされているリソース グループの [概要] セクションを選択します。

    ![SSH の概要](./media/ethereum-poa-deployment-guide/ssh-overview.png)

2.  [ネットワーク セキュリティ グループ] を選択します

    ![SSH の NSG](./media/ethereum-poa-deployment-guide/ssh-nsg.png)

3.  \"[allow-ssh]\" ルールを選択します

    ![ssh-allow](./media/ethereum-poa-deployment-guide/ssh-allow.png)

4.  \"[アクション]\" を [許可] に変更します

    ![SSH 有効化を許可](./media/ethereum-poa-deployment-guide/ssh-enable-allow.png)

5.  \"[保存]\" をクリックします (変更が適用されるまでに数分かかることがあります)

これで、指定した管理者ユーザー名とパスワード/SSH キーを利用して、SSH 経由でバリデーター ノードの仮想マシンにリモート接続できます。
1 番目のバリデーター ノードにアクセスするために実行する SSH コマンドは、'SSH\_TO\_FIRST\_VL\_NODE\_REGION1' (サンプル デプロイの場合は ssh - p 4000 poaadmin\@leader4vb.eastus.cloudapp.azure.com) というテンプレート デプロイ出力パラメーターに指定されています。 トランザクション ノードを追加するには、ポート番号を 1 ずつ増やします (たとえば、最初のトランザクション ノードはポート 4000 です)。

複数のリージョンにデプロイした場合は、上記のコマンドを、そのリージョン内のロード バランサーの DNS 名または IP アドレスに変更します。 他のリージョンの DNS 名または IP アドレスを調べるには、名前付け規則 \*\*\*\*\*-lbpip-reg\# が使用されたリソースを見つけ、その DNS 名と IP アドレスのプロパティを確認します。

### <a name="azure-traffic-manager-load-balancing"></a>Azure Traffic Manager の負荷分散

Azure Traffic Manager を使用すると、異なるリージョン内の複数のデプロイ間で着信トラフィックをルーティングすることにより、PoA ネットワークのダウンタイムを短縮し、応答性を向上できます。 組み込みの正常性チェックと自動再ルーティングによって、RPC エンドポイントとガバナンス DApp の高可用性が確保されます。 この機能は、複数のリージョンにデプロイ済みで稼働準備ができている場合に便利です。

Traffic Manager を利用すると、以下のことが可能になります。

-   自動フェールオーバーにより、PoA ネットワークの可用性を向上する。

-   ネットワーク待機時間が最も短い Azure の場所にエンド ユーザーをルーティングすることにより、ネットワークの応答性を向上する。

Traffic Manager プロファイルを作成すると、プロファイルの DNS 名を使用してネットワークにアクセスできます。 また、他のコンソーシアム メンバーがネットワークに追加されたら、そのメンバーがデプロイしたバリデーター間の負荷分散に Traffic Manager を使用できます。

#### <a name="creating-a-traffic-manager-profile"></a>Traffic Manager プロファイルの作成

Azure portal で \"[Create a resource]\(リソースの作成\)\" ボタンをクリックした後に、\"[Traffic Manager プロファイル]\" を検索して選択します。

![Azure Traffic Manager の検索](./media/ethereum-poa-deployment-guide/traffic-manager-search.png)

プロファイルに一意の名前を付け、PoA のデプロイ時に作成されたリソース グループを選択します。

![Traffic Manager の作成](./media/ethereum-poa-deployment-guide/traffic-manager-create.png)

デプロイが完了したら、リソース グループでインスタンスを選択します。 Traffic Manager にアクセスするための DNS 名は、[概要] タブで確認できます。

![Traffic Manager の DNS の確認](./media/ethereum-poa-deployment-guide/traffic-manager-dns.png)

[エンドポイント] タブを選択し、[追加] ボタンをクリックします。 [ターゲット リソースの種類] を [パブリック IP アドレス] に変更します。 1 番目のリージョンのロード バランサーのパブリック IP アドレスを選択します。

![Traffic Manager のルーティング](./media/ethereum-poa-deployment-guide/traffic-manager-routing.png)

同じ手順を、デプロイ済みのネットワークのリージョンごとに繰り返します。 エンドポイントは、"\"有効\"" 状態になると自動的に読み込まれ、Traffic Manager の DNS 名でリージョン負荷分散されます。 これで、ドキュメントの他の手順で \[CONSORTIUM\_DATA\_URL\] パラメーターの代わりにこの DNS を使用できます。

## <a name="data-api"></a>データ API

各コンソーシアム メンバーは、他のメンバーがネットワークに接続するために必要な情報をホストします。 既存のメンバーは、メンバーのデプロイの前に [CONSORTIUM_DATA_URL] を提供します。 デプロイ時に、参加するメンバーは次のエンドポイントで JSON インターフェイスから情報を取得します。

`<CONSORTIUM_DATA_URL>/networkinfo`

応答には、参加するメンバーに役立つ情報 (ジェネシス ブロック、バリデーター セットのコントラクト ABI、ブートノード) だけでなく、既存のメンバーに役立つ情報 (バリデーターのアドレス) も含まれています。 複数のクラウド プロバイダーにわたってコンソーシアムを拡張するときは、この標準化を使用することをお勧めします。 この API では、次の構造を持つ JSON 形式の応答が返されます。
```json
{
  "$id": "", 
  "type": "object", 
  "definitions": {}, 
  "$schema": "http://json-schema.org/draft-07/schema#", 
  "properties": {
    "majorVersion": {
      "$id": "/properties/majorVersion", 
      "type": "integer", 
      "title": "This schema’s major version", 
      "default": 0, 
      "examples": [
        0
      ]
    }, 
    "minorVersion": {
      "$id": "/properties/minorVersion", 
      "type": "integer", 
      "title": "This schema’s minor version", 
      "default": 0, 
      "examples": [
        0
      ]
    }, 
    "bootnodes": {
      "$id": "/properties/bootnodes", 
      "type": "array", 
      "items": {
        "$id": "/properties/bootnodes/items", 
        "type": "string", 
        "title": "This member’s bootnodes", 
        "default": "", 
        "examples": [
          "enode://a348586f0fb0516c19de75bf54ca930a08f1594b7202020810b72c5f8d90635189d72d8b96f306f08761d576836a6bfce112cfb6ae6a3330588260f79a3d0ecb@10.1.17.5:30300", 
          "enode://2d8474289af0bb38e3600a7a481734b2ab19d4eaf719f698fe885fb239f5d33faf217a860b170e2763b67c2f18d91c41272de37ac67386f80d1de57a3d58ddf2@10.1.17.4:30300"
        ]
      }
    }, 
    "valSetContract": {
      "$id": "/properties/valSetContract", 
      "type": "string", 
      "title": "The ValidatorSet Contract Source", 
      "default": "", 
      "examples": [
        "pragma solidity 0.4.21;\n\nimport \"./SafeMath.sol\";\nimport \"./Utils.sol\";\n\ncontract ValidatorSet …"
      ]
    }, 
    "adminContract": {
      "$id": "/properties/adminContract", 
      "type": "string", 
      "title": "The AdminSet Contract Source", 
      "default": "", 
      "examples": [
        "pragma solidity 0.4.21;\nimport \"./SafeMath.sol\";\nimport \"./SimpleValidatorSet.sol\";\nimport \"./Admin.sol\";\n\ncontract AdminValidatorSet is SimpleValidatorSet { …"
      ]
    }, 
    "adminContractABI": {
      "$id": "/properties/adminContractABI", 
      "type": "string", 
      "title": "The Admin Contract ABI", 
      "default": "", 
      "examples": [
        "[{\"constant\":false,\"inputs\":[{\"name\":\"proposedAdminAddress\",\"type\":\"address\"},…"
      ]
    }, 
    "paritySpec": {
      "$id": "/properties/paritySpec", 
      "type": "string", 
      "title": "The Parity client spec file", 
      "default": "", 
      "examples": [
        "\n{\n \"name\": \"PoA\",\n \"engine\": {\n \"authorityRound\": {\n \"params\": {\n \"stepDuration\": \"2\",\n \"validators\" : {\n \"safeContract\": \"0x0000000000000000000000000000000000000006\"\n },\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\"\n }\n }\n },\n \"params\": {\n \"gasLimitBoundDivisor\": \"0x400\",\n \"maximumExtraDataSize\": \"0x2A\",\n \"minGasLimit\": \"0x2FAF080\",\n \"networkID\" : \"0x9a2112\",\n \"wasmActivationTransition\": \"0x0\"\n },\n \"genesis\": {\n \"seal\": {\n \"authorityRound\": {\n \"step\": \"0x0\",\n \"signature\": \"0x0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000\"\n }\n },\n \"difficulty\": \"0x20000\",\n \"gasLimit\": \"0x2FAF080\"\n },\n \"accounts\": {\n \"0x0000000000000000000000000000000000000001\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ecrecover\", \"pricing\": { \"linear\": { \"base\": 3000, \"word\": 0 } } } },\n \"0x0000000000000000000000000000000000000002\": { \"balance\": \"1\", \"builtin\": { \"name\": \"sha256\", \"pricing\": { \"linear\": { \"base\": 60, \"word\": 12 } } } },\n \"0x0000000000000000000000000000000000000003\": { \"balance\": \"1\", \"builtin\": { \"name\": \"ripemd160\", \"pricing\": { \"linear\": { \"base\": 600, \"word\": 120 } } } },\n \"0x0000000000000000000000000000000000000004\": { \"balance\": \"1\", \"builtin\": { \"name\": \"identity\", \"pricing\": { \"linear\": { \"base\": 15, \"word\": 3 } } } },\n \"0x0000000000000000000000000000000000000006\": { \"balance\": \"0\", \"constructor\" : \"…\" }\n }\n}"
      ]
    }, 
    "errorMessage": {
      "$id": "/properties/errorMessage", 
      "type": "string", 
      "title": "Error message", 
      "default": "", 
      "examples": [
        ""
      ]
    }, 
    "addressList": {
      "$id": "/properties/addressList", 
      "type": "object", 
      "properties": {
        "addresses": {
          "$id": "/properties/addressList/properties/addresses", 
          "type": "array", 
          "items": {
            "$id": "/properties/addressList/properties/addresses/items", 
            "type": "string", 
            "title": "This member’s validator addresses", 
            "default": "", 
            "examples": [
              "0x00a3cff0dccc0ecb6ae0461045e0e467cff4805f", 
              "0x009ce13a7b2532cbd89b2d28cecd75f7cc8c0727"
            ]
          }
        }
      }
    }
  }
}

```
## <a name="development"></a>開発

### <a name="programmatically-interacting-with-a-smart-contract"></a>プログラムによるスマート コントラクトの操作

> [!WARNING]
> ネットワーク経由で Ethereum の秘密キーを送信することは絶対にしないでください。 必ず、最初に各トランザクションに署名してから、署名済みのトランザクションをネットワークで送信するようにしてください。

次の例では、*ethereumjs-wallet* を使用して Ethereum アドレスを生成し、*ethereumjs-tx* を使用してローカルで署名し、*web3* を使用して未加工のトランザクションを Ethereum RPC エンドポイントに送信します。

この例では、次のような単純な Hello-World スマート コントラクトを使用します。

```javascript
pragma solidity ^0.4.11;
contract postBox {
    string message;
    function postMsg(string text) public {
        message = text; 
    }
    function getMsg() public view returns (string) {
        return message;
    }
}
```

この例は、コントラクトが既にデプロイされていることを前提としています。 プログラムでコントラクトをデプロイするには、*solc* と *web3* を使用できます。 最初に、次のノード モジュールをインストールします。
```
sudo npm install web3@0.20.2
sudo npm install ethereumjs-tx@1.3.6
sudo npm install ethereumjs-wallet@0.6.1
```
この nodeJS スクリプトは、次の作業を実行します。

-   未加工のトランザクション postMsg を構築します

-   生成された秘密キーを使用してトランザクションに署名します

-   署名済みのトランザクションを Ethereum ネットワークに送信します

```javascript
var ethereumjs = require('ethereumjs-tx')
var wallet = require('ethereumjs-wallet')
var Web3 = require('web3')

// TODO Replace with your contract address
var address = "0xfe53559f5f7a77125039a993e8d5d9c2901edc58";
var abi = [{"constant": false,"inputs": [{"name": "text","type": "string"}],"name": "postMsg","outputs": [],"payable": false,"stateMutability": "nonpayable","type": "function"},{"constant": true,"inputs": [],"name": "getMsg","outputs": [{"name": "","type": "string"}],"payable": false,"stateMutability": "view","type": "function"}];

// Generate a new Ethereum account
var account = wallet.generate();
var accountAddress = account.getAddressString()
var privateKey = account.getPrivateKey();

 // TODO Replace with your RPC endpoint
 var web3 = new Web3(new Web3.providers.HttpProvider(
    "http://testzvdky-dns-reg1.eastus.cloudapp.azure.com:8545"));
 
// Get the current nonce of the account
 web3.eth.getTransactionCount(accountAddress, function (err, nonce) {
   var data = web3.eth.contract(abi).at(address).postMsg.getData("Hello World");
   var rawTx = {
     nonce: nonce,
     gasPrice: '0x00', 
     gasLimit: '0x2FAF080',
     to: address, 
     value: '0x00', 
     data: data
   }
   var tx = new ethereumjs(rawTx);
   
   tx.sign(privateKey);

   var raw = '0x' + tx.serialize().toString('hex');
   web3.eth.sendRawTransaction(raw, function (txErr, transactionHash) {
     console.log("TX Hash: " + transactionHash);
     console.log("Error: " + txErr);
   });
 });
```

### <a name="debug-a-smart-contract-truffle-develop"></a>スマート コントラクトのデバッグ (Truffle Develop)

Truffle には、スマート コントラクトのデバッグに使用できるローカルな開発ネットワークがあります。 詳細なチュートリアルについては、[こちら](http://truffleframework.com/tutorials/debugging-a-smart-contract)を参照してください。

### <a name="webassembly-wasm-support"></a>WebAssembly (WASM) のサポート

新しくデプロイされた PoA ネットワークでは、WebAssembly のサポートが既に有効になっています。 これにより、Web-Assembly (Rust、C、C++) にトランスパイルされる任意の言語でスマート コントラクトの開発を実行できます。 詳細については、次のリンクを参照してください。

-   Parity による WebAssembly の概要 - <https://wiki.parity.io/WebAssembly-Home>

-   Parity Technologies 提供のチュートリアル - <https://github.com/paritytech/pwasm-tutorial>

## <a name="faq"></a>FAQ

### <a name="i-notice-a-bunch-of-transactions-on-the-network-that-i-didnt-send-where-are-these-coming-from"></a>送信しなかった多数のトランザクションがネットワーク上にあることに気づきました。 これはどこから生じたものですか?

[パーソナル API](https://web3js.readthedocs.io/en/1.0/web3-eth-personal.html) をロック解除することは安全ではありません。 ボット アカウントはロック解除された Ethereum アカウントを待機しており、資金を流出させようと試みます。 ボットはこれらのアカウントが実際の ether を含んでいると想定し、最初に残高を奪おうとします。 ネットワーク上でパーソナル API を有効にしないでください。 その代わりに、MetaMask などのウォレットを使用して手動で、または「プログラムによるスマート コントラクトの操作」セクションの説明に従ってプログラムで、トランザクションに事前に署名します。

### <a name="how-to-ssh-onto-a-vm"></a>VM に SSH 接続するにはどうすればよいですか?

セキュリティ上の理由により、SSH ポートはロックダウンされています。 [このガイドに従って SSH ポートを有効化](#_Accessing_Nodes_via)してください。

### <a name="how-do-i-set-up-an-audit-member-or-transaction-nodes"></a>監査メンバーまたはトランザクション ノードを設定するにはどうすればよいですか?

トランザクション ノードは、ネットワークとピアリングされているが、コンセンサスには参加していない Parity クライアントのセットです。 これらのノードを使用しても、Ethereum トランザクションを送信し、スマート コントラクトの状態を読み取ることは可能です。
これは、ネットワーク規制者に監査機能を提供するためのメカニズムとしてうまく機能します。 そのためには「コンソーシアムの拡張」の手順 2 に従うだけです。

### <a name="why-are-metamask-transactions-taking-a-long-time"></a>MetaMask トランザクションに長時間かかっているのはなぜですか?

トランザクションが正しい順序で受信されるようにするために、各 Ethereum トランザクションには増分される nonce が付けられています。 別のネットワークで MetaMask のアカウントを使用した場合、その nonce 値をリセットする必要があります。 設定アイコン (3 本のバー)、[設定]、[アカウントのリセット] の順にクリックしてください。 トランザクションの履歴がクリアされ、トランザクションを再送信できるようになります。

### <a name="are-public-ip-deployments-compatible-with-private-network-deployments"></a>パブリック IP のデプロイは、プライベート ネットワークのデプロイと互換性がありますか?

いいえ。ピアリングには双方向通信が必要なため、ネットワーク全体がパブリックかプライベートのいずれかである必要があります。

## <a name="next-steps"></a>次の手順

[Ethereum Proof-of-Authority Consortium](https://portal.azure.com/?pub_source=email&pub_status=success#create/microsoft-azure-blockchain.azure-blockchain-ethereumethereum-poa-consortium) ソリューションを使用して作業を開始します。
