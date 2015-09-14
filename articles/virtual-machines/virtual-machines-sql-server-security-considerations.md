<properties 
	pageTitle="Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項"
	description="Azure の仮想マシンで実行されている SQL Server をセキュリティで保護するための一般的なガイダンスについて説明します。"
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>
<tags 
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="08/19/2015"
	ms.author="jroth"/>

# Azure Virtual Machines における SQL Server のセキュリティに関する考慮事項

このトピックでは、Azure VM の SQL Server インスタンスへのセキュリティで保護されたアクセスの確立に役立つ全体的なセキュリティ ガイドラインについて説明します。ただし、Azure の SQL Server データベース インスタンスをより安全に保護するには、Azure のセキュリティに関するベスト プラクティスに加えて、従来のオンプレミスのセキュリティに関するベスト プラクティスも実装することをお勧めします。

SQL Server のセキュリティ ベスト プラクティスの詳細については、[SQL Server 2008 R2 のセキュリティに関するベスト プラクティス - 運用作業と管理作業](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)を参照してください。

Azure はいくつかの業界規制および標準に準拠しているため、ユーザーは仮想マシンで実行されている SQL Server を使用して、標準に準拠したソリューションを構築できます。Azure での法規制遵守に関する情報については、[Azure セキュリティ センター](http://azure.microsoft.com/support/trust-center/)を参照してください。

次に示すのは、Azure VM の SQL Server インスタンスへの接続とその構成の際に考慮する必要がある、セキュリティに関する推奨事項の一覧です。

## アカウントの管理に関する注意点:

- **Administrator** という名前ではない一意のローカル管理者アカウントを作成します。

- すべてのアカウントに複雑で強力なパスワードを使用します。強力なパスワードを作成する方法の詳細については、セーフティとセキュリティ センターの記事「[強力なパスワードの作成](http://go.microsoft.com/fwlink/?LinkId=293596)」を参照してください。

- 既定では、SQL Server 仮想マシンのセットアップ時に、Azure は Windows 認証を選択します。そのため、**SA** ログインは無効となり、パスワードはセットアップによって割り当てられます。**SA** ログインは使用せず、有効にしないことをお勧めします。次に示すのは、SQL ログインが必要な場合の代替方法です。
	- **CONTROL SERVER** 権限を持つ SQL アカウントを作成します。
	- **SA** ログインを使用する必要がある場合は、ログインを有効にし、名前を変更して新しいパスワードを割り当てます。
	- 上記のどちらのオプションでも、認証モードを [SQL Server 認証モードと Windows 認証モード] に変更する必要があります。詳細については、「サーバーの認証モードの変更」を参照してください。

- CONTROL SERVER 権限を持つ SQL アカウントを作成します。

- SA ログインを使用する必要がある場合は、ログインを有効にし、名前を変更して新しいパスワードを割り当てます。

- 上記のどちらのオプションでも、認証モードを **[SQL Server 認証モードと Windows 認証モード]** に変更する必要があります。詳細については、[サーバーの認証モードの変更](https://msdn.microsoft.com/library/ms188670.aspx)を参照してください。

## Azure 仮想マシンへの接続のセキュリティ保護に関する注意事項:

- 仮想マシンの管理には、パブリック RDP ポートではなく、[Azure Virtual Network](../virtual-network/virtual-networks-overview.md) を使用することを検討してください。

- 仮想マシンのエンドポイントは、使用しない場合は削除します。

- Azure Virtual Machines の SQL Server データベース エンジンのインスタンスで、暗号化された接続オプションを有効にします。署名付き証明書で SQL Server インスタンスを構成します。詳細については、[データベース エンジンへの暗号化接続の有効化](https://msdn.microsoft.com/library/ms191192.aspx)および[接続文字列の構文](https://msdn.microsoft.com/library/ms254500.aspx)を参照してください。

- 仮想マシンが特定のネットワークからのみアクセスされる場合は、Windows ファイアウォールを使用して、特定の IP アドレスまたはネットワーク サブネットへのアクセスを制限します。また、エンドポイントに ACL を追加して、許可されたクライアントのみにトラフィックを制限することを検討してください。エンドポイントで ACL を使用する手順については、[エンドポイントでの ACL の管理](virtual-machines-set-up-endpoints.md#manage-the-acl-on-an-endpoint)に関するページを参照してください。

## 次のステップ

パフォーマンスに関するベスト プラクティスにも関心がある場合は、[Azure Virtual Machines での SQL Server のパフォーマンスに関するベスト プラクティス](virtual-machines-sql-server-performance-best-practices.md)を参照してください。

Azure VM での SQL Server の実行に関するその他のトピックについては、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-sql-server-infrastructure-services.md)」を参照してください。

<!---HONumber=September15_HO1-->