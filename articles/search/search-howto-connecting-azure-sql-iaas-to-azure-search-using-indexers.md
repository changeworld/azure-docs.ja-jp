---
title: 検索インデックス作成のための Azure SQL VM 接続
titleSuffix: Azure Cognitive Search
description: 暗号化された接続を有効にして、Azure Cognitive Search のインデクサーから Azure の仮想マシン (VM) 上の SQL Server に接続できるようにファイアウォールを構成します。
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/19/2021
ms.openlocfilehash: 23c5d138463a52f4ff4c52b4a919b71a87b7fd6d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104802881"
---
# <a name="configure-a-connection-from-an-azure-cognitive-search-indexer-to-sql-server-on-an-azure-vm"></a>Azure VM で Azure Cognitive Search インデクサーから SQL Server への接続を構成する

Azure 仮想マシン上のデータベースからコンテンツを抽出するように [Azure SQL インデクサー](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#faq)を構成する場合は、セキュリティで保護された接続を行うために追加の手順が必要になります。 

Azure Cognitive Search から仮想マシン上の SQL Server への接続は、パブリック インターネット接続です。 セキュリティで保護された接続を成功させるには、次の手順を実行します。

+ 仮想マシン上の SQL Server インスタンスの完全修飾ドメイン名のために、[認証機関プロバイダー](https://en.wikipedia.org/wiki/Certificate_authority#Providers)から証明書を取得します。

+ 証明書を仮想マシンにインストールし、この記事の手順を使用して、VM で暗号化された接続を有効にし、構成します。

## <a name="enable-encrypted-connections"></a>暗号化された接続を有効にする

Azure Cognitive Search には、パブリック インターネット接続経由のすべてのインデクサー要求に対する暗号化されたチャネルが必要です。 このセクションでは、これを機能させるための手順を示します。

1. 証明書のプロパティを調べて、サブジェクト名が VM の完全修飾ドメイン名 (FQDN) であることを確認します。 プロパティは、CertUtils などのツールまたは証明書スナップインを使用して表示できます。 FQDN は、 **Azure Portal** で、VM サービス ブレードの [Essentials] セクションにある [[パブリック IP アドレス/DNS 名ラベル]](https://portal.azure.com/)フィールドから入手できます。
  
   + 新しい **Resource Manager** テンプレートを使用して作成された VM の場合、FQDN の形式は `<your-VM-name>.<region>.cloudapp.azure.com` です。

   + **クラシック** VM として作成された VM の場合、FQDN の形式は `<your-cloud-service-name.cloudapp.net>` です。

1. レジストリ エディター (regedit) を使用して、証明書を使用するように SQL Server を構成します。 

   このタスクにはよく SQL Server 構成マネージャーが使用されますが、このシナリオでは使用できません。 SQL Server 構成マネージャーでは、インポートされている証明書が検出されません。これは、Azure 上の VM の FQDN が、VM によって特定された FQDN と一致しないためです (VM は、ドメインをローカル コンピューターまたはそれが参加しているネットワーク ドメインとして識別します)。 名前が一致しない場合は、regedit を使用して証明書を指定します。

   + regedit で、レジストリ キー `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate` に移動します。

     `[MSSQL13.MSSQLSERVER]` の部分は、バージョンとインスタンス名によって異なります。 

   + **証明書** キーの値を、VM にインポートした TLS/SSL 証明書の **拇印** に設定します。

     拇印を取得する方法は複数ありますが、いくつかの方法は他の方法よりも優れています。 MMC の **証明書** スナップインからコピーする場合は、 [このサポート記事で説明されているように](https://support.microsoft.com/kb/2023869/)非表示の先頭文字を含めてしまい、その結果、接続を試みたときにエラーが発生する可能性があります。 この問題を修正するための回避策はいくつかあります。 最も簡単な方法は、regedit のキー値フィールドで Backspace キーを押してから拇印の最初の文字を再入力して、先頭文字を削除することです。 また、別のツールを使用して拇印をコピーすることもできます。

1. サービス アカウントにアクセス許可を付与します。 

    SQL Server サービス アカウントに TLS/SSL 証明書の秘密キーに関する適切なアクセス許可が付与されていることを確認します。 この手順を見過ごすと、SQL Server は起動されません。 このタスクには **証明書** スナップインまたは **CertUtils** を使用できます。

1. SQL Server サービスを再起動します。

## <a name="configure-sql-server-connectivity-in-the-vm"></a>VM で SQL Server への接続を構成する

Azure Cognitive Search に必要な、暗号化された接続を設定した後は、Azure VM 上の SQL Server に固有の追加の構成手順があります。 その構成手順をまだ実行していない場合は、以下のいずれかの記事を参照して構成を実行します。

+ **Resource Manager** VM の場合は、「 [Connect to a SQL Server Virtual Machine on Azure using Resource Manager](../azure-sql/virtual-machines/windows/ways-to-connect-to-sql.md)」(Resource Manager を使用した Azure での SQL Server 仮想マシンへの接続) をご覧ください。 

+ **クラシック** VM の場合は、「 [Connect to a SQL Server Virtual Machine on Azure Classic](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-connect)」(Azure クラシックでの SQL Server 仮想マシンへの接続) をご覧ください。

特に、各記事の、インターネット経由での接続に関するセクションを確認してください。

## <a name="configure-the-network-security-group-nsg"></a>ネットワーク セキュリティ グループ (NSG) を構成する

外部から Azure VM にアクセスできるように、NSG および対応する Azure エンドポイントまたはアクセス制御リスト (ACL) を構成することはめずらしくありません。 独自のアプリケーション ロジックから SQL Azure VM に接続できるようにするために、この構成を既に実行している可能性があります。 Azure Cognitive Search から SQL Azure VM への接続もこれと同じです。 

以下のリンクでは、VM デプロイメントの NSG を構成する手順を説明しています。 これらの手順を、IP アドレスに基づいて ACL および Azure Cognitive Search エンドポイントに使用してください。

> [!NOTE]
> バックグラウンドの場合は、「 [ネットワーク セキュリティ グループ (NSG) について](../virtual-network/network-security-groups-overview.md)

+ **リソース マネージャー** VM の場合は、「 [How to create NSGs for ARM deployments](../virtual-network/tutorial-filter-network-traffic.md)」(ARM デプロイメントの NSG を作成する方法) をご覧ください。

+ **クラシック** VM の場合は、「 [How to create NSGs for Classic deployments](/previous-versions/azure/virtual-network/virtual-networks-create-nsg-classic-ps)」(クラシック デプロイメントの NSG を作成する方法) をご覧ください。

IP アドレスの指定ではいくつかの課題が生じる可能性がありますが、問題点と考えられる回避策を理解していれば簡単に解決できます。 以降のセクションでは、ACL 内の IP アドレスに関する問題に対処するための推奨事項を説明します。

### <a name="restrict-access-to-the-azure-cognitive-search"></a>Azure Cognitive Search へのアクセスを制限する

SQL Azure VM をすべての接続要求に開放するのではなく、ACL で Search サービスの IP アドレスと `AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)の IP アドレス範囲へのアクセスを制限することを強くお勧めします。

IP アドレスは、Search サービスの FQDN (`<your-search-service-name>.search.windows.net` など) に ping を実行することで確認できます。 Search サービスの IP アドレスを変更することはできますが、変更される可能性はほとんどありません。 この IP アドレスがサービスの有効期間中に変わることはほとんどありません。

`AzureCognitiveSearch` [サービス タグ](../virtual-network/service-tags-overview.md#available-service-tags)の IP アドレス範囲を確認するには、[ダウンロード可能な JSON ファイル](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)または [Service Tag Discovery API](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) を使用します。 IP アドレス範囲は毎週更新されます。

### <a name="include-the-azure-cognitive-search-portal-ip-addresses"></a>Azure Cognitive Search ポータルの IP アドレスを含める

Azure portal を使用してインデクサーを作成する場合は、ポータルに SQL Azure 仮想マシンへの受信アクセスを付与する必要があります。 ファイアウォールの受信規則では、ポータルの IP アドレスを指定する必要があります。

ポータルの IP アドレスを取得するには、ping `stamp2.ext.search.windows.net` を使用します。これはトラフィック マネージャーのドメインです。 要求はタイムアウトになりますが、IP アドレスはステータス メッセージに表示されます。 たとえば、"Pinging azsyrie.northcentralus.cloudapp.azure.com [52.252.175.48]" というメッセージでは、IP アドレスは "52.252.175.48" になります。

> [!NOTE]
> 異なるリージョンのクラスターは、異なるトラフィック マネージャーに接続します。 ドメイン名に関係なく、ping から返された IP アドレスは正しいもので、リージョン内の Azure portal で受信ファイアウォール規則を定義するときに使用します。

## <a name="next-steps"></a>次のステップ

これで構成が完了し、Azure VM 上 の SQL Server を Azure Cognitive Search インデクサーのデータ ソースとして指定できるようになりました。 詳細については、[インデクサーを使用した Azure Cognitive Search への Azure SQL Database の接続](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)に関する記事をご覧ください。