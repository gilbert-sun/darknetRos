**YOLOv2 for Home Care**
==

## A. Results

|![](https://i.imgur.com/Esmuvz1.jpg)|![](https://i.imgur.com/CVjdJCS.jpg)| 
| -------- | -------- | 


|![](https://i.imgur.com/aMMDmof.jpg)|![](https://i.imgur.com/ekcBX7d.jpg)|
| -------- | -------- | 


## B. Data Path

### <font color="4f0099">darknet_modified/</font>
├── ...
├── <font color="4f0099">cfg/</font>
├── <font color="4f0099">obj/</font>
├── <font color="4f0099">results/</font>
├── <font color="4f0099">src/</font>
│   ├── ...
│   └── image.c
├── <font color="4f0099">examples/</font>
│   ├── ...
│   ├── detector.c
│   └── darknet.c
├── <font color="4f0099">Final_3action</font>
│   ├── 0828_3action_GT.txt
│   ├── 0828_3action.txt
│   ├── 0828-yolo-action.weights
│   ├── <font color="4f0099">Helpful_Tools/</font>
│   ├── <font color="4f0099">Original_Dataset/</font>
│   ├── <font color="4f0099">JPEGImages/</font>
│   └──  <font color="4f0099">labels/</font>
├── <font color="4f0099">0511/</font>
│   ├── 4action.data
│   ├── yolo-action.cfg
│   ├── 4action_0629.weights
│   ├── trainlist_0627.txt
│   ├── GT_trainlist_0627.txt
│   ├── <font color="4f0099">JPEGImages/</font>
│   ├── <font color="4f0099">labels/</font>
└── Makefile


1. ${YOLO_ROOT}: /home/e200/darknet_modified
2. ${3action_ROOT}: ${YOLO_ROOT}/Final_3action
3. Image DIR: ${DATA_ROOT}/JPEGImages
4. Groundtruth DIR: ${DATA_ROOT}/labels
(資料夾名稱一定要是 labels，並放在與影像資料夾同個路徑)
5. Original Image: ${DATA_ROOT}/Original_Dataset
6. Unlabeled Image: ${DATA_ROOT}/Unlabeled

## C. How to Test & Train (Modified)
### 1. Test (Images)
* Command:
    ```
    $ cd darknet_modified
    $ ./darknet detector test cfg/action.data cfg/yolo-action.cfg 0828-yolo-action.weights test.txt
    ```
 
  *  action.data: Class 數量、Train&Valid Data 位置、Class Name 檔位置、儲存 Weights 檔位置
  *  yolo-action.cfg: 描述 YOLO 的網路架構
  *  yolo-action_final.weights:   網路架構的係數(替換此檔已切換不同Weights)
  *  test.txt:  要批次進行 Test 的影像之絕對位置(修改此檔以 Test 別的影像) 
* Output:
  * ${YOLO_ROOT}/results/Images with Bbox (偵測結果標記在原始影像)
  * ${YOLO_ROOT}/predict.txt(偵測結果文字檔)
  (class) (左上座標x y)(右下座標x y)
  ```=1
  photo 000013469
  standing 22 12 227 479
  photo 000085370
  standing 99 121 402 685
  sitting 369 89 633 719
  sitting 579 92 811 719
  standing 722 83 1115 719
  ```
### 2. Test (Video or Webcam):
```
$ ./darknet detector demo cfg/action.data cfg/yolo-action.cfg 0828-yolo-action.weights
$ ./darknet detector demo cfg/action.data cfg/yolo-action.cfg 0828-yolo-action.weights Video
```
如果有 Input Video 就是 Test 該 Video，若無則是開啟 Webcam
### 3. Train:
* Command:
    ```
    $ cd darknet_modified/ 
    $ ./darknet detector train cfg/action.data cfg/yolo-action.cfg darknet19_448.conv.23
    ```
  * darknet19_448.conv.23: 
用來當作初始值的 Weights，此檔為 Pre-trained on ImageNet。可任意使用別的 Pre-trained weights，e.g. yolo.weights（Pre-trained on COCO）
* Output:
  * ${YOLO_ROOT}/backup/weights  (每隔一定 training 次數會存一個 weights)

## D. Training Details
* 0814:
  * Standing: 2245
  * Sitting: 2900
  * Falling: 1718
  * Training Weights: 0814-yolo-action.weights (40000 次疊代)
  * IoU Threshold = 0.5 (Between GroundTruth & Predict bbox)
  * Training) Average Recall = 0.954287
  * (Training) Average Precision = 0.927417

* 0828:
  * Standing: 3127
  * Sitting: 3499
  * Falling: 2609
  * Training Weights: 0828-yolo-action.weights (80000次疊代)
  * IoU Threshold = 0.5 (Between GroundTruth & Predict bbox)
  * (Training) Average Recall = 0.970741
  * (Training) Average Precision = 0.969551

* 0309:
  * $Recall = \frac{TP}{TP + FN}$

  * $Precision = \frac{TP}{TP + FP}$
  * $Accuracy = \frac{TP}{TP + FP + FN}$
  * $IoU\ Threshold = \frac{Area\  of\ Overlap}{Area\ of\ Union}$
    
    ![](https://i.imgur.com/Vwnb15M.png)

    ![](https://i.imgur.com/4tiCSrH.png)

  * Efficiency
    |Foward Timimg (sec)|Tiny-yolo|Yolo|
    | -------- | -------- | -------- | 
    |Webcam|0.015|0.025|
    |Video|0.023|0.042|


  * Training Curve
  (MSCOCO pre-trained weights (yolo.weights))
  ![](https://i.imgur.com/3LrQNRZ.png)
  ![](https://i.imgur.com/PsQhfdg.png) 
  * Datasets
    *  Photos (# of photos)
    Training: 5354 (70%)
    Testing: 2259 (30%)
    * Bounding Boxes (# of boxes)

        | | Standing | Sitting | Falling | Total
        | :--------: | :--------: | :--------: | :--------: | :--------: |
        |Training|2148|2447|1847|6442|
        |Testing|979|1052|762|2793|
    
* 0622:
  Threshold = 0.5 
	Average Recall = 0.989156 
	Average Precision = 0.987611 
	Average Accuracy = 0.977032 
	Standing Accuracy = 0.979462 (16310/16652) 
	Sitting Accuracy = 0.968316 (5226/5397) 
	Falling Accuracy = 0.992002 (3597/3626) 
	Bending Accuracy = 0.96322 (3326/3453) 




## E. How to Label Image
${TOOL_ROOT}: /home/e200/Downloads/ BBox-Label-Tool-multi-class
![](https://i.imgur.com/vIIliuP.png)

0. 將圖片放入 ${TOOL_ROOT}/Images/0001/，產生的 Label 檔會在 ${TOOL_ROOT}/Labels/0001
1. 執行方式：python ${TOOL_ROOT}/main.py，開啟在上方 Image Dir 輸入「 1」，然後按 Load
2. 右上角可以選取 Class，0:Standing, 1:Sitting, 2:Falling（可以在${TOOL_ROOT}/class.txt 修改），選完要「按 Confirm Class 才算有選到」
3. 滑鼠點一下開始框 BBOX，在點一下框選完成，在框的時候可以按 ESC 取消
4. 右邊選取 BBOX 後按 Delete 可以刪除，Clear All 是全刪
5. 框 BBox 不用怕框超出界外，有設定超過的會自動修正至邊界6. 按下 Next 之後會作儲存的動作輸出 Label 檔案，並出現下一張圖片，框好的圖片會「自動刪除」，這是避免重複產生 Label 檔案，所以 Prev 這個按鍵沒有用（ Next 的鍵盤快捷鍵是「 d」，善用可以提高效率）
7. 如果這張圖片沒有任何要標的物體，直接 Next 就不會產生 Label 檔，這是避免有空白檔案
8. 所有資訊可以在 Terminal 上看到，包含當前圖片、框取的 BBox，如果誤刪還可以找得回來
9. 做到最後一張時按下 Next 會沒反應，並在 Terminal 上看到錯誤訊息，代表已經標完所有圖片
10. 標記完可以透過 Label 的檔名確認有哪些圖片需要做 training（沒物體或不需要的不會產生Label 檔案）
* 絕對不要忘記要按 Confirm Class，可以在右邊看到框好的 BBOX 做確認 Class 有沒有選錯
* 如果圖片太大會無法完整顯示，可以修改 main.py 內的 SIZE_FACTOR 調整圖片放大縮小
* 要根據圖片的副檔名修改 main.py 內 IMG_TYPE，一次只能讀一種（ .jpg, .png…）

```python=23
IMG_TYPE = '.jpg'
SIZE_FACTOR = 1.0


class LabelTool():
```

## F. Datasets

0. Dataset List
http://homepages.inf.ed.ac.uk/rbf/CVonline/Imagedbase.htm#action
1. VOC2012 Action
http://host.robots.ox.ac.uk/pascal/VOC/voc2012/index.html
2. Stanford 40
http://vision.stanford.edu/Datasets/40actions.html
3. Willow Actions
http://www.di.ens.fr/willow/research/stillactions/
4. Cornell Activity Datasets
http://pr.cs.cornell.edu/humanactivities/data.php#data
5. Freiburg Sitting people
http://www2.informatik.uni-freiburg.de/~oliveira/dataset.html
6. MPII Human Pose Dataset
http://human-pose.mpi-inf.mpg.de/
7. UR Fall Detection Dataset
http://fenix.univ.rzeszow.pl/~mkepski/ds/uf.html
8. Le2i Fall detection Dataset
http://le2i.cnrs.fr/Fall-detection-Dataset?lang=fr
9. The Images of Groups Dataset
http://chenlab.ece.cornell.edu/people/Andy/ImagesOfGroups.html
* Database
    | Datasets | Video Clip # | Total Frame |
    | :--------: | :--------: | :--------: | 
    |UR Fall|30|2849|
    |Le2i Fall|193|108807|
    |Others|N/A|2018|

* Current Poses Numbers (Bounding boxes #)
    | |Standing|Sitting|Lying|Bending|
    | :--------: | :--------: | :--------: |:--------: |:--------: | 
    |**UR Fall**|870|286|903|790|
    |**Le2i Fall**|13745|3357|2669|2547|
    |**Others**|1885|1671|53|0|
    |**Total**|**16500**|**5314**|**3625**|**3337**|




## G. 0-100 Use YOLO Test & Train
1. Install OpenCV, CUDA, CuDNN
2. `$ git clone https://github.com/pjreddie/darknet`
3. `$ cd darkent/`
4. 修改 Makefile (Turn On GPU, CUDNN, OPENCV)，如果 opencv 的資料夾名稱非預設的也要修正
```shell=1
GPU = 1
CUDNN = 1
OPENCV = 1
DEBUG = 0
```
```shell=36
ifeq ($(OPENCV), 1)
COMMON += -DOPENCV
CFLAGS += -DOPENCV
LDFLAGS += `pkg-config --lib opencv-2.4.13`
COMMON += `pkg-config --cflags opencv-2.4.13`
```
5. `$ make`
6. 準備好 Training Data、Validation Data 和其 labels
需建立 Training Data List（記錄 Training Data 的絕對路徑，txt 檔）、Valid Data List
labels 資料夾要在和 Training Data 資料夾同個路徑下
Ground Truth 格式：
（ Class index）（ Bbox Center X Y）（ Bbox W H），需要 normalize [0, 1]
```
2 0.463 0.692 0.328 0.258
```
7. 建立 action.names，記錄 class 種類（順序重要，GroundTruth 是用 index，從 0 開始）
```
standing
sitting
falling
```
8. 複製 cfg/voc.data，改名成 action.data，修改內容
```
classes = 4
train  = /home/e200/darknet_modified/0511/trainlist_0627.txt
valid  = /home/e200/darknet_modified/0511/trainlist_0627.txt
names = /home/e200/darknet_modified/0511/action.names
backup = backup
```
9. 複製 cfg/yolo-voc.cfg，改名成 yolo-action.cfg，修改內容
```=18
learning_rate=0.001
burn_in=1000
max_batches = 80200
policy=steps
steps=40000,60000
scales=.1,.1
```
```=233
[convolutional]
size=1
stride=1
pad=1
filters=45
activation=linear


[region]
anchors =  1.3221, 1.73145, 3.19275, 4.00944, 5.05587, 8.09892, 9.47112, 4.84053, 11.2364, 10.0071
bias_match=1
classes=4
coords=4
num=5
softmax=1
jitter=.3
rescore=1
```
max_batches 決定 training 的疊代次數
[region]前一層的[convolution]，設定 filters = (classes + 5)*5、classes 數量
 
10. 下載 ImageNet Pre-trained 的 Weight 
```
$ curl-O https://pjreddie.com/media/files/darknet19_448.conv.23
```
11. 即可開始 Training
```
$ ./darknet detector train cfg/action.data cfg/yolo-action.cfg darknet19_448.conv.23
```
12. Training 後得到的 weights 存在  ${YOLO_ROOT}/backup
13. Test 指令：
```
$ ./darknet detector test 「 cfg/action.data」「 cfg/yolo-action.cfg」「 Weights」「 Img_Name」
```
* Weights: 選擇要用的 Weights 檔 ex. backup/yolo-action_final.weights
* img_Name: 未修改過的 YOLO 一次只能 input/output 單一圖片
* 執行完後會將結果圖片儲存在 ${YOLO_ROOT}/predictions.jpg
## H. Helpful Tools Intro.
1. ComputeAccuracy.cpp: 用來計算準確度的程式，需用 g++編譯，結果會顯示於 Terminal 上
`$ ./ComputeAccuracy 「Predict.txt」「 GroundTruth.txt」「Mode」「IoU Threshold」`
*  Predict.txt: 記錄 YOLO 偵測的結果，有固定格式
*  GroundTruth.txt: 記錄 GroundTruth，必須與 Predict.txt 影像偵測的順序、格式相同
photo Image_Name(without Data Type .jpg)
（ Class_Name） （ Upper_Left x y） （ Lower_Right x y）
![](https://i.imgur.com/ZyrEuZz.png)

*  Mode: 決定程式的功能    
    
    0. 計算準確度
    1. 檢查 Predict.txt 裡面有沒有多的影像（相對於 GroundTruth.txt）
    2. 檢查 GroundTruth.txt 裡面有沒有多的影像（相對於 Predict.txt）
    3. 檢查 Predict.txt 和 GroundTruth.txt 內的影像順序是否一樣
* IoU Threshold: Predict Bbox 和 GroundTruth Bbox 重疊比率高於多少門檻才算有偵測到，若無輸入預設是 0.5

3. Count_object.py: 計算 labels 檔案內各個 class 的物體數量
4. GenTrainList.py: 產生 Training、Validation List
5. move_rename.py: 根據 label 檔去搬移對應的 Image 到指定的資料夾
6. TransferToYoloOutput.py: 將所有 label 檔整合成可計算準確度的格式（GroundTruth），可用來計算 Training data 之準確度

7. plot.py: 利用所有weights檔產生accuracy後，畫出正確率
8. SplitTrainTest.py: 將Img資料夾中照片隨機分為Train/Test (Train:Test=7:3)並產生List


## I. Python Wrapper

### 1. Tree

#### <font color="4f0099">darknet/</font>
├── ...
├── <font color="4f0099">python/</font>
│   ├── <font color = "#">darknet.py</font>
│   ├── detector_camera.py
│   └── detector.py
├── <font color="4f0099">fall_anal/</font>
│   ├── ...
│   ├── <font color="4f0099">data</font>
│   ├── fall_anal.py
│   └── model_file
├── <font color="58e258">libdarknet.so</font>
├── <font color="4f0099">latest_model/</font>
├── <font color="4f0099">fall_videos/</font>
└── <font color="4f0099">testing_videos/</font>

* <font color="4f0099">python/</font>
    * detector.py: 測試單張圖片
    * detector_camera.py: 測試影片或接webcam
* <font color="4f0099">fall_anal/</font>
    * data: 內有可供測試之txt檔
    * fall_anal.py: 偵測跌倒的分類器
    * model_file: 分類模型檔
* <font color="4f0099">latest model/</font>: 包含不同weights, data, cfg檔案
* <font color="4f0099">fall_videos/</font>: 包含接近需求視角的輸出影片
* <font color="4f0099">testing_videos/</font>: 包含可供測試的影片
* libdarknet.so: sharing library


### 2. Demo

1. 更改`.data`檔內 train, valid, names路徑
2. Command
    ```
    $ cd darknet/python/
    $ python detector_camera.py ../latest_model/yolo-action.cfg ../latest_model/yolo-action_final.weights ../latest_model/action.data ../testing_videos/coffee_3.avi
    $ python detector_camera.py ../latest_model/yolo-action.cfg ../latest_model/yolo-action_final.weights ../latest_model/action.data webcam
    ```
    若要測試影片則加入影片位置，若要從webcam輸入則加上`webcam`
    
    ```
    $ python detector_camera.py ../latest_model/yolo-action.cfg ../latest_model/yolo-action_final.weights ../latest_model/action.data webcam savevideo
    ```
    若要儲存影片則加入`savevideo`
    
3. Output

    Detection FLAG
    ( ( Pose, Probility ) , ( x, y, w, h ) )
    FPS of video
    FPS of detection

    ex:
    ```
    FALL DETECTION 
    ('sitting 0.85', (86, 120, 78, 78))
    Detection Ends
    FPS of video: 25.00
    FPS of detection: 28.23
    ```
    
    
## J. Fall Detection
1. SVM
    |Datasets|Trainig|Testing|
    | :--------: | :--------: | :--------: |
    |Video Clip Number|100|43|
    |Accuracy|98.6594%|96.6964%|
    
2. Video
    |Datasets|Training|Testing|
    | :--------: | :--------: | :--------: |
    |Video Clip Number|100|43|
    |Accuracy|90% (90/100)|86% (37/43)|

## K. Transfer to TX1
1. ${YOLO_ROOT}/cfg/action.data
2. ${YOLO_ROOT}/cfg/yolo-action.cfg
3. ${YOLO_ROOT}/Final_3action/action.names
4. ${YOLO_ROOT}/Final_3action/0828-yolo-action.weights




