---
title: 物理サーバーの評価と移行の Azure Migrate サポート マトリックス
description: Azure Migrate サービスを使用した物理サーバーの評価と移行に関する設定と制限について、要点を説明します。
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: raynew
ms.openlocfilehash: 55ada93e6a77dd7e31640988bad95034f77c90dd
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091891"
---
# <a name="support-matrix-for-physical-server-assessment-and-migration"></a>物理サーバーの評価と移行のサポート マトリックス

[Azure Migrate サービス](migrate-overview.md)を使用すると、コンピューターを評価したり、Microsoft Azure クラウドに移行したりできます。 この記事では、オンプレミスの物理サーバーを評価して移行する際にサポートされる設定と制限について概要を説明します。

> [!NOTE]
> ここで言及されている機能がまだ Azure Migrate ポータルに表示されない場合は、しばらくお待ちください。 来週あたりに表示されるようになります。


## <a name="physical-server-scenarios"></a>物理サーバーのシナリオ

この表は、物理サーバーに関してサポートされるシナリオをまとめたものです。

**Deployment** | **詳細***
--- | ---
**オンプレミスの物理サーバーを評価する** | 最初の評価を[設定](tutorial-prepare-physical.md)します。<br/><br/> 評価を[実行](tutorial-assess-physical.md)する。
**物理サーバーを Azure に移行する** | Azure への移行を[試します](tutorial-migrate-physical-virtual-machines.md)。


## <a name="azure-migrate-projects"></a>Azure Migrate プロジェクト

**サポート** | **詳細**
--- | ---
**Azure のアクセス許可** | Azure Migrate プロジェクトを作成するには、サブスクリプションに共同作成者または所有者アクセス許可が必要です。
**物理サーバー** | 1 つのプロジェクトで最大 35,000 個の物理サーバーを評価します。 1 つの Azure サブスクリプションに複数のプロジェクトを含めることができます。 評価の上限に達するまでは、1 つのプロジェクトに物理サーバー、VMware VM、Hyper-V VM を含めることができます。
**地理的な場所** | Azure Migrate プロジェクトは、いくつかの地域で作成できます。 プロジェクトを作成できるのは特定の地域に限られますが、ターゲットの場所がそれ以外であるマシンを評価または移行することは可能です。 プロジェクトの地域は、検出されたメタデータを格納するためにのみ使用されます。

  **地理的な場所** | **メタデータ ストレージの場所**
  --- | ---
  Azure Government | 米国政府バージニア州
  アジア太平洋 | 東アジアまたは東南アジア
  オーストラリア | オーストラリア東部またはオーストラリア南東部
  ブラジル | ブラジル南部
  カナダ | カナダ中部またはカナダ東部
  ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
  フランス | フランス中部
  インド | インド中部またはインド南部
  日本 |  東日本または西日本
  韓国 | 韓国中部または韓国南部
  イギリス | 英国南部または英国西部
  米国 | 米国中部または米国西部 2


 > [!NOTE]
 > Azure Government は現在、[古いバージョン](https://docs.microsoft.com/azure/migrate/migrate-services-overview#azure-migrate-versions)の Azure Migrate でのみサポートされます。


## <a name="assessment-physical-server-requirements"></a>評価 - 物理サーバーの要件

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **物理サーバーの展開**       | 物理サーバーは、スタンドアロンにすることも、クラスターにデプロイすることもできます。 |
| **アクセス許可**           | **Windows:** 探索に含めるすべての Windows サーバー上にローカル ユーザー アカウントを設定します。ユーザー アカウントは、これらのグループ (リモート デスクトップ ユーザー、パフォーマンス モニター ユーザー、パフォーマンス ログ ユーザー) に追加する必要があります。 <br/> **Linux:** 検出する Linux サーバーのルート アカウントが必要です。 |
| **オペレーティング システム** | 次を除くすべての [Windows](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines) および [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) オペレーティング システムがサポートされています。<br/> Windows Server 2003 <br/> SUSE Linux|


## <a name="assessment-appliance-requirements"></a>評価 - アプライアンスの要件

Azure Migrate では、評価のために軽量アプライアンスを実行して物理サーバーを検出し、サーバー メタデータとパフォーマンス データを Azure Migrate に送信します。 アプライアンスは物理サーバーまたは VM のいずれかで実行でき、Azure portal からダウンロードした PowerShell スクリプトを使用して設定します。 次の表は、アプライアンスの要件をまとめたものです。

| **サポート**                | **詳細**               
| :-------------------       | :------------------- |
| **アプライアンスのデプロイ**   |  アプライアンスを物理サーバーまたは仮想マシンのいずれかにデプロイします。<br/>  ホスト マシンは、Windows Server 2012 R2 以降が実行されている必要があります。<br/> ホストには、アプライアンス VM に 16 GB の RAM、8 つの vCPU、約 80 GB のストレージ スペース、1 つの外部スイッチを割り当てることができる十分な領域が必要です。<br/> アプライアンスには、静的または動的 IP アドレス、およびインターネット アクセスが必要です。
| **Azure Migrate プロジェクト**  |  単一のプロジェクトにアプライアンスを関連付けることができます。<br/> 任意の数のアプライアンスを 1 つのプロジェクトに関連付けることができます。<br/> プロジェクト内で最大 35,000 個のマシンを評価できます。
| **検出**              | 1 つのアプライアンスで最大 250 個のサーバーを検出できます。
| **評価グループ**       | 1 つのグループに最大 35,000 個のマシンを追加できます。
| **評価**             | 1 回の評価で最大 35,000 個のマシンを評価できます。


## <a name="assessment-appliance-url-access"></a>評価 - アプライアンスの URL アクセス

VM を評価するには、Azure Migrate アプライアンスがインターネットに接続されている必要があります。

- アプライアンスをデプロイすると、下の表にまとめた URL への接続チェックが Azure Migrate によって実行されます。
- URL ベースのプロキシを使用している場合は、表内の URL へのアクセスを許可して、URL の探索中に受信されたすべての CNAME レコードがプロキシによって解決されるようにします。
- インターセプト プロキシがある場合は、プロキシ サーバーからアプライアンスへのサーバー証明書のインポートが必要になる場合があります。


**URL** | **詳細**  
--- | ---
*.portal.azure.com | Azure portal への移動
*.windows.net <br/> *.msftauth.net <br/> *.msauth.net <br/> *.microsoft.com <br/> *.live.com  | Azure サブスクリプションにサインインします。
*.microsoftonline.com <br/> *.microsoftonline-p.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
management.azure.com | アプライアンスからサービスへの通信のための Azure Active Directory アプリケーションの作成。
dc.services.visualstudio.com | ログ記録と監視
*.vault.azure.net | アプライアンスとサービス間の通信時の Azure Key Vault のシークレットを管理します。
aka.ms/* | aka リンクへのアクセスを許可します。
https://download.microsoft.com/download/* | Microsoft ダウンロード サイトからのダウンロードを許可します。



## <a name="assessment-port-requirements"></a>評価 - ポートの要件

次の表は、評価のためのポート要件をまとめたものです。

**デバイス** | **Connection**
--- | ---
**アプライアンス** | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ``` https://<appliance-ip-or-name>:44368 ```<br/> ポート 443、5671、5672 で検出とパフォーマンスのメタデータを Azure Migrate に送信するためのアウトバウンド接続。
**物理サーバー** | **Windows:** ポート 443、5989 で Windows サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 <br/> **Linux:** ポート 22 (UDP) で Linux サーバーから構成とパフォーマンスのメタデータをプルするための受信接続。 |


## <a name="next-steps"></a>次の手順

物理サーバーの評価と移行のために、[物理サーバーの評価を準備します](tutorial-prepare-physical.md)。
