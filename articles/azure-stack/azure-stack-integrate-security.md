---
title: "Azure Stack とデータセンターの統合 - セキュリティ"
description: "Azure Stack のセキュリティをお使いのデータセンターのセキュリティと統合する方法について説明します"
services: azure-stack
author: jeffgilb
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 02/28/2018
ms.author: jeffgilb
ms.reviewer: wfayed
keywords: 
ms.openlocfilehash: 8ce9045a3e4fd12d61e9b1600ee98880762bc544
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2018
---
# <a name="azure-stack-datacenter-integration---security"></a>Azure Stack とデータセンターの統合 - セキュリティ
Azure Stack はセキュリティを考慮して設計、構築されたものです。 Azure Stack はロック ダウンされたシステムなので、ソフトウェア セキュリティ エージェントのインストールはサポートされていません。

この記事は、お使いのデータ センター内に既にデプロイされたセキュリティ ソリューションと Azure Stack のセキュリティ機能を統合するのに役立ちます。

## <a name="security-logs"></a>セキュリティ ログ

Azure Stack は、2 分おきに、インフラストラクチャ ロールとスケール ユニットのノードについてのオペレーティング システムとセキュリティのイベントを収集します。 ログはストレージ アカウントの BLOB コンテナーに格納されます。

インフラストラクチャ ロールごとに 1 つのストレージ アカウント、すべての一般的なオペレーティング システム イベントについて 1 つの全般ストレージ アカウントがあります。

REST プロトコルを使用して BLOB コンテナーへの URL を取得することにより、正常性リソースプロバイダーを呼び出すことができます。 サード パーティのセキュリティ ソリューションでは、API およびストレージ アカウントを使用して、処理するイベントを取得することができます。

### <a name="use-azure-storage-explorer-to-view-events"></a>Azure Storage Explorer を使用してイベントを表示する

Azure Storage Explorer と呼ばれるツールを使用して、Azure Stack によって収集されたイベントを取得することができます。 Azure Storage Explorer は [http://storageexplorer.com](http://storageexplorer.com) からダウンロードできます。

次の手順は、Azure Stack 用に Azure Storage Explorer を構成するのに使用できる例です。

1. Azure Stack 管理者ポータルにオペレーターとしてサインインします。
2. **[ストレージ アカウント]** を参照して、**frphealthaccount** を探します。 **Frphealthaccount** アカウントは、オペレーティング システムのすべてのイベントを格納するために使用される全般ストレージ アカウントです。

   ![ストレージ アカウント](media/azure-stack-integrate-security/storage-accounts.png)

3. **[frphealthaccount]** を選択し、**[アクセス キー]** をクリックします。

   ![[アクセス キー]](media/azure-stack-integrate-security/access-keys.png)

4. アクセス キーをクリップボードにコピーします。
5. Azure ストレージ エクスプローラーを開きます。
6. **[編集]** メニューで **[Target Azure Stack]\(対象となる Azure Stack\)** を選択します。
7. **[アカウントの追加]** を選択し、**[Use a storage account name and key]\(ストレージ アカウント名とキーを使用\)** を選択します。

   ![ストレージの接続](media/azure-stack-integrate-security/connect-storage.png)

8. **[次へ]** をクリックします。
9. **[外部ストレージの接続]** ページで、次のようにします。

   a.[サインオン URL] ボックスに、次のパターンを使用して、ユーザーが RightScale アプリケーションへのサインオンに使用する URL を入力します。 アカウント名 **frphealthaccount** を入力します。

   b. ストレージ アカウントのアクセス キーを貼り付けます。

   c. **[ストレージ エンドポイントのドメイン]** の下で、**[Other]\(その他\)** を選択し、ストレージ エンドポイントの **[Region].[DomainName]** を指定します。

   d. **[Use HTTP]\(HTTP を使用する\)** チェックボックスをオンにします。

   ![外部ストレージの接続](media/azure-stack-integrate-security/attach-storage.png)

10. **[次へ]** をクリックして概要を確認し、ウィザードを **[完了]** します。
11. 各々 の BLOB コンテナーを参照し、イベントをダウンロードできるようになりました。

   ![BLOB の参照](media/azure-stack-integrate-security/browse-blob.png)

### <a name="use-programming-languages-to-access-events"></a>プログラミング言語を使用してイベントにアクセスする

さまざまなプログラミング言語を使用して、ストレージ アカウントにアクセスすることができます。 次のドキュメントを使用して、お使いの言語と一致する例を取得してください。

[https://azure.microsoft.com/resources/samples/?term=storage+account](https://azure.microsoft.com/resources/samples/?term=storage+account)

## <a name="device-access-auditing"></a>デバイスのアクセスの監査

Azure Stack 内のすべての物理デバイスは、TACACS または RADIUS の使用をサポートします。 これには、ベースボード管理コント ローラー (BMC) およびネットワーク スイッチへのアクセスが含まれます。

Azure Stack ソリューションには、RADIUS と TACACS のいずれも組み込まれていません。 ただしこのソリューションは、市場で入手可能な既存の RADIUS または TACACS ソリューションの使用をサポートすることが検証済みです。

RADIUS に対してのみ、MSCHAPv2 が検証されました。 これは、RADIUS を使用する最も安全な実装です。
Azure Stack ソリューションに含まれているデバイスで TACAS または RADIUS を有効にするには、OEM ハードウェア ベンダーに問い合わせてください。

## <a name="syslog"></a>syslog

Azure Stack 内のすべての物理デバイスは、Syslog メッセージを送信できます。 Azure Stack ソリューションには、Syslog サーバーは含まれていません。 ただしこのソリューションは、市場で入手可能な既存の Syslog ソリューションへのメッセージ送信をサポートすることが検証済みです。

Syslog の送信先アドレスは、デプロイ時に収集される省略可能なパラメーターですが、デプロイ後に追加することもできます。

## <a name="next-steps"></a>次の手順

[サービス ポリシー](azure-stack-servicing-policy.md)
