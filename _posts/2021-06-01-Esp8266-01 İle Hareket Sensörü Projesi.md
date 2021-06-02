---
layout: post
title: "Esp8266-01 + IFTTT + PIR Sensor Kullanarak Hareket Sensörü Projesi"
excerpt: ""
tags: [Esp8266,PIR sensor,Motion Sensor,Hareket Sensörü,IFTTT,3D Printer,3 Boyutlu Yazıcı,DIY Project,Deep Sleep,Wifi Manager,Static IP]
categories: [Esp8266-01] 
date: 2021-06-01 11:39:00
comments: true
share: true 
image:  
  feature: 
---

![AsenkronServer](https://drive.google.com/uc?export=view&id=11bZA4BDXUXLq8-hZP5KknDR4OOC4TX9b)

### Proje Hakkında

Herkese merhabalar, bu blog yazısındaki projede uzun süre çalışabilen hareket algıladığında Google Tablolara (Google Sheets) kayıt tutabilen ve aynı zamanda IFTTT üzerinden mobil cihazınıza bildirim gönderebilen bir proje gerçekleştireceğim.

#### Proje Gereksinimleri 

* Esp8266-01 
* HC-SR501 PIR Sensor (Hareket Sensörü)
* 1 Adet Toggle Switch
* Bir kaç bağlantı kablosu
* 2 adet AA pil
* [Ürün şasisi (3d printer ile çıkacak .stl formatlı dosyalar)](https://www.thingiverse.com/thing:4840469){:target="_blank"}
* 4 adet 3 mm vida



#### Projede Kullanılan Bazı Kütüphane Ve Platformlar

* [IFTTT](https://ifttt.com/){:target="_blank"}
* [Google Sheet](https://www.google.com/sheets/about/){:target="_blank"}
* [WifiManager Kütüphanesi](https://github.com/tzapu/WiFiManager){:target="_blank"}


### Proje İçin Kullanılan Platformların Tanıtılması Ve Kurulumu

#### IFTTT Nedir ?

![Tittle-Photo](https://drive.google.com/uc?id=1NgsrsokYAW9vGFxjRrjeGAEee_HoS7J8)

IFTTT “If This, Then That” anlamına gelir. IFTTT hem bir websitesidir hem de bir mobil uygulamaya sahiptir. Platformun sağladığı şey uygulamaları birbirine bağlamak. Kısacası servislerin ve cihazların birbirleri ile konuşması diyebiliriz. Örneğin android telefonunuzda bir arama yaparsanız, o aramanın günlüğü bir Google e-tablosuna eklenir. Bunu IFTTT size sağlar. Veya evde telefonunuzu kaybettiğiniz zaman google home mini cihazınıza "Benim telefonum nerede ?" sorusunu iletirseniz telefonunuzu çaldıracaktır. Bir başka örnek ise toplantı saatiniz geldiğinde philips hue ampüllerinizi yeşil renkte yakabilirsiniz. Bu ve bunun gibi senaryoları IFTTT ile yapmanız mümkün. 
Bu projede IFTTT aracılığı ile aşağıdaki olaylar gerçekleştirilecek;
 
* Hareket sensörü eğer bir hareket algıladıysa bu hareketin zamanını ve "Hareket Algılandı !" yazısını Google Tablolara (Google Sheets) kayıt etmesini gerçekleştireceğiz.

* IFTTT notification applet'i aracılığı ile IFTTT kurulu olan mobil cihazınıza hareketin algılandığını bildirim olarak göndereceğiz.

<br>

#### Google Tablolar (Google Sheets) Nedir ?

<br>

![Tittle-Photo](https://drive.google.com/uc?id=1pYliib1K-MKSHgHeTYScSi4_-xigr6C0)

<br>

Google E-Tablolar, e-tablolar oluşturmak ve düzenlemek için kullanılan, ücretsiz web üzerinden kontrol edilebilen bir programdır. Google E-Tablolar, Google Dokümanlar ve Google Slaytlarla beraber Google’ın Drive içerisinde bir parçası olarak gösterilebilir. Microsoft’un Office programlarına benzer bir yapıdadır. Microsoft Office uygulamalarına göre sistemin artıları var. Uzaktan birden fazla sayıda cihazla kontrol, grup olarak çalışma gibi artılarla beraber Google tabloların bunların dışında birden fazla avantajlı özelliği vardır.

<br>

#### Wifi Manager Kütüphanesi Nedir ?

WifiManager bir wifi yönetim kütüphanesidir. Bu kütüphane esp8266-01 kartının bağlanması gereken wifi ağını kod içerisinde tanımlamak yerine cihazın kullanıcıya sunmuş olduğu web sayfası sayesinde dışarıdan girme özelliği sunuyor. WifiManager kütüphanesinin çalışma şeklinin akış diyagramı aşağıda görülmektedir.

![Tittle-Photo](https://drive.google.com/uc?id=1v6B51nMlziV2WTL1qvUUdXD2kVETkwVE)

<br>

### Deep Sleep Nedir ?

<br>

![Tittle-Photo](https://drive.google.com/uc?id=1pqeKRWUHagcQ5pqNvR3e9bse5vjrpPHq)

Pil ile çalışan bir esp8266 ile proje yaptıysanız veya nodeMCU kartınızı bir güç bankasına bağladıysanız bir süre sonra, özellikle projede wifi kullanıyorsanız pilin uzun sürmediğini fark edeceksiniz. Özellikle Iot cihazlarda ve mobil çalışan cihazlarda bu durum büyük problemdir. Ancak DeepSleep buna büyük anlamda çözüm getirir. Wifi bağlantısı cihazın daha fazla akım çekmesine sebep olur. Bu nedenle pilin enerjisini hızlıca tüketmesi kaçınılmazdır. Bu durum Deep Sleep sayesinde kolayca ve büyük bir oranla aşılabilir. DeepSleep mikroişlemci tarafında bir özelliktir.

 Varsayalım bir hava istasyonu projesi yaptınız. Dht11 sıcaklık ve nem sensöründen gelen verileri esp8266 kartı aracılığı ile internete çıkarıyorsunuz ve her saat başı bu işlemi yaptırıyorsunuz. Varsayalım her bir işlem sadece 5 saniyede kendini tamamlasın. Cihaz bir gün içerisinde sadece 24 kez bu işlemi gerçekleştirecek ve toplamda 120 saniye yani 2 dakika çalışacak. Bir günde 24*60 = 1440 dakika var. 
 1440 - 24 = 1416 dakika cihaz boşuna çalışmış olacak. Burada DeepSleep imdadımıza yetişiyor. DeepSleep sayesinde cihaz görevi bittikten sonra derin uykuya geçerek işlemciyi kapatıyor ve bir sonraki göreve gelene kadar uyanmıyor.

Burada esp8266 modülünü uyandırmak için [RTC](https://en.wikipedia.org/wiki/Real-time_clock){:target="_blank"} kullanılabileceği gibi modül üzerinde bulunan enable pinine 3v kare sinyal gönderilerek de uyandırılabilir. 

Bu projede cihazı uyandıran etken hareket sensöründen esp8266 üzerindeki enable pinine gelen ve yaklaşık olarak 20 saniye süren 3v kare sinyaldir.

![Tittle-Photo](https://drive.google.com/uc?id=1Hab03AvicGZn5WiPFKJaqE8WajHTMnSq)

Yukarıdaki tabloda görüldüğü gibi deepsleep modunda modül RTC hariç diğer her şeyi (Wifi,System Clock, CPU) kapatarak yaklaşık 20 mikroamperlik çekilen akım miktarına ulaşıyor. Tabloda bu modül için 3 farklı sleep modu bulunmaktadır. Bunlardan en az güç tüketen sleep modu deepsleep'dir.

## Projenin IFTTT Kurulumu
Bu kısımda proje için gerekli IFTTT ayaralarının kurulumu anlatılmaktadır.  
#### IFTTT Google Tablolar Bağlantısı

IFTTT web sitesine [buradan](https://ifttt.com/) ulaşabilirsiniz. Siteye google hesabınızla kayıt olup giriş yaptıktan sonra sırasıyla aşağıdaki adımları izleyerek google tablolara bağlanarak hareket algılandığında tabloya kayıt yaptırabiliriz.
 
Ana sayfada "create" butonuna bastıktan ve if kutucuğu içerisindeki "add" butonuna bastıktan sonra servisler içerisinden webhook servisini seçiyoruz.

<br>

**İlk olarak IFTTT sitesinde ana sayfada iken sağ üst köşedeki "Create" butonuna tıklıyoruz.**
![IFTTT Create](https://drive.google.com/uc?export=view&id=1iLJ4JJd7-m1fLEu8Y1wJM15RXsZlJXZF) 

<br>

**Gelen sayfada if kutucuğu içerisindeki "add" butonuna tıklıyoruz.**
![IFTTT IF THIS THAN THAT](https://drive.google.com/uc?export=view&id=1B8G0RTALe21FgpL3oJmEuasAai2cAXGc)

<br>

**event name kısmına Iot_Motion_Sensor yazıyorum bu benim yazdığım programın bir gereği siz başka bir şey yazabilirsiniz ancak az sonra göstereceğim link içerisinde bunu belirtmeniz gerekir. create trigger butonuna tıkladıktan sonra then kutucuğunun içerisindeki add butonuna tıklıyoruz ve tekrardan yeni bir servis arıyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=18sMi6XtDlcHowGZ0iyk4smMEw91zyjsN)

<br>

**Google sheets servisini arıyoruz ve seçiyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1jHGeHfhtW_ecD_TgvVwF99dPzqlFIO0C)

<br>

**Daha sonra add row to spreadsheet seçeneğine tıklıyoruz. Bunu seçmemizin sebebi tabloya her yeni bir bilgi geldiğinde yeni bir satıra ekleniyor olmasıdır.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1CnbDMIM9bwLpFpeo8HDDd8WhYBOnJyEa)

**Ayarlar aşağıdaki gibi olmalıdır eğer isterseniz tablo adını değiştirebilirsiniz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1HWE3MFWzSjnRSMarpMJIO2zsxnvD8CME)

<br>

**Triggerdan önceki eğik çizgiden itibaren sona kadar olan kısmı kopyalayarak kod içerisindeki ilgili kısma yapıştırıyoruz. Burada test it butonuna basarak tabloya kayıt işlemini test edebilirsiniz. Eğer bu aşamaya kadar her şey doğru gittiyse google drive hesabınız içerisinde bir tablo oluşturacak ve içerisinde bir kayıt belirecektir.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1lmpzAYEXq-tjezLYjkVUDAoIKOXzS7Tp)

#### IFTTT Bildirim Ayarları 

Bu kısımda IFTTT mobil uygulaması aracılığıyla telefona bildirim gönderme özelliğini gerçekleştireceğiz. 

**Yukarıdaki işlemle aynı olarak create butonuna basarak yeni bir senaryo oluşturacağız. Daha sonra if kutucuğundaki add butonuna tıkladıktan sonra webhook'u seçiyoruz. Aşağıdaki gibi textbox kısmına notification yazıp create trigger butonuna tıklıyoruz.** 

![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1wkm9n_knZmtL2VYaVRRS5LRsVxHbKdnK)

**Then kutucuğu içerisindeki add butonuna tıklayıp çıkan pencerede notification appletini aratarak buluyoruz ve daha sonra tıklıyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1MfW7cZt85L6TZKJ-CtEq8YvpAqq65M0h)

**Bu kısımda iki seçenek geliyor. Biz "Send a Notification From The IFTTT App" seçeneğine tıklayacağız.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1FP2x8NdCgMiMY0yMHHxEz5yJR8uP1q6h)

**Son olarak bildirimin içeriğini çıkan pencerede girdikten sonra create action butonuna basarak senaryomuzu oluşturmuş oluyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1ni5QuGvwtegBdMvGtUUGGoPY2yBjR7QY)

**Bu kısımdan sonra google tablolara ekleme yaptığımız bölümde olduğu gibi linki alarak kod içerisindeki ilgili bölüme yapıştırıyoruz.**

<br>

## Proje Devre Şeması

![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1FD-H2XcboMtBJ9XSJiew-ljU0Qpym0FJ)

<br>

## Proje Kaynak Kodları

```c
#include <ESP8266WiFi.h>
#include <ESP8266HTTPClient.h>  
#include <DNSServer.h>
#include <WiFiManager.h> 

void Send_Notification_From_IFTTT(); 
void Start_To_Wifi_Connection();
void makeIFTTTRequest(String status); 

// *********IFTTT****************

const char* server = "maker.ifttt.com";

const char* resource = "google tablolar kayıt linki"; // Google tablolar kayıt linki

HTTPClient http;

void setup() {
  Serial.begin(9600);
  WiFiManager wifiManager;      
  wifiManager.autoConnect("Mlock Motion Security"); // "Mlock Motion Security" adında bir AP oluşturur.
}

void loop() {
  Send_Notification_From_IFTTT();
  makeIFTTTRequest("Hareket algılandı!");
  ESP.deepSleep(0);                                 // Derin uykuya geçirir.
}

void Send_Notification_From_IFTTT()    
{ 
  String url = "http://maker.ifttt.com/bildirim linki"; // Bildirim senaryosunun linki
  http.begin(url);
  http.GET();
  http.end();
}

void makeIFTTTRequest(String status) {
  WiFiClient client;
  Serial.print("Connecting to "); 
  Serial.print(server);
  
  int retries = 5;
  while(!!!client.connect(server, 80) && (retries-- > 0)) {
    Serial.print(".");
  }
  Serial.println();
  if(!!!client.connected()) {
    Serial.println("Failed to connect...");
  }
  
  Serial.print("Request resource: "); 
  Serial.println(resource);

  String jsonObject = String("{\"value1\":\"") + status + "\"}";
                      
  client.println(String("POST ") + resource + " HTTP/1.1");
  client.println(String("Host: ") + server); 
  client.println("Connection: close\r\nContent-Type: application/json");
  client.print("Content-Length: ");
  client.println(jsonObject.length());
  client.println();
  client.println(jsonObject);
        
  int timeout = 5 * 10; //5 seconds             
  while(!!!client.available() && (timeout-- > 0)){
    delay(100);
  }
  if(!!!client.available()) {
    Serial.println("No response...");
  }
  while(client.available()){
    Serial.write(client.read());
  }
  
  Serial.println("\nclosing connection");
  client.stop(); 
}
```

<br>

## Sonuç :) 

{% include youtube.html id='HDxh7Rv0afM' %}

<br>