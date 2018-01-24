---
title: "Azure Stack 統合システム デプロイのための Azure Stack 公開キー インフラストラクチャ証明書を生成する | Microsoft Docs"
description: "Azure Stack 統合システムの Azure Stack PKI 証明書のデプロイ プロセスについて説明します。"
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: jeffgilb
ms.reviewer: wfayed
ms.openlocfilehash: e7edbc1c4aa6e3cb1026d493886ef7ca704b9131
ms.sourcegitcommit: 5108f637c457a276fffcf2b8b332a67774b05981
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/17/2018
---
# <a name="generate-pki-certificates-for-azure-stack-deployment"></a>Azure Stack デプロイのための PKI 証明書を生成する
これで Azure Stack デプロイのための [PKI 証明書の要件](azure-stack-pki-certs.md)がわかったので、選択した証明機関 (CA) からこれらの証明書を取得する必要があります。 

## <a name="request-certificates-using-an-inf-file"></a>INF ファイルを使用して証明書を要求する
パブリック CA または内部 CA のどちらかに証明書を要求する 1 つの方法に、INF ファイルの使用があります。 Windows 組み込みの certreq.exe ユーティリティは、証明書の詳細を指定した INF ファイルを使用して、このセクションで説明されている要求ファイルを生成できます。 

### <a name="sample-inf-file"></a>サンプルの INF ファイル 
サンプルの証明書要求 INF ファイルを使用すると、CA (内部またはパブリックのどちらか) に送信するためのオフラインの証明書要求ファイルを作成できます。 この INF は、1 つのワイルドカード証明書で、必要なすべてのエンドポイント (オプションの PaaS サービスを含む) に対応しています。 

サンプルの INF ファイルでは、リージョンが **sea** に等しく、外部 FQDN 値が **sea&#46;contoso&#46;com** であることを前提にしています。デプロイのための .INF ファイルを生成する前に、これらの値を環境に一致するように変更します。 

    
    [Version] 
    Signature="$Windows NT$"

    [NewRequest] 
    Subject = "C=US, O=Microsoft, L=Redmond, ST=Washington, CN=portal.sea.contoso.com"

    Exportable = TRUE                   ; Private key is not exportable 
    KeyLength = 2048                    ; Common key sizes: 512, 1024, 2048, 4096, 8192, 16384 
    KeySpec = 1                         ; AT_KEYEXCHANGE 
    KeyUsage = 0xA0                     ; Digital Signature, Key Encipherment 
    MachineKeySet = True                ; The key belongs to the local computer account 
    ProviderName = "Microsoft RSA SChannel Cryptographic Provider" 
    ProviderType = 12 
    SMIME = FALSE 
    RequestType = PKCS10
    HashAlgorithm = SHA256

    ; At least certreq.exe shipping with Windows Vista/Server 2008 is required to interpret the [Strings] and [Extensions] sections below

    [Strings] 
    szOID_SUBJECT_ALT_NAME2 = "2.5.29.17" 
    szOID_ENHANCED_KEY_USAGE = "2.5.29.37" 
    szOID_PKIX_KP_SERVER_AUTH = "1.3.6.1.5.5.7.3.1" 
    szOID_PKIX_KP_CLIENT_AUTH = "1.3.6.1.5.5.7.3.2"

    [Extensions] 
    %szOID_SUBJECT_ALT_NAME2% = "{text}dns=*.sea.contoso.com&dns=*.blob.sea.contoso.com&dns=*.queue.sea.contoso.com&dns=*.table.sea.contoso.com&dns=*.vault.sea.contoso.com&dns=*.adminvault.sea.contoso.com&dns=*.dbadapter.sea.contoso.com&dns=*.appservice.sea.contoso.com&dns=*.scm.appservice.sea.contoso.com&dns=api.appservice.sea.contoso.com&dns=ftp.appservice.sea.contoso.com&dns=sso.appservice.sea.contoso.com&dns=adminportal.sea.contoso.com&dns=management.sea.contoso.com&dns=adminmanagement.sea.contoso.com" 
    %szOID_ENHANCED_KEY_USAGE% = "{text}%szOID_PKIX_KP_SERVER_AUTH%,%szOID_PKIX_KP_CLIENT_AUTH%"

    [RequestAttributes]
    

## <a name="generate-and-submit-request-to-the-ca"></a>要求を生成して CA に送信する
次のワークフローでは、前に生成されたサンプルの INF ファイルをカスタマイズおよび使用して CA に証明書を要求する方法について説明します。

1. **INF ファイルを編集して保存します**。 提供されているサンプルをコピーし、それを新しいテキスト ファイルに保存します。 サブジェクト名と外部 FQDN をデプロイに一致する値に置き換え、そのファイルを .INF ファイルとして保存します。
2. **certreq を使用して要求を生成します**。 Windows コンピューターを使用して、管理者としてコマンド プロンプトを起動し、次のコマンドを実行して要求 (.req) ファイルを生成します: `certreq -new <yourinffile>.inf <yourreqfilename>.req`。
3. **CA に送信します**。 生成された .REQ ファイルを CA (内部またはパブリックのどちらか) に送信します。
4. **.CER をインポートします**。 CA が .CER ファイルを返します。 要求ファイルを生成したのと同じ Windows コンピューターを使用して、返された .CER ファイルをコンピューター/個人用ストアにインポートします。 
5. **.PFX をエクスポートし、デプロイ フォルダーにコピーします**。 証明書 (秘密キーを含む) を .PFX ファイルとしてエクスポートし、その .PFX ファイルを「[Azure Stack deployment PKI requirements (Azure Stack デプロイの PKI の要件)](azure-stack-pki-certs.md)」で説明されているデプロイ フォルダーにコピーします。

