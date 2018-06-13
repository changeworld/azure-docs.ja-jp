---
title: Azure Stack の証明書に関する問題を修復する | Microsoft Docs
description: Azure Stack 適合性チェッカーを使用して、証明書の問題を確認し、修復します。
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/08/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 0d2c4d848f861e4e07dbd0de4609344955ca26f7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/10/2018
ms.locfileid: "33937573"
---
# <a name="remediate-common-issues-for-azure-stack-pki-certificates"></a>Azure Stack PKI 証明書に関する一般的な問題を修復する
この記事の情報は、Azure Stack PKI 証明書の一般的な問題について理解し、解決するうえで役立ちます。 Azure Stack 適合性チェッカー ツールを使用して [Azure Stack PKI 証明書を検証](azure-stack-validate-pki-certs.md)すると、問題を見つけることができます。 このツールは、証明書が Azure Stack デプロイと Azure Stack シークレット ローテーションの PKI 要件を満たしていることを確認し、その結果のログを [report.json ファイル](azure-stack-validation-report.md)に出力します。  

## <a name="read-pfx"></a>PFX の読み取り
**警告** - 証明書の個人情報がパスワードのみで保護されています。  
**解決策** - **[証明書のプライバシーを有効にする]** のオプション設定を使用して PFX ファイルをエクスポートすることをお勧めします。  

**エラー** - PFX ファイルが無効です。  
**解決策** - 「[デプロイ用の Azure Stack PKI 証明書の準備](azure-stack-prepare-pki-certs.md)」の手順を使用して、証明書を再度エクスポートします。

## <a name="signature-algorithm"></a>署名アルゴリズム
**エラー** - 署名アルゴリズムが SHA1 です。    
**解決策** - 「Azure Stack 証明書署名要求の生成」の手順を使用して、署名アルゴリズム SHA256 で証明書署名要求 (CSR) を再生成します。 その後、CSR を証明機関に再送信して、証明書を再発行します。

## <a name="private-key"></a>秘密キー
**エラー** - 秘密キーがないか、秘密キーにローカル コンピューター属性が含まれません。  
**解決策** - 「デプロイ用の Azure Stack PKI 証明書の準備」の手順を使用して、CSR を生成したコンピューターから証明書を再度エクスポートします。 この手順には、ローカル コンピューターの証明書ストアからのエクスポートが含まれます。

## <a name="certificate-chain"></a>証明書チェーン
**エラー** - 証明書チェーンが完全ではありません。  
**解決策** - 証明書には、完全な証明書チェーンを含める必要があります。  「[デプロイ用の Azure Stack PKI 証明書の準備](azure-stack-prepare-pki-certs.md)」の手順を使用して証明書を再度エクスポートし、**[証明書のパスにある証明書を可能であればすべて含む]** オプションを選択します。

## <a name="dns-names"></a>DNS 名
**エラー** - 証明書の DNSNameList に、Azure Stack サービス エンドポイント名または有効なワイルドカード一致が含まれません。  ワイルドカード一致は、DNS 名の左端の名前空間に対してのみ有効です。 たとえば、_*.region.domain.com_ は、*portal.region.domain.com* に対してのみ有効です。_*.table.region.domain.com_ に対しては有効ではありません。  
**解決策** - 「Azure Stack 証明書署名要求の生成」の手順を使用して、Azure Stack エンドポイントをサポートするように正しい DNS 名で CSR を再生成します。 CSR を証明機関に再送信し、「[デプロイ用の Azure Stack PKI 証明書の準備](azure-stack-prepare-pki-certs.md)」の手順に従って、CSR を生成したマシンから、証明書をエクスポートします。  

## <a name="key-usage"></a>キー使用法
**エラー** - キー使用法に、デジタル署名またはキーの暗号化がありません。または拡張キー使用法に、サーバー認証またはクライアント認証がありません。  
**解決策** - 「[Azure Stack 証明書署名要求の生成](azure-stack-get-pki-certs.md)」の手順を使用して、正しいキー使用法の属性で CSR を再生成します。  CSR を証明機関に再送信して、証明書テンプレートによって要求のキー使用法が上書きされていないことを確認します。

## <a name="key-size"></a>キー サイズ
**エラー** - キー サイズが 2048 未満です    
**解決策** - 「[Azure Stack 証明書署名要求の生成](azure-stack-get-pki-certs.md)」の手順を使用して、正しいキーの長さ (2048) で CSR を再生成し、CSR を証明機関に再送信します。

## <a name="chain-order"></a>チェーンの順序
**エラー** - 証明書チェーンの順序が正しくありません。  
**解決策** - 「**デプロイ用の Azure Stack PKI 証明書の準備**」の手順を使用して、証明書を再度エクスポートし、[[証明書のパスにある証明書を可能であればすべて含む]](azure-stack-prepare-pki-certs.md) オプションを選択します。 リーフ証明書のみがエクスポート用に選択されていることを確認します。 

## <a name="other-certificates"></a>他の証明書
**エラー** - PFX パッケージに、リーフ証明書または証明書チェーンの一部ではない証明書が含まれています。  
**解決策** - 「**デプロイ用の Azure Stack PKI 証明書の準備**」の手順を使用して、証明書を再度エクスポートし、[[証明書のパスにある証明書を可能であればすべて含む]](azure-stack-prepare-pki-certs.md) オプションを選択します。 リーフ証明書のみがエクスポート用に選択されていることを確認します。

## <a name="fix-common-packaging-issues"></a>パッケージに関する一般的な問題の修正
AzsReadinessChecker は、PFX ファイルをインポートしてから、エクスポートし、次のようなパッケージに関する一般的な問題を修正できます。 
 - "*秘密キー*" にローカル コンピューター 属性がない。
 - "*証明書チェーン*" が完全ではないか、間違っている  (PFX パッケージに証明書チェーンが含まれない場合、ローカル コンピューターに証明書チェーンを含める必要があります)。 
 - "*他の証明書*"。
ただし、新しい CSR を生成し、証明書を再発行する必要がある場合、AzsReadinessChecker は役に立ちません。 

### <a name="prerequisites"></a>前提条件
ツールが実行されているコンピューターで、次の前提条件を満たす必要があります。 
 - インターネットに接続された Windows 10 または Windows Server 2016。
 - PowerShell 5.1 以降。 お使いのバージョンを確認するには、次の PowerShell コマンドを実行し、"*メジャー*" バージョンと "*マイナー*" バージョンを確かめます。

   > `$PSVersionTable.PSVersion`
 - [PowerShell for Azure Stack](azure-stack-powershell-install.md) を構成します。 
 - 最新バージョンの [Microsoft Azure Stack 適合性チェッカー](https://aka.ms/AzsReadinessChecker) ツールをダウンロードします。

### <a name="import-and-export-an-existing-pfx-file"></a>既存の PFX ファイルのインポートとエクスポート
1. 前提条件を満たしているコンピューターで、管理 PowerShell プロンプトを開き、次のコマンドを実行して、AzsReadinessChecker をインストールします  
   > `Install-Module Microsoft.AzureStack.ReadinessChecker- Force`

2. PowerShell プロンプトから次を実行して、PFX パスワードを設定します。 *PFXpassword* を実際のパスワードで置き換えます。 
   > `$password = Read-Host -Prompt PFXpassword -AsSecureString`

3. PowerShell プロンプトから次を実行して、新しい PFX ファイルをエクスポートします。
   - *- PfxPath* については、操作している PFX ファイルへのパスを指定します。  次の例では、パスは *.\certificates\ssl.pfx* です。
   - *- ExportPFXPath* については、エクスポートする PFX ファイルの名前と場所を指定します。  次の例では、パスは *.\certificates\ssl_new.pfx* です

   > `Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx`  

4. ツールが完了したら、成功したか出力を確認します。![結果](./media/azure-stack-remediate-certs/remediate-results.png)

## <a name="next-steps"></a>次の手順
[Azure Stack のセキュリティについて詳しく学習する](azure-stack-rotate-secrets.md)