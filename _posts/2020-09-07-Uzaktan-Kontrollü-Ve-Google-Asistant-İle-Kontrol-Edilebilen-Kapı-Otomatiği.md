---
layout: post
title: "Rfid Ve Google Asistant İle Açılabilen Kapı Otomatiği"
excerpt: ""
tags: []
categories: [nodeMCU] 
date: 2021-03-27 11:39:00
comments: true
share: true 
image:  
  feature: 
---

![Tittle-Photo](https://drive.google.com/uc?id=1xHLijZcZAthUZLFhfuBar0_oeQHLvsBL)

# Proje Hakkında

Herkese merhabalar, bu blog yazısında Google asistan, rfid kart(anahtarlık), Blynk üzerinden açılabilen aynı zamanda google tablolar üzerinde kimin giriş yaptığını ve ne zaman girdiğini log tutabilen kapı otomatiği projesinden bahsedeceğim.

#### Proje Gereksinimleri 

* RC522 RFID NFC modülü
* NodeMCU geliştirme kartı 
* Yeşil, sarı, kırmızı (3 adet) 5mm led
* 1 adet buton
* 1 adet 220V röle
* LM2596 ayarlanabilir voltaj düşürücü
* 2 Pin yeşil klemens
* Delikli pertinaks
* [3D printerdan çıkarılması gereken STL dosyaları](https://www.thingiverse.com/thing:4808529){:target="_blank"}

#### Projede Kullanılan Platformlar

* [IFTTT](https://ifttt.com/){:target="_blank"}
* [Blynk](https://blynk.io/){:target="_blank"}
* [Google Sheet](https://www.google.com/sheets/about/){:target="_blank"}

# 1. Adım Proje İçin Blynk Kurulumu

#### Blynk Nedir ?

Blynk nesnelerin interneti için tasarlanmış bir IOT platformudur. Donanımı uzaktan kontrol edebilir,donanıma bağlı sensörlerden gelen veriyi okuyabilir, verileri depolayabilir, görselleştirebilir ve diğer birçok şeyi yapabilen bir IOT platformudur. 

#### Blynk Kurulumu ? 

Google Play Store üzerinden Blynk uygulamasını android cihazınıza [bu linkten](https://play.google.com/store/apps/details?id=cc.blynk&hl=tr) ulaşarak kurabilirsiniz. Cihazınıza kurduktan sonra bir blynk hesabı oluşturmalısınız. Hesap oluşturduktan sonra "New Project" butonuna tıklayarak yeni bir proje oluşturmalısınız.

![Blynk Google Play](https://drive.google.com/uc?id=1SGSmGWn1AgqbUdxsJi8LUmAtFHZQ8fiN)

"New Project" butonuna tıkliyoruz|Proje adı, kartı ve bağlantı tipini seçerek "create" butonuna tıklıyoruz butona tıkladığınız anda kod içerisinde kullanacağımız auth kodu emailinize gönderilecek.
:-------------------------:|:-------------------------:
![New Project](https://drive.google.com/uc?export=view&id=1xeOLmsgKh12LBAX_sXz6e-fiD6r8TSP8)  |  ![New Project Ayarları](https://drive.google.com/uc?export=view&id=1xV83gX9S5CM69jvV2zPhj5L-ez34CA02)

<br>

Widget kutusundan bir button ekliyoruz|Eklenen butonun pinini bir sanal pin olan V2 olarak değiştiriyoruz
:-------------------------:|:-------------------------:
![Button](https://drive.google.com/uc?export=view&id=1xlu-KwwMM17ob2y_DbUzFxnK6zsWlyGI)  |  ![Button Ayarları](https://drive.google.com/uc?export=view&id=1xhAAgSCMZNqoEnDWMeSvg0m6eD5ZBe54)

<br>

Widget kutusundan bir notification ekliyoruz|Eklenen notification widget'ın ayarları
:-------------------------:|:-------------------------:
![Button](https://drive.google.com/uc?export=view&id=1xgV8GeeIrfvCDjl0rTl9BZbxcfxCe9kn)  |  ![Button Ayarları](https://drive.google.com/uc?export=view&id=1xgtCliMMW6ngqb3uLnj_9TEh7MnOpY3U)

<br>

##### Eklenen widgetlar sonrası blynk paneli genel görünümü
![New Project](https://drive.google.com/uc?export=view&id=1y10caqjzqgRAtYqLVmAMviwtJG_KtK0L) 

<br>

# 2. Adım Proje İçin IFTTT (Google Assistant) Kurulumu

#### IFTTT Nedir ?

IFTTT “If This, Then That” anlamına gelir. IFTTT hem bir websitesidir hem de bir mobil uygulamaya sahiptir. Platformun sağladığı şey uygulamaları birbirine bağlamak. Kısacası servislerin ve cihazların birbirleri ile konuşması diyebiliriz. Örneğin android telefonunuzda bir arama yaparsanız, o aramanın günlüğü bir Google e-tablosuna eklenir. Bunu IfTT size sağlar. Veya evde telefonunuzu kaybettiğiniz zaman google home mini cihazınıza "Benim telefonum nerede ?" sorusunu iletirseniz telefonunuzu çaldıracaktır. Bir başka örnek ise toplantı saatiniz geldiğinde philips hue ampüllerinizi yeşil renkte yakabilirsiniz. Bu ve bunun gibi senaryoları IFTTT ile yapmanız mümkün.

Ben bu projede evinizdeki google home mini cihazına veya telefonunuzdaki google asistanınıza "hey google open the door" dediğinizde dış kapı otomatiğini tetiklettirmesini öğreteceğim.

#### IFTTT (Google assistant Applet) Kurulumu ?

IFTTT web sitesine [buradan](https://ifttt.com/) ulaşabilirsiniz. Siteye google hesabınızla kayıt olup giriş yaptıktan sonra sırasıyla aşağıdaki adımları izleyerek google asistan ile oluşturduğumuz sisteme komut gönderip kapyı açtırabiliriz.

**İlk olarak IFTTT sitesinde ana sayfada iken sağ üst köşedeki "Create" butonuna tıklıyoruz.**
![IFTTT Create](https://drive.google.com/uc?export=view&id=1iLJ4JJd7-m1fLEu8Y1wJM15RXsZlJXZF) 

<br>

**Gelen sayfada if kutucuğu içerisindeki "add" butonuna tıklıyoruz.**
![IFTTT IF THIS THAN THAT](https://drive.google.com/uc?export=view&id=1B8G0RTALe21FgpL3oJmEuasAai2cAXGc) 

<br>

**Ekrana gelen servisler içerisinden google assistant seçeneğini seçiyoruz.**
![Google Assistant](https://drive.google.com/uc?export=view&id=1tg7RbTajvHXVjf-WGprBpYe6847GIqdh)


<br>

**Bu kısımda olayın tetiklenmesi için google asistanınıza ne söylemek istediğinizi ve asistanınızın size dönüş olarak ne cevap vereceğini belirliyorsunuz. Create Trigger butonuna basıp tetikleyiciyi oluşturmuş oluyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1cTxWRGKWHt1BWMtlnqw2s9fN_3S_P6XQ)

<br>

**Gelen sayfada then that kutucuğu içerisindeki "add" butonuna tıklıyoruz ve servisler arasından webhooks'u seçiyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1jpKy1QGhKs6HmuK5v5cCx6XrOQV3cPt1)

<br>

**"authcode" yazan kısma blynk'in kayıt olunan mail adresine gönderdiği auth kodunu yapıştırıyoruz ve diğer ayarları bu şekilde değiştiriyoruz. Create action butonuna basıyoruz**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1pNMt2I5e3UAf8mZKxlS6TuFrKEVl1J6y)

<br>

# 2. Adım Proje İçin IFTTT (Google Sheets Applet) Kurulumu

#### IFTTT (Google Sheets Applet) Kurulumu ?

Her giren kullanıcı için okuttuğu rfid kartın id'si, giriş yaptığı zaman, adı soyadı ve açılıp açılmadığı bilgilerini göndermek için google tablolarda kayıt tutuyoruz. Google sheet appletlerini eklemek için sırasıyla aşağıdaki adımları izlememiz gerekiyor. 
Tekrar ana sayfada "create" tuşuna bastıktan ve if kutucuğu içerisindeki "add" butonuna bastıktan sonra servisler içerisinden webhook servisini seçiyoruz.

**event name kısmına logger yazıyorum bu benim yazdığım programın bir gereği siz başka bir şey yazabilirsiniz. create trigger butonuna tıkladıktan sonra then kutucuğunun içerisindeki add butonuna tıklıyoruz ve tekrardan yeni bir servis arıyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1i8Gb5ZHZktLfCakpXfq7BvEFNx8HvWAR)

<br>

**Google sheets servisini arıyoruz ve seçiyoruz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1jHGeHfhtW_ecD_TgvVwF99dPzqlFIO0C)

<br>

**Daha sonra add row to spreadsheet seçeneğine tıklıyoruz. Bunu seçmemizin sebebi tabloya her yeni bir bilgi geldiğinde yeni bir satıra ekleniyor.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1CnbDMIM9bwLpFpeo8HDDd8WhYBOnJyEa)

<br>

**Ayarlar aşağıdaki gibi olmalıdır eğer isterseniz tablo adını değiştirebilirsiniz.**
![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1zQlM7q6lLmZfqfHNvF38NUgc3yuhNDs1)

<br>

## Proje Fritzing Şeması

![google asistant ayarlar](https://drive.google.com/uc?export=view&id=1Fkml9wqiZ7cEA1TqU5v_uhlK3bOEZdzJ)

<br>

## Proje Kaynak Kodları

```c

#include <SPI.h>    // Rfid modülünün haberleşmesi için SPI kütüphanesi eklendi.
#include <MFRC522.h>  // Rfid modülü için MFRC522 kütüphanesi eklendi.

#include <WiFiClient.h> 
#define BLYNK_PRINT Serial
#include <ESP8266WiFi.h>
#include <BlynkSimpleEsp8266.h>

//***** KABLOSUZ AYARLARI ******* 
char auth[] = "Blynk auth code";    //Blynk auth code girilecek
const char* ssid = "Wifi SSID";     //Bağlanılacak olan wifi ağı adı
const char* pass = "Wifi Pass";     //Bağlanılacak olan wifi ağı şifresi


//************* IFTTT ****************
const char* server = "maker.ifttt.com"; 
// Kişiye özel IFTTT Url'i
const char* resource = "Google Sheet appletinden gelen link";  // Google Sheet appletinde bulunan link

//*********** RFID **************
#define RST_PIN         D8           
#define SS_PIN          D4          
#define relay           D3
#define button          D8

MFRC522 mfrc522(SS_PIN, RST_PIN);   
String readCardId = ""; // okunan kart idsi
String tempid = "";     // değişken kart idsi

//******** Led Pins and Button Pin *************
#define green           D1
#define red             D0
#define yellow          D2

unsigned long lastTime = 0;
unsigned long lastTime1 = 0;
unsigned long lastTime2 = 0;

unsigned long delayTime = 10000; 
unsigned long delayTime1 = 2500;  
unsigned long delayTime2 = 60000;

WiFiClient client;

//***** ANAHTAR LİSTESİ ***********
String Key_list[2][2] =          // Anahtar Listesi
  {
  {"örnek kullanıcı adı1","örnek kullanıcı 1 kartid"},
  {"örnek kullanıcı adı2","örnek kullanıcı 2 kartid"},
  

  };

// Google tablolar ile bağlantı sağlayan fonksiyon 
void connectGoogleSheets()
{
  int retries = 8;

  while(!!!client.connect(server, 80) && (retries-- > 0)) {
    Serial.print(".");
  }
} 
// IFTTT web servisine bir http isteği gönderir.
void makeIFTTTRequest(String name_surname,String kart_id,String durum) {
  digitalWrite(red,HIGH);
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

  
  String jsonObject = String("{\"value1\":\"") + name_surname + "\",\"value2\":\"" + kart_id
                      + "\",\"value3\":\"" + durum + "\"}";
                      
            
  client.println(String("POST ") + resource + " HTTP/1.1");
  client.println(String("Host: ") + server); 
  client.println("Connection: close\r\nContent-Type: application/json");
  client.print("Content-Length: ");
  client.println(jsonObject.length());
  client.println();
  client.println(jsonObject);
        
  int timeout = 5 * 10; // 5 seconds             
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
  digitalWrite(red,LOW);
}
String getID()  
{
  digitalWrite(green,HIGH);
  delay(200);
  readCardId = "";
  if(!mfrc522.PICC_IsNewCardPresent())
    {
      return "";
    }
    if(!mfrc522.PICC_ReadCardSerial())
    {
      return "";
    }
    for(int i = 0; i < mfrc522.uid.size;i++)
    {
      readCardId += String(mfrc522.uid.uidByte[i],HEX);
    }
    Serial.println(readCardId);
    digitalWrite(green,LOW);
    return readCardId;
}

void openDoor()   // Kapı aç metodu kapı otomatiğini 200 milisaniye açıyor ve kapatıyor.
{
  digitalWrite(relay,HIGH);
  delay(200);
  digitalWrite(relay,LOW);
}

void decision()   //Karar fonksiyonu ile gelen kart id'nin kararı verilir.
{
  
  for(int count=0;count<11;count++)
    {
      if(Key_list[count][1] == tempid)
      {
        digitalWrite(red,HIGH);
        digitalWrite(green,LOW);
        
        Serial.print(Key_list[count][0]);
       // Serial.println(" Adli kisi giris yapti.");
        openDoor();
        if(Blynk.connected()) makeIFTTTRequest(Key_list[count][0],Key_list[count][1],"Açıldı.");
        
        digitalWrite(red,LOW);
        digitalWrite(green,HIGH);
        tempid = "";
        break;
      }
    }
     if(tempid != "")
      {
        if(Blynk.connected()) makeIFTTTRequest("Bilinmeyen Kullanici",tempid,"Açılmadı.");
        digitalWrite(red,HIGH);
        delay(250);
        digitalWrite(red,LOW);
      }
}

BLYNK_WRITE(V2) // Blynk Write(v2) ile ikinci sanal pinden okuma alınır.Alınan okuma 1 ise kapı açma izni verilir.
{
  digitalWrite(green,LOW);
  int pinData = param.asInt(); 
  if(pinData == 1){
    openDoor();
    makeIFTTTRequest("Google Asistan","Kartız","Açıldı.");
  }
  digitalWrite(green,HIGH);
}

void setup() {
  Serial.begin(9600);  // Seri haberleşmeyi başlatıyoruz.                                         
  SPI.begin();         // Spi haberleşme başlatıldı.                                       
  mfrc522.PCD_Init();  // Rfid modül başlatıldı.
  
  // röle,ledler,ve buton için giriş çıkış atandı.
  pinMode(relay,OUTPUT);
  pinMode(green,OUTPUT);
  pinMode(red,OUTPUT);
  pinMode(yellow,OUTPUT);
  pinMode(button,INPUT);
  digitalWrite(relay,LOW);
  
  Blynk.config(auth,"blynk-cloud.com", 8080);
  
}

void loop() {
  unsigned long nowTime = millis(); 
  unsigned long nowTime1 = millis();
  unsigned long nowTime2 = millis();

  if (nowTime - lastTime >= delayTime) 
  {
    lastTime = nowTime;    
    if(WiFi.status() != WL_CONNECTED) // her 10 saniyede bir internet bağlantısı kontrol edilir eğer bağlantı yok ise tekrar bağlanmayı dener. 
    {
      digitalWrite(yellow,LOW);
      WiFi.begin(ssid,pass);
      Blynk.config(auth,"blynk-cloud.com", 8080);
    } 
    else digitalWrite(yellow,HIGH);
  }

  if(nowTime1 - lastTime1 >= delayTime1) // her 2,5 saniyede bir kapı açma isteği butonuna basılıp basılmadığı kontrol ediliyor.
  {
    lastTime1 = nowTime1;
    if(digitalRead(button) == true){
      digitalWrite(red,HIGH);
      Blynk.notify("Bina içersindeki kapıdan bir kişi kapı açma isteği gönderdi."); 
    }
    else digitalWrite(red,LOW);
  }

  if(nowTime2 - lastTime2 >= delayTime2)  // bir dakikada bir google tablolar ile bağlantı sağlanıyor.
  {
    lastTime2 = nowTime2;
    connectGoogleSheets();
  }
 
  tempid = getID(); //kart üzerinde okunan kart id tempid değişkenine atılır.
  decision();  // karar metodu ile kapının açılıp açılmayacağı kararı verilir.

  Blynk.run();
}

```

<br>

## Sonuç

{% include youtube.html id='S1oL-IkkozY' %}

<br>
