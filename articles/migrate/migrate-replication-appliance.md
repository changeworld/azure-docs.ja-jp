---
title: Azure Migrate レプリケーション アプライアンスのアーキテクチャ | Microsoft Docs
description: Azure Migrate レプリケーション アプライアンスの概要を示します
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810110"
---
# <a name="replication-appliance"></a>レプリケーション アプライアンス

この記事では、エージェントベースの移行を使用して、VMware VM、物理マシン、プライベート/パブリック クラウドの VM を Azure に移行する場合に、Azure Migrate: Server Assessment によって使用されるレプリケーション アプライアンスについて説明します。 

ツールは [Azure Migrate](migrate-overview.md) ハブ内で入手できます。 このハブには、評価および移行のためのネイティブ ツールと、他の Azure サービスおよび独立系ソフトウェア ベンダー (ISV) からのツールが用意されています。


## <a name="appliance-overview"></a>アプライアンスの概要

レプリケーション アプライアンスは、VMware VM または物理サーバーとして、単一のオンプレミス マシンとしてデプロイされます。 以下が実行されます。
- **レプリケーション アプライアンス**:レプリケーション アプライアンスは、Azure にレプリケートするオンプレミスの VMware VM と物理サーバーについて、通信を調整し、データ レプリケーションを管理します。
- **プロセス サーバー**:レプリケーション アプライアンス上に既定でインストールされるプロセス サーバーでは、以下の操作が行われます。
    - **レプリケーション ゲートウェイ**:レプリケーション ゲートウェイとして機能します。 レプリケーションが有効になっているマシンからレプリケーション データを受け取ります。 キャッシュ、圧縮、暗号化によってレプリケーション データを最適化し、Azure に送信します。
    - **エージェント インストーラー**:モビリティ サービスのプッシュ インストールが実行されます。 移行のためにレプリケートする各オンプレミス マシン上で、このサービスがインストールされ、実行されている必要があります。

## <a name="appliance-deployment"></a>アプライアンスのデプロイ

**以下としてデプロイ** | **用途** | **詳細**
--- | --- |  ---
VMware VM | 通常は、Azure Migrate 移行ツールとエージェントベースの移行を使用して VMware VM を移行する場合に使用されます。 | OVA テンプレートを Azure Migrate ハブからダウンロードし、それを vCenter Server にインポートして、アプライアンス VM を作成します。
物理マシン | VMware インフラストラクチャがない場合や、OVA テンプレートを使用して VMware VM を作成できない場合に、オンプレミスの物理サーバーを移行するときに使用します。 | ソフトウェア インストーラーを Azure Migrate ハブからダウンロードして実行し、アプライアンス マシンを設定します。

## <a name="appliance-deployment-requirements"></a>アプライアンスのデプロイ要件

アプライアンスのデプロイ要件を[ご確認](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements)ください。



## <a name="appliance-license"></a>アプライアンスのライセンス
アプライアンスには、180 日間有効な Windows Server 2016 評価版ライセンスが付属します。 評価期間が期限切れ間近の場合は、新しいアプライアンスをダウンロードしてデプロイするか、アプライアンス VM のオペレーティング システムのライセンスをアクティブ化することをお勧めします。

## <a name="replication-process"></a>レプリケーション プロセス

1. VM に対してレプリケーションを有効にした場合、指定したレプリケーション ポリシーを使用して、Azure Storage への最初のレプリケーションが開始されます。 
2. トラフィックは、インターネット経由で Azure Storage のパブリック エンドポイントにレプリケートされます。 オンプレミス サイトから Azure へのサイト間仮想プライベート ネットワーク (VPN) を介したトラフィックのレプリケートはサポートされていません。
3. 初期レプリケーションが完了すると、差分レプリケーションが開始されます。 マシンの追跡された変更はログに記録されます。
4. 通信は、次のように行われます。
    - VM は、レプリケーション管理のために、受信ポート HTTPS 443 上でレプリケーション アプライアンスと通信します。
    - レプリケーション アプライアンスは、送信ポート HTTPS 443 を通じて Azure によるレプリケーションを調整します。
    - VM は、受信ポート HTTPS 9443 上でプロセス サーバーにレプリケーション データを送信します (プロセス サーバーは、レプリケーション アプライアンス上で実行されます)。 このポートは変更可能です。
    - プロセス サーバーは、レプリケーション データを受信し、データを最適化して暗号化し、送信ポート 443 経由で Azure ストレージに送信します。
5. レプリケーション データ ログは、まず Azure のキャッシュ ストレージ アカウントに記録されます。 これらのログは処理され、データは Azure マネージド ディスクに格納されます。

![アーキテクチャ](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>アプライアンスのアップグレード

アプライアンスは、Azure Migrate ハブから手動でアップグレードされます。 常に最新バージョンを実行することをお勧めします。

1. [Azure Migrate] > [サーバー] > [Azure Migrate:Server Assessment]、[インフラストラクチャ サーバー] で、 **[構成サーバー]** をクリックします。
2. **[構成サーバー]** では、新しいバージョンのレプリケーション アプライアンスが使用可能な場合に、 **[エージェントのバージョン]** にリンクが表示されます。 
3. インストーラーをレプリケーション アプライアンス マシンにダウンロードし、アップグレードをインストールします。 インストーラーによって、アプライアンス上で現在実行中のバージョンが検出されます。
 
## <a name="next-steps"></a>次の手順

VMware 用にアプライアンスを設定する[方法を確認する](tutorial-assess-vmware.md#set-up-the-appliance-vm)。
Hyper-V 用にアプライアンスを設定する[方法を確認する](tutorial-assess-hyper-v.md#set-up-the-appliance-vm)。

