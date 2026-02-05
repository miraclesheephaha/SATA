# SATA Introduction  
## RAID MODE
* RAID3:資料拆分：當你寫入一筆資料時，它會被拆成「位元 (Bit)」或「位元組 (Byte)」，分散存放在多顆資料碟中。專用校驗碟 (Dedicated Parity Drive)：系統會計算這些資料的 XOR (互斥或) 結果，並將結果存放在最後一顆「校驗專用碟」。(XOR是兩個一樣地為0)

<img width="478" height="190" alt="image" src="https://github.com/user-attachments/assets/c908ff35-6d75-4701-b253-c65e359915f0" />

* RAID3與RAID4與RAID5差別
  
  <img width="391" height="164" alt="image" src="https://github.com/user-attachments/assets/7d187b9c-b9a9-4e7d-9619-14f8e2cedefe" />

1. *RAID 3：位元級同步 (Bit-level)*  
  RAID 3 是「軍隊式」管理。資料被切成極小的碎片分散在所有資料碟。  
  運作方式：讀取一個 4KB 檔案時，控制器要求所有硬碟同步動作，每顆硬碟貢獻幾位元。  
  優點：大檔案連續讀取極快。  
  缺點：無法處理併發（多個小任務），且校驗碟負荷極重。  
  現況：已幾乎絕跡，被 RAID 5 取代。    
2. *RAID 4：區塊級專用校驗 (Block-level)*  
RAID 4 改進了分割方式，將資料切成 Block (如 4KB)。  
運作方式：讀取小檔案時，只需要讀取存有該 Block 的那一顆硬碟，其他硬碟可以去忙別的讀取請求。  
致命傷：寫入瓶頸。無論你寫資料到哪一顆碟，校驗碟都必須同時被寫入更新。這就像全公司的員工辦理報帳，都要經過同一個會計，會計（校驗碟）會忙死。  
現況：除了極少數專業儲存廠商（如 NetApp）外，市面上極罕見。
3. *RAID 5：分佈式校驗 (The Winner)*  
   RAID 5 是現代伺服器（如你的 C620 平台）最常用的模式，它解決了 RAID 4 的會計塞車問題。  
   運作方式：它不再設有「專用校驗碟」。校驗碼 $P$ 會像階梯一樣，輪流儲存在不同的硬碟中。  
     寫入 Block 1 時，校驗碼存硬碟 D。  
     寫入 Block 2 時，校驗碼存硬碟 C。  
   優點：分散寫入壓力。所有硬碟都分擔了資料儲存與校驗計算的工作，讀取與寫入效能平衡。  
   缺點：當有一顆硬碟損壞時，重建資料（Rebuild）時效能會大幅下降。  
4. RAID 6 的技術奧秘：雙重校驗 (P + Q)
   RAID 5 只使用一種簡單的 XOR ($P$) 計算。而 RAID 6 則引入了第二組完全不同的校驗演算法（通常是基於 Galois Field 伽羅瓦域的編碼，稱為 $Q$ 校驗）。  
   P 校驗：簡單的 XOR 運算，能找出一顆硬碟掉失的資料。  
   Q 校驗：複雜的線性代數運算。即使兩顆硬碟同時消失，透過 P 和 Q 聯立方程式，控制器依然能解出原始資料。

## 其他問題  
<img width="290" height="153" alt="image" src="https://github.com/user-attachments/assets/4bdd8200-4749-4c20-ac0b-17df8e8d6a96" />  

PCIe與SATA的差異?  
PCIe 路徑：CPU $\rightarrow$ PCIe Root Port $\rightarrow$ 設備 (如 NVMe 控制器)(PCIe 是傳輸資料的通道)  
SATA 路徑：CPU $\rightarrow$ PCIe (總線) $\rightarrow$ PCH 內部的 AHCI HBA $\rightarrow$ SATA 接口(SATA 是接在 PCIe 總線底下的一個「子系統」)  

PCIe 是更底層、更通用的傳輸層級。  
SATA 是建立在儲存需求上的應用介面層級。  
在現代架構中，SATA 控制器本身就是一個 PCIe 設備。  



