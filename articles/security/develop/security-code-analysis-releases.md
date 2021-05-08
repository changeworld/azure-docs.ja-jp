---
title: Microsoft Security Code Analysis リリース
description: この記事では、Microsoft Security Code Analysis 拡張機能の今後のリリースについて説明します
author: sukhans
manager: sukhans
ms.author: terrylan
ms.date: 03/22/2021
ms.topic: article
ms.service: security
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 7596df66dbcbe1b7cdefab4811da7174bc83ac65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801181"
---
# <a name="microsoft-security-code-analysis-releases-and-roadmap"></a>Microsoft Security Code Analysis リリースとロードマップ

> [!Note]
> 2022 年 3 月 1 日より、Microsoft Security Code Analysis (MSCA) 拡張機能は廃止される予定です。 既存の MSCA のお客様は、2022 年 3 月 1 日まで MSCA にアクセスできます。 Azure DevOps の代替のオプションについては、[OWASP ソースコード分析ツール](https://owasp.org/www-community/Source_Code_Analysis_Tools)に関するページを参照してください。 GitHub への移行を計画しているお客様については、[GitHub Advanced Security](https://docs.github.com/github/getting-started-with-github/about-github-advanced-security) に関するページをご確認ください。

Developer サポートと提携している Microsoft Security Code Analysis チームから、MSCA 拡張機能の最近および今後の機能強化についてお知らせします。


## <a name="credential-scanner-v20-released-in-april-2020"></a>Credential Scanner v2.0: 2020 年 4 月リリース

### <a name="innovations--improvements"></a>イノベーションと機能強化

- **コア エンジン**

   - ほぼ線形の実行時間での 25% の平均パフォーマンス アップグレード
   - 精度向上のためのコンテキストまたは証拠ベースの検索とランク付け
   - 明確なプレースホルダーに対する一般的なパスワード検出と一致ロジック (fakePassword など) の機能強化

- **対象範囲** - 25 種類以上のシークレットをサポートしています。これには、要求される次の上位のものが含まれます。

   - ファブリック アカウント証明書のパスフレーズ
   - クライアント シークレット キーまたは API キー
   - HTTP Authorization ヘッダー
   - Amazon S3 クライアント シークレット アクセス キー
   - Azure Active Directory クライアント アクセス トークン
   - Azure 関数マスター キーまたは API キー
   - Power BI アクセス キー
   - Azure Resource Manager テンプレートのパスワードのパターン

- **出力**

   - SARIF 2.1 と CSV ファイルの出力ファイル形式のサポート

## <a name="binskim-v160-released-in-april-2020"></a>BinSkim v1.6.0: 2020 年 4 月リリース

### <a name="improvements"></a>機能強化

- 機能: 最終 SARIF v2 (バージョン 2.1.16) に更新します。 この更新により、コマンドラインで --hashes を渡すときに、結果のキャッシュが有効になります。スキャン対象の複数のコピーを含むディレクトリを再帰的に分析する場合、パフォーマンスが大幅に向上します。
- バグの修正: BA2021.DoNotMarkWritableSectionsAsExecutable 出力のスペルミスを修正します。
- パフォーマンス: IL ライブラリ (事前にコンパイルされた) バイナリを含む、マネージド アセンブリに対するすべての非混合モードの PDB 読み込みを削除します。
- 検知漏れの修正: バイナリと共に配置されている PDB が、分析中のバイナリと実際に一致することを確認します。
- 機能: --ローカル シンボル ディレクトリ引数を提供して、追加の PDB の検索場所を指定します (ローカル、シンボルサーバー以外)。
- 誤検知の修正: 生成された .NET Core ネイティブ ブートストラップ exe (ユーザーが制御可能なコードではない) に対して、PDB ドリブンの分析をスキップします。

## <a name="whats-next-in-q3-cy20"></a>Q3 CY20 の新機能

- Java セキュリティ分析ツール
- Python セキュリティ分析ツール
- ES lint による TypeScript と JavaScript の TS lint の置換
- Resource Manager テンプレート分析ツール

## <a name="tool-deprecation-notification"></a>非推奨になったツールの通知

### <a name="microsoft-security-risk-detection-msrd-is-deprecated-on-june-26-2020"></a>Microsoft Security Risk Detection (MSRD) は 2020 年 6 月 26 日に非推奨となりました。

非推奨になった MSRD ファジー サービスは、Azure のオープン ソースのセルフホステッド開発者ファジー プラットフォームに置き換えられます。 現在、このプラットフォームは、Microsoft の主要な製品チームの多くと提携して開発およびテストされています。 このファジー プラットフォームには、サニタイザーが統合され、ソフトウェア プロジェクトで時間の経過と共に拡張される、CI/CD パイプラインに組み込まれた適応性のある学習ファジー テストが可能になります。 このプラットフォームのオープン ソース リリースは、2020 年の後半にスケジュールされています。

## <a name="next-steps"></a>次のステップ

Microsoft Security Code Analysis をオンボードしてインストールする方法については、[オンボードとインストールのガイド](security-code-analysis-onboard.md)を参照してください。

この拡張機能と提供されるツールにさらに質問がある場合は、[FAQ ページ](security-code-analysis-faq.md)を参照してください。
