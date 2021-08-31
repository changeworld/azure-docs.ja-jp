---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736588"
---
**[Next: Advanced Settings]\(次へ: 詳細設定\)** を選択したら、次のようにします。

1. **[Enable SSH access]\(SSH アクセスを有効にする\)** をオンにします。
1. **[SSH public key source]\(SSH 公開キーのソース\)** で、ドロップダウンから次のいずれかのオプションを選択します。
    * **[新しいキーの組を生成]** の場合:
        1. **[Key pair name]\(キーの組の名前\)** にキーの名前を入力します。
        1. **［作成］** を選択します
        1. **[Download private key and create compute]\(秘密キーをダウンロードしてコンピューティングを作成する\)** を選択します。  キーは通常、 **[ダウンロード]** フォルダーにダウンロードされます。  
    * **[Use existing public key stored in Azure]\(Azure に保存されている既存の公開キーを使用する\)** を選択した場合には、 **[Stored key]\(保存されているキー\)** でキーを探して選択します。
    * **[既存の公開キーを使用]** を選択した場合には、("ssh-rsa" から始まる) 1 行の形式か、複数行の PEM 形式で RSA 公開キーを入力します。 SSH キーは、Linux と OS X では ssh-keygen を、Windows では PuTTYGen を使用して生成できます。