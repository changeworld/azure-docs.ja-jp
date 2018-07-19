---
title: Azure Stack Ethereum ブロックチェーン ソリューション テンプレート
description: カスタム ソリューション テンプレートを使用して、Azure Stack 上にコンソーシアム Ethereum ブロックチェーン ネットワークをデプロイおよび構成します
services: azure-stack
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 07/03/2018
ms.topic: article
ms.service: azure-stack
ms.reviewer: coborn
manager: femila
ms.openlocfilehash: 0e03b524834f528ddb7555a344fbebe720b4d9ff
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2018
ms.locfileid: "37446976"
---
# <a name="azure-stack-ethereum-blockchain-solution-templates"></a>Azure Stack Ethereum ブロックチェーン ソリューション テンプレート

Ethereum ソリューション テンプレートは、Azure と Ethereum の最小限の知識で、マルチメンバー コンソーシアム Ethereum ブロックチェーン ネットワークを簡単かつ短時間でデプロイおよび構成できるように設計されています。

Azure Stack テナント ポータルでわずかなユーザー入力とワンクリック デプロイを行うことで、各メンバーはネットワーク フットプリントをプロビジョニングできます。 各メンバーのネットワーク フットプリントは、アプリケーションまたはユーザーがトランザクションを送信するために対話できる一連の負荷分散型トランザクション ノード、トランザクションを記録する一連のマイニング ノード、および Network Virtual Appliance (NVA) から構成されます。 以降の接続手順では、NVA を接続して、完全に構成されたマルチメンバー ブロックチェーン ネットワークを作成します。

## <a name="prerequisites"></a>前提条件

[Marketplace](azure-stack-download-azure-marketplace-item.md) から次の項目をダウンロードしてください。

* Ubuntu Server 16.04 LTS バージョン 16.04.201802220
* Windows Server 2016 
* Linux 2.0 用のカスタム スクリプト 
* カスタム スクリプト拡張機能 

Azure でのブロックチェーン シナリオの詳細については、「[Ethereum プルーフオブワーク コンソーシアム ソリューション テンプレート](../blockchain-workbench/ethereum-deployment-guide.md)」を参照してください。

複数の仮想マシンのデプロイをサポートできる Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) を作成してください。

## <a name="deployment-architecture"></a>デプロイメント アーキテクチャ

このソリューション テンプレートは、単一または複数メンバーの Ethereum コンソーシアム ネットワークをデプロイできます。 仮想ネットワークは、ネットワーク仮想アプライアンスと接続リソースを使用してチェーン トポロジで接続されます。 

## <a name="deployment-use-cases"></a>デプロイのユース ケース

このテンプレートでは、リーダーとメンバーが参加する Ethereum コンソーシアムをさまざまな方法でデプロイすることができます。テスト済みのものを次に示します。
- Azure AD または AD FS を使用したマルチノードの Azure Stack では、同じサブスクリプションまたは異なるサブスクリプションを使用してリードとメンバーをデプロイします。
- (Azure AD を使用した) 単一ノードの Azure Stack では、同じサブスクリプションを使用してリードとメンバーをデプロイします。

### <a name="standalone-and-consortium-leader-deployment"></a>スタンドアロンとコンソーシアム リーダーのデプロイ

コンソーシアム リーダー テンプレートは、ネットワーク内の最初のメンバーのフットプリントを構成します。 

1. [リーダー テンプレートを GitHub から](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/ConsortiumLeader/mainTemplate.json)ダウンロードします
2. Azure Stack 管理ポータルで、**[新規] > [テンプレートのデプロイ]** を選択して、カスタム テンプレートからデプロイします。
3. **[テンプレートの編集]** を選択して、新しいカスタム テンプレートを編集します。
4. 右側の編集ウィンドウで、前の手順でダウンロードしたリーダー テンプレート JSON をコピーして貼り付けます。
    
    ![リーダー テンプレートを編集する](media/azure-stack-ethereum/edit-leader-template.png)

5. **[保存]** を選択します。
6. **[パラメーターの編集]** を選択し、デプロイに合わせてテンプレート パラメーターを入力します。
    
    ![リーダー テンプレートのパラメーターを編集する](media/azure-stack-ethereum/edit-leader-parameters.png)

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | デプロイされたリソースの命名規則の基礎として使用される文字列。 | 長さ 1 から 6 文字の英数字 | eth
    AUTHTYPE | 仮想マシンに対して認証する方法。 | [パスワード] または [SSH 公開キー] | パスワード
    ADMINUSERNAME | デプロイされた各 VM の管理者ユーザー名 | 1 から 64 文字 | gethadmin
    ADMINPASSWORD ([認証の種類] = [パスワード])| デプロイされた各仮想マシンの管理者アカウントのパスワード。 パスワードには、小文字、大文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります。 <br />VM にはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます。|12 から 72 文字|
    ADMINSSHKEY ([認証の種類] = [sshPublicKey]) | リモート ログインに使用される Secure Shell キー。 | |
    GENESISBLOCK | カスタム ジェネシス ブロックを表す JSON 文字列。  このパラメーターの値の指定は省略可能です。 | |
    ETHEREUMACCOUNTPSSWD | Ethereum アカウントをセキュリティで保護するために使用する管理パスワード。 | |
    ETHEREUMACCOUNTPASSPHRASE | Ethereum アカウントに関連付けられた秘密キーの生成に使用されるパスフレーズ。 | |
    ETHEREUMNETWORKID | コンソーシアムのネットワーク ID。 | 5 から 999,999,999 の間の任意の値を使用します | 72
    CONSORTIUMMEMBERID | コンソーシアム ネットワークの各メンバーに関連付けられた ID。   | この ID は、ネットワーク内で一意である必要があります。 | 0
    NUMMININGNODES | マイニング ノードの数。 | 2 から 15 の間。 | 2
    MNNODEVMSIZE | マイニング ノードの VM サイズ。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | マイニング ノード ストレージのパフォーマンス。 | | Standard_LRS
    NUMTXNODES | トランザクション ノードの数。 | 1 から 5 の間。 | 1
    TXNODEVMSIZE | トランザクション ノードの VM サイズ。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | トランザクション ノードのストレージ パフォーマンス。 | | Standard_LRS
    BASEURL | 依存するテンプレートを取得するためのベース URL。 | デプロイ テンプレートをカスタマイズする場合を除き、既定値を使用します。 | 

7. **[OK]** を選択します。
8. **[カスタム デプロイ]** で、**[サブスクリプション]**、**[リソース グループ]**、および **[リソース グループの場所]** を指定します。
    
    ![リーダーのデプロイ パラメーター](media/azure-stack-ethereum/leader-deployment-parameters.png)

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    サブスクリプション | コンソーシアム ネットワークをデプロイするサブスクリプション | | 消費サブスクリプション
    リソース グループ | コンソーシアム ネットワークをデプロイするリソース グループ | | EthereumResources
    リージョン | リソース グループの Azure リージョン。 | | local

8. **[作成]** を選択します。

デプロイが完了するまで 20 分以上かかる場合があります。

デプロイが完了した後、**Microsoft.Template** のデプロイの概要をリソース グループのデプロイ セクションで確認できます。 概要には、コンソーシアム メンバーを参加させるために使用できる出力値が含まれています。

リーダーのデプロイを確認するには、リーダーの管理サイトを参照してください。 管理サイトのアドレスは、**Microsoft.Template** デプロイの出力セクションで確認できます。  

![リーダーのデプロイの概要](media/azure-stack-ethereum/ethereum-node-status.png)

### <a name="joining-consortium-member-deployment"></a>コンソーシアム メンバー参加デプロイ

1. [コンソーシアム メンバー テンプレートを GitHub から](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/JoiningMember/mainTemplate.json)ダウンロードします
2. Azure Stack 管理ポータルで、**[新規] > [テンプレートのデプロイ]** を選択して、カスタム テンプレートからデプロイします。
3. **[テンプレートの編集]** を選択して、新しいカスタム テンプレートを編集します。
4. 右側の編集ウィンドウで、前の手順でダウンロードしたリーダー テンプレート JSON をコピーして貼り付けます。
5. **[保存]** を選択します。
6. **[パラメーターの編集]** を選択し、デプロイに合わせてテンプレート パラメーターを入力します。

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    NAMEPREFIX | デプロイされたリソースの命名規則の基礎として使用される文字列。 | 長さ 1 から 6 文字の英数字 | eth
    AUTHTYPE | 仮想マシンに対して認証する方法。 | [パスワード] または [SSH 公開キー] | パスワード
    ADMINUSERNAME | デプロイされた各 VM の管理者ユーザー名 | 1 から 64 文字 | gethadmin
    ADMINPASSWORD ([認証の種類] = [パスワード])| デプロイされた各仮想マシンの管理者アカウントのパスワード。 パスワードには、小文字、大文字、数字、特殊文字の 4 種類のうち 3 種類を使用する必要があります。 <br />VM にはすべて、最初の段階で同じパスワードが与えられます。プロビジョニング後にそのパスワードを変更できます。|12 から 72 文字|
    ADMINSSHKEY ([認証の種類] = [sshPublicKey]) | リモート ログインに使用される Secure Shell キー。 | |
    CONSORTIUMMEMBERID | コンソーシアム ネットワークの各メンバーに関連付けられた ID。   | この ID は、ネットワーク内で一意である必要があります。 | 0
    NUMMININGNODES | マイニング ノードの数。 | 2 から 15 の間。 | 2
    MNNODEVMSIZE | マイニング ノードの VM サイズ。 | | Standard_A1
    MNSTORAGEACCOUNTTYPE | マイニング ノード ストレージのパフォーマンス。 | | Standard_LRS
    NUMTXNODES | トランザクション ノードの数。 | 1 から 5 の間。 | 1
    TXNODEVMSIZE | トランザクション ノードの VM サイズ。 | | Standard_A1
    TXSTORAGEACCOUNTTYPE | トランザクション ノードのストレージ パフォーマンス。 | | Standard_LRS
    CONSORTIUMDATA | 他のメンバーのデプロイによって提供された、関連するコンソーシアム構成データを指す URL。 この値は、リーダーのデプロイの出力で確認できます。 | |
    REMOTEMEMBERVNETADDRESSSPACE | リーダーの NVA IP アドレス。 この値は、リーダーのデプロイの出力で確認できます。 | | 
    REMOTEMEMBERNVAPUBLICIP | リーダーの NVA IP アドレス。 この値は、リーダーのデプロイの出力で確認できます。 | | 
    CONNECTIONSHAREDKEY | 接続を確立しているコンソーシアム ネットワークのメンバー間で事前に確立されたシークレット。 | |
    BASEURL | テンプレートのベース URL。 | デプロイ テンプレートをカスタマイズする場合を除き、既定値を使用します。 | 

7. **[OK]** を選択します。
8. **[カスタム デプロイ]** で、**[サブスクリプション]**、**[リソース グループ]**、および **[リソース グループの場所]** を指定します。

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    サブスクリプション | コンソーシアム ネットワークをデプロイするサブスクリプション | | 消費サブスクリプション
    リソース グループ | コンソーシアム ネットワークをデプロイするリソース グループ | | MemberResources
    リージョン | リソース グループの Azure リージョン。 | | local

8. **[作成]** を選択します。

デプロイが完了するまで 20 分以上かかる場合があります。

デプロイが完了した後、**Microsoft.Template** のデプロイの概要をリソース グループのデプロイ セクションで確認できます。 概要には、コンソーシアム メンバーを接続するために使用できる出力値が含まれています。

メンバーのデプロイを確認するには、メンバーの管理サイトを参照してください。 管理サイトのアドレスは、Microsoft.Template デプロイの出力セクションで確認できます。

![メンバーのデプロイの概要](media/azure-stack-ethereum/ethereum-node-status-2.png)

図に示すように、メンバーのノード状態は **[実行されていない]** です。 これは、メンバーとリーダーの間の接続が確立されていないためです。 メンバーとリーダーの間の接続は、双方向の接続です。 メンバーをデプロイすると、テンプレートによってメンバーからリーダーへの接続が自動的に作成されます。 リーダーからメンバーへの接続を作成するには、次の手順に進みます。

### <a name="connect-member-and-leader"></a>メンバーとリーダーを接続する

このテンプレートは、リーダーからリモート メンバーへの接続を作成します。 

1. [メンバーとリーダーの接続テンプレートを GitHub から](https://raw.githubusercontent.com/Azure/AzureStack-QuickStart-Templates/master/ethereum-consortium-blockchain/marketplace/Connection/mainTemplate.json)ダウンロードします
2. Azure Stack 管理ポータルで、**[新規] > [テンプレートのデプロイ]** を選択して、カスタム テンプレートからデプロイします。
3. **[テンプレートの編集]** を選択して、新しいカスタム テンプレートを編集します。
4. 右側の編集ウィンドウで、前の手順でダウンロードしたリーダー テンプレート JSON をコピーして貼り付けます。
    
    ![接続テンプレートを編集する](media/azure-stack-ethereum/edit-connect-template.png)

5. **[保存]** を選択します。
6. **[パラメーターの編集]** を選択し、デプロイに合わせてテンプレート パラメーターを入力します。
    
    ![接続テンプレートのパラメーターを編集する](media/azure-stack-ethereum/edit-connect-parameters.png)

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    MEMBERNAMEPREFIX | リーダーの名前のプレフィックス。 この値は、リーダーのデプロイの出力で確認できます。  | 長さ 1 から 6 文字の英数字 | |
    MEMBERROUTETABLENAME | リーダーのルート テーブルの名前。 この値は、リーダーのデプロイの出力で確認できます。 |  | 
    REMOTEMEMBERVNETADDRESSSPACE | メンバーのアドレス空間。 この値は、メンバーのデプロイの出力で確認できます。 | |
    CONNECTIONSHAREDKEY | 接続を確立しているコンソーシアム ネットワークのメンバー間で事前に確立されたシークレット。  | |
    REMOTEMEMBERNVAPUBLICIP | メンバーの NVA IP アドレス。 この値は、メンバーのデプロイの出力で確認できます。 | |
    MEMBERNVAPRIVATEIP | リーダーのプライベート NVA IP アドレス。 この値は、リーダーのデプロイの出力で確認できます。 | |
    LOCATION | Azure Stack 環境の場所。 | | local
    BASEURL | テンプレートのベース URL。 | デプロイ テンプレートをカスタマイズする場合を除き、既定値を使用します。 | 

7. **[OK]** を選択します。
8. **[カスタム デプロイ]** で、**[サブスクリプション]**、**[リソース グループ]**、および **[リソース グループの場所]** を指定します。
    
    ![デプロイの接続パラメーター](media/azure-stack-ethereum/connect-deployment-parameters.png)

    パラメーター名 | 説明 | 使用できる値 | 値の例
    ---------------|-------------|----------------|-------------
    サブスクリプション | リーダーのサブスクリプション。 | | 消費サブスクリプション
    リソース グループ | リーダーのリソース グループ。 | | EthereumResources
    リージョン | リソース グループの Azure リージョン。 | | local

8. **[作成]** を選択します。

デプロイが完了した後、リーダーとメンバーが通信を開始するまでに数分かかります。 デプロイを確認するには、メンバーの管理サイトを更新します。 メンバーのノードの状態が [実行中] と表示されます。 

![デプロイを検証する](media/azure-stack-ethereum/ethererum-node-status-3.png)

## <a name="next-steps"></a>次の手順

- Ethereum と Azure の詳細については、「[ブロックチェーン テクノロジとアプリケーション | Microsoft Azure](https://azure.microsoft.com/solutions/blockchain/)」を参照してください。
- Azure でのブロックチェーン シナリオの詳細については、「[Ethereum プルーフオブワーク コンソーシアム ソリューション テンプレート](../blockchain-workbench/ethereum-deployment-guide.md)」を参照してください。