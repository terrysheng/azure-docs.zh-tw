---
title: "使用 StorSimple Manager 裝置儀表板 | Microsoft Docs"
description: "描述 StorSimple Manager 服務裝置儀表板，以及如何用它來檢視儲存體度量和連線的啟動器，並尋找序號和 IQN。"
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: 6c213969-a385-461f-b698-78ef5b8a79cc
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2209790b54f5dd18a413f2e08ddc6ffd6aef662
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 11/07/2017
---
# <a name="use-the-device-dashboard-in-storsimple-manager-service"></a>在 StorSimple Manager 服務中使用裝置儀表板  
> [!NOTE]
> StorSimple 的傳統入口網站已過時。 按照淘汰排程，StorSimple 裝置管理員會自動移至新的 Azure 入口網站。 您將收到關於此移動的電子郵件和入口網站通知。 本文件也很快就會淘汰。 若要檢視適用於新 Azure 入口網站的文章版本，請移至[在 StorSimple Manager 服務中使用裝置儀表板](storsimple-8000-device-dashboard.md)。 若有關於移動的任何問題，請參閱[常見問題集：移至 Azure 入口網站](storsimple-8000-move-azure-portal-faq.md)。


## <a name="overview"></a>概觀
StorSimple Manager 裝置儀表板可提供特定 StorSimple 裝置的資訊概觀；相對於服務儀表板，其提供 Microsoft Azure StorSimple 解決方案中所有裝置的相關資訊。

![裝置儀表板頁面](./media/storsimple-device-dashboard/StorSimple_DeviceDashbaord1M.png)

儀表板包含下列資訊：

* **圖表區域** – 您可以在儀表板頂端的圖表區域中查看相關儲存體度量。 在此圖表中，您可以檢視主要儲存體總計 (主機寫入至您的裝置的資料數量) 和您的裝置一段時間內所使用的雲端儲存體總計的度量資訊。
  
     在此情況下，「主要儲存體」是指由主機寫入的資料總量，並可依磁碟區類型細分︰「主要分層式儲存體」包含在本機儲存的資料和分層至雲端的資料；「主要本機釘選儲存體」只包含在本機儲存的資料。 相反地，「雲端儲存體」是儲存在雲端中的資料總量測量。 這包括分層式資料和備份。 請注意，儲存在雲端中的資料會進行重複資料刪除和壓縮，而主要儲存體會指出進行重複資料刪除和壓縮之前已使用的儲存體數量。 (您可以比較這兩個數字以了解壓縮率)。對於主要和雲端儲存體，所顯示的數量會以您設定的追蹤頻率為基礎。 例如，如果您選擇一週頻率，則此圖表會顯示上週每一天的資料。
  
     您可以如下所示設定圖表：
  
  * 若要查看一段時間耗用的雲端儲存體數量，請選取 [ **使用的雲端儲存體** ] 選項。 若要查看主機所寫入的儲存體總量，請選取 [使用的主要分層式儲存體] 和 [使用的本機釘選儲存體] 選項。 在圖中，兩個選項都已選取；因此，此圖表會顯示雲端和主要儲存體的儲存體數量。 請注意，任何在安裝 Update 2 之前使用的主要儲存體皆是以「使用的主要分層式儲存體」  行表示。
  * 使用圖表右上角的下拉式功能表，指定 1 週、1 個月、3 個月或 1 年的期間。 請注意，最上層圖表每天只會重新整理一次，因此會反映前一天的總計。
    
    如需詳細資訊，請參閱 [使用 StorSimple Manager 服務監視 StorSimple 裝置](storsimple-monitor-device.md)。
* **使用量概觀** – 在 [使用量概觀] 區域中，您可以看到使用的主要儲存體數量、佈建的儲存體數量以及您裝置的最大儲存體容量。 藉由比較這些使用量數字與可用的最大儲存體數量，您可以快速查看是否需要取得額外的儲存體。 請注意，本概觀會每 15 分鐘更新一次，但由於更新頻率的差異，可能會顯示與上述圖表區域 (每日更新) 中不同的數字。 如需詳細資訊，請參閱 [使用 StorSimple Manager 服務監視 StorSimple 裝置](storsimple-monitor-device.md)。
* **警示** – [警示] 區域包含您裝置的警示概觀。 警示會依嚴重性分組，並提供一個計數以顯示每個嚴重性層級的警示數目。 按一下警示嚴重性即可開啟 [警示] 索引標籤的限定範圍檢視，僅顯示此裝置的該嚴重性層級的警示。
* **工作** – [工作] 區域會顯示最近工作活動的結果。 這可確保系統如預期般運作，或可讓您知道您需要採取更正動作。 若要查看最近完成工作的詳細資訊，請按一下 [在過去 24 小時內成功的工作] 。
* 儀表板右邊的 [快速概覽]  區域提供實用資訊，例如裝置機型、序號、狀態、描述和磁碟區數目。

您也可以從裝置儀表板設定容錯移轉及檢視連接的啟動器。

可在此頁面執行的一般工作包括：

* 檢視連接的啟動器
* 尋找裝置序號
* 尋找裝置目標 IQN

## <a name="view-connected-initiators"></a>檢視連接的啟動器
按一下裝置儀表板的 [快速概覽] 區域中提供的 [檢視連線的啟動器] 連結，即可檢視連接至您裝置的 iSCSI 啟動器。 此頁面提供已成功連接到您的裝置之啟動器的表格式清單。 對於每個啟動器，您可以查看：

* 已連接之啟動器的 iSCSI 完整格式名稱 (IQN)。
* 允許這個已連接之啟動器的存取控制記錄 (ACR) 名稱。
* 已連接之啟動器的 IP 位址。
* 您的存放裝置上啟動器連接到的網路介面。 範圍從 DATA 0 到 DATA 5。
* 根據目前的 ACR 組態，連接的啟動器允許存取的所有磁碟區。

如果您在此清單中看到非預期的啟動器，或看不到預期的啟動器，請檢閱您的 ACR 組態。 最多 512 個啟動器可連接到您的裝置。

## <a name="find-the-device-serial-number"></a>尋找裝置序號
當您在裝置上設定 Microsoft 多重路徑 I/O (MPIO) 時，您可能需要裝置序號。 執行下列步驟來尋找裝置序號。

#### <a name="to-find-the-device-serial-number"></a>若要尋找裝置序號：
1. 瀏覽至 [裝置] > [儀表板]。
2. 在儀表板的右窗格中，找出 [快速概覽]  區域。
3. 向下捲動並找出序號。

## <a name="find-the-device-target-iqn"></a>尋找裝置目標 IQN
當您在 StorSimple 裝置上設定 Challenge Handshake 驗證通訊協定 (CHAP) 時，您可能需要裝置目標 IQN。 執行下列步驟來尋找裝置目標 IQN。

### <a name="to-find-the-device-target-iqn"></a>若要尋找裝置目標 IQN：
1. 瀏覽至 [裝置] > [儀表板]。
2. 在儀表板的右窗格中，找出 [快速概覽]  區域。
3. 向下捲動並找出目標 IQN。

## <a name="next-steps"></a>後續步驟
* [深入了解 StorSimple Manager 服務儀表板](storsimple-service-dashboard.md)。
* 深入了解 [使用 StorSimple Manager 服務管理 StorSimple 裝置](storsimple-manager-service-administration.md)。

