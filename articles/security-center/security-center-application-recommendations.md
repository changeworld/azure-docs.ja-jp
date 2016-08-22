<properties
   pageTitle="Azure Security Center でのアプリケーションの保護 | Microsoft Azure"
   description="このドキュメントでは、Azure Security Center での推奨事項に従ってご使用の Azure アプリケーションを保護し、セキュリティ ポリシーを使用してコンプライアンスを順守する方法について説明します。"
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

# Azure Security Center でのアプリケーションの保護

Azure セキュリティ センターは、Azure リソースのセキュリティの状態を分析します。潜在的なセキュリティの脆弱性を識別すると、Security Center は、必要な管理を構成するプロセスを説明する推奨事項を作成します。推奨事項は、仮想マシン (VM)、ネットワーク、SQL、およびアプリケーションといった、Azure のリソースの種類に適用されます。

この記事では、アプリケーションに適用される推奨事項について説明します。アプリケーションに関する推奨事項は、Web アプリケーション ファイアウォールのデプロイが中心です。次の表を参考にすると、アプリケーションに関する利用可能な推奨事項と、それぞれを適用した場合の結果を理解しやすくなります。

## アプリケーションに関する利用可能な推奨事項

|推奨|Description|
|-----|-----|
|[Web アプリケーション ファイアウォールの追加](security-center-add-web-application-firewall.md)|Web エンドポイントに Web アプリケーション ファイアウォール (WAF) をデプロイすることをお勧めします。セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。(リソース マネージャー デプロイ モデルを使用して作成した) WAF アプライアンスは、別の仮想ネットワークにデプロイする必要があります。(クラシック デプロイ モデルを使用して作成した) WAF アプライアンスは、ネットワーク セキュリティ グループの使用に限定されています。今後、このサポートは、全面的にカスタマイズされた WAF アプライアンスのデプロイ (クラシック) へと拡大される予定です。|
|[アプリケーション保護を完了する](security-center-add-web-application-firewall.md#finalize-application-protection)|WAF の構成を完了するには、WAF アプライアンスにトラフィックを再ルーティングする必要があります。この推奨事項に従うと、必要なセットアップの変更が完了します。|

## 関連項目

その他の Azure リソースの種類に適用される推奨事項の詳細については、次をご覧ください。

- [Azure Security Center での仮想マシンの保護](security-center-virtual-machine-recommendations.md)
- [Azure Security Center でのネットワークの保護](security-center-network-recommendations.md)
- [Azure Security Center での Azure SQL サービスの保護](security-center-sql-service-recommendations.md)

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。

<!---HONumber=AcomDC_0810_2016-->