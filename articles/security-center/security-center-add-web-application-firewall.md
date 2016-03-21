<properties
   pageTitle="Azure Security Center で Web アプリケーション ファイアウォールを追加する | Microsoft Azure"
   description="このドキュメントでは、**Web アプリケーション ファイアウォールの追加**という Azure Security Center の推奨事項を実装する方法について説明します。"
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
   ms.date="02/23/2016"
   ms.author="terrylan"/>

# Azure Security Center で Web アプリケーション ファイアウォールを追加する

Azure Security Center では、Web アプリケーションを保護するために、Microsoft パートナーの Web アプリケーション ファイアウォール (WAF) を追加することをお勧めする場合があります。このドキュメントでは、追加方法の例を紹介します。

> [AZURE.NOTE] このドキュメントの情報は、Azure Security Center のプレビュー リリースに適用されます。このドキュメントでは、サンプルのデプロイを使用してサービスについて紹介します。ステップ バイ ステップ ガイドではありません。

## 推奨事項の実装

1. **[推奨事項]** ブレードで、**[Web アプリケーション ファイアウォールを使用した安全な Web アプリケーション]** を選択します。![][1]

2. **[Web アプリケーション ファイアウォールを使用した安全な Web アプリケーション]** ブレードで、Web アプリケーションを選択します。**[Web アプリケーション ファイアウォールの追加]** ブレードが開きます。![][2]
3. 利用可能な場合は、既存の Web アプリケーション ファイアウォールを使用することを選択するか、または新しい WAF を作成することができます。この例では、利用可能な既存の WAF がないため、新しい WAF を作成します。

4. 新しい WAF を作成するには、統合されたパートナーの一覧からソリューションを選択します。この例では、**[Barracuda Web アプリケーション ファイアウォール]** を選択します。
5. パートナー ソリューションに関する情報を提供する **[Barracuda Web アプリケーション ファイアウォール]** ブレードが開きます。情報ブレードで **[作成]** クリックします。![][3]

6. **[新しい Web アプリケーション ファイアウォール]** ブレードが開きます。ここで **VM 構成**手順を実行して、**WAF 情報**を提供できます。**[VM 構成]** を選択します。

7. **[VM 構成]** ブレードで、WAF を実行する仮想マシンの起動に必要な情報を入力します。 ![][4]
8. **[新しい Web アプリケーション ファイアウォール]** ブレードに戻り、**[WAF 情報]** を選択します。**[WAF 情報]** ブレードで WAF そのものを構成します。手順 7 では、WAF が実行される仮想マシンを構成できます。手順 8 では、WAF そのものをプロビジョニングできます。

9. **[推奨事項]** ブレードに戻ります。WAF の作成後に、新しいエントリ **[Web アプリケーション ファイアウォールのセットアップの完了]** が生成されました。このエントリによって、Azure Virtual Network でアプリケーションを保護できるようにするには、そのネットワーク内で実際に WAF に接続するプロセスを完了する必要があることがわかります。![][5]

10. **[Web アプリケーション ファイアウォールのセットアップの完了]** を選択します。新しいブレードが開きます。トラフィックを再ルーティングする必要がある Web アプリケーションがあることが確認できます。
11. 対象の Web アプリケーションを選択します。Web アプリケーション ファイアウォールのセットアップを完了するための手順が表示されたブレードが開きます。手順を完了した後、**[トラフィックを制限]** を選択します。セキュリティ センターによって WAF が接続されます。![][6]

> [AZURE.NOTE] 自動プロビジョニング プロセスは、個別の VNet にデプロイされている WAF パッケージ (リソース マネージャーのデプロイ モデルを使用して作成) に基づいています。VM (クラシック) 上で保護されている Web アプリケーションへのアクセスは、NSG のみを使用する WAF アプライアンスに制限されます。今後、このサポートは、全面的にカスタマイズされた WAF パッケージのデプロイ (クラシック) へと拡張される予定です。Azure リソースの詳細については、「[クラシック デプロイ モデルとリソース マネージャーのデプロイ モデル](../azure-classic-rm.md)」を参照してください。

その WAF からのログは完全に統合されます。セキュリティ センターは、重要なセキュリティ アラートを表示できるように、ログの収集と分析を自動的に開始できます。

## 次のステップ

このドキュメントでは、"Web アプリケーションの追加" というセキュリティ センターの推奨事項を実装する方法について説明しました。 Web アプリケーション ファイアウォールを構成する方法の詳細については、次を参照してください。

- [App Service 環境の Web アプリケーション ファイアウォール (WAF) を構成する](../app-service-web/app-service-app-service-environment-web-application-firewall.md)

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティ ポリシーの設定](security-center-policies.md)」 -- セキュリティ ポリシーの構成方法について説明します。
- 「[Azure Security Center でのセキュリティ ヘルスの監視](security-center-monitoring.md)」 – Azure リソースのヘルスを監視する方法について説明します。
- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 - セキュリティの警告の管理および応答の方法について説明します。
- 「[Azure Security Center でのセキュリティに関する推奨事項の管理](security-center-recommendations.md)」 -- 推奨事項に従って Azure リソースを保護する方法について説明します。
- 「[Azure Security Center に関する FAQ](security-center-faq.md)」 – このサービスの使用に関してよく寄せられる質問を見つけます。
- 「[Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/)」 – Azure のセキュリティとコンプライアンスについてまとめたブログ記事を見つけます。

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]: ./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png

<!---HONumber=AcomDC_0224_2016-->