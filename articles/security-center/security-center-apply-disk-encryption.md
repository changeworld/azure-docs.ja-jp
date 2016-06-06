<properties
   pageTitle="Azure Security Center で Disk Encryption を適用する | Microsoft Azure"
   description="このドキュメントでは、**ディスク暗号化の適用**という Azure セキュリティ センターの推奨事項を実装する方法について説明します。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="terrylan"/>

# Azure Security Center で Disk Encryption を適用する

お使いの Windows または Linux VM ディスクが Azure Disk Encryption を使用して暗号化されていない場合、Azure セキュリティ センターにより、ディスク暗号化を適用することが推奨されます。Disk Encryption を使用すると、Windows および Linux IaaS VM ディスクを暗号化できます。VM 上の OS とデータ ボリュームの両方を暗号化することをお勧めします。


Disk Encryption では、業界標準である Windows の [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) 機能と Linux の [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) 機能を利用して OS とデータの暗号化を実現します。この暗号化により、データを保護し、組織のセキュリティとコンプライアンスに関する義務を果たすことができます。Disk Encryption は [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) と統合されています。これは、Key Vault サブスクリプションでディスク暗号化キーとシークレットを制御および管理し、[Azure Storage](https://azure.microsoft.com/documentation/services/storage/) で保管している、VM ディスク内のすべてのデータを確実に暗号化する場合に役立ちます。

> [AZURE.NOTE] Azure Disk Encryption は、Windows Server オペレーティング システム (Windows Server 2008 R2、Windows Server 2012、Windows Server 2012 R2) でサポートされています。Disk Encryption は、Linux サーバー オペレーティング システム (Ubuntu、CentOS、SUSE、SUSE Linux Enterprise Server (SLES)) でサポートされています。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[ディスク暗号化の適用]** を選択します。
2. **[ディスク暗号化の適用]** ブレードに、Disk Encryption が推奨される VM の一覧が表示されます。
3. 手順に従って、これらの VM に暗号化を適用します。

![][1]

Security Center で暗号化が必要と識別された Azure Virtual Machines を暗号化するには、次の手順をお勧めします。

- Azure PowerShell のインストールおよび構成。これにより、Azure 仮想マシンの暗号化に必要な必須コンポーネントを設定するための PowerShell コマンドを実行できるようになります。
- Azure Disk Encryption の前提条件となる Azure PowerShell スクリプトの取得と実行。
- 仮想マシンの暗号化。

[Azure Virtual Machines の暗号化](security-center-disk-encryption.md)では、次の手順を段階的に説明します。このトピックでは、ディスク暗号化を構成するクライアント コンピューターとして Windows 10 を使用していることを想定しています。

必須コンポーネントの設定と Azure 仮想マシンの暗号化の構成に使用できる方法は多数あります。既に Azure PowerShell または Azure CLI についてよく知っている方は、他の方法を好む場合もあります。こうした方法の詳細については、「[Azure Disk Encryption](../security/azure-security-disk-encryption.md)」を参照してください。



## 次のステップ

このドキュメントでは、"ディスク暗号化の適用" というセキュリティ センターの推奨事項を実装する方法について説明しました。 ディスク暗号化の詳細については、以下を参照してください。

- 「[Encryption and key management with Azure Key Vault (Azure Key Vault を使用した暗号化とキー管理)](https://azure.microsoft.com/documentation/videos/azurecon-2015-encryption-and-key-management-with-azure-key-vault/)」(動画、36 分 39 秒) -- IaaS VM と Azure Key Vault のディスク暗号化管理を使用し、データを保護する方法について説明しています。
- [Azure Virtual Machines の暗号化](security-center-disk-encryption.md) (ドキュメント) -- Azure Virtual Machines を暗号化する方法について説明します。
- 「[Azure Disk Encryption](../security/azure-security-disk-encryption.md)」(ドキュメント) -- Windows と Linux VM のディスク暗号化を有効にする方法について説明しています。

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure セキュリティ センターでのセキュリティ ポリシーの設定](security-center-policies.md)」 -- セキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 -- Azure リソースの正常性を監視する方法について説明します。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 -- セキュリティの警告の管理および応答の方法について説明します。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 -- 推奨事項に従って Azure リソースを保護する方法について説明します。
- 「[Azure Security Center に関する FAQ](security-center-faq.md)」 -- このサービスの使用に関してよく寄せられる質問を見つけます。
- 「[Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)」 -- Azure のセキュリティとコンプライアンスについてまとめたブログ記事を見つけます。



<!--Image references-->
[1]: ./media/security-center-apply-disk-encryption/apply-disk-encryption.png

<!---HONumber=AcomDC_0525_2016-->