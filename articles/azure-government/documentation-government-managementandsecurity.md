<properties
	pageTitle="Azure Government のドキュメント | Microsoft Azure"
	description="Azure Government アプリケーションの機能の比較と開発におけるガイダンスを示します。"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="09/23/2016"
	ms.author="ryansoc"/>


#  Azure Government の管理とセキュリティ

##  Key Vault

以下の情報は、Azure Key Vault に関する Azure Government の機能領域について記述したものです。

| 許可される規制対象データ | 許可されない規制対象データ |
|--------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Azure Key Vault キーで暗号化されるすべてのデータには、規制対象データが含まれていてもかまいません。 | Azure Key Vault のメタデータに、輸出規制対象データを含めることは許可されません。このメタデータには、Key Vault を作成したり管理したりする際に入力するあらゆる構成データが含まれます。リソース グループ名、Key Vault 名、サブスクリプション名の各フィールドには規制対象データを入力しないでください。 |

Key Vault は、Azure Government で一般提供されています。パブリックと同様、拡張機能は存在しません。Key Vault の利用は、PowerShell と CLI のみとなります。

詳細については、[Azure Key Vault のパブリック ドキュメント](/key-vault-get-started)を参照してください。

## Log Analytics

Log Analytics は、Azure Government ではプレビュー段階です。

### パブリック Azure との違い

次の Log Analytics の機能とソリューションは、Azure Government では現在使用できません。この一覧は、機能/ソリューションの状態が変更された時点で更新されます。

+ ほぼリアルタイムのメトリック
  - 時間範囲が 6 時間未満のメトリック グラフを表示している場合、グラフは新しいメトリック値で自動更新されません
+ PowerBI へのデータのエクスポート
+ ネットワーク監視ソリューション
+ Office 365 ソリューション
+ Windows 10 アップグレード分析ソリューション
+ アプリケーション間の依存関係の監視
+ 更新の評価
+ Azure ポータルの統合
  - 監視する Azure ストレージ アカウントを選択するには、PowerShell または Resource Manager テンプレートを使用する必要があります
  - Log Analytics エージェントを有効にする仮想マシンを選択するには、PowerShell または Resource Manager テンプレートを使用する必要があります
+ Linux 監視
+ OMS モバイル アプリケーション
+ Microsoft Monitoring Agent の VM 拡張機能
+ OMS Linux エージェントの VM 拡張機能
+ 使用状況データ
+ Azure Automation を使用したアラート電子メールと修復

次の Log Analytics 機能は、Azure Government では動作が異なります。

+ Windows エージェントは、Azure Government の Log Analytics ポータルからダウンロードする必要があります。
+ セキュリティと監査ソリューションで、悪意のある IP 検出がサポートされていません。
+ データ コレクター API を使用してデータをアップロードするには、Azure Government の URL を使用する必要があります。

### よく寄せられる質問

+ パブリック Azure の Log Analytics のデータを Azure Government に移行できますか。
  - いいえ。パブリック Azure のデータやワークスペースを Azure Government に移動することはできません。
+ OMS Log Analytics ポータルから、パブリック Azure と Azure Government のワークスペースを切り替えることができますか。
  - いいえ。パブリック Azure と Azure Government のポータルは別個であり、情報を共有することはできません。

詳細については、[Log Analytics のパブリック ドキュメント](../log-analytics/log-analytics-overview.md)をご覧ください。

## 次のステップ

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を講読してください。

<!---HONumber=AcomDC_0928_2016-->