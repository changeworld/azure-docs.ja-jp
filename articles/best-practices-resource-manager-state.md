<properties
	pageTitle="Resource Manager テンプレートでの状態の処理 | Microsoft Azure"
	description="複合オブジェクトを使用して Azure リソース マネージャー テンプレートやリンクされたテンプレートと状態データを共有する推奨方法を示します。"
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="tomfitz"/>

# Azure リソース マネージャーのテンプレートでの状態の共有

このトピックでは、テンプレート内で状態を管理して共有するためのベスト プラクティスを説明します。このトピックで使用するパラメーターと変数は、デプロイ要件を適切に整理するために定義できる種類のオブジェクトの例を示しています。これらの例から、使用環境で意味のあるプロパティ値を使用する独自のオブジェクトを実装できます。

このトピックは大型のホワイト ペーパーの一部です。ペーパーの完全版を参照するには、[World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf) をダウンロードしてください。


## 標準の構成設定を指定する

高い柔軟性と多様性に対応したテンプレートというのは、決して一般的ではありません。一般的なパターンはむしろ既製の構成を選べるようにすることであり、事実、T シャツのような標準的なサイズ区分 (サンドボックス、S、M、L など) が多く使われています。コミュニティ エディション、エンタープライズ エディションなど製品の提供形態も、T シャツのサイズと同様の区分といえます。また、MapReduce、NoSQL など、テクノロジのワークロードに基づく構成で大中小を決める場合もあります。

複合オブジェクトを使用すると、データのコレクションを格納する変数 ("プロパティ バッグ" と呼ばれます) を作成し、そのデータを使用して、テンプレートでのリソースの宣言をしやすくすることができます。このアプローチの特徴は、さまざまなサイズの構成を顧客向けにあらかじめ用意しておき、良質な既製の構成を提供できることです。既製の構成が用意されていなければ、エンド カスタマー自身がクラスターの規模を決め、プラットフォーム リソースの制約を加味しながら、ストレージ アカウントや各種リソースの分割サイズを計算しなければなりません (クラスター サイズとリソースの制約のため)。顧客の利便性を高めることとは別に、既製の構成の数は少ない方がサポートしやすく、密集度も高めやすくなります。

次の例は、データのコレクションを表す複合オブジェクトを含む変数を定義する方法を示します。コレクションは、仮想マシンのサイズ、ネットワークの設定、オペレーティング システムの設定、および可用性の設定に使用される値を定義します。

    "variables": {
      "tshirtSize": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      },
      "tshirtSizeMedium": {
        "vmSize": "Standard_A3",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-8disk-resources.json')]",
        "vmCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1],
          "jumpbox": 0
        }
      },
      "tshirtSizeLarge": {
        "vmSize": "Standard_A4",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-16disk-resources.json')]",
        "vmCount": 3,
        "slaveCount": 2,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 2,
          "pool": "db",
          "map": [0,1,1],
          "jumpbox": 0
        }
      },
      "osSettings": {
        "scripts": [
          "[concat(variables('templateBaseUrl'), 'install_postgresql.sh')]",
          "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/ubuntu/vm-disk-utils-0.1.sh"
        ],
        "imageReference": {
          "publisher": "Canonical",
          "offer": "UbuntuServer",
          "sku": "14.04.2-LTS",
          "version": "latest"
        }
      },
      "networkSettings": {
        "vnetName": "[parameters('virtualNetworkName')]",
        "addressPrefix": "10.0.0.0/16",
        "subnets": {
          "dmz": {
            "name": "dmz",
            "prefix": "10.0.0.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          },
          "data": {
            "name": "data",
            "prefix": "10.0.1.0/24",
            "vnet": "[parameters('virtualNetworkName')]"
          }
        }
      },
      "availabilitySetSettings": {
        "name": "pgsqlAvailabilitySet",
        "fdCount": 3,
        "udCount": 5
      }
    }

**tshirtSize** 変数は、パラメーター (**Small**、**Medium**、**Large**) で指定した T シャツのサイズを **tshirtSize** のテキストに連結することに注意してください。この変数を使用して、その T シャツ サイズの関連する複合オブジェクト変数を取得します。

テンプレートで、後でこれらの変数を参照できます。名前付きの変数とそのプロパティを参照できると、テンプレートの構文を簡略化し、コンテキストがわかりやすくなります。次の例では、上記のオブジェクトを使用して値を設定することで、デプロイするリソースを定義します。たとえば、VM のサイズを設定するために `variables('tshirtSize').vmSize` の値を取得する一方、ディスクのサイズの値を `variables('tshirtSize').diskSize` から取得しています。さらに、リンクされたテンプレートの URI を `variables('tshirtSize').vmTemplate` の値と共に設定しています。

    "name": "master-node",
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared')]"
    ],
    "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('tshirtSize').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "value": "[parameters('adminPassword')]"
          },
          "replicatorPassword": {
            "value": "[parameters('replicatorPassword')]"
          },
          "osSettings": {
            "value": "[variables('osSettings')]"
          },
          "subnet": {
            "value": "[variables('networkSettings').subnets.data]"
          },
          "commonSettings": {
            "value": {
              "region": "[parameters('region')]",
              "adminUsername": "[parameters('adminUsername')]",
              "namespace": "ms"
            }
          },
          "storageSettings": {
            "value":"[variables('tshirtSize').storage]"
          },
          "machineSettings": {
            "value": {
              "vmSize": "[variables('tshirtSize').vmSize]",
              "diskSize": "[variables('tshirtSize').diskSize]",
              "vmCount": 1,
              "availabilitySet": "[variables('availabilitySetSettings').name]"
            }
          },
          "masterIpAddress": {
            "value": "0"
          },
          "dbType": {
            "value": "MASTER"
          }
        }
      }
    }

## 状態をテンプレートに渡す

デプロイ中に直接指定するパラメーターを使用して、状態をテンプレートに共有します。

次の表では、テンプレートで一般的に使用されるパラメーターを一覧にしています。

名前 | 値 | 説明
---- | ----- | -----------
location | Azure リージョンの制約付き一覧の文字列 | リソースがデプロイされる場所です。
storageAccountNamePrefix | String | VM のディスクが配置されるストレージ アカウントの一意の DNS 名
domainName | String | パブリックにアクセスできる jumpbox VM のドメイン名。形式は次のとおりです。**{domainName}.{location}.cloudapp.com**。例: **mydomainname.westus.cloudapp.azure.com**
adminUsername | String | VM のユーザー名
adminPassword | String | VM のパスワード
tshirtSize | 提供される T シャツ サイズの制約付き一覧の文字列 | プロビジョニングする名前付きのスケール ユニットのサイズ。たとえば、"Small"、"Medium"、"Large"
virtualNetworkName | String | コンシューマーが使用する仮想ネットワークの名前。
enableJumpbox | 制約付き一覧の文字列 (enabled/disabled) | 環境の jumpbox を有効にするかどうかを識別するパラメーター。値: "enabled"、"disabled"

前のセクションで使用した **tshirtSize** パラメータは、次のように定義されます。

    "parameters": {
      "tshirtSize": {
        "type": "string",
        "defaultValue": "Small",
        "allowedValues": [
          "Small",
          "Medium",
          "Large"
        ],
        "metadata": {
          "Description": "T-shirt size of the MongoDB deployment"
        }
      }
    }


## リンクされたテンプレートに状態を渡す

リンクされたテンプレートへの接続時には、多くの場合、静的変数と生成された変数を組み合わせて使用します。

### 静的変数

静的変数は、URL など、基本の値を指定するためによく使用されます。このような基本の値は、テンプレート全体で使用されます。

次のテンプレートの抜粋では、`templateBaseUrl` により、GitHub におけるテンプレートのルートの場所を指定します。その次の行では、基本 URL と共有リソース テンプレートの既知の名前を連結する新しい変数 `sharedTemplateUrl` を作成します。その下では、複合オブジェクト変数を使用して、T シャツ サイズを格納します。ここで、基本 URL が `vmTemplate` プロパティに格納されている既知の構成テンプレートの場所に連結されます。

この方法の利点は、テンプレートの場所が変わった場合でも、1 か所で静的変数を変更するだけで済むという点です。その場所から、リンクされたテンプレート全体に静的変数が渡されます。

    "variables": {
      "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
      "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
      "tshirtSizeSmall": {
        "vmSize": "Standard_A1",
        "diskSize": 1023,
        "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
        "vmCount": 2,
        "slaveCount": 1,
        "storage": {
          "name": "[parameters('storageAccountNamePrefix')]",
          "count": 1,
          "pool": "db",
          "map": [0,0],
          "jumpbox": 0
        }
      }
    }

### 生成された変数

静的変数に加えて、いくつかの変数が動的に生成されます。このセクションでは、生成された変数の一般的な種類を明確にします。

#### tshirtSize

上記の例で、生成されたこの変数について説明しています。

#### networkSettings

容量、機能、またはエンド ツー エンドのスコープを持つソリューション テンプレートでは、リンクされたテンプレートは通常、ネットワーク上に存在するリソースを作成します。わかりやすい方法の 1 つとして、複合オブジェクトを使用して、ネットワークの設定を格納し、リンクされたテンプレートに渡すことが挙げられます。

以下にネットワーク設定をやり取りする例を示します。

    "networkSettings": {
      "vnetName": "[parameters('virtualNetworkName')]",
      "addressPrefix": "10.0.0.0/16",
      "subnets": {
        "dmz": {
          "name": "dmz",
          "prefix": "10.0.0.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        },
        "data": {
          "name": "data",
          "prefix": "10.0.1.0/24",
          "vnet": "[parameters('virtualNetworkName')]"
        }
      }
    }

#### availabilitySettings

リンクされたテンプレートで作成されたリソースは、多くの場合、可用性セットに配置されます。次の例では、可用性セット名と共に、使用する障害ドメインと更新ドメインの数も指定しています。

    "availabilitySetSettings": {
      "name": "pgsqlAvailabilitySet",
      "fdCount": 3,
      "udCount": 5
    }

複数の可用性セット (たとえば、あるセットをマスター ノード用に、別のセットをデータ ノード用に使用) が必要な場合、名前をプレフィックスとして使用して、複数の可用性セットを指定するか、前に示した、特定の T シャツ サイズの変数を作成するモデルに従います。

#### storageSettings

多くの場合、ストレージの詳細は、リンクされたテンプレートと共有されます。次の例では、*storageSettings* オブジェクトは、ストレージ アカウント名とコンテナー名の詳細を提供します。

    "storageSettings": {
        "vhdStorageAccountName": "[parameters('storageAccountName')]",
        "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
        "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]"
    }

#### osSettings

リンクされたテンプレートを使用すると、場合によっては、異なる既知の構成の種類における、さまざまなノードの種類に対して、オペレーティング システムの設定を渡す必要があります。複合オブジェクトを使用すると、オペレーティング システムの情報を簡単に格納し、共有できます。また、複数のオペレーティング システムを選択してデプロイすることも容易になります。

次の例では、*osSettings* のオブジェクトを示しています。

    "osSettings": {
      "imageReference": {
        "publisher": "Canonical",
        "offer": "UbuntuServer",
        "sku": "14.04.2-LTS",
        "version": "latest"
      }
    }

#### machineSettings

生成された変数である *machineSettings* は、新しい VM を作成するための中核となる変数を組み合わせた複合オブジェクトです。以下のように、管理者のユーザー名とパスワード、VM 名のプレフィックス、オペレーティング システム イメージの参照が含まれています。

    "machineSettings": {
        "adminUsername": "[parameters('adminUsername')]",
        "adminPassword": "[parameters('adminPassword')]",
        "machineNamePrefix": "mongodb-",
        "osImageReference": {
            "publisher": "[variables('osFamilySpec').imagePublisher]",
            "offer": "[variables('osFamilySpec').imageOffer]",
            "sku": "[variables('osFamilySpec').imageSKU]",
            "version": "latest"
        }
    },

*osImageReference* が、メイン テンプレートで定義された *osSettings* 変数から値を取得することに注意してください。つまり、VM のオペレーティング システムを簡単に変更できます。全体的に、またはテンプレートのコンシューマーの設定に基づいて変更できます。

#### vmScripts

*vmScripts* オブジェクトには、VM インスタンスでダウンロードして実行するスクリプトの詳細が含まれています。これには、外部の参照や内部の参照などが含まれています。外部の参照には、インフラストラクチャが含まれます。内部の参照には、インストールされているソフトウェアと構成が含まれます。

*scriptsToDownload* プロパティを使用して、VM にダウンロードするスクリプトを一覧表示します。

次の例に示すように、このオブジェクトには、さまざまな種類のアクションで使用するコマンドライン引数への参照も含まれます。これらのアクションには、個別のノードに対する既定のインストールの実行、すべてのノードのデプロイ後に実行されるインストール、指定のテンプレートに固有のその他のスクリプトなどがあります。

この例は、MongoDB をデプロイするのに使用するテンプレートの一部です。この MongoDB には、高可用性を実現するためにアービターが必要です。アービターをインストールするために、*arbiterNodeInstallCommand* が *vmScripts* に追加されています。

変数のセクションには特定のテキストを定義する変数があり、適切な値を指定してスクリプトを実行します。

    "vmScripts": {
        "scriptsToDownload": [
            "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
            "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
        ],
        "regularNodeInstallCommand": "[variables('installCommand')]",
        "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
        "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
    },


## テンプレートから状態を返す

テンプレートにデータを渡すだけでなく、呼び出し元のテンプレートにデータを返して共有することもできます。リンクされているテンプレートの **outputs** セクションで、ソース テンプレートで使用可能なキー/値ペアを指定できます。

次の例は、リンクされているテンプレートで生成されたプライベート IP アドレスを渡す方法を示します。

    "outputs": {
        "masterip": {
            "value": "[reference(concat(variables('nicName'),0)).ipConfigurations[0].properties.privateIPAddress]",
            "type": "string"
         }
    }

メイン テンプレート内では、次の構文でこのデータを使用できます。

    "[reference('master-node').outputs.masterip.value]"

この式は、出力のセクションまたはメイン テンプレートの resources セクションで使用することができます。この式は実行時の状態に依存するため、変数のセクションでは使用できません。メイン テンプレートからこの値を返すには、次のように使用します。

    "outputs": { 
      "masterIpAddress": {
        "value": "[reference('master-node').outputs.masterip.value]",
        "type": "string"
      }
     
リンクされたテンプレートの出力のセクションを使用して仮想マシンのデータ ディスクを取得する例については、「[仮想マシン用の複数のデータ ディスクの作成](resource-group-create-multiple.md#creating-multiple-data-disks-for-a-virtual-machine)」を参照してください。

## 仮想マシンの認証設定を定義する

上記の構成設定と同様のパターンで、仮想マシンの認証設定を指定することができます。認証の種類を渡すためのパラメーターを作成します。

    "parameters": {
      "authenticationType": {
        "allowedValues": [
          "password",
          "sshPublicKey"
        ],
        "defaultValue": "password",
        "metadata": {
          "description": "Authentication type"
        },
        "type": "string"
      }
    }

さまざまな認証の種類の変数と、パラメーターの値に基づいてこのデプロイメントで使用する種類を格納するための変数を追加します。

    "variables": {
      "osProfile": "[variables(concat('osProfile', parameters('authenticationType')))]",
      "osProfilepassword": {
        "adminPassword": "[parameters('adminPassword')]",
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]"
      },
      "osProfilesshPublicKey": {
        "adminUsername": "notused",
        "computerName": "[parameters('vmName')]",
        "customData": "[base64(variables('customData'))]",
        "linuxConfiguration": {
          "disablePasswordAuthentication": "true",
          "ssh": {
            "publicKeys": [
              {
                "keyData": "[parameters('sshPublicKey')]",
                "path": "/home/notused/.ssh/authorized_keys"
              }
            ]
          }
        }
      }
    }

仮想マシンを定義する場合、作成した変数に **osProfile** を設定します。

    {
      "type": "Microsoft.Compute/virtualMachines",
      ...
      "osProfile": "[variables('osProfile')]"
    }


## 次のステップ
- テンプレートのセクションについては、「[Azure Resource Manager のテンプレートの作成](resource-group-authoring-templates.md)」を参照してください。
- テンプレートで使用できる関数については、「[Azure Resource Manager のテンプレートの関数](resource-group-template-functions.md)」を参照してください。

<!---HONumber=AcomDC_0518_2016-->