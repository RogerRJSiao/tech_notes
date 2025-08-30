

1. 導入套件cv2，開啟及寫入圖檔
```
import cv2
img = cv2.imread("your-pic.jpg")
h,w,c = img.shape   #圖高、圖寬、通道數
cv2.inshow("視窗名稱", img)
cv2.imwrite("result.png", img)
```

2. 調整圖像大小、彩色轉灰階、負片、馬賽克
```
resized_img = cv2.resize(img, (400, 300))
crop_img = img[y:y+h, x:x+w]    #opencv讀取的影像陣列是numpy，以像素切片
grey_img = cv2.imread("your-pic.jpg", cv2.IMREAD_GRAYSCALE)
rgb_img = cv2.cvtcolor(img, cv2.COLOR_BGR2RGB) #cv2初始讀取是BGR
neg_img = cv2.bitwise_not(img) #產生負片
```
- 抓取影像資訊：[roboflow](https://roboflow.com)、小畫家
- 加入中文字 from PIL import ImageFont, ImageDraw, Image 
- 馬賽克 https://steam.oxxostudio.tw/category/python/ai/opencv-mosaic.html
    - 先用resize()把圖縮小，再resize()把圖放大，最後置換原本位置影像。

3. 調整圖像by imutils
```
import cv2
import imutils
img = cv2.imread("your-pic.jpg")
rotated_img = cv2.rotate(img, angle=90)
translated_img = cv2.translate(img, 25, -50)
flipped_img = cv2.flip(img, -1)
```

4. 影像偵測
```
#取得影像
cap = cv2.VideoCapture(0, cv2.CAP_DSHOW) ＃0=內建第一台，1=外接第一台
#取得影格尺寸
w = cap.get(cv2.CAP_PROP_FRAME_WIDTH)
h = cap.get(cv2.CAP_PROP_FRAME_HEIGHT)

frame_cnt = 0
while(cap.isQpened()):
    ret, frame = cap.read():    #ret表示有無影格，frame影像內容
    if not ret:
        break
    #顯示圖像
    cv2.imshow("彈窗名稱", frame)
    #計算影格數
    frame_cnt = frame_cnt + 1
    #鍵盤偵測
    if cv2.waitKey(1) & OxFF == ord('1'): #每1毫秒更新1次，直到按下1結束
        break
print("總影格數 ＝ ", frame_cnt)
cap.release()
cv2.destroyAllWindows()
```
- 計算 FPS = 總影格數 除以 總秒數

5. 爬蟲串流影像
- STEAM教學網：https://steam.oxxostudio.tw/category/python/spider/mjpeg.html
- 彰化市中山路

6. 影像辨識-numpy陣列格式
    - 多維度陣列
    - x軸是axis=0，y軸是axis=1。先列數、再欄數。
```
#指定型別
a = np.array([[1,2,3],[4,5,6]], type=float)
#顯示陣列元素型別
a.dtype
#元素總數
a.size
#每個維度元素數目
a.shape
#最大維度數
ndim
#變更維度
b = a.reshape(3, 2)
#攤平成單一維度，投入資料給AI
b = a.flatten()
#切片
c = a[1:3]
#列合併
c = np.concatenate((a,b), axis=0) 
#欄合併
c = np.concatenate((a,b), axis=1) 
```