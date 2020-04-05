---
title: チュートリアル - Ansible を使用して Azure Shared Image Gallery から VM または仮想マシン スケール セットを作成する
description: Ansible を使用して、Shared Image Gallery にある一般化されたイメージに基づく VM または仮想マシン スケール セットを作成する方法について説明します。
keywords: Ansible, Azure, DevOps, Bash, プレイブック, 仮想マシン, 仮想マシン スケール セット, Shared Image Gallery
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155826"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>チュートリアル:Ansible を使用して Azure Shared Image Gallery から VM または仮想マシン スケール セットを作成する

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Shared Image Gallery](/azure/virtual-machines/windows/shared-image-galleries) は、カスタムのマネージド イメージを簡単に管理、共有、整理できるようにするサービスです。 この機能は、多くのイメージが保持、共有されているシナリオに役立ちます。 カスタム イメージは、サブスクリプション間および Azure Active Directory のテナント間で共有できます。 また、デプロイのスケーリングにかかる時間を短縮するために、イメージを複数のリージョンにレプリケートすることもできます。

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * 一般化された VM とカスタム イメージを作成する
> * Shared Image Gallery を作成する
> * 共有イメージとイメージ バージョンを作成する
> * 一般化されたイメージを使用して VM を作成する
> * 一般化されたイメージを使用して仮想マシン スケール セットを作成する
> * Shared Image Gallery、イメージ、およびバージョンに関する情報を取得する

## <a name="prerequisites"></a>前提条件

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>サンプル プレイブックを取得する

サンプル プレイブック一式を取得するには、次の 2 つの方法があります。

- [SIG フォルダーをダウンロード](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image)してローカル コンピューターに保存します。
- セクションごとに新しいファイルを作成し、その中にサンプル プレイブックをコピーします。

`vars.yml` ファイルには、このチュートリアル用のすべてのサンプル プレイブックで使用される変数が含まれています。 このファイルを編集して、一意の名前と値を指定できます。

最初のサンプル プレイブック `00-prerequisites.yml` では、このチュートリアルを完了するために必要なものを作成します。
- リソース グループ。これは、Azure リソースがデプロイ、管理される論理コンテナーです。
- 仮想ネットワーク、サブネット、パブリック IP アドレス、VM 用ネットワーク インターフェイス カード。
- ソース仮想マシン。これは、一般化されたイメージの作成に使用されます。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 00-prerequisites.yml
```

[Azure portal](https://portal.azure.com) で、`vars.yml` で指定したリソース グループをチェックして、作成した新しい仮想マシンとさまざまなリソースを確認します。

## <a name="generalize-the-vm-and-create-a-custom-image"></a>VM を一般化してカスタム イメージを作成する

次のプレイブック `01a-create-generalized-image.yml` では、前の手順で作成したソース VM を一般化し、それに基づくカスタム イメージを作成します。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 01a-create-generalized-image.yml
```

リソース グループを確認し、`testimagea` が表示されていることを確認します。

## <a name="create-the-shared-image-gallery"></a>Shared Image Gallery を作成する

このイメージ ギャラリーは、イメージを共有および管理するためのリポジトリです。 `02-create-shared-image-gallery.yml` のサンプル プレイブック コードにより、リソース グループ内に Shared Image Gallery が作成されます。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

これで、新しいギャラリー `myGallery` がリソース グループ内に表示されます。

## <a name="create-a-shared-image-and-image-version"></a>共有イメージとイメージ バージョンを作成する

次のプレイブック `03a-create-shared-image-generalized.yml` では、イメージ定義とイメージ バージョンを作成します。

イメージ定義には、イメージの種類 (Windows または Linux)、リリース ノート、最小および最大のメモリ要件が含まれます。 イメージ バージョンとは、イメージのバージョンです。 ギャラリー、イメージ定義、イメージ バージョンは、イメージを論理グループに整理するのに役立ちます。 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

これで、リソース グループには、お使いのギャラリーのイメージ定義とイメージ バージョンが作成されました。

## <a name="create-a-vm-based-on-the-generalized-image"></a>一般化されたイメージに基づく VM を作成する

最後に、`04a-create-vm-using-generalized-image.yml` を実行して、前の手順で作成した一般化されたイメージに基づく VM を作成します。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>一般化されたイメージに基づく仮想マシン スケール セットを作成する

一般化されたイメージに基づく仮想マシン スケール セットを作成することもできます。 そのためには、`05a-create-vmss-using-generalized-image.yml` を実行します。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>ギャラリーに関する情報を取得する

`06-get-info.yml` を実行することで、ギャラリー、イメージ定義、バージョンに関する情報を取得できます。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>共有イメージを削除する

ギャラリー リソースを削除するには、サンプル プレイブック `07-delete-gallery.yml` を参照してください。 リソースを逆の順序で削除します。 まず、イメージ バージョンを削除します。 すべてのイメージのバージョンを削除した後、イメージの定義を削除できます。 すべてのイメージの定義を削除した後、ギャラリーを削除できます。

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

この記事で作成したリソースが不要になったら、削除してください。 

このセクションのサンプル プレイブック コードは、次の目的で使用します。

- 前に作成した 2 つのリソース グループを削除する

次のプレイブックを `cleanup.yml` という名前で保存します。

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

以下に、サンプル プレイブックを使用する際に考慮すべき重要な点をいくつか示します。

- `{{ resource_group_name }}` プレースホルダーは実際のリソース グループの名前に置き換えます。
- 指定した 2 つのリソース グループ内のすべてのリソースが削除されます。

`ansible-playbook` コマンドを使用してプレイブックを実行します。

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"] 
> [Azure 上の Ansible](/azure/ansible/)