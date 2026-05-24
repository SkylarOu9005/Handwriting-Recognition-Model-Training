---

# 手寫辨識模型訓練與互動介面 (Handwriting Digit Recognition with Gradio UI)

本專案建構了一個能夠辨識手寫數字（0-9）的深層神經網路（DNN），使用經典的 MNIST 資料集進行訓練與測試 。除了專注於模型架構的微調優化以防止過擬合（Overfitting）外 ，本專案亦整合了 **Gradio** 互動套件，建立即時的手寫畫布 UI，讓使用者可以直接在網頁上繪圖並即時進行辨識測試 。

##  專案成果

**準確度**：經過模型架構與超參數的多次迭代測試，最終在測試集上達到 **98.21%** 的資料正確率 。

**防止過擬合之配套措施**：在模型中插入 **Dropout 層**，並導入 **EarlyStopping 回呼函數**，成功平滑訓練曲線並確保泛化能力 。

**即時互動的手寫畫布**：捨棄傳統僅能用程式碼輸入的方式，透過 Gradio 打造直覺的手寫測試介面 。

---

## 模型架構與優化歷程

為了在小型的 MNIST 資料集上找出「準確度最高」且「不產生過擬合」的組合，本專案調整並測試了多種隱藏層數與優化器設定 ：

### 1. 網路層數與神經元設定

實驗證實，神經元數量**逐層遞減**的結構能取得更好的辨識效果 。最終採用的最佳架構如下 ：

**輸入層 (Input Layer)**：784 個神經元（將 $28 \times 28$ 的灰階圖片攤平）。

**隱藏層 1 (Hidden Layer 1)**：784 個神經元，使用 `ReLU` 激發函數 ，後接 `Dropout(0.3)` 隨機丟棄 30% 神經元 。

**隱藏層 2 (Hidden Layer 2)**：392 個神經元，使用 `ReLU` 激發函數 ，後接 `Dropout(0.3)` 。

**輸出層 (Output Layer)**：10 個神經元，使用 `Softmax` 激發函數，輸出各數字的機率分佈 。



> 
> **補充**：曾嘗試使用 5 層隱藏層（如全部設 196 或遞減至 32），但效果皆不如精簡的 2 層結構 ；過多層數易引發過擬合，驗證結果證實少量隱藏層搭配精確的防過擬合機制效果最為顯著 。
> 
> 

### 2. 優化器與損失函數微調

* 
**損失函數 (Loss Function)**：採用了適合多分類任務的 `categorical_crossentropy` 函數。


* 
**優化器 (Optimizer)**：早期使用 `SGD(lr=0.001)` 時，模型準確率僅有 91.71% 。隨後將其更換為具備自動調整學習率特性的 **`Adam` 優化器**，使辨識率大幅攀升 。



### 3. 訓練配置與 EarlyStopping

* 設定 `batch_size=55`、最多 `epochs=20` 。


* 切分 20% 的訓練資料作為驗證集 (`validation_split=0.2`) 。


* 加入 `EarlyStopping(monitor='val_loss', patience=5)` 監控驗證集損失 ，當模型表現連續 5 個 epoch 不再進步時自動終止訓練，防止過度依賴訓練數據 。



---

##  訓練曲線結果展示

透過 `matplotlib` 繪製的訓練歷程圖顯示，在 EarlyStopping 的保護下，訓練在第 10 個 Epoch 時提前停止（因驗證集準確率 `val_accuracy` 已達到穩定飽和，約在 98.00% 上下波動），有效地避免了後續可能引發的過擬合現象 。

**訓練集準確率 (Accuracy)**：~ 98.92% 

**驗證集準確率 (Val Accuracy)**：~ 98.00% 

**最終測試集總評量 (Test Accuracy)**：**98.21%** 



---

## 環境需求

專案內主要使用 Python 3.11 進行開發，所需核心套件如下 ：

```bash
pip install tensorflow numpy matplotlib gradio ipywidgets

```

### 執行專案

1. 可於 Google Colab 中開啟。
2. 依序執行 Jupyter Notebook 內的單元格進行資料處理、模型訓練 。
3. 啟動最後一個單元格的 Gradio 區塊即可開啟 Web UI 進行實際手寫測試 ！

---

## 作者與致謝

**開發者**：歐靜嬡 (Skylar Ou) 
**GitHub repository**：[Handwriting-Recognition-Model-Training](https://www.google.com/search?q=https://github.com/SkylarOu9005/Handwriting-Recognition-Model-Training)
