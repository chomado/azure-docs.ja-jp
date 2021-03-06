---
title: 複数のリソース インスタンスの作成
description: Azure Resource Manager テンプレートを作成して、複数の Azure リソース インスタンスを作成する方法を説明します。
author: mumian
ms.date: 04/08/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 83afff3aa15caa1743f66eea9eaee541492b8d1c
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260838"
---
# <a name="tutorial-create-multiple-resource-instances-with-arm-templates"></a>チュートリアル:ARM テンプレートを使用した複数のリソース インスタンスの作成

Azure Resource Manager (ARM) テンプレートでの反復処理を通じて、Azure リソースのインスタンスを複数作成する方法について説明します。 このチュートリアルでは、3 つのストレージ アカウント インスタンスが作成されるようテンプレートを変更します。

![Azure Resource Manager による複数のインスタンスの作成の図](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances-diagram.png)

このチュートリアルに含まれるタスクは次のとおりです。

> [!div class="checklist"]
> * クイック スタート テンプレートを開く
> * テンプレートの編集
> * テンプレートのデプロイ

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

この記事を完了するには、以下が必要です。

* Visual Studio Code と Resource Manager ツール拡張機能。 [Visual Studio Code を使って ARM テンプレートを作成する方法](use-vs-code-to-create-template.md)に関するページを参照してください。

## <a name="open-a-quickstart-template"></a>クイック スタート テンプレートを開く

[Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/)は、ARM テンプレートのリポジトリです。 テンプレートを最初から作成しなくても、サンプル テンプレートを探してカスタマイズすることができます。 このクイック スタートで使用されるテンプレートは、[Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) と呼ばれます。 テンプレートにより、Azure ストレージ アカウント リソースが定義されます。

1. Visual Studio Code から、 **[ファイル]** > **[ファイルを開く]** を選択します。
2. **[ファイル名]** に以下の URL を貼り付けます。

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. **[開く]** を選択して、ファイルを開きます。
4. テンプレートで "Microsoft.Storage/storageAccounts" リソースが定義されています。 テンプレートを[テンプレート リファレンス](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)と比較します。 カスタマイズする前にテンプレートの基本をある程度理解することは役に立ちます。
5. **[ファイル]** > **[名前を付けて保存]** を選択し、ファイルを **azuredeploy.json** としてご自身のローカル コンピューターに保存します。

## <a name="edit-the-template"></a>テンプレートの編集

既存のテンプレートでは、1 つのストレージ アカウントが作成されます。 3 つのストレージ アカウントが作成されるよう、テンプレートをカスタマイズします。

Visual Studio Code で、次の 4 つの変更を行います。

![Azure Resource Manager で複数のインスタンスを作成する](./media/template-tutorial-create-multiple-instances/resource-manager-template-create-multiple-instances.png)

1. ストレージ アカウントのリソース定義に `copy` 要素を追加します。 copy 要素には、そのループの反復回数と変数を指定します。 数値は正の整数で、800 を超えることはできません。
2. `copyIndex()` 関数は、ループ内の現在の繰り返しを返します。 インデックスを名前のプレフィックスとして使用します。 `copyIndex()` は 0 から始まります。 インデックス値をオフセットするには、copyIndex() 関数に値を渡します。 たとえば、 *copyindex (1)* のように指定します。
3. **variables** 要素を削除します。これ以上使われないためです。
4. **outputs** 要素を削除します。 これは不要になりました。

完成したテンプレートは、次のようになります。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    }
  ]
}
```

複数のインスタンスの作成の詳細については、[ARM テンプレートでリソースまたはプロパティの複数のインスタンスをデプロイする方法](./copy-resources.md)に関するページを参照してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

デプロイ手順については、Visual Studio Code のクイック スタートの「[テンプレートのデプロイ](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template)」セクションを参照してください。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

3 つのストレージ アカウントをすべて一覧表示するには、--name パラメータを省略します。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
echo "Enter a project name that is used to generate resource group name:" &&
read projectName &&
resourceGroupName="${projectName}rg" &&
az storage account list --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
$resourceGroupName = "${projectName}rg"
Get-AzStorageAccount -ResourceGroupName $resourceGroupName
Write-Host "Press [ENTER] to continue ..."
```

---

テンプレートの名前定義とストレージ アカウント名を比較します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。

1. Azure portal で、左側のメニューから **[リソース グループ]** を選択します。
2. **[名前でフィルター]** フィールドに、リソース グループ名を入力します。
3. リソース グループ名を選択します。  リソース グループ内の合計 3 つのリソースが表示されます。
4. トップ メニューから **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、複数のストレージ アカウント インスタンスを作成する方法について説明しました。  次のチュートリアルでは、複数のリソースと複数のリソース タイプを含むテンプレートを作成します。 一部のリソースには依存リソースがあります。

> [!div class="nextstepaction"]
> [依存リソースの作成](./template-tutorial-create-templates-with-dependent-resources.md)
