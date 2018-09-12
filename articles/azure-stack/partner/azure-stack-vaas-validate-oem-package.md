---
title: Azure Stack のサービスとしての検証で OEM (相手先ブランド供給) パッケージを検証する | Microsoft Docs
description: サービスとしての検証で OEM (相手先ブランド供給) パッケージをチェックする方法について説明します。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: cefa32c35df4e87d4d2b983ee8c4a16dc065e774
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/07/2018
ms.locfileid: "44160457"
---
# <a name="validate-oem-packages"></a>OEM パッケージの検証

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

完了しているソリューションの検証に関してファームウェアやドライバーに変更が生じたときは、新しい OEM パッケージをテストすることができます。 テストに合格したパッケージは、Microsoft によって署名されます。 テストには、Windows Server ロゴと PCS テストに合格したドライバーとファームウェアと共に、更新された OEM 拡張機能パッケージが含まれている必要があります。

すべてのテストは、結果が**成功**であれば 24 時間以内に完了します。 いずれかのテストの結果が**失敗**であった場合は、[Microsoft Collaborate](https://aka.ms/collaborate) にバグを提出し、[vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) にメールを送信して Microsoft に通知してください。

## <a name="get-your-oem-package-signed"></a>OEM パッケージに署名してもらう

1. 現在、最新の月例更新プログラムが適用済みであることを確認します。 最新バージョンについては、[「Azure Stack のオペレーター ドキュメント」の「概要」の「リリース ノート」](https://docs.microsoft.com/azure/azure-stack/)で確認してください。

    Azure Stack を対象とした Microsoft のソフトウェア更新プログラムは名前付け規則を使って指定されます。たとえば 1803 は、2018 年 3 月の更新プログラムであることを示します。 Azure Stack の更新ポリシー、周期、リリース ノートについては、「[Azure Stack サービス ポリシー](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy)」を参照してください。

1. 「Azure Stack の検証テストを実行する」の説明に従って **Test-AzureStack** を実行してシステムの正常性状態をチェックします。 警告やエラーがあれば、テストを開始する前にすべて解消しておいてください。

2. [検証ポータル](https://azurestackvalidation.com)で、既存のソリューションを選択します。 まだソリューションを追加していない場合は、「[新しいソリューションの追加](azure-stack-vaas-validate-solution-new.md#add-a-new-solution)」を参照してください。

3. **[Package Validations]\(パッケージの検証\)** タイルの **[開始]** を選択して新しいワークフローを開始します。

    ![パッケージの検証](media/image3.png)

4.  診断接続文字列を指定します。 その手順については、[ストレージ アカウントの設定](azure-stack-vaas-set-up-account.md)に関するページを参照してください。

    パッケージの検証を実行するたびに、OEM 拡張機能パッケージを指定する必要があります。 Azure Stack のデプロイ時にソリューションにインストールされた OEM パッケージを指定します。 その手順については、「[Create an Azure storage blob to store logs (ログを格納する Azure Storage Blob の作成)](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs)」を参照してください。

    実行時のデータ入力のミスを防ぐために、必須フィールドへの入力には、環境変数を含んだ JSON ファイルを使ってください。 その手順については、[Azure Stack デプロイにおける構成ファイルの取得](azure-stack-vaas-parameters.md)に関するページを参照してください。

5. テストを実行します。

6. すべてのテストが正常に完了したら、ソリューションとパッケージの検証の名前を [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) に送信して、パッケージの署名を要求してください。

## <a name="next-steps"></a>次の手順

- [Azure Stack のサービスとしての検証](https://docs.microsoft.com/azure/azure-stack/partner)について、さらに詳しい情報をご覧ください。
