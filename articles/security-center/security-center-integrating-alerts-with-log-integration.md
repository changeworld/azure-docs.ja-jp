<properties
   pageTitle="Azure Security Center の警告と Azure ログの統合 (プレビュー) | Microsoft Azure"
   description="この記事では、Security Center の警告と Azure ログの統合の概要について説明します。"
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
   ms.date="08/08/2016"
   ms.author="terrylan"/>

# Security Center の警告と Azure ログの統合 (プレビュー)

セキュリティ操作およびインシデント対応チームの多くが、セキュリティ情報イベント管理 (SIEM) ソリューションを、セキュリティ警告のトリアージと調査の開始点として使用します。Azure ログ統合により、お客様は、Azure Security Center の警告を、Azure 診断および Azure 監査ログによって収集された仮想マシンのセキュリティ イベントとともに、ログ分析または SIEM ソリューションとほぼリアルタイムで同期させることができます。

Azure ログ統合は、HP ArcSight、Splunk、IBM Qradar などで動作します。

## 統合できるログ

Azure では、すべてのサービスの広範なログ記録を作成します。これらのログは、次のように分類されます。

- **コントロール/管理ログ**は、Azure Resource Manager の CREATE、UPDATE、および DELETE 操作の可視性を提供します。
- **データ プレーン ログ**は、Azure のリソースを使用する場合に発生するイベントの可視性を提供します。仮想マシンの Windows イベント ログ (セキュリティ ログとアプリケーション ログ) はその例です。

Azure ログ統合では、現在、次のログの統合をサポートしています。

- Azure VM ログ
- Azure 監査ログ
- Azure Security Center のアラート

## Azure ログ統合のインストール

[Azure ログ統合](https://www.microsoft.com/download/details.aspx?id=53324)をダウンロードします。

Azure ログ統合サービスは、インストール先のマシンから利用統計情報を収集します。収集される利用統計情報を以下に示します。

- Azure ログ統合の実行中に発生する例外
- 処理されたクエリおよびイベントの数に関するメトリック
- 使用されている Azlog.exe コマンド ライン オプションについての統計情報

> [AZURE.NOTE] このオプションをオフにして、利用統計情報の収集を無効にすることができます。

## Azure 監査ログと Security Center のアラートの統合

1. コマンド プロンプトを開き、**cd** により、現在のディレクトリを **C:\\Program Files\\Microsoft Azure Log Integration** に変更します。

2. **azlog createazureid** コマンドを実行して、Azure サブスクリプションをホストする Azure Active Directory (AD) テナントに [Azure Active Directory サービス プリンシパル](../active-directory/active-directory-application-objects.md)を作成します。

    Azure のログインを求められます。

    > [AZURE.NOTE] サブスクリプション所有者またはサブスクリプションの共同管理者である必要があります。

    Azure への認証は、Azure AD によって行われます。Azure ログ統合のサービス プリンシパルを作成すると、Azure サブスクリプションからの読み取りアクセス許可が付与された、Azure AD の ID が作成されます。

3. **azlog authorize <SubscriptionID>** コマンドを実行して、手順 2 で作成したサービス プリンシパルにサブスクリプションでの閲覧者アクセス許可を割り当てます。**SubscriptionID** を指定しない場合は、作成者がアクセスできるすべてのサブスクリプションに対する閲覧者のロールがサービス プリンシパルに割り当てられます。

    > [AZURE.NOTE] **authorize** コマンドを、**createazureid** コマンドの直後に実行すると警告が表示されることがあります。これは、Azure AD のアカウントの作成からアカウントが使用できるようになるまでに一定の待機時間があるためです。**createazureid** コマンドを実行した後で約 10 秒間待ってから **authorize** コマンドを実行すれば、この警告が表示されることはありません。

4. 次のフォルダーを調べて、監査ログの JSON ファイルがあることを確認します。

  - **C:\\Users\\azlog\\AzureResourceManagerJson**
  - **C:\\Users\\azlog\\AzureResourceManagerJsonLD**

5. 次のフォルダーを調べて、Security Center のアラートが存在することを確認します。

  - **C:\\Users\\azlog\\AzureSecurityCenterJson**
  - **C:\\Users\\azlog\\AzureSecurityCenterJsonLD**

6. 標準的な SIEM ファイル フォワーダー コネクタで、SIEM インスタンスにデータをパイプ処理する適切なフォルダーをポイントします。SIEM 構成については、[SIEM 構成](https://azsiempublicdrops.blob.core.windows.net/drops/ALL.htm)に関するページをご覧ください。

Azure ログ統合に関する質問がある場合は、[AzSIEMteam@microsoft.com](mailto:AzSIEMteam@microsoft.com) 宛に電子メールを送信してください。

## 次のステップ

Azure 監査ログとプロパティ定義の詳細については、次をご覧ください。

- [リソース マネージャーの監査操作](../resource-group-audit.md)
- 「[List the management events in a subscription (サブスクリプションでの管理イベントを一覧表示する)](https://msdn.microsoft.com/library/azure/dn931934.aspx)」-- 監査ログのイベントを取得する方法です。

セキュリティ センターの詳細については、次を参照してください。

- 「[Azure Security Center でのセキュリティの警告の管理と対応](security-center-managing-and-responding-alerts.md)」 -- セキュリティの警告の管理と対応の方法について説明しています。
- 「[Azure Security Center のよく寄せられる質問 (FAQ)](security-center-faq.md)」 -- このサービスの使用に関してよく寄せられる質問が記載されています。
- [Azure セキュリティ ブログ](http://blogs.msdn.com/b/azuresecurity/) -- Azure のセキュリティに関する最新のニュースと情報を入手できます。

<!---HONumber=AcomDC_0921_2016-->