## 공항 지진감시 시스템

 기상청의 '지진정보알리미' 어플리케이션을 통해 정보를 수신하고 있음.
 
 <img width="450" height="300" src="/images/KMA_1.jpg"></img>
 
  **하루에 지진 규모 +5 이상의 지진이 다수가 발생, 감시 및 분석 업무의 과부화로 작용하고 있음**
 
 **지진의 최초 발생부터 보고까지의 인지시간 및 업무절차에 수십분이 소요되어 지진정보에 대한 적시성이 낮음**
 
 **2015년도 지진규모 +4.5 이상이 7163개가 감지되고 발표**되었으며, 
 하루에도 나오는 수십개의 지진정보 중에서도 항공사 취항공항에 영향을 줄 수 있는 중요한 지진정보만을 받을 수 있는 시스템이 부재.
 
 따라서, 취항 공항 주변에서 발생하는 지진들에 대해서 실시간으로 감시하며, Threshold(기준값)이상의 지진규모를 판단하고 Notification 생성하여 SMS통한 Alert이 가능한 시스템 구축.


#### 1. DataSource
: 지진에 관련된 데이터는 전세계적으로 공유가 잘되고 있는 편이다. GSN(Global Seismographic Network) 가 오래된 공유 채널인것으로 보이며,
요즘은 FDSN(Federation of Digital Broad-Band Seismograph Networks)를 통해 Quakeml 데이터 포멧으로 공유되고 있는것 같다.
그중에서 하루에도 Event(지진파를 분석하여 사람이 읽을 수 있을 정도로 가공된 지진정보) 수십건이 올라오는 두 곳
USGS와 EMSC에 대해서 간략한 정보를 소개한다.

 * USGS (미 지질조사국)
 
  현재 USGS에서는 세계에서 발생하는 모든 지진들의 정보를 웹으로 통해 보여주고 있다 (UPDATE 간격 5분).

 [USGS](http://earthquake.usgs.gov/earthquakes/map/)
 
 <img width="500" height="330" src="/images/usgs_1.jpg"></img>
 
 하지만 WEB을 통해서 실시간 감시를 해야하기 때문에 수동적이며 근무 여건에 따라 공항운영에 영향을 줄만한 지진에 대한 정보에 대해 인지가 늦다.
 
 USGS에서는 WEB 뿐만이 아니라 API를 통해서 외부에 제공되는 채널이 있다. (웹브라우저를 통해 열어보면 아래와 같다.)

 [최근 한달간 중요지진 : Significant EarthQauke in one Month](http://earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.quakeml)
 
 ![datafeed](/images/usgs_data_feed.jpg)
 
 * EMSC (유럽지중해지진센터)
  EMSC 에서도 세계에서 발생하는 거의 모든 지진들의 정보를 웹을 통해 보여주고 있다.

 [EMSC](http://www.emsc-csem.org/#2w)

 USGS와 마찬가지로 수동적인 통신채널이나, TW 네트워크(Taiwan 지진 네트워크)와 연동되어 있어 대만 지진의 경우도 잘 입수된다.
 
 queary url에 대한 설명은 [EMSC_quary](http://www.seismicportal.eu/fdsn-wsevent.html)
 
 해당 조합의 예시는 아래와 같다.
  http://www.seismicportal.eu/fdsnws/event/1/query?limit=10
 
 ![webdata](images/EMSC_.web.jpg)



#### 2. How to Recongnize danger or not

  문제점은 하루에도 발생하는 수십개의 지진에 대해서 공항운영에 지장을 줄 지진인지 아닌지에 대한 판단이 필요하다는 점이다.
  
  근무자가 실시간으로 계속 감시를 한다는것은 효율적이지 않다. 지진 발생지역이 공항인근이 아닌경우가 대부분이기 때문이다.
  
  또한, 공항 인근에서 발생한 지진을 공항근무자가 먼저 인지할 수 있으나, 각자의 업무수행중 지진정보를 '종합통제'에 전파하기까지는 수십분의 시간이 소요된다.
  
  하지만, 지진감시에 대한 경계를 늦출 수 없으며, 해당 지진감시를 시스템화하여 인지 부터 전파를 최대한 신속하게 할 필요가 있다.
  
  공항지진감시 프로그램을 통하여 USGS의 Data_Feed에서 나오는 지진의 위치와 인근 공항과의 거리를 계산함으로써 인지한다.
  
  **진원지와 공항과의 거리(NM) = USGS 지진정보GPS 좌표 ~ 취항공항 전체의 GPS 좌표**
  

#### 3. Notification

  일정 기준(예: 진도 규모 5.5, 근접 공항과의 거리 100NM)을 만족하는 지진이 감지 되었다고 한다면. 
  
  아래와 같은 해당 지진 정보에 대한 RAW_DATA를 사람이 인지 할수 있는 정보로 가공하여 전파 한다.
  
   ```xml
   <eventParameters publicID="quakeml:earthquake.usgs.gov/earthquakes/feed/v1.0/summary/significant_month.quakeml">
   <event catalog:datasource="us" catalog:eventsource="us" catalog:eventid="10005cc4" publicID="quakeml:earthquake.usgs.gov/earthquakes/feed/v1.0/detail/us10005cc4.quakeml">
   <description>
   <type>earthquake name</type>
   <text>Northern East Pacific Rise</text>
   </description>
   <origin catalog:datasource="us" catalog:dataid="us10005cc4" catalog:eventsource="us" catalog:eventid="10005cc4" publicID="quakeml:earthquake.usgs.gov/realtime/product/origin/us10005cc4/us/1461898830040/product.xml">
   <time>
   <value>2016-04-29T01:33:38.940Z</value> =========== 발생시각
   </time>
   <longitude>
   <value>-103.715</value> =========================== 위도
   </longitude>
   <latitude>
   <value>10.2705</value> ============================ 경도
   </latitude>
   <depth>
   <value>10000</value>
   <uncertainty>1700</uncertainty>
   </depth>
   <originUncertainty>
   <horizontalUncertainty>9100</horizontalUncertainty>
   <preferredDescription>horizontal uncertainty</preferredDescription>
   </originUncertainty>
   <quality>
   <usedPhaseCount>196</usedPhaseCount>
   <standardError>0.94</standardError>
   <azimuthalGap>62</azimuthalGap>
   <minimumDistance>8.803</minimumDistance>
   </quality>
   <evaluationMode>manual</evaluationMode>
   <creationInfo>
   <agencyID>us</agencyID>
   <creationTime>2016-04-29T03:00:30.040Z</creationTime>
   </creationInfo>
   </origin>
   <magnitude catalog:datasource="us" catalog:dataid="us10005cc4" catalog:eventsource="us" catalog:eventid="10005cc4" publicID="quakeml:earthquake.usgs.gov/realtime/product/origin/us10005cc4/us/1461898830040/product.xml#magnitude">
   <mag>
   <value>6.6</value> ============================== 지진 규모
   </mag>
   <type>mww</type>
   <originID>
   quakeml:earthquake.usgs.gov/realtime/product/origin/us10005cc4/us/1461898830040/product.xml
   </originID>
   <evaluationMode>manual</evaluationMode>
   <creationInfo>
   <agencyID>us</agencyID>
   <creationTime>2016-04-29T03:00:30.040Z</creationTime>
   </creationInfo>
   </magnitude>
   <preferredOriginID>
   quakeml:earthquake.usgs.gov/realtime/product/origin/us10005cc4/us/1461898830040/product.xml
   </preferredOriginID>
   <preferredMagnitudeID>
   quakeml:earthquake.usgs.gov/realtime/product/origin/us10005cc4/us/1461898830040/product.xml#magnitude
   </preferredMagnitudeID>
   <type>earthquake</type>
   <creationInfo>
   <agencyID>us</agencyID>
   <creationTime>2016-04-29T16:09:17.040Z</creationTime>
   </creationInfo>
   </event>
   ```
  
  위의 RAW_DATA 에서 가져올 요소는 '발생시각', '위도', '경도', '지진 규모(Magnitutde)' 이다.
  
  1차 필터링 기준은 **지진규모** (예 : 5.5 이상)
  2차 필터링 기준은 **취항 공항과의 거리** (예 : 100 NM 이내)
  
  이때 취항 공항과의 거리계산을 위해서는 '위도', '경도' 좌표가 필요하며.
  
  프로그램 내부 알고리즘을 통해서 취항공항들과의 모든거리를 계산하고 2차 필터링 기준 이내의 공항이 발생하면
  
  아래와 같은 정보로 가공한다. (예시)
  
  이때 '인근 공항' 에 대한 부분은 앞서 서술한 프로그램을 통해 계산된 값을 사용한다.
    
    **지진정보**
    
    발생시각 : 2016년 4월 29일 0133Z
    
    지진 규모 : 6.6
    
    인근 공항 : TPE NE 80NM, OKA WS 90NM
  

#### 4. Alert

  Notification 과정을 매 5분마다 반복한다(USGS DATA FEED UPDATE TIME).
  
  이때 가공된 지진정보를 전파해야 하나, 이메일과 같은 방식의 전파로는 한계가 있으므로 **SMS를 통한 전파**가 가장 효율적이라고 생각한다.
  
  합의를 통한 일정 기준 조건에 부합하면 SMS를 통한 Alert을 전파한다.
  
  SMS를 통한 Notification의 자동전파를 위해서는 '지진감시프로그램과' 연결 가능한 외부 서비스 업체가 필요하다.
  
   * 국내
   국내 휴대전화번호로는 아래 업체에서 SMS 전파가 가능하다.
 
   가격은 문자 한건당 20원
   
    [청기와랩](http://www.bluehouselab.com/sms/)
    <img width="450" height="300" src="/images/sms_3.jpg"></img>
   
   * 국외
   해외 휴대전화번호로는 Twillio 업체를 통한 SMS 전파가 가능하다.
 
   가격은 문자 한건당 0.0075~0.02$(한화 9원~24원))
   
    [TwillIO](http://https://www.twilio.com/sms)
    <img width="450" height="300" src="/images/sms_2.jpg"></img>
  

#### 5. 해당 서비스 개발 및 시스템 운영 비용
  
  **시스템 작동 개요도**
  
   ![OVERVIEW](/images/system-overview.png)
   
   일반 PC에서도 프로그램 작동 가능하나, 정전 및 운영체제(Windows)특성에 따라 불안정한 요인이 내제.
   
   프로그램 구동 안정성 과 라이센스 비용을 감안하여 리눅스 운영체제를 택하고 외부 클라우드 업체의 VPC에서 365/24 운영한다.
   (가격 대비 장애 대응이 용이)
   
   저렴한 VPC(가상PC)서비스 업체
   
   해외 
   [Conoha](https://www.conoha.jp) : 월 약 9,700원(900 엔) - 설치비 없음
   
   국내
   [Cafe24](http://www.cafe24.com/?controller=product_page&type=server&page=virtual_linux) : 월 5,500원(설치비 22,000원 별도)
   
   SMS Alert 적용
    
   한달간 약 110명 유효 정보 제공 5번 SMS 발송시 최대 발생요금 11000원
  
   **지진감시 시스템 최소 유지 비용 약 월 20,000~25,000원(+-)**
  

#### 6. 시스템 활성시 기대효과
 
  1. **빠른 인지가 가능(USGS 업데이트 5분 + SMS 전파 5분 이내 = 총 10분 이내)**
 
  2. **설정 기준(진진 규모 및 공항 인근 지역)에 따른 핵심적인 정보만을 전파.**
  
     > 정보 제공 기준 예시) 
 
     > +5 취항공항 인근 100NM 이내 -> 종합통제 커뮤니케이션용 휴대폰 SMS 전파
     
     > +7 이상 취항공항 인근 80NM 이내 -> 종합통제부문 전원 SMS 전파
 
  3. **24시간 감시 가능하므로 업무로드 감소 및 효율적인 감시.**
