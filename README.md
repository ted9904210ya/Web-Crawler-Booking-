# Web-Crawler-Booking-

如何抓booking每頁的旅館評價
安裝Katalon Selenium IDE(for Chrome)、chromedriver.exe

      Web-Crawler-Booking-/image.png
    
第一部分:翻頁和抓取旅館英文名字
用英文版是因為後面需抓出英文旅館名放進各連結裡
1.	按New新建case
2.	按Record開始錄製
3.	按下一頁
  
4.	按Stop後，再按Export
 
部分程式
chrome_path = "D:/cdriver/chromedriver.exe" 
#chromedriver.exe執行檔所存在的路徑
driver = webdriver.Chrome(chrome_path) #預設使用Chrome開啟
#英文版booking，使用 GET 方式下載普通網頁
driver.get("https://www.booking.com/searchresults.en-gb.html?aid=376396&label=booking-name-kdA......")
soup=BeautifulSoup(driver.page_source,'lxml')
#得到他的原始碼，並用BeautifulSoup剖析內容，lxml乃一好用的解析器
檢視原始碼
while len(soup.select('.sr_pagination_link')) > 0: #(找(各頁))這個長度是否 > 0
 
for hotelname in soup.select('.sr-hotel__name'): #把這頁裡面的連結全部找出來(旅館的英文名字)
 
第二部分:轉換成中文網站
觀察各旅館的網址，大部分都是以這形式表示
 
 
部分程式
turnname=hotelname.text #.text 可以取得不含 html 標籤的純文字內容。
lowname=turnname.lower() #把大寫轉為小寫 
lowname = "-".join(lowname.split()) #按字符串的每個空格分割，換成-連接
#用format，如把 t-hotel-kaohsiung 放進連結{}裡       commentsURL='https://www.booking.com/hotel/tw/{}.zh-tw.html?aid=304142; ...... '.format(lowname)
第三部分:抓取每個旅館的中文名和評價
title=soupc.select('#hp_hotel_name') #找出中文版booking的旅館名
 
score=soupc.select('.review-score-badge') #整體評分
 
for review in soupc.select('.review_item_review'): #找該旅館的評論    
if(review.select('.review_neg')): #負評
print("-:",review.select('.review_neg')[0].i.next_sibling) 
#.i.next_sibling，往旁邊爬留言，回傳同一階層的標籤。<i class="review_item_icon">눉</i>。即不要눉
    if (review.select('.review_pos')): #正評
print("+:",review.select('.review_pos')[0].i.next_sibling)
 
完整程式和結果

from selenium import webdriver # #import selenium模組
from bs4 import BeautifulSoup
import re
import time

chrome_path = "D:/cdriver/chromedriver.exe" #chromedriver.exe執行檔所存在的路徑
driver = webdriver.Chrome(chrome_path) #預設使用Chrome開啟
browser = webdriver.Chrome(chrome_path)
#英文版booking，使用 GET 方式下載普通網頁
driver.get("https://www.booking.com/searchresults.en-gb.html?aid=376396&label=booking-name-kdA**Qvoj_QoLR*zTmL6tAS193318466105%3Apl%3Ata%3Ap1%3Ap2%3Aac%3Aap1t1%3Aneg%3Afi%3Atiaud-146342138230%3Akwd-65526620%3Alp1012825%3Ali%3Adec%3Adm&sid=ce7c3baffa06863efbd9276e6ce5dc72&class_interval=1&dest_id=-2632378&dest_type=city&from_sf=1&group_adults=2&group_children=0&label_click=undef&no_rooms=1&raw_dest_type=city&room1=A%2CA&sb_price_type=total&search_selected=1&src=index&src_elem=sb&ss=%E9%AB%98%E9%9B%84%2C%20%E9%AB%98%E9%9B%84%E5%9C%B0%E5%8D%80%2C%20%E8%87%BA%E7%81%A3&ss_raw=%E9%AB%98%E9%9B%84&ssb=empty&rows=15&offset=15")
soup=BeautifulSoup(driver.page_source,'lxml') #得到他的原始碼，並用BeautifulSoup剖析內容，lxml乃一好用的解析器

while len(soup.select('.sr_pagination_link')) > 0: #(找(各頁))這個長度是否 > 0
    for hotelname in soup.select('.sr-hotel__name'): #把這頁裡面的連結全部找出來(旅館的英文名字) 
        turnname=hotelname.text #.text 可以取得不含 html 標籤的純文字內容。
        lowname=turnname.lower() #把大寫轉為小寫 
        lowname = "-".join(lowname.split()) #按字符串的每個空格分割，換成-連接
        #用format，如把 t-hotel-kaohsiung 放進連結{}裡
        commentsURL='https://www.booking.com/hotel/tw/{}.zh-tw.html?aid=304142;label=gen173nr-1FCAEoggJCAlhYSDNYBGjnAYgBAZgBMMIBCndpbmRvd3MgMTDIAQzYAQHoAQH4AQuSAgF5qAID;sid=ce7c3baffa06863efbd9276e6ce5dc72;dest_id=-2632378&dest_type=city&dist=0&group_adults=2&hapos=1&hpos=1&room1=A%2CA&sb_price_type=total&srepoch=1526739460&srfid=9f9a3ba5054a9e95a0c848fdd8692c6c52994233X1&srpvid=478c64811a890191&type=total&ucfs=1&#hotelTmpl'.format(lowname)  
        browser.get(commentsURL) 
        soupc=BeautifulSoup(browser.page_source,'lxml')
        title=soupc.select('#hp_hotel_name') #找出中文版booking的旅館名
        if (len(title)): #如果有找到則true
            print('------旅館名稱------')  
            print(hotelname.text,title[0].text) #後者是一個list
            print('------該旅館的評價------') 
            score=soupc.select('.review-score-badge') #整體評分
            print('整體評分: %s'%score[0].text)
            for review in soupc.select('.review_item_review'): #找該旅館的評論    
                if(review.select('.review_neg')): #負評
                    print ("-:",review.select('.review_neg')[0].i.next_sibling) 
                    # .i.next_sibling，往旁邊爬留言，回傳同一階層的標籤。<i class="review_item_icon">눉</i>。即不要눉
                if (review.select('.review_pos')): #正評
                    print ("+:",review.select('.review_pos')[0].i.next_sibling)  
    time.sleep(5) #停一下，避免造成對方主機的負擔和被鎖
    driver.find_element_by_link_text("Next page").click() #按下一頁
    soup=BeautifulSoup(driver.page_source,'lxml') #重新解析下一頁網站的內容
driver.close() #關閉網站
browser.close()
 
 
------旅館名稱------

Eden Exoticism Planet
 
伊甸風情精品旅店

------該旅館的評價------
整體評分: 
8.2

-: 床鋪過軟；棉被容易拖到地上；床的正上方有樑柱造成壓力
+: 服務周到，態度也很親切
-: 沒有
+: 方便性停車
-: 312 _浴室門 沒辦法關,會自動打開,洗澡要一直推門,很不方便
-: 住宿價格高卻不含早餐
+: 房間大且乾淨
-: 房間的主題很"特別"
+: 裡面沒有附早餐，但是泡麵可以無限免費叫和吃到飽這個很划算
頗推薦的
-: 沒主動告知沒有早餐！如果要⋯需自費
-: 浴室疑似抽風機還是什麼聲音很吵、廁所電燈關了還一直爆閃都關不掉、電影看到一半會卡住斷線，要重新連線，而且不止一次。
-: 有蚊子
+: 地方設計不錯
+: 房型能自己選擇，讓我每次入住的時候，都能住不同的房型，真是太棒囉👍
-: 早上電視評道完全不能使用，時間到前也沒有電話通知快到時間
+: 房型，乾淨度都蠻好的，空氣也乾淨
-: 反應沒熱水  櫃台一問三不知  還說他也不知道……
+: 離夜市近
-: 浴缸旁的電視只有三個頻道可以看
+: 房間舒適溫馨 服務人員服務超好
-: 價格偏高
+: 乾靜.方便且近瑞豐夜市
-: 風格平平
+: 地段
-: 完全沒有不喜歡
+: 櫃檯服務超級好 房間超級乾淨
------旅館名稱------

Hakodate Motel
 
函館經典汽車旅館

------該旅館的評價------
整體評分: 
7.6

-: 可以在便宜一點
+: 環境舒適，空間大，空調很棒
+: 乾淨
-: 菸味很重，有蚊子
+: 地點
+: 地點不錯，附近很多吃的東西，買宵夜方便！
+: 非常好
-: 房間8點可入住，我9點到竟然先把房間給其他休息的客人，還需要我等待休息客人出場，理由竟然是我沒有8點到，爛透了，第一次遇到這種業者
+: 地點還算方便，外面就是捷運，離巨蛋（瑞豐夜市進）
-: 價格
+: 一進房內的溫度非常他媽的舒適（其他的汽旅像冰遺館凍庫一樣
-: 沙發前面沒有電視
+: 好
-: 設備老舊，潮濕
+: 地點方便，乾淨
-: 房間的燈壞, 換了另一間也是一樣。
+: 房間很大、職員服務態度很好! 讚!
-: 門一打開時，異味稍重
-: 一進門霉味重，設備上有灰塵，床單棉被不乾淨 塵蟎多到皮膚很癢！
冰箱有污漬未清乾淨...
跟照片上看見的完全不一樣！
-: 設備老舊 無保養修繕 清潔不徹底
+: 無
-: 房間潮濕都是煙味
+: 交通方便
.
.
.
.
.
.

 
