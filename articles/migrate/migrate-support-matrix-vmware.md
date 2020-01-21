---
title: Azure Migrate での VMware 評価サポート
description: Azure Migrate での VMware 評価サポートの詳細を知る
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 2a9c5504d99f439723a250b619b9f9b660ea9c59
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029006"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 評価のサポートマトリックス 

この記事では、[Azure Migrate を使用した VMware Vm 評価サポートの設定と制限事項について説明します。Server Assessment](migrate-services-overview.md#azure-migrate-server-migration-tool) でクリックします。 VMware Vm を Azure に移行する方法については、「[移行サポート マトリックス](migrate-support-matrix-vmware-migration.md)」を参照してください。

## <a name="overview"></a>概要

この記事を使用して オンプレミス マシンの Azure への移行を評価するには、Azure Migrate の追加が必要です。Azure Migrate プロジェクトのサーバー評価ツール [Azure Migrate アプライアンス](migrate-appliance.md)をデプロイします。 アプライアンスは、オンプレミス マシンを継続的に検出し、構成データとパフォーマンスデータを Azure に送信します。 マシンの検出後、検出されたマシンをグループにまとめ、グループの評価を実行します。


## <a name="limitations"></a>制限事項

**サポート** | **詳細**
--- | ---
**評価の上限**| 1 つの[プロジェクト](migrate-support-matrix.md#azure-migrate-projects)で最大 35,000 台の VMware VM を検出・評価できます。
**プロジェクトの制限** | 1 つの Azure サブスクリプションで複数のプロジェクトを作成できます。 評価の上限に達するまで、1 つのプロジェクトに VMware VM、Hyper-V VM、および物理サーバーを含めることができます。
**検出** | Azure Migrate アプライアンスで、vCenter Server 上の VMware VM を最大 10,000 台検出できます。
**評価** | 1 つのグループに最大 35,000 個のマシンを追加できます。<br/><br/> 1 回の評価で最大 35,000 個の VM を評価できます。

評価の詳細については[こちら](concepts-assessment-calculation.md)をご覧ください。


## <a name="application-discovery"></a>アプリケーションの検出

マシンの検出に加えて、Azure Migrate は以下の内容を実行できます。Server Assessment で、アプリ、ロール、フィーチャーを検出できます。 アプリ インベントリを検出することで、オンプレミスのワークロードに合わせて調整された移行パスを特定し、計画することができます。 

**サポート** | **詳細**
--- | ---
探索 | 検出はエージェントレスで、マシンのゲスト資格情報を使用し、WMI と SSH 呼び出しでマシンにリモートアクセスします。
サポートされているマシン | オンプレミス VMware VM
マシンのオペレーティング システム | Windows と Linux のすべてのバージョン
[資格情報] | 現在、すべての Windows サーバーに対して 1 つの資格情報と、すべての Linux サーバーに対して 1 つの資格情報を使用することがサポートされています。<br/><br/> Windows VM 用にゲスト ユーザー アカウントを作成し、すべての Linux VM 用に通常/標準ユーザー アカウント (非 sudo アクセス) を作成します。
制限 | アプリ検出に関しては、1アプライアンスにつき最大 10,000 個を検出できます。 

## <a name="vmware-requirements"></a>VMware の要件

**VMware** | **詳細**
--- | ---
**vCenter Server** | 検出・評価するマシンは、 vCenter Server バージョン 5.5、6.0、6.5、または 6.7 でマネージドされている必要があります。
**アクセス許可 (評価)** | vCenter Server の読み取り専用アカウント。
**アクセス許可 (アプリ検出)** | 読み取り専用アクセス権を持つ vCenter Server アカウントと、仮想マシン > ゲスト操作 の権限が有効。
**アクセス許可 (依存関係の視覚化)** | 読み取り専用アクセス権を持つ Center Server アカウントと、**仮想マシン** > **ゲスト操作**.の権限が有効。


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate アプライアンスの要件

Azure Migrate では、[Azure Migrate アプライアンス](migrate-appliance.md)を使用して検出と評価を行います。 VMware 用アプライアンスは、vCenter Server にインポートされた OVA テンプレートを使用してデプロイされます。 

- VMware の[アプライアンスの要件](migrate-appliance.md#appliance---vmware)を確認してください。
- アプライアンスがアクセスする必要のある [URL](migrate-appliance.md#url-access) を確認してください。

## <a name="port-access"></a>ポート アクセス

**[デバイス]** | **[接続]**
--- | ---
アプライアンス | TCP ポート 3389 で、アプライアンスへのリモート デスクトップ接続を許可するための受信接続。<br/><br/> ポート 44368 で、次の URL を使用してアプライアンス管理アプリにリモートでアクセスするためのインバウンド接続: ```https://<appliance-ip-or-name>:44368``` <br/><br/>ポート 443、5671、5672 で、検出とパフォーマンスのメタデータを Azure Migrate に送信するための送信接続。
vCenter サーバー | TCP ポート 443 で、アプライアンスが評価用に構成およびパフォーマンスのメタデータを収集できるようにするインバウンド接続。 <br/><br/> 既定では、アプライアンスはポート 443 で vCenter に接続します。 vCenter Server が別のポートでリッスンする場合、検出の設定時にポートを変更できます。

## <a name="dependency-visualization"></a>依存関係の視覚化

依存関係の視覚化は、評価および移行するマシン間の依存関係を視覚化するのに役立ちます。 一般的に依存関係マッピングは、より高い信頼度でマシンを評価したい場合に使用します。 VMware VM の場合、依存関係の視覚化は次のようにサポートされます。

- **エージェントレスの依存関係の視覚化**:このオプションは現在プレビューの段階です。 マシンにエージェントをインストールする必要はありません。
    - これは、エージェントレスの視覚化が有効になっているマシンから TCP 接続データをキャプチャすることで機能します。 依存関係の検出が開始された後、アプライアンスでは 5 分間のポーリング間隔でマシンからデータが収集されます。
    - 収集されるデータは次のとおりです。
        - TCP 接続
        - アクティブな接続を含むプロセスの名前
        - 上記のプロセスを実行するインストール済みのアプリケーションの名前
        - いいえ。 ポーリング間隔ごとに検出される接続の数
- **エージェントベースの依存関係の視覚化**:エージェントベースの依存関係の視覚化を使用するには、分析するオンプレミスの各マシンに次のエージェントをダウンロードしてインストールする必要があります。
    - 各マシンに Microsoft Monitoring agent (MMA) をインストールします。 MMA エージェントのインストール方法の詳細については、[こちら](how-to-create-group-machine-dependencies.md#install-the-mma)をご覧ください。
    - 各マシンに Dependency agent をインストールします。 依存関係エージェントのインストール方法の詳細については、[こちら](how-to-create-group-machine-dependencies.md#install-the-dependency-agent)をご覧ください。
    - また、インターネットに接続されていないマシンの場合、それらに Log Analytics ゲートウェイをダウンロードしてインストールする必要があります。


## <a name="next-steps"></a>次のステップ

- 評価作成のベストプラクティスを[確認](best-practices-assessment.md)します。
- VMware 評価の[準備](tutorial-prepare-vmware.md)
