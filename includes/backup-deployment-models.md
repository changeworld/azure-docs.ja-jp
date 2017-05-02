Azure Backup サービスには、バックアップ コンテナーと Recovery Services コンテナーの 2 種類のコンテナーがあります。 最初にあったのは、バックアップ コンテナーです。 次に、拡張 Resource Manager デプロイメントをサポートするために、Recovery Services コンテナーが加わりました。 特にクラシック デプロイメントが必要でない限り、Resource Manager デプロイメントを使用することをお勧めします。

| **デプロイ** | **ポータル** | **コンテナー** |
| --- | --- | --- |
| クラシック |[クラシック](https://manage.windowsazure.com) |バックアップ |
| Resource Manager |[Azure](https://portal.azure.com) |復旧サービス |

> [!NOTE]
> Resource Manager モデルでデプロイされたソリューションをバックアップ コンテナーで保護することはできません。 ただし、Recovery Services コンテナーを使用して、クラシック デプロイによるサーバーと VM を保護することはできます。  
> 
> 

