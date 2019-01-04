---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 3c6485406c67bf84b9e0fdfb9f4683abe5062d6c
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444220"
---
自己署名ルート証明書を作成した後、ルート証明書の (秘密キーではなく) 公開キーの .cer ファイルをエクスポートします。 後でこのファイルを Azure にアップロードします。 次の手順で、自己署名ルート証明書の .cer ファイルをエクスポートしてください。

1. 証明書から .cer ファイルを取得するには、**[ユーザー証明書の管理]** を開きます。 自己署名ルート証明書を探して右クリックします (通常は 'Current User\Personal\Certificates' にあります)。 **[すべてのタスク]**、**[エクスポート]** の順にクリックします。 **証明書のエクスポート ウィザード**が開きます。 Current User\Personal\Certificates で証明書が見つからない場合は、ローカル コンピューター証明書用に証明書マネージャーを開いた可能性があります (この場合はタイトルが "証明書 - 現在のユーザー" ではなく "証明書 - ローカル コンピューター" となります)。 現在のユーザー スコープで証明書マネージャーを開くには、証明書が作成されたのと同じ PowerShell から、「```certmgr```」と入力することによって証明書マネージャーを起動します。

   ![エクスポート](./media/vpn-gateway-certificates-export-public-key-include/export.png)
2. ウィザードで **[次へ]** をクリックします。

   ![証明書をエクスポートします。](./media/vpn-gateway-certificates-export-public-key-include/exportwizard.png)
3. **[いいえ、秘密キーをエクスポートしません]** を選択して、**[次へ]** をクリックします。

   ![秘密キーをエクスポートしません](./media/vpn-gateway-certificates-export-public-key-include/notprivatekey.png)
4. **[エクスポート ファイルの形式]** ページで **[Base-64 encoded X.509 (.CER)]** を選択し、**[次へ]** をクリックします。

   ![Base-64 エンコード](./media/vpn-gateway-certificates-export-public-key-include/base64.png)
5. **[エクスポートするファイル]** で、**[参照]** をクリックして証明書をエクスポートする場所を選択します。 **[ファイル名]** に証明書ファイルの名前を指定します。 次に、 **[次へ]** をクリックします。

   ![参照](./media/vpn-gateway-certificates-export-public-key-include/browse.png)
6. **[完了]** をクリックして、証明書をエクスポートします。

   ![[完了]](./media/vpn-gateway-certificates-export-public-key-include/finish.png)
7. 証明書が正常にエクスポートされました。

   ![Success](./media/vpn-gateway-certificates-export-public-key-include/success.png)
8. エクスポートされた証明書は次のようになります。

   ![エクスポート済み](./media/vpn-gateway-certificates-export-public-key-include/exported.png)
9. メモ帳を使ってエクスポートした証明書を開くと、この例のようなものが表示されます。 青で示したセクションには、Azure にアップロードされる情報が含まれます。 証明書をメモ帳で開いてもこのように表示されない場合は、通常、Base-64 エンコードの X.509 (.CER) 形式を使ってエクスポートしなかったことを意味します。 また、別のテキスト エディターを使う場合は、一部のエディターでは意図しない書式設定がバックグラウンドで行われる場合があることに注意してください。 これにより、この証明書から Azure にテキストをアップロードすると問題が発生する可能性があります。

   ![メモ帳で開く](./media/vpn-gateway-certificates-export-public-key-include/notepad.png)
