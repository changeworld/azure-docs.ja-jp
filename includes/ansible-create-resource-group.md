---
author: tomarchermsft
ms.service: ansible
ms.topic: include
ms.date: 08/09/2018
ms.author: tarcher
ms.openlocfilehash: fe995535cd42571ad96f192883e48f4b6ea1eb0e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160405"
---
1. Cloud Shell で、`rg.yml` という名前のファイルを作成します。

    ```bash
    vi rg.yml
    ```

1. **I** キーを選択して挿入モードに入ります。

1. 以下のコードをエディターに貼り付けます。

   ```yaml
   ---
   - hosts: localhost
     connection: local
     tasks:
       - name: Create resource group
         azure_rm_resourcegroup:
           name: ansible-rg
           location: eastus
         register: rg
       - debug:
           var: rg
   ```

1. **Esc** キーを押して、挿入モードを終了します。

1. ファイルを保存し、次のコマンドを入力して vi エディターを終了します。

    ```bash
    :wq
    ```

1. プレイブック `rg.yml` を実行します。

   ```bash
   ansible-playbook rg.yml
   ```

ansible コマンドを実行した結果は、次の出力のようになります。

```output
PLAY [localhost] *********************************************************************************

TASK [Gathering Facts] ***************************************************************************
ok: [localhost]

TASK [Create resource group] *********************************************************************
changed: [localhost]

TASK [debug] *************************************************************************************
ok: [localhost] => {
    "rg": {
        "changed": true,
        "contains_resources": false,
        "failed": false,
        "state": {
            "id": "/subscriptions/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/resourceGroups/ansible-rg",
            "location": "eastus",
            "name": "ansible-rg",
            "provisioning_state": "Succeeded",
            "tags": null
        }
    }
}

PLAY RECAP ***************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0
```