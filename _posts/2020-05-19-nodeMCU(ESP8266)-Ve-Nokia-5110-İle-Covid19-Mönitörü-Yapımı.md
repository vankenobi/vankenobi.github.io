---
layout: post
title: "NodeMCU Ve Nokia 5110 Ekranı İle Covid-19 Monitörü Projesi"
excerpt: ""
tags: [NodeMCU,Esp8266,IOT,COVID-19,Corona Virüs,Worldometer,ThingSpeak,Nokia5110,Iot,InternetOfThings,Nesnelerin İnterneti]
categories: [nodeMCU]
share: true
date:   2020-05-19 20:15:02
comments: true
image: 
  feature: https://drive.google.com/uc?id=1ws7zkULNMkbVfTmeUVNx6AGVo1Wct5z7
---



Bu projede 1.6" Nokia 5110 ekranı ile nodeMCU kartını kullanarak anlık ülkemizdeki corona virüs istatiklerini gösterebileceğimiz bir monitör yaptım. Anlık olarak ekran üzerinde; toplam vaka sayısı, vefat eden kişi ve iyileşen hasta sayılarını vermektedir.

>  Projeye başlamadan önce proje içerisinde veri transferi yapmamıza yarayacak ThingSpeak sitesinden biraz bahsetmek istiyorum. [ThingSpeak](https://thingspeak.com/) ücretsiz olarak hem arduino sensörleri ile okuduğunuz değerleri yazmaya, hem de yazdığınız değerleri telefonunuzda, bilgisayarınızda veya diğer akıllı cihazlarınızda anlık görebilmenize ve okuyabilmenize imkan tanıyan bir platform. API için kanal oluşturuyorsunuz ve sadece sizin bildiğiniz şifreler ile (API key) verilerinizi grafik üzerinde takip edebiliyorsunuz.Sadece veri gönderip okumaktan farklı olarak internetteki bir veriyi API ile IOT cihazlarınıza gönderebilirsiniz. Bizim bu projede yaptığımız da tam olarak bu.

### Malzeme Listesi

* <span style="font-size:18px">NodeMCU(ESP8266) Kartı</span> 
* <span style="font-size:18px">Nokia 5110 ekranı</span> 

### COVID-19 Verilerini Çekme
>**Worldometer sitesinden COVID-19 verisini çekeceğimiz ülkenin sayfasına gitmemiz gerekiyor. Bunun için [Worldometer](https://www.worldometers.info/coronavirus/) sitesine gidiyoruz.**


![Worldometer](https://drive.google.com/uc?id=1guj430F_Nb7h785DPZEanxTvHflQ-eBT)

>**Siteye girdikten sonra sitenin alt kısmında bulunan tabloda verisini çekmek istediğimiz ülkenin linkine tıklyoruz.**

<br />



![Worldometer](https://drive.google.com/uc?id=19K-hrviACswPIWBRbzcdphvZafJpXjUm)
{:class="img-responsive"}

>**Karşımıza gelen sayfada klavyenizden "F12" tuşuna basarak veya mouse'nuzla sağ tık atıp inceleye basarak sayfa kaynağının görüntülendiği ekranı açıyoruz.**

<br />


![Worldometer](https://drive.google.com/uc?id=1Pmy21HS2mZHAZDgyAM_6LGFBoe1u6UJS)
{:class="img-responsive"}

>**Bu kısımda mavi alan içerisinde gösterilen kısma geliyoruz ve mouse ile sağ tık atıp /Copy/Copy XPath seçeneğine tıklıyoruz. Bu kopyalanan veriyi ThinkSpeak ekranında kullanacağız.**

<br />



### ThingSpeak Ayarları

![Worldometer](https://drive.google.com/uc?id=1G85QbMpMbS_mFTT3uoJ336xzuQDUBquY){:class="img-responsive"}

>**Projenin başında bahsettiğim gibi projemizde veri transferini sağlayacak olan ThingSpeak sitesine kayıt olmamız gerekiyor. Eğer kayıt olduysanız site anasayfasında üstte bulunan Apps butonuna gelin ve altta çıkan seçeneklerden ThingHTTP'ye tıklayın.**

<br />

![Worldometer](https://drive.google.com/uc?id=1t6lvGaHPbXU-ZXO5avTyAZ1dD1Eb3-et){:class="img-responsive"}

>**ThingHTTP'ye tıkladıktan sonra karşımıza çıkan sayfada NewThingHTTP butonuna basıyoruz.**

<br />

![Worldometer](https://drive.google.com/uc?id=19k5UP3FxbW2rNiuGdEFC5_oK6VMDhKfx){:class="img-responsive"}

>**Karşımıza gelen ekranda Name yazan kısma istediğinizi yazabilirsiniz. Ben Türkiye'deki COVID-19 verilerini çekmek istediğim için "Turkey" yazdım. Parse String kısmına kopyaladığımız XPath'i yapıştırıyoruz ve "Save ThingHTTP" butonuna basıyoruz.**

<br />

![image-title-here](https://drive.google.com/uc?id=1y4hhlLZ0EvMTGCDuykayWZeYk9kji3ZU)
{:class="img-responsive"}

>**Kırmızı dikdörtgen ile işaretlenmiş alan içerisindeki API'yı kopyalıyoruz.**

<br />



![image-title-here](https://drive.google.com/uc?id=1LKcq7rvfNLO-5H7JSJg6qAzP8AE6msZ4)
{:class="img-responsive"}

>**Kopyaladığımız API'yı yukarıdaki fotoğraftaki kırmızı alan içerisindeki api_key kısmına yapıştırıyoruz. Bu arada ağ adı ve ağ parolası yazan kısma bulunduğunuz ortamdaki wifi ağının adını ve parolasını girmeyi unutmayınız.**

### Proje Kaynak Kodu

{% highlight cpp %}
#include <graphicsLCD.h>
#include <ESP8266WiFi.h>                                                  
#include <ESP8266HTTPClient.h>
                                                                       
const char* ssid = "AğAdı";                                        
const char* password =  "AğParolası"; 
const char* host = "api.thingspeak.com";                                     
const int httpPortRead = 80;                                           

#define lcd_RST_pin D8
#define lcd_CS_pin D7
#define lcd_DIN_pin D5
#define lcd_CLK_pin D4

graphicsLCD lcd(lcd_RST_pin,lcd_CS_pin,lcd_DIN_pin,lcd_CLK_pin);

const char* url1 = "/apps/thinghttp/send_request?api_key=Api key yapıştıracağınız kısım";
 
String toplam_vaka,vefat_eden,iyilesen,Data_Raw,Data_Raw_1,Data_Raw_2,Data_Raw_3;  
int indx;
WiFiClient client;                                                        
HTTPClient http; 


String _parse(String value)
{
  String kelime ;
  for(int i=0; i < value.length();i++)
  {
    if(value.substring(i,i+1) != ",")
      kelime += value.substring(i,i+1);  
  }
  return kelime;
}

void setup() {
  Serial.begin(9600);
  lcd.begin();  
  lcd.clear();                                                         
  WiFi.disconnect();                                                
  delay(1000);                                                           
  WiFi.begin(ssid, password);                                               
  Serial.println("Wifi agina baglandı.");                                       
  Serial.println(WiFi.localIP());
}  

 void loop()                                                                      
{
    if( http.begin(host,httpPortRead,url1))                                              
      {
        int httpCode = http.GET();                                                                  
        if (httpCode > 0)                                                              
        {
          if (httpCode == HTTP_CODE_OK || httpCode == HTTP_CODE_MOVED_PERMANENTLY) 
            {   
              lcd.line(1);
              lcd.column(36);
              lcd.print("Turkey");
              Data_Raw = http.getString();  
              
              Data_Raw_1 = Data_Raw;                                  
              indx = Data_Raw_1.indexOf(">");                                                      
              Data_Raw_1.remove(0,indx+1);                        
              indx = Data_Raw_1.indexOf("<");                                                     
              Data_Raw_1.remove(indx,Data_Raw_1.length());                        
              toplam_vaka=Data_Raw_1;
              toplam_vaka = _parse(toplam_vaka); 
              lcd.line(4);
              lcd.print("Toplam vaka: ");
              lcd.print(toplam_vaka.toInt()); 
              lcd.print("k");


              Data_Raw_2=Data_Raw;
              indx = Data_Raw_2.indexOf("<span>");
              Data_Raw_2.remove(0,indx+6);
              Data_Raw_3=Data_Raw_2;
              indx = Data_Raw_2.indexOf("</span>");
              Data_Raw_2.remove(indx,Data_Raw_2.length());
              vefat_eden=Data_Raw_2;
              vefat_eden = _parse(vefat_eden);
              lcd.line(5);
              lcd.print("Vefat eden: ");
              lcd.print(vefat_eden.toInt());  
  

              indx = Data_Raw_3.indexOf("<span>");
              Data_Raw_3.remove(0,indx+6);
              indx = Data_Raw_3.indexOf("<");
              Data_Raw_3.remove(indx,Data_Raw_3.length());
              
              iyilesen=Data_Raw_3;
              iyilesen = _parse(iyilesen);
              lcd.line(6);
              lcd.print("Iyilesen: ");
              lcd.print(iyilesen.toInt());  
                                                                         
            }
        }
        else 
        {
          Serial.printf("[HTTP] istegi alinamadi, Hata: %s\n", http.errorToString(httpCode).c_str());
        }
        http.end();                                                                 
      } 
      else 
      {
        Serial.printf("[HTTP} baglanılamıyor.\n");
      }
    delay(5000);

    while (WiFi.status() != WL_CONNECTED)                                      
      { 
        WiFi.disconnect();                                                        
        delay(1000);                                                             
        WiFi.begin(ssid, password);                                              
        Serial.println("Wifi agına tekrar baglaniyor.");       
        delay(10000);                                                             
      }
} 
String prs(String value)
{
  for(int i=0; value.length();i++)
  {
    Serial.println(value.substring(i,i));
  }
}
{% endhighlight %}

### Fritzing Şeması


![image-title-here](https://drive.google.com/uc?id=1r8PjmfXzo9z4W8N3CiNr9mqrVJJ05_qO)
{:class="img-responsive"}

### Sonuç


![image-title-here](https://drive.google.com/uc?id=1pOMffX0ygMv5w6Fm2dVfhKiE38HyHZ32)
{:class="img-responsive"}








