<properties
   pageTitle="Azure Security Center での Azure SQL サービスの保護 | Microsoft Azure"
   description="このドキュメントでは、Azure Security Center での推奨事項に従って Azure SQL サービスを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/04/2016"
   ms.author="terrylan"/>

# Azure Security Center での Azure SQL サービスの保護

Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。推奨事項は、仮想マシン (VM)、ネットワーク、SQL、およびアプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、Azure SQL サービスに適用される推奨事項について説明します。Azure SQL サービスに関する推奨事項は、Azure SQL サーバーおよびデータベースの監査と SQL データベースの暗号化を有効にすることが中心です。次の表を参考にすると、SQL サービスに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## SQL サービスに関する利用可能な推奨事項

|推奨|Description|
|-----|-----|
|[サーバーの SQL 監査の有効化](security-center-enable-auditing-on-sql-servers.md)|Azure SQL サーバーの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。|
|[データベースの SQL 監査の有効化](security-center-enable-auditing-on-sql-databases.md)|Azure SQL データベースの監査を有効にすることをお勧めします (Azure SQL サービスのみ。仮想マシンで実行されている SQL を除く)。|
|[SQL データベースでの透過的なデータ暗号化の有効化](security-center-enable-transparent-data-encryption.md)|SQL データベース (Azure SQL のサービスのみ) に対して暗号化を有効にすることをお勧めします。|

## 関連項目

その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

- [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
- [Azure Security Center でのアプリケーションの保護](security-center-application-recommendations.md)
- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。

<!---HONumber=AcomDC_0810_2016-->