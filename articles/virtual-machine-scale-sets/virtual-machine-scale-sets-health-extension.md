---
title: アプリケーションの正常性拡張機能と Azure 仮想マシン スケール セットを使用する
description: アプリケーションの正常性拡張機能を使用して、仮想マシン スケール セットにデプロイされたご自身のアプリケーションの正常性を監視する方法について説明します。
author: mimckitt
tags: azure-resource-manager
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: mimckitt
ms.openlocfilehash: cb5f1d48bb1a95db004d9da553e19a35071c73b0
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273734"
---
# <a name="using-application-health-extension-with-virtual-machine-scale-sets"></a>アプリケーションの正常性拡張機能と仮想マシン スケール セットの使用
お使いのアプリケーションの正常性の監視は、ご自身のデプロイを管理およびアップグレードするための重要なシグナルです。 Azure 仮想マシン スケール セットでは、[OS イメージの自動アップグレード](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)などの[ローリング アップグレード](virtual-machine-scale-sets-automatic-upgrade.md)がサポートされ、個々のインスタンスの正常性を監視することで、ご自身のデプロイをアップグレードします。

この記事では、アプリケーションの正常性拡張機能を使用して、仮想マシン スケール セットにデプロイされたご自身のアプリケーションの正常性を監視する方法について説明します。

## <a name="prerequisites"></a>前提条件
この記事では、次の内容を熟知していることを前提としています。
-   Azure 仮想マシン[拡張機能](../virtual-machines/extensions/overview.md)
-   仮想マシン スケール セットの[変更](virtual-machine-scale-sets-upgrade-scale-set.md)

## <a name="when-to-use-the-application-health-extension"></a>アプリケーションの正常性拡張機能を使用するタイミング
アプリケーションの正常性拡張機能は、仮想マシン スケール セットのインスタンス内にデプロイされ、スケール セット インスタンス内から VM の正常性についてレポートします。 拡張機能は、アプリケーション エンドポイントでプローブを実行し、そのインスタンスでアプリケーションの状態を更新するように構成することができます。 このインスタンスの状態は Azure によってチェックされ、インスタンスがアップグレード操作の対象となるかどうかが判断されます。

拡張機能は VM 内から正常性をレポートするため、(カスタム Azure Load Balancer [プローブ](../load-balancer/load-balancer-custom-probe-overview.md)を使用する) アプリケーションの正常性プローブなどの外部プローブが使用できない状況で使用できます。

## <a name="extension-schema"></a>拡張機能のスキーマ

次の JSON は、アプリケーションの正常性拡張機能のスキーマを示しています。 拡張機能には、少なくとも "tcp" または "http" 要求が必要で、それぞれにポートまたは要求パスが関連付けられている必要があります。

```json
{
  "type": "extensions",
  "name": "HealthExtension",
  "apiVersion": "2018-10-01",
  "location": "<location>",  
  "properties": {
    "publisher": "Microsoft.ManagedServices",
    "type": "< ApplicationHealthLinux or ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}  
```

### <a name="property-values"></a>プロパティ値

| Name | 値/例 | データ型
| ---- | ---- | ---- 
| apiVersion | `2018-10-01` | date |
| publisher | `Microsoft.ManagedServices` | string |
| type | `ApplicationHealthLinux` (Linux)、`ApplicationHealthWindows` (Windows) | string |
| typeHandlerVersion | `1.0` | INT |

### <a name="settings"></a>設定

| Name | 値/例 | データ型
| ---- | ---- | ----
| protocol | `http` または `tcp` | string |
| port | プロトコルが `http` の場合は省略可能、プロトコルが `tcp` の場合は必須 | INT |
| requestPath | プロトコルが `http` の場合は必須、プロトコルが `tcp` の場合は許可されていません | string |

## <a name="deploy-the-application-health-extension"></a>アプリケーションの正常性拡張機能をデプロイする
次の例で詳しく示すように、アプリケーションの正常性拡張機能をお使いのスケール セットにデプロイする方法は複数あります。

### <a name="rest-api"></a>REST API

次の例は、Windows ベースのスケール セットのスケール セット モデルで extensionProfile に (myHealthExtension という名前の) アプリケーションの正常性拡張機能を追加しています。

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/extensions/myHealthExtension?api-version=2018-10-01`
```

```json
{
  "name": "myHealthExtension",
  "properties": {
    "publisher": " Microsoft.ManagedServices",
    "type": "< ApplicationHealthWindows>",
    "autoUpgradeMinorVersion": true,
    "typeHandlerVersion": "1.0",
    "settings": {
      "protocol": "<protocol>",
      "port": "<port>",
      "requestPath": "</requestPath>"
    }
  }
}
```
`PATCH` を使用して、既にデプロイされている拡張機能を編集します。

### <a name="azure-powershell"></a>Azure PowerShell

[Add-AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) コマンドレットを使用して、アプリケーションの正常性拡張機能をスケール セット モデルの定義に追加します。

次の例は、Windows ベースのスケール セットのスケール セット モデルの `extensionProfile` にアプリケーションの正常性拡張機能を追加しています。 この例では、新しい Az PowerShell モジュールが使用されます。

```azurepowershell-interactive
# Define the scale set variables
$vmScaleSetName = "myVMScaleSet"
$vmScaleSetResourceGroup = "myVMScaleSetResourceGroup"

# Define the Application Health extension properties
$publicConfig = @{"protocol" = "http"; "port" = 80; "requestPath" = "/healthEndpoint"};
$extensionName = "myHealthExtension"
$extensionType = "ApplicationHealthWindows"
$publisher = "Microsoft.ManagedServices"

# Get the scale set object
$vmScaleSet = Get-AzVmss `
  -ResourceGroupName $vmScaleSetResourceGroup `
  -VMScaleSetName $vmScaleSetName

# Add the Application Health extension to the scale set model
Add-AzVmssExtension -VirtualMachineScaleSet $vmScaleSet `
  -Name $extensionName `
  -Publisher $publisher `
  -Setting $publicConfig `
  -Type $extensionType `
  -TypeHandlerVersion "1.0" `
  -AutoUpgradeMinorVersion $True

# Update the scale set
Update-AzVmss -ResourceGroupName $vmScaleSetResourceGroup `
  -Name $vmScaleSetName `
  -VirtualMachineScaleSet $vmScaleSet
```


### <a name="azure-cli-20"></a>Azure CLI 2.0

[az vmss 拡張機能セット](/cli/azure/vmss/extension#az-vmss-extension-set) を使用して、アプリケーションの正常性拡張機能をスケール セット モデルの定義に追加します。

次の例は、Linux ベースのスケール セットのスケール セット モデルにアプリケーションの正常性拡張機能を追加しています。

```azurecli-interactive
az vmss extension set \
  --name ApplicationHealthLinux \
  --publisher Microsoft.ManagedServices \
  --version 1.0 \
  --resource-group <myVMScaleSetResourceGroup> \
  --vmss-name <myVMScaleSet> \
  --settings ./extension.json
```
extension.json ファイルの内容です。

```json
{
  "protocol": "<protocol>",
  "port": "<port>",
  "requestPath": "</requestPath>"
}
```


## <a name="troubleshoot"></a>[トラブルシューティング]
拡張機能の実行の出力は、次のディレクトリ内のファイルにログ記録されます。

```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.ManagedServices.ApplicationHealthWindows\<version>\
```

```Linux
/var/lib/waagent/apphealth
```

ログには、アプリケーションの正常性状態も定期的にキャプチャされます。

## <a name="next-steps"></a>次のステップ
仮想マシン スケール セットに[ご自身のアプリケーションをデプロイする](virtual-machine-scale-sets-deploy-app.md)方法を学習します。
