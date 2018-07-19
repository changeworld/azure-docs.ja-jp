---
title: Azure Stack 統合システム デプロイのための Azure Stack 公開キー インフラストラクチャ証明書を生成する | Microsoft Docs
description: Azure Stack 統合システムの Azure Stack PKI 証明書のデプロイ プロセスについて説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: mabrigg
ms.reviewer: ppacent
ms.openlocfilehash: b5adc1bb5a5aae96f37cc312588aa71e57d8342e
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2018
ms.locfileid: "37083228"
---
# <a name="azure-stack-certificates-signing-request-generation"></a>Azure Stack 証明書署名要求の生成

この記事で説明されている Azure Stack 適合性チェッカー ツールは、[PowerShell ギャラリーから](https://aka.ms/AzsReadinessChecker)入手可能です。 このツールでは、Azure Stack デプロイに適した証明書署名要求 (CSR) が作成されます。 証明書は、デプロイ前のテストに十分な時間を確保した上で、要求、生成、検証する必要があります。

Azure Stack 適合性チェッカー ツール (AzsReadinessChecker) では、次の証明書要求を実行します。

 - **標準証明書の要求**  
    [Azure Stack デプロイのための PKI 証明書の生成](azure-stack-get-pki-certs.md)に関する説明に従って、要求します。
 - **サービスとしてのプラットフォーム**  
    必要に応じて、「[Azure Stack 公開キー インフラストラクチャ証明書の要件」の「オプションの PaaS 証明書](azure-stack-pki-certs.md#optional-paas-certificates)」で指定されている証明書に対するサービスとしてのプラットフォーム (PaaS) 名を要求します。



## <a name="prerequisites"></a>前提条件

Azure Stack デプロイのための PKI 証明書に対する CSR を生成する前に、システムは次の前提条件を満たしている必要があります。

 - Microsoft Azure Stack 適合性チェッカー
 - 証明書の属性:
    - リージョン名
    - 外部完全修飾ドメイン名 (FQDN)
    - 件名
 - Windows 10 または Windows Server 2016
 
  > [!NOTE]
  > 証明機関から証明書が送り返されたら、「[Azure Stack PKI 証明書の準備](azure-stack-prepare-pki-certs.md)」の手順を同じシステムで完了する必要があります。

## <a name="generate-certificate-signing-requests"></a>証明書の署名要求を生成する

次の手順を使って、Azure Stack PKI 証明書を準備し、検証します。 

1.  次のコマンドレットを実行して、PowerShell プロンプト (5.1 以上) から AzsReadinessChecker をインストールします。

    ````PowerShell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ````

2.  順序指定されたディクショナリとして**サブジェクト**を宣言します。 例:  

    ````PowerShell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"} 
    ````
    > [!note]  
    > 共通名 (CN) が指定されている場合、証明書要求の最初の DNS 名によって上書きされます。

3.  既に存在する出力ディレクトリを宣言します。 例: 

    ````PowerShell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ````
4.  ID システムを宣言します

    Azure Active Directory

    ```PowerShell
    $IdentitySystem = "AAD"
    ````

    Active Directory フェデレーション サービス (AD FS)

    ```PowerShell
    $IdentitySystem = "ADFS"
    ````

5. Azure Stack デプロイのために**リージョン名**と**外部 FQDN** を宣言します。

    ```PowerShell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ````

    > [!note]  
    > `<regionName>.<externalFQDN>` は、Azure Stack のすべての外部 DNS が作成されるベースを形成します。この例では、ポータルは `portal.east.azurestack.contoso.com` となります。  

6. DNS 名ごとに証明書署名要求を生成する場合は、次のように実行します。

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    PaaS サービスを含めるには、スイッチ ```-IncludePaaS``` を指定します

7. Dev/Test 環境の場合、 複数のサブジェクトの別名 (SAN) を含む 1 つの証明書要求を生成するには、**-RequestType SingleCSR** パラメーターと値を追加します (運用環境では、推奨**されません**)。

    ```PowerShell  
    Start-AzsReadinessChecker -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ````

    PaaS サービスを含めるには、スイッチ ```-IncludePaaS``` を指定します
    
8. 出力を確認します。

    ````PowerShell  
    AzsReadinessChecker v1.1803.405.3 started
    Starting Certificate Request Generation

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Finished Certificate Request Generation

    AzsReadinessChecker Log location: C:\Program Files\WindowsPowerShell\Modules\Microsoft.AzureStack.ReadinessChecker\1.1803.405.3\AzsReadinessChecker.log
    AzsReadinessChecker Completed
    ````

9.  生成された **.REQ** ファイルを CA (内部またはパブリック) に送信します。  **Start-AzsReadinessChecker** の出力ディレクトリには、証明機関への送信に必要な CSR が含まれています。  また、参照として、証明書要求の生成中に使用される INF ファイルを含む子ディレクトリが含まれています。 CA が生成された要求を使用して、[Azure Stack PKI の要件](azure-stack-pki-certs.md)を満たす証明書を生成することを確認してください。

## <a name="next-steps"></a>次の手順

[Azure Stack PKI 証明書の準備](azure-stack-prepare-pki-certs.md)
