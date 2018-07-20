---
title: Azure インフラストラクチャの整合性
description: この記事では、Azure インフラストラクチャの整合性について説明します。
services: security
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 867bc66a68bec662153d8336e649cf46df02f101
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2018
ms.locfileid: "37901318"
---
# <a name="azure-infrastructure-integrity"></a>Azure インフラストラクチャの整合性

## <a name="software-installation"></a>ソフトウェア インストール
Azure 環境にインストールされているソフトウェア スタック内のすべてのコンポーネントは、Microsoft のセキュリティ開発ライフサイクル (SDL) プロセスに従ってカスタムビルドされています。 すべてのソフトウェア コンポーネント (OS イメージや SQL Database を含む) が、変更およびリリース管理プロセスの一部としてデプロイされます。 すべてのノード上で実行される OS は、Windows Server 2008 または Windows Server 2012 のカスタマイズされたバージョンです。 正確なバージョンは、OS に期待される役割に従って FC によって選択されます。 さらに、ホスト OS では、承認されていないソフトウェア コンポーネントのインストールは許可されません。

一部の Microsoft Azure コンポーネント (RDFE や開発者ポータルなど) は、ゲスト OS 上で実行されているゲスト VM 上の Azure 顧客としてデプロイされます。

## <a name="virus-scans-on-builds"></a>ビルドに対するウイルス スキャン
Azure ソフトウェア コンポーネント (OS を含む) の各ビルドには、Microsoft Endpoint Protection (MEP) ウイルス対策ツールを使用してウイルス スキャンを実行する必要があります。 各ウイルス スキャンでは、関連付けられたビルド ディレクトリ内に、スキャンの対象とスキャンの結果が詳細に記載されたログが作成されます。 このウイルス スキャンは、Azure 内のすべてのコンポーネントに対するビルド ソース コードの一部です。 コードは、クリーンな、成功したウイルス スキャンなしで運用に移行されることはありません。 何からの問題が示されている場合、そのビルドは凍結された後、"悪意のある" コードがビルドに挿入された場所を識別するために Microsoft セキュリティ内のセキュリティ チームに渡されます。

## <a name="closedlocked-environment"></a>閉じられ、ロックされた環境
既定では、Azure インフラストラクチャ ノードとゲスト VM にはどのようなユーザー アカウントも作成されません。 さらに、既定の Windows 管理者アカウントも無効になっています。 Microsoft Azure Live Support (WALS) の管理者は、適切な認証を使用してこれらのコンピューターにログインし、応急修復のために Azure 運用ネットワークを管理できます。

## <a name="microsoft-azure-sql-database-authentication"></a>Microsoft Azure SQL Database の認証
SQL Server の他のすべての実装と同様に、ユーザー アカウントの管理は緊密に制御する必要があります。 Microsoft Azure SQL Database では、SQL Server 認証のみがサポートされています。 さらに、顧客のデータ セキュリティ モデルを補完するために、強力なパスワードを持ち、特定の権限で構成されたユーザー アカウントを使用する必要があります。

## <a name="firewallacls-between-msft-corpnet-and-microsoft-azure-cluster"></a>MSFT 企業ネットワークと Microsoft Azure クラスターの間のファイアウォール/ACL
サービス プラットフォームと Microsoft 企業ネットワークの間の ACL/ファイアウォールは、承認されていない内部関係者のアクセスから Microsoft Azure SQL Database を保護します。 さらに、Microsoft 企業ネットワークの IP アドレス範囲内のユーザーのみが WinFabric プラットフォーム管理エンドポイントにアクセスできます。

## <a name="firewallacls-between-nodes-in-an-azure-sql-db-cluster"></a>Azure SQL DB クラスター内のノード間のファイアウォール/ACL
保護を強化するために、多層防御戦略の一部として、Microsoft Azure SQL DB クラスター内のノード間に ACL/ファイアウォールが実装されています。 実行中のすべてのコードだけでなく、WinFabric プラットフォーム クラスター内のすべての通信が信頼できます。

## <a name="custom-mas-watchdogs"></a>カスタム MA (ウォッチドッグ)
Microsoft Azure SQL Database は、Microsoft Azure SQL DB クラスターの正常性を監視するために、ウォッチドッグと呼ばれるカスタム MA を採用しています。

## <a name="web-protocols"></a>Web プロトコル

### <a name="role-instance-monitoring-and-restart"></a>ロール インスタンスの監視と再開
Azure では、デプロイされているすべての実行中のロール (インターネットに接続する Web ロールまたはバックエンド処理 worker ロール) が、それぞれがプロビジョニングされているサービスを効果的かつ効率的に提供していることを確認するために、持続した正常性監視の対象になっていることが保証されます。 ロールが (ホストされているアプリケーションの致命的な障害、またはロール インスタンス自体にある基になる構成の問題のどちらかで) 異常な状態になった場合、Microsoft Azure FC はロール インスタンス内の問題を検出し、是正状態を開始します。

### <a name="compute-connectivity"></a>コンピューティング接続
Azure では、デプロイされているアプリケーション/サービスに、標準の Web ベースのプロトコル経由でアクセスできることが保証されます。 インターネットに接続する Web ロール仮想インスタンスは、外部のインターネット接続が可能であり、Web ユーザーから直接アクセスできます。 バックエンド処理 worker ロール仮想インスタンスは、パブリックにアクセス可能な Web ロール仮想インスタンスの代わりに worker ロールが実行する操作の機密性と整合性を保護するために、外部のインターネット接続は可能ですが、外部の Web ユーザーから直接アクセスすることはできません。

## <a name="next-steps"></a>次の手順
Microsoft が Azure インフラストラクチャをセキュリティ保護するために行っていることの詳細については、次を参照してください。

- [Azure ファシリティ、プレミス、および物理的なセキュリティ](azure-physical-security.md)
- [Azure インフラストラクチャの可用性](azure-infrastructure-availability.md)
- [Azure 情報システムのコンポーネントと境界](azure-infrastructure-components.md)
- [Azure ネットワーク アーキテクチャ](azure-infrastructure-network.md)
- [Azure 運用ネットワーク](azure-production-network.md)
- [Microsoft Azure SQL Database のセキュリティ機能](azure-infrastructure-sql.md)
- [Azure の運用操作と管理](azure-infrastructure-operations.md)
- [Azure インフラストラクチャの監視](azure-infrastructure-monitoring.md)
- [Azure での顧客データの保護](azure-protection-of-customer-data.md)
