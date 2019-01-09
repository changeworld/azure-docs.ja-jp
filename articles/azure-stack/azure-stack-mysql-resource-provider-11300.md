---
title: Azure Stack MySQL リソース プロバイダー 1.1.30.0 のリリース ノート | Microsoft Docs
description: 既知の問題やダウンロード場所など、Azure Stack MySQL リソース プロバイダーの最新の更新プログラムについて説明します。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: jeffgilb
ms.reviewer: georgel
ms.openlocfilehash: 2f300e496873c0b048ccc1acc078bf1650e6bd9c
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166287"
---
# <a name="mysql-resource-provider-11300--release-notes"></a>MySQL リソース プロバイダー 1.1.30.0 のリリース ノート

*適用対象: Azure Stack 統合システムと Azure Stack Development Kit*

これらのリリース ノートでは、MySQL リソース プロバイダー バージョン 1.1.30.0 の機能強化と既知の問題について説明します。

## <a name="build-reference"></a>ビルドのリファレンス
MySQL リソース プロバイダー バイナリをダウンロードした後、自己展開ツールを実行してコンテンツを一時ディレクトリに展開します。 リソース プロバイダーには、対応する最低限の Azure Stack ビルドがあります。 MySQL リソース プロバイダーのこのバージョンをインストールするために必要な Azure Stack の最小リリース バージョンを次に示します。

> |最小の Azure Stack バージョン|MySQL リソース プロバイダーのバージョン|
> |-----|-----|
> |Azure Stack 1808 更新プログラム (1.1808.0.97)|[1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
> |     |     |

> [!IMPORTANT]
> MySQL リソース プロバイダーの最新バージョンをデプロイする前に、サポートされている最低限の Azure Stack 更新プログラムを Azure Stack 統合システムに適用するか、最新の Azure Stack Development Kit (ASDK) をデプロイします。

## <a name="new-features-and-fixes"></a>新機能と修正
このバージョンの Azure Stack MySQL リソース プロバイダーには、次の機能強化と修正プログラムが含まれています。

- **MySQL リソース プロバイダーのデプロイに対して有効になっているテレメトリ**。 MySQL リソース プロバイダーのデプロイに対してテレメトリ収集が有効になっています。 収集されるテレメトリには、リソース プロバイダーのデプロイ、開始時刻と終了時刻、終了状態、終了メッセージ、エラーの詳細 (該当する場合) が含まれています。

- **TLS 1.2 暗号化の更新**。 リソース プロバイダーと内部の Azure Stack コンポーネントとの通信に対して TLS 1.2 のみのサポートが有効になっています。 

### <a name="fixes"></a>修正

- **MySQL リソース プロバイダーの Azure Stack PowerShell との互換性**。 MySQL リソース プロバイダーは、Azure Stack 2018-03-01-hybrid PowerShell プロファイルと連携し、AzureRM 1.3.0 以降との互換性を提供するように更新されました。

- **MySQL ログインの [パスワードの変更] ブレード**。 [パスワードの変更] ブレードでパスワードを変更できないという問題が修正されました。 パスワード変更通知からリンクを削除しました。

## <a name="known-issues"></a>既知の問題 

- **MySQL SKU はポータルに表示されるまで最大 1 時間かかることがあります**。 新しい MySQL データベースを作成するときに、新規に作成される SKU が表示されて使用できるようになるまで、最大 1 時間かかることがあります。 

    **対処法**: なし。

- **再利用された MySQL ログイン**。 同じサブスクリプションの既存のログインと同じユーザー名で新しい MySQL ログインを作成しようとすると、同じログインと既存のパスワードが再利用されます。 

    **対処法**: 同じサブスクリプションに新しいログインを作成するときに別のユーザー名を使用するか、同じユーザー名のログインを異なるサブスクリプションに作成します。

- **TLS 1.2 サポート要件**。 TLS 1.2 が有効になっていないコンピューターから MySQL リソース プロバイダーをデプロイまたは更新しようとすると、操作が失敗する可能性があります。 リソース プロバイダーのデプロイまたは更新に使用するコンピューターで、次の PowerShell コマンドを実行して、TLS 1.2 がサポート対象であるかどうかを確認します。

  ```powershell
  [System.Net.ServicePointManager]::SecurityProtocol
  ```

  **Tls12** がコマンドの出力に含まれていない場合、TLS 1.2 はそのコンピューターでは使用できません。

    **対処法**: 次の PowerShell コマンドを実行して TLS 1.2 を有効にし、同じ PowerShell セッションからリソース プロバイダーのデプロイを開始するかまたはスクリプトを更新します。

    ```powershell
    [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
    ```
 
### <a name="known-issues-for-cloud-admins-operating-azure-stack"></a>Azure Stack を運用するクラウド管理者に関する既知の問題
[Azure Stack リリース ノート](azure-stack-servicing-policy.md)内のドキュメントをご覧ください。

## <a name="next-steps"></a>次の手順
[MySQL リソース プロバイダーの詳細を確認します](azure-stack-mysql-resource-provider.md)。

[MySQL リソースプロバイダーのデプロイを準備します](azure-stack-mysql-resource-provider-deploy.md#prerequisites)。

[MySQL リソース プロバイダーを以前のバージョンからアップグレードします](azure-stack-mysql-resource-provider-update.md)。 