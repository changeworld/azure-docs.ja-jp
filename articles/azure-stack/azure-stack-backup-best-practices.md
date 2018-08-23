---
title: Azure Stack のためのインフラストラクチャ バックアップ サービスのベスト プラクティス | Microsoft Docs
description: 自社のデータセンターに Azure Stack を展開して管理する際の一連のベスト プラクティスに従って、致命的な障害が発生した場合のデータ損失を軽減できます。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 221FDE40-3EF8-4F54-A075-0C4D66EECE1A
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.openlocfilehash: 08d8822410545fb0ae3a2a99de00b38566c9834c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2018
ms.locfileid: "41947942"
---
# <a name="infrastructure-backup-service-best-practices"></a>インフラストラクチャ バックアップ サービスのベスト プラクティス

*適用先: Azure Stack 統合システムと Azure Stack 開発キット*

自社のデータセンターに Azure Stack を展開して管理する際のベスト プラクティスに従って、致命的な障害が発生した場合のデータ損失を軽減できます。

ベスト プラクティスは定期的に確認して、操作フローに変更が加えられた場合でもインストールがコンプライアンスに準拠していることを確認してください。 ベスト プラクティスを適用する過程で問題が生じた場合は、Microsoft サポートにお問い合わせください。

## <a name="configuration-best-practices"></a>構成のベスト プラクティス

### <a name="deployment"></a>Deployment

各 Azure Stack Cloud の展開後に、インフラストラクチャ バックアップを有効にします。 Azure Stack PowerShell を使用することで、作業者の管理 API エンドポイントにアクセスできるクライアントやサーバーから、バックアップをスケジュールできます。

### <a name="networking"></a>ネットワーク

パスの汎用名前付け規則 (UNC) 文字列には、完全修飾ドメイン名 (FQDN) を使用する必要があります。 名前解決ができない場合は、IP アドレスも可能です。 UNC 文字列は、共有ファイルやデバイスといった、リソースの場所を指定します。

### <a name="encryption"></a>暗号化

暗号化キーを使用して、外部ストレージにエクスポートされるバックアップ データを暗号化します。 キーは、[PowerShell で Azure Stack のバックアップを有効にする](azure-stack-backup-enable-backup-powershell.md)一環として生成されます。

キーは、安全な場所 (パブリックの Azure Key Vault シークレット) に格納する必要があります。 このキーは、Azure Stack の再展開時に使用します。 

![安全な場所にキーを格納する。](media\azure-stack-backup\azure-stack-backup-encryption2.png)

## <a name="operational-best-practices"></a>運用上のベスト プラクティス

### <a name="backups"></a>バックアップ

 - インフラストラクチャ バックアップ コントローラーは、オンデマンドでトリガーする必要があります。 少なくとも 1 日 2 回はバックアップすることをお勧めします。
 - バックアップ ジョブはシステムの稼働中に実行されるため、管理エクスペリエンスやユーザーのアプリケーションにダウンタイムが発生しません。 それなりに負荷がかかっているソリューションでは、バックアップ ジョブに 20 - 40 分かかると見積もってください。
 - OEM 提供の手順に従って、ネットワーク スイッチを手動でバックアップします。また、インフラストラクチャ バックアップ コントローラーがコントロール プレーン バックアップ データを格納している場所と同じバックアップ共有上に、ハードウェア ライフサイクル ホスト (HLH) を格納する必要があります。 スイッチと HLH 構成は、リージョンのフォルダーに格納することを検討してください。 同一リージョンに Azure Stack インスタンスが複数ある場合は、スケール ユニットに属している構成ごとに識別子を使用することを検討してください。

### <a name="folder-names"></a>フォルダー名

 - インフラストラクチャでは、MASBACKUP フォルダーが自動的に作成されます。 これは、Microsoft によって管理される共有です。 MASBACKUP と同じレベルの共有を作成することもできます。 Azure Stack 以外で作成された MASBACKUP 内に、フォルダーやストレージ データを作成することは推奨されません。 
 -  フォルダー名に FQDN とリージョンを使用して、バックアップ データを、他のクラウドと区別します。 Azure Stack のデプロイの完全修飾ドメイン名 (FQDN) とエンドポイントは、リージョン パラメーターと外部ドメイン名 パラメーターの組み合わせです。 詳細については、「[Azure Stack とデータセンターの統合 - DNS](azure-stack-integrate-dns.md)」をご覧ください。

たとえば、バックアップ共有を、fileserver01.contoso.com 上でホストされている AzSBackups とします。 このファイル共有では、外部ドメイン名を使用する、Azure Stack 展開ごとのフォルダーと、リージョン名を使用するサブフォルダーが存在する可能性があります。 

FQDN: contoso.com  
リージョン: nyc


    \\fileserver01.contoso.com\AzSBackups
    \\fileserver01.contoso.com\AzSBackups\contoso.com
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\MASBackup

MASBackup フォルダーは、Azure Stack が Azure Stack のバックアップ データを格納する場所です。 ご自身が保持するデータの格納に、このフォルダーは使用しないでください。 同様に OEM も、バックアップ データの格納にはこのフォルダーを使用しないでください。 

OEM は、各社のコンポーネントのバックアップ データは、リージョン フォルダー配下に格納することを推奨します。 各ネットワーク スイッチ、ハードウェア ライフサイクル ホスト (HLH) などは、それぞれのサブフォルダーに格納されることもあります。 例: 

    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\HLH
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Switches
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\DeploymentData
    \\fileserver01.contoso.com\AzSBackups\contoso.com\nyc\Registration

### <a name="monitoring"></a>監視

システムでサポートされるアラートは、以下のとおりです。

| アラート:                                                   | 説明                                                                                     | 修復                                                                                                                                |
|---------------------------------------------------------|-------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ファイル共有の容量が不足しているため、バックアップに失敗しました | ファイル共有が容量不足のため、バックアップ コントローラーがバックアップ ファイルをその場所にエクスポートできません。 | ストレージ容量を追加して、バックアップをやり直します。 既存のバックアップを (最も古いものから) 削除して、空き容量を増やします。                    |
| 接続の問題のため、バックアップに失敗しました。             | Azure Stack とファイル共有間のネットワークに問題があります。                          | ネットワークの問題に対処して、バックアップをやり直します。                                                                                            |
| パスに障害があるため、バックアップに失敗しました                | ファイル共有のパスを解決できません                                                          | 別のコンピューターからこの共有をマップして、共有にアクセスできることを確認します。 パスが無効になっている場合は、更新が必要な場合があります。       |
| 認証の問題のため、バックアップに失敗しました               | 資格情報に問題があるか、認証に影響を与えるネットワークの問題がある可能性があります。    | 別のコンピューターからこの共有をマップして、共有にアクセスできることを確認します。 資格情報が無効になっている場合は、更新が必要な場合があります。 |
| 一般的なエラーのため、バックアップに失敗しました                    | 断続的な問題のため、要求が失敗した可能性があります。 バックアップをやり直してください。                    | サポートに問い合わせます                                                                                                                               |

## <a name="next-steps"></a>次の手順

 - [インフラストラクチャ バックアップ サービス](azure-stack-backup-reference.md)の参照資料を確認します。  
 - [インフラストラクチャ バックアップ サービス](azure-stack-backup-enable-backup-console.md)を有効にします。
