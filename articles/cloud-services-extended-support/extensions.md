---
title: Cloud Services (延長サポート) の拡張機能
description: Cloud Services (延長サポート) の拡張機能
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 6db43acda679c6c1c1edd6336f693cc4757b6d45
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220919"
---
# <a name="extensions-for-cloud-services-extended-support"></a>Cloud Services (延長サポート) の拡張機能

拡張機能は、ロールでのデプロイ後の構成および自動化タスクを提供する小さなアプリケーションです。 たとえば、リモート デスクトップ拡張機能を使用して、クラウド サービス (延長サポート) のデプロイ中にロールでリモート デスクトップ接続を有効にすることができます。  

## <a name="remote-desktop-extension"></a>リモート デスクトップ拡張機能

リモート デスクトップを使用して、Azure で実行されているロールのデスクトップにアクセスできます。 リモート デスクトップ接続を使用すると、アプリケーションの問題をその実行中にトラブルシューティングしたり、診断したりできます。

サービス定義にリモート デスクトップ モジュールを含めるか、またはリモート デスクトップ拡張機能を使用することによって、開発中にロールでリモート デスクトップ接続を有効にすることができます。 

詳細については、[Azure portal からのリモート デスクトップの構成](enable-rdp.md)に関するページを参照してください。

## <a name="windows-azure-diagnostics-extension"></a>Windows Azure Diagnostics 拡張機能

どのクラウド サービスでも主要なパフォーマンス メトリックを監視することができます。 各クラウド サービス ロールは最小限のデータ (CPU 使用率、ネットワーク使用状況、ディスク使用率) を収集します。 クラウド サービスでロールに Microsoft.Azure.Diagnostics 拡張機能が適用されている場合、そのロールは追加のデータ ポイントを収集できます。 

ベーシックな監視機能では、ロール インスタンスのパフォーマンス カウンター データは、3 分間隔でサンプリングされて収集されます。 このベーシックな監視のデータはストレージ アカウントには格納されないため、追加のコストが伴うことはありません。 

高度な監視機能では、5 分、1 時間、12 時間の間隔で追加のメトリックがサンプリングされて収集されます。 集計されたデータはストレージ アカウントにテーブルとして格納され、10 日後に消去されます。 使用されるストレージ アカウントはロールごとに構成されます。ロールによって異なるストレージ アカウントを使用できます。 

詳細については、「[Cloud Services (延長サポート) で Windows Azure Diagnostics 拡張機能を適用する](enable-wad.md)」を参照してください。

## <a name="anti-malware-extension"></a>マルウェア対策の拡張機能
Azure のアプリケーションまたはサービスでは、PowerShell コマンドレットを使用して Azure Cloud Services 向けの Microsoft マルウェア対策を有効化および構成できます。 Microsoft Antimalware は、Windows Server 2012 R2 以前を実行している Cloud Services プラットフォームに無効状態でインストールされているので、Azure アプリケーションで有効化する必要があることに注意してください。 Windows Server 2016 以降では、Windows Defender が既定で有効になっているため、これらのコマンドレットを使用してマルウェア対策を構成できます。

詳細については、[延長サポート (CS-ES) を使用した Azure Cloud Service への Microsoft Antimalware の追加](https://docs.microsoft.com/azure/security/fundamentals/antimalware-code-samples#add-microsoft-antimalware-to-azure-cloud-service-using-extended-support)に関する記事を参照してください

Azure のマルウェア対策の詳細については、[こちら](https://docs.microsoft.com/azure/security/fundamentals/antimalware)を参照してください



## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認します。
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
