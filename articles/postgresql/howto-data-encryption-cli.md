---
title: データの暗号化 - Azure CLI - Azure Database for PostgreSQL - 単一サーバー
description: Azure CLI を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: c778130a95be1aa0afe81241130b7daf165360c4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522571"
---
# <a name="data-encryption-for-azure-database-for-postgresql-single-server-by-using-the-azure-cli"></a>Azure CLI を使用した Azure Database for PostgreSQL 単一サーバーのデータ暗号化

Azure CLI を使用して Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定および管理する方法について説明します。

## <a name="prerequisites-for-azure-cli"></a>Azure CLI の前提条件

* Azure サブスクリプションがあり、そのサブスクリプションの管理者である必要があります。
* カスタマー マネージド キーに使用するキー コンテナーとキーを作成します。 また、キー コンテナーの消去防止と論理的な削除も有効にします。

    ```azurecli-interactive
    az keyvault create -g <resource_group> -n <vault_name> --enable-soft-delete true --enable-purge-protection true
    ```

* 作成された Azure Key Vault で、Azure Database for PostgreSQL 単一サーバーのデータ暗号化に使用するキーを作成します。

    ```azurecli-interactive
    az keyvault key create --name <key_name> -p software --vault-name <vault_name>
    ```

* 既存のキー コンテナーを使用するには、カスタママー マネージド キーとして使用すための次のプロパティが必要です。
  * [論理的な削除](../key-vault/key-vault-ovw-soft-delete.md)

    ```azurecli-interactive
    az resource update --id $(az keyvault show --name \ <key_vault_name> -o tsv | awk '{print $1}') --set \ properties.enableSoftDelete=true
    ```

  * [消去保護](../key-vault/key-vault-ovw-soft-delete.md#purge-protection)

    ```azurecli-interactive
    az keyvault update --name <key_vault_name> --resource-group <resource_group_name>  --enable-purge-protection true
    ```

* カスタマー マネージド キーとして使用するには、キーに次の属性が必要です。
  * 有効期限がない
  * 無効化されていない
  * **get**、**wrap** および **unwrap** の操作を実行する

## <a name="set-the-right-permissions-for-key-operations"></a>キー操作に対する適切なアクセス許可を設定する

1. Azure Database for PostgreSQL 単一サーバーのマネージド ID を取得する方法は 2 つあります。

    ### <a name="create-an-new-azure-database-for-mysql-server-with-a-managed-identity"></a>マネージド ID を持つ Azure Database for MySQL サーバーを作成する。

    ```azurecli-interactive
    az postgres server create --name -g <resource_group> --location <locations> --storage-size <size>  -u <user>-p <pwd> --backup-retention <7> --sku-name <sku name> --geo-redundant-backup <Enabled/Disabled>  --assign-identity
    ```

    ### <a name="update-an-existing-the-azure-database-for-mysql-server-to-get-a-managed-identity"></a>既存の Azure Database for MySQL サーバーを更新して、マネージド ID を取得する。

    ```azurecli-interactive
    az postgres server update –name <server name>  -g <resoure_group> --assign-identity
    ```

2. PostgreSQL 単一サーバーの名前である**プリンシパル**に対して、**キーのアクセス許可** (**get**、**wrap**、**unwrap**) を設定します。

    ```azurecli-interactive
    az keyvault set-policy --name -g <resource_group> --key-permissions get unwrapKey wrapKey --object-id <principal id of the server>
    ```

## <a name="set-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーのデータ暗号化を設定する

1. Azure Key Vault に作成されたキーを使用して、Azure Database for PostgreSQL 単一サーバーのデータ暗号化を有効にします。

    ```azurecli-interactive
    az postgres server key create –name  <server name>  -g <resource_group> --kid <key url>
    ```

    キーの URL: https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

## <a name="using-data-encryption-for-restore-or-replica-servers"></a>復元またはレプリカ サーバーでのデータ暗号化の使用

Key Vault に格納されている顧客のマネージド キーで Azure Database for PostgreSQL 単一サーバーが暗号化されると、そのサーバーの新しく作成されたコピーも暗号化されます。 この新しいコピーは、ローカルまたは geo 復元操作を使用するか、レプリカ (ローカル/リージョン間) 操作を使用して作成できます。 そのため、暗号化された PostgreSQL 単一サーバーの場合は、次の手順を使用して、暗号化済みの復元されたサーバーを作成できます。

### <a name="creating-a-restoredreplica-server"></a>復元/レプリカ サーバーの作成

  *  [復元サーバーの作成](howto-restore-server-cli.md) 
  *  [読み取りレプリカ サーバーの作成](howto-read-replicas-cli.md) 

### <a name="once-the-server-is-restored-revalidate-data-encryption-the-restored-server"></a>サーバーが復元された後、復元されたサーバーのデータ暗号化を再検証する

    ```azurecli-interactive
    az postgres server key create –name  <server name> -g <resource_group> --kid <key url>
    ```

## <a name="additional-capability-for-the-key-being-used-for-the-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 単一サーバーに使用されているキーの追加機能

### <a name="get-the-key-used"></a>使用されているキーを取得する

    ```azurecli-interactive
    az mysql server key show --name  <server name>  -g <resource_group> --kid <key url>
    ```

    Key url:  https://YourVaultName.vault.azure.net/keys/YourKeyName/01234567890123456789012345678901>

### <a name="list-the-key-used"></a>使用されているキーを一覧表示する

    ```azurecli-interactive
    az postgres server key list --name  <server name>  -g <resource_group>
    ```

### <a name="drop-the-key-being-used"></a>使用されているキーを削除する

    ```azurecli-interactive
    az postgres server key delete -g <resource_group> --kid <key url> 
    ```
## <a name="using-an-azure-resource-manager-template-to-enable-data-encryption"></a>Azure Resource Manager テンプレートを使用したデータ暗号化の有効化

Azure portal とは別に、新規および既存のサーバー用の Azure Resource Manager テンプレートを使用して、Azure Database for PostgreSQL の単一サーバー上でデータ暗号化を有効にすることもできます。

### <a name="for-a-new-server"></a>新しいサーバーの場合

事前に作成したいずれかの Azure Resource Manager テンプレートを使用して、データ暗号化を有効にしてサーバーをプロビジョニングします。[データの暗号化を使用した例](https://github.com/Azure/azure-postgresql/tree/master/arm-templates/ExampleWithDataEncryption)

この Azure Resource Manager テンプレートでは、Azure Database for PostgreSQL の単一サーバーを作成し、パラメーターとして渡された **KeyVault** および **Key** を使用して、サーバーのデータ暗号化を有効にします。

### <a name="for-an-existing-server"></a>既存のサーバーの場合
また、Azure Resource Manager テンプレートを使用して、既存の Azure Database for PostgreSQL 単一サーバー上でデータ暗号化を有効にすることもできます。

* プロパティ オブジェクトの `Uri` プロパティで以前コピーした Azure Key Vault キーのリソース ID を渡します。

* *2020-01-01-preview* を API バージョンとして使用します。

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string"
    },
    "serverName": {
      "type": "string"
    },
    "keyVaultName": {
      "type": "string",
      "metadata": {
        "description": "Key vault name where the key to use is stored"
      }
    },
    "keyVaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "Key vault resource group name where it is stored"
      }
    },
    "keyName": {
      "type": "string",
      "metadata": {
        "description": "Key name in the key vault to use as encryption protector"
      }
    },
    "keyVersion": {
      "type": "string",
      "metadata": {
        "description": "Version of the key in the key vault to use as encryption protector"
      }
    }
  },
  "variables": {
    "serverKeyName": "[concat(parameters('keyVaultName'), '_', parameters('keyName'), '_', parameters('keyVersion'))]"
  },
  "resources": [
    {
      "type": "Microsoft.DBforPostgreSQL/servers",
      "apiVersion": "2017-12-01",
      "kind": "",
      "location": "[parameters('location')]",
      "identity": {
        "type": "SystemAssigned"
      },
      "name": "[parameters('serverName')]",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2019-05-01",
      "name": "addAccessPolicy",
      "resourceGroup": "[parameters('keyVaultResourceGroupName')]",
      "dependsOn": [
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "type": "Microsoft.KeyVault/vaults/accessPolicies",
              "name": "[concat(parameters('keyVaultName'), '/add')]",
              "apiVersion": "2018-02-14-preview",
              "properties": {
                "accessPolicies": [
                  {
                    "tenantId": "[subscription().tenantId]",
                    "objectId": "[reference(resourceId('Microsoft.DBforPostgreSQL/servers/', parameters('serverName')), '2017-12-01', 'Full').identity.principalId]",
                    "permissions": {
                      "keys": [
                        "get",
                        "wrapKey",
                        "unwrapKey"
                      ]
                    }
                  }
                ]
              }
            }
          ]
        }
      }
    },
    {
      "name": "[concat(parameters('serverName'), '/', variables('serverKeyName'))]",
      "type": "Microsoft.DBforPostgreSQL/servers/keys",
      "apiVersion": "2020-01-01-preview",
      "dependsOn": [
        "addAccessPolicy",
        "[resourceId('Microsoft.DBforPostgreSQL/servers', parameters('serverName'))]"
      ],
      "properties": {
        "serverKeyType": "AzureKeyVault",
        "uri": "[concat(reference(resourceId(parameters('keyVaultResourceGroupName'), 'Microsoft.KeyVault/vaults/', parameters('keyVaultName')), '2018-02-14-preview', 'Full').properties.vaultUri, 'keys/', parameters('keyName'), '/', parameters('keyVersion'))]"
      }
    }
  ]
}
```

## <a name="next-steps"></a>次のステップ

 データ暗号化の詳細については、「[カスタマー マネージド キーを使用した Azure Database for PostgreSQL 単一サーバーのデータの暗号化](concepts-data-encryption-postgresql.md)」を参照してください。
