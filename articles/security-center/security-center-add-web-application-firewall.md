<properties
   pageTitle="Azure Security Center で Web アプリケーション ファイアウォールを追加する | Microsoft Azure"
   description="このドキュメントでは、Azure Security Center の推奨事項である ";**Web アプリケーション ファイアウォールの追加**"; と ";**アプリケーション保護を完了する**"; を実装する方法について説明します。"
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
   ms.date="07/21/2016"
   ms.author="terrylan"/>

# Azure Security Center で Web アプリケーション ファイアウォールを追加する

Azure Security Center では、Web アプリケーションを保護するために、Microsoft パートナーの Web アプリケーション ファイアウォール (WAF) を追加することをお勧めする場合があります。このドキュメントでは、追加方法の例を紹介します。

> [AZURE.NOTE] このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[Secure web application using web application firewall (Web アプリケーション ファイアウォールを使用して、Web アプリケーションをセキュリティで保護する)]** を選択します。![Secure web Application][1]

2. **[Web アプリケーション ファイアウォールを使用した安全な Web アプリケーション]** ブレードで、Web アプリケーションを選択します。**[Web アプリケーション ファイアウォールの追加]** ブレードが開きます。![Web アプリケーション ファイアウォールの追加][2]
3. 利用可能な場合は、既存の Web アプリケーション ファイアウォールを使用することを選択するか、または新しい WAF を作成することができます。この例では、利用可能な既存の WAF がないため、新しい WAF を作成します。

4. 新しい WAF を作成するには、統合されたパートナーの一覧からソリューションを選択します。この例では、**[Barracuda Web アプリケーション ファイアウォール]** を選択します。
5. パートナー ソリューションに関する情報を提供する **[Barracuda Web アプリケーション ファイアウォール]** ブレードが開きます。情報ブレードで **[作成]** クリックします。![Firewall information blade][3]

6. **[新しい Web アプリケーション ファイアウォール]** ブレードが開きます。ここで **VM 構成**手順を実行して、**WAF 情報**を提供できます。**[VM 構成]** を選択します。

7. **[VM 構成]** ブレードで、WAF を実行する仮想マシンの起動に必要な情報を入力します。 ![VM configuration][4]
8. **[新しい Web アプリケーション ファイアウォール]** ブレードに戻り、**[WAF 情報]** を選択します。**[WAF 情報]** ブレードで WAF そのものを構成します。手順 7 では、WAF が実行される仮想マシンを構成できます。手順 8 では、WAF そのものをプロビジョニングできます。

## アプリケーション保護を完了する

1. **[推奨事項]** ブレードに戻ります。WAF を作成した後は、**[Finalize application protection (アプリケーション保護を完了する)]** という新しいエントリが生成されています。このエントリは、WAF によるアプリケーションの保護を有効にするには、Azure Virtual Network 内で実際に WAF を接続するプロセスを完了する必要があることを示しています。![Finalize application protection][5]

2. **[Finalize application protection (アプリケーション保護を完了する)]** を選択します。新しいブレードが開きます。トラフィックを再ルーティングする必要がある Web アプリケーションがあることが確認できます。
3. 対象の Web アプリケーションを選択します。Web アプリケーション ファイアウォールのセットアップを完了するための手順が表示されたブレードが開きます。手順を最後まで実行し、**[トラフィックを制限する]** を選択します。セキュリティ センターによって WAF が接続されます。![Restrict traffic][6]

> [AZURE.NOTE] セキュリティ センターで複数の Web アプリケーションを保護するには、対象のアプリケーションを既存の WAF デプロイに追加します。(リソース マネージャー デプロイ モデルを使用して作成した) WAF アプライアンスは、別の仮想ネットワークにデプロイする必要があります。(クラシック デプロイ モデルを使用して作成した) WAF アプライアンスは、ネットワーク セキュリティ グループの使用に限定されています。今後、このサポートは、全面的にカスタマイズされた WAF アプライアンスのデプロイ (クラシック) へと拡大される予定です。Azure リソースの詳細については、[クラシック デプロイメント モデルと Resource Manager デプロイメント モデル](../azure-classic-rm.md)に関するページを参照してください。

その WAF からのログは完全に統合されます。セキュリティ センターは、重要なセキュリティ アラートを表示できるように、ログの収集と分析を自動的に開始できます。

## 関連項目

このドキュメントでは、"Web アプリケーションの追加" というセキュリティ センターの推奨事項を実装する方法について説明しました。 Web アプリケーション ファイアウォールを構成する方法の詳細については、次を参照してください。

- [App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」-- Azure サブスクリプションとリソース グループのセキュリティ ポリシーの構成方法について説明しています。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」-- Azure リソースの正常性を監視する方法について説明しています。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」-- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」-- 推奨事項に従って Azure リソースを保護する方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」-- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティとコンプライアンスについてのブログ記事を確認できます。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0727_2016-->