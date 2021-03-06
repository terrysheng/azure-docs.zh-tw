---
title: "設定 Azure Service Fabric 叢集 | Microsoft Docs"
description: "本快速入門可協助您在 Azure 上建立 Windows 或 Linux Service Fabric 叢集。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2017
ms.author: ryanwi
ms.openlocfilehash: caf76bb739fa92982c511c8e3e6c6aaf2bf9d6c1
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/03/2017
---
# <a name="create-your-first-service-fabric-cluster-on-azure"></a>在 Azure 上建立您的第一個 Service Fabric 叢集
[Azure Service Fabric 叢集](service-fabric-deploy-anywhere.md)是一組由網路連線的虛擬或實體機器，可用來將您的微服務部署到其中並進行管理。 此快速入門可協助您建立包含五個節點的叢集，在短短幾分鐘內透過 [Azure PowerShell](https://msdn.microsoft.com/library/dn135248) 或 [Azure 入口網站](http://portal.azure.com)在 Windows 或 Linux 上執行該叢集。 在此工作中，您也可以使用 Azure CLI。  

如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) 。


## <a name="use-the-azure-portal"></a>使用 Azure 入口網站

登入 Azure 入口網站 (網址是 [http://portal.azure.com](http://portal.azure.com))。

### <a name="create-the-cluster"></a>建立叢集

1. 在 Azure 入口網站的左上角，選取 [新增]。
2. 搜尋 [Service Fabric]，並從傳回的結果中選取 [Service Fabric 叢集]。 然後選取 [建立]。
3. 填妥 Service Fabric 的 [基本資料] 表單。 針對 [作業系統]，選取您希望叢集節點執行的 Windows 或 Linux 版本。 在此輸入的使用者名稱和密碼用於登入虛擬機器。 對於 [資源群組]，建立新的資源群組。 資源群組是在其中建立和共同管理 Azure 資源的邏輯容器。 完成後，選取 [確定]。

    ![叢集設定輸出的螢幕擷取畫面][cluster-setup-basics]

4. 填妥 [叢集組態] 表單。 對於 [節點類型計數]，請輸入 **1**。

5. 選取 [節點類型 1 (主要)] 並填妥 [節點類型組態] 表單。 輸入節點類型名稱，並將[持久性層](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)設定為「銅級」。 然後選取 VM 大小。

    節點類型可定義 VM 大小、VM 數目、自訂端點，以及該類型 VM 的其他設定。 所定義的每個節點類型都會設定為不同的虛擬機器擴展集，以便用集合形式部署和管理虛擬機器。 每個節點類型可以獨立相應增加或相應減少，可以開啟不同組的連接埠，並有不同的容量計量。 第一個 (或主要) 節點類型就是 Service Fabric 系統服務所在的節點類型。 這個節點類型必須有五個或多個 VM。

    對於任何生產部署而言，[容量規劃](service-fabric-cluster-capacity.md)都是一個很重要的步驟。 不過，在此快速入門中，您不會執行應用程式，因此選取 DS1_v2 Standard VM 大小。 針對[可靠性層](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster)選取「銀級」，並將初始虛擬機器擴展集容量指定為 5。  

    自訂端點會在 Azure Load Balancer 中開啟連接埠，以便與在叢集上執行的應用程式連線。  輸入 **80, 8172** 以開啟連接埠 80 和 8172。

    請勿選取 [進行進階設定] 方塊，其用於自訂 TCP/HTTP 管理端點、應用程式連接埠範圍、[放置條件約束](service-fabric-cluster-resource-manager-configure-services.md#placement-constraints)和[容量屬性](service-fabric-cluster-resource-manager-metrics.md)。    
    
    ![節點類型組態的螢幕擷取畫面][node-type-config]

    選取 [確定] 。

6. 在 [叢集組態] 表單中，將 [診斷] 設定為 [開啟]。 在此快速入門中，您不需要輸入任何 [Fabric 設定](service-fabric-cluster-fabric-settings.md)屬性。  在 [Fabric 版本] 中，選取 [自動] 升級模式，以便 Microsoft 自動更新執行叢集的 Fabric 程式碼版本。  如果您想要[選擇支援的版本](service-fabric-cluster-upgrade.md) (升級目標)，請將模式設定為 [手動] 。     

    選取 [確定] 。

7. 填妥 [安全性] 表單。 在此快速入門中選取 [不安全]。 請注意，一般而言，您應該建立適用於生產工作負載的安全叢集。 任何人都可以匿名方式連線到不安全的叢集並執行管理作業。  

   Service Fabric 會使用憑證來提供驗證與加密，以保護叢集和其應用程式的各個層面。 如需詳細資訊，請參閱 [Service Fabric 叢集安全性案例](service-fabric-cluster-security.md)。 若要使用 Azure Active Directory 進行使用者驗證，或為了應用程式安全性設定憑證，請參閱[從 Resource Manager 範本建立叢集](service-fabric-cluster-creation-via-arm.md)。

    選取 [確定] 。

8. 檢閱摘要。 如果需要下載從您輸入之設定建立的 Azure Resource Manager 範本，請選取 [下載範本和參數]。 選取 [建立] 以建立叢集。

    您可以在通知功能中看到叢集的建立進度。 (請選取下畫面右上角狀態列附近的「鈴鐺」圖示。)如果您在建立叢集時選取了 [釘選到「開始面板」]，就會看到 [部署 Service Fabric 叢集] 已釘選到 [開始] 面板。

### <a name="connect-to-the-cluster-by-using-powershell"></a>使用 PowerShell 連線到叢集
透過 PowerShell 進行連線，以確認叢集正在執行。 ServiceFabric PowerShell 模組會隨著 [Service Fabric SDK](service-fabric-get-started.md) 一起安裝。 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps) Cmdlet 會建立叢集連線。   

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint quickstartcluster.westus2.cloudapp.azure.com:19000
```
如需連線到叢集的其他範例，請參閱[連線到安全的叢集](service-fabric-connect-to-secure-cluster.md)。 連線到叢集之後，使用 [Get-ServiceFabricNode](/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) Cmdlet 來顯示叢集中的節點清單以及每個節點的狀態資訊。 每個節點的 **HealthState** 應該為「正常」。

```powershell
PS C:\Users\sfuser> Get-ServiceFabricNode |Format-Table

NodeDeactivationInfo NodeName     IpAddressOrFQDN NodeType  CodeVersion  ConfigVersion NodeStatus NodeUpTime NodeDownTime HealthState
-------------------- --------     --------------- --------  -----------  ------------- ---------- ---------- ------------ -----------
                     _nodetype1_2 10.0.0.6        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_1 10.0.0.5        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_0 10.0.0.4        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_4 10.0.0.8        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
                     _nodetype1_3 10.0.0.7        nodetype1 5.7.198.9494 1                     Up 03:00:38   00:00:00              Ok
```

### <a name="remove-the-cluster"></a>移除叢集
Service Fabric 叢集是由叢集資源本身和其他 Azure 資源所構成。 若要完全刪除 Service Fabric 叢集，您也必須將組成叢集的所有資源刪除。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。 如需刪除叢集或刪除資源群組中一些 (但並非全部) 資源的其他方式，請參閱[刪除叢集](service-fabric-cluster-delete.md)。

在 Azure 入口網站中刪除資源群組：
1. 瀏覽至您需要刪除的 Service Fabric 叢集。
2. 在 [叢集基本資訊] 頁面上，選取 [資源群組] 名稱。
3. 在 [資源群組基本資訊] 上，選取 [刪除資源群組]。 然後，遵循該頁面上的指示，以完成資源群組的刪除。
    ![[資源群組基本資訊] 頁面的螢幕擷取畫面，並將 [刪除資源群組] 反白顯示][cluster-delete]


## <a name="use-azure-powershell"></a>使用 Azure PowerShell
建立叢集的另一種方式是使用 PowerShell。 方式如下：

1. 在您的電腦下載 [Azure Powershell 模組版本 4.0 版或更新版本](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)。

2. 執行 [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/new-azurermservicefabriccluster) cmdlet，以建立使用 X.509 憑證保護的五節點 Service Fabric 叢集。 此命令會建立自我簽署的憑證，並將它上傳到新的金鑰保存庫。 憑證也會複製到本機目錄。 設定 *-OS* 參數選擇在叢集節點執行的 Windows 或 Linux 版本。 視需要自訂參數。 

    ```powershell
    #Provide the subscription Id
    $subscriptionId = 'yourSubscriptionId'

    # Certificate variables.
    $certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
    $certfolder="c:\mycertificates\"

    # Variables for VM admin.
    $adminuser="vmadmin"
    $adminpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force 

    # Variables for common values
    $clusterloc="SouthCentralUS"
    $clustername = "mysfcluster"
    $groupname="mysfclustergroup"       
    $vmsku = "Standard_D2_v2"
    $vaultname = "mykeyvault"
    $subname="$clustername.$clusterloc.cloudapp.azure.com"

    # Set the number of cluster nodes. Possible values: 1, 3-99
    $clustersize=5 

    # Set the context to the subscription ID where the cluster will be created
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    # Create the Service Fabric cluster.
    New-AzureRmServiceFabricCluster -Name $clustername -ResourceGroupName $groupname -Location $clusterloc `
    -ClusterSize $clustersize -VmUserName $adminuser -VmPassword $adminpwd -CertificateSubjectName $subname `
    -CertificatePassword $certpwd -CertificateOutputFolder $certfolder `
    -OS WindowsServer2016DatacenterwithContainers -VmSku $vmsku -KeyVaultName $vaultname
    ```

    命令可能需要 10 分鐘到 30 分鐘才能完成。 此輸出包含憑證相關資訊、憑證上傳至的 Key Vault，以及複製憑證的本機資料夾。     

3. 複製整個輸出並儲存到文字檔 (您稍後需要參考的資料)。 記下輸出中的下列資訊： 

    - CertificateSavedLocalPath
    - CertificateThumbprint
    - ManagementEndpoint
    - ClientConnectionEndpointPort

### <a name="install-the-certificate-on-your-local-machine"></a>在本機電腦上安裝憑證
  
若要連線到叢集，請將憑證安裝到目前使用者的個人 (My) 存放區。 

執行下列命令：

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\<certificatename>.pfx `
        -Password $certpwd
```

您現在即可連線到您安全的叢集。

### <a name="connect-to-a-secure-cluster"></a>連線到安全的叢集 

執行 [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) Cmdlet 以連線到安全的叢集。 憑證詳細資料必須與用來設定叢集的憑證相符。 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <ManagementEndpoint>:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint <CertificateThumbprint> `
          -FindType FindByThumbprint -FindValue <CertificateThumbprint> `
          -StoreLocation CurrentUser -StoreName My
```

下列範例顯示範例參數： 

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mycluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

執行下列命令來檢查您連線的叢集是否狀況良好。

```powershell

Get-ServiceFabricClusterHealth

```
### <a name="remove-the-cluster"></a>移除叢集
叢集是由叢集資源本身和其他 Azure 資源所構成。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。 

```powershell
$groupname="mysfclustergroup"
Remove-AzureRmResourceGroup -Name $groupname -Force
```
## <a name="use-azure-cli"></a>使用 Azure CLI
建立叢集的另一種方式是使用 CLI。 方式如下：

1. 在您的電腦上安裝 [Azure CLI 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)。
2. 登入 Azure 並選取您要在其中建立叢集的訂用帳戶。
   ```azurecli
   az login
   az account set --subscription <GUID>
   ```
3. 執行 [az sf cluster create](/cli/azure/sf/cluster?view=azure-cli-latest#az_sf_cluster_create) 命令，以建立使用 X.509 憑證保護的五節點 Service Fabric 叢集。 此命令會建立自我簽署的憑證，並將它上傳到新的金鑰保存庫。 憑證也會複製到本機目錄。 設定 *-os* 參數選擇在叢集節點執行的 Windows 或 Linux 版本。 視需要自訂參數。

    ```azurecli
    #!/bin/bash

    # Variables
    ResourceGroupName="aztestclustergroup" 
    ClusterName="aztestcluster" 
    Location="southcentralus" 
    Password="q6D7nN%6ck@6" 
    Subject="aztestcluster.southcentralus.cloudapp.azure.com" 
    VaultName="aztestkeyvault" 
    VaultGroupName="testvaultgroup"
    VmPassword="Mypa$$word!321"
    VmUserName="sfadminuser"

    # Create resource groups
    az group create --name $ResourceGroupName --location $Location 
    az group create --name $VaultGroupName --location $Location

    # Create secure five node Linux cluster. Creates a key vault in a resource group
    # and creates a certficate in the key vault. The certificate's subject name must match 
    # the domain that you use to access the Service Fabric cluster. The certificate is downloaded locally.
    az sf cluster create --resource-group $ResourceGroupName --location $Location --certificate-output-folder . \
        --certificate-password $Password --certificate-subject-name $Subject --cluster-name $ClusterName \
        --cluster-size 5 --os UbuntuServer1604 --vault-name $VaultName --vault-resource-group $VaultGroupName \
        --vm-password $VmPassword --vm-user-name $VmUserName
    ```
    
### <a name="connect-to-the-cluster"></a>連接到叢集
執行下列 CLI 命令以使用憑證來連線到叢集。  使用用戶端憑證來進行驗證時，請確定憑證詳細資料與部署至叢集節點的憑證相符。 對自我簽署憑證使用 `--no-verify` 選項。

```azurecli
az sf cluster select --endpoint https://aztestcluster.southcentralus.cloudapp.azure.com:19080 --pem ./linuxcluster201709161647.pem --no-verify
```

執行下列命令來檢查您連線的叢集是否狀況良好。

```azurecli
az sf cluster health
```

### <a name="connect-to-the-nodes-directly"></a>直接連線到節點 

若要連線至 Linux 叢集中的節點，您可以使用安全殼層 (SSH) 命令。 指定從 3389 之後的連接埠號碼。 例如，針對稍早建立的五個節點叢集，命令如下所示：
```bash
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3389
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3390
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3391
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3392
ssh sfadminuser@aztestcluster.southcentralus.cloudapp.azure.com -p 3393
```

### <a name="remove-the-cluster"></a>移除叢集
叢集是由叢集資源本身和其他 Azure 資源所構成。 刪除叢集及其取用之所有資源的最簡單方式，就是刪除資源群組。 

```azurecli
ResourceGroupName = "aztestclustergroup"
az group delete --name $ResourceGroupName
```

## <a name="next-steps"></a>後續步驟
您已設定好開發叢集，請嘗試下列作業︰
* [使用 Service Fabric 總管視覺化叢集](service-fabric-visualizing-your-cluster.md)
* [移除叢集](service-fabric-cluster-delete.md) 
* [使用 PowerShell 部署應用程式](service-fabric-deploy-remove-applications.md)
* [使用 CLI 部署應用程式](service-fabric-application-lifecycle-sfctl.md)


[cluster-setup-basics]: ./media/service-fabric-get-started-azure-cluster/basics.png
[node-type-config]: ./media/service-fabric-get-started-azure-cluster/nodetypeconfig.png
[cluster-status]: ./media/service-fabric-get-started-azure-cluster/clusterstatus.png
[service-fabric-explorer]: ./media/service-fabric-get-started-azure-cluster/sfx.png
[cluster-delete]: ./media/service-fabric-get-started-azure-cluster/delete.png
