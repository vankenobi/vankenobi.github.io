---
layout: post
title: "Esp32 İle Asenkron Olarak Çalışan Web Server Projesi"
excerpt: ""
tags: [Esp32,Asenkron,Web Server]
categories: [ESP32] 
date:   2020-06-07 16:15:00
comments: true
share: true
image: 
  feature: https://drive.google.com/uc?id=1RBA39QFpLErzHYOGzV23YcPVw0rxgIsN
---

## Esp32 Kartının Kurulumu

![Asenkron]( https://drive.google.com/file/d/1RBA39QFpLErzHYOGzV23YcPVw0rxgIsN/view )

Bilgisayarınızda halihazırda kurulu bir Arduino IDE'si olduğunu varsayıyorum. Ancak projede kullanacağımız ESP32, IDE'nin standart kurulumunda gelmiyor o yüzden bazı ek işlemler yapmamız gerekecek.

Arduino IDE'sini açıp
<span style="background-color:#e5eff5 padding:5px;border-radius:5px">Dosya>Tercihler</span> menüsünden <span style="background-color:#e5eff5; padding:5px; border-radius:5px;">Ek Devre Kartları Yöneticisi URL'leri</span> kısmına aşağıdaki adresi ekleyelim:
<span style="background-color:#e5eff5;padding:5px">https://dl.espressif.com/dl/package_esp32_index.json</span>

Daha sonra <span style="background-color:#e5eff5;padding:5px; border-radius:5px;">Araçlar>Kart>Kart Yöneticisi</span> kısmından Esp32 kartını bulup kuralım.



![Kart Yöneticisi Penceresi](https://drive.google.com/uc?id=1_UJ_4ufjx3jU0qw4xVHieJplmRmiBgTe )

<br />

## Projeye Giriş



![Asenkron Server](https://drive.google.com/uc?id=1UtJH7mTVt5Axql39misBsLFdw_CotiET)



Projeye başlamadan önce senkron, asenkron gibi kavramları websocket'in ne olduğundan bahsetmek istiyorum.

> <span style="background-color:#c0c0c0; padding:5px;border-radius:5px">Senkron Nedir ?</span><br>
Kelime anlamı **eşzamanlı** olan senkron kelimesi yazılımda birtakım işlemlerin sıra ile yapılmasıdır.Basit bir analoji yapmak gerekirse bir fatura ödeme merkezindesiniz ve faturanızı ödemek için kuyruğa girdiniz. Sizin önünüzde fatura ödemek isteyen kişilerin faturaları ödenmeden sizin faturanız ödenmez. İşte bu şekilde işlemleri sıralı bir şekilde yapılmasına senkron programlama denir.

> <span style="background-color:#c0c0c0; padding:5px;border-radius:5px"> Asenkron Nedir ? </span> <br>
Kelime anlamı "Aynı zamanda veya birlikte meydana gelmeyen" olan asenkron kelimesi yazılımda yürütülen işlemlerin uzun sürmesinden dolayı yürütülmesi gereken diğer işlemlerin beklemeden çalışmasına devam edebilmesini sağlar. Senkronu açıklarken yaptığım gibi basit bir analoji yapmam gerekirse mutfakta iki çeşit yemek pişiriyorsunuz ve birini yapmaya başladınız. Elinizdeki yemek kaynarken diğer yemeğe başlamak için elinizdeki yemeğin bitmesini beklemezsiniz. İşte bu duruma asenkron programlama denir.

> <span style="background-color:#c0c0c0; padding:5px;border-radius:5px"> Websocket Nedir ? </span> <br>
WebSocket, tek bir TCP bağlantısı üzerinden tam çift yönlü iletişim kanalı sağlayan bir bilgisayar iletişim protokolüdür. WebSocket protokolü, sunucuya ve sunucudan gerçek zamanlı veri aktarımını sağlayarak, tarayıcı ile web sunucusu arasında etkileşimi sağlamaktadır. Bu, sunucunun istemci istemeden tarayıcıya içerik gönderebileceği ve bağlantıyı açık tutarak istediği zaman mesaj alabilmesini veya gönderebilmesini sağlayan standart bir yöntem ile sağlanmaktadır.Bizim bu projemizde ise web sayfası yenilenmeden kart üzerindeki ledi açıp kapatabilmemizi sağlanıyor.<br>
<span style="background-color:#c0c0c0; padding:5px;border-radius:5px"> Neden Websocket ? </span><br>
Yeni bir istek HTTP protokolündeki kuralların tekrar edilmesine ve fazla trafik kullanımına neden olur.Trafik kullanımı ve HTTP protokolüyle gerçek zamanlı uygulamaların yönetilmesi maliyetli ve zor olduğundan dolayı websocket kullanılır.

<br />

Proje içerisinde kullanacağımız kütüphaneler Esp32 kartımızın ağa bağlanmasını ve üzerinde bir web sayfasını asenkron çalıştırmasını sağlayacak.

{% highlight cpp %}
  #include <WiFi.h>
  #include <AsyncTCP.h>
  #include <ESPAsyncWebServer.h>
{% endhighlight %}


Proje içerisinde kullanılan kütüphanelerin linkleri:

* <span style="font-size:18px"> [AsyncTCP.h](https://github.com/me-no-dev/AsyncTCP)</span>
* <span style="font-size:18px"> [ESPAsyncWebServer.h](https://github.com/me-no-dev/ESPAsyncWebServer/)</span>
* <span style="font-size:18px"> Wifi.h kütüphanesi Arduino IDE'si içerisinde halihazırda mevcut. </span>

<br />

{% highlight cpp %}
    const char* ssid = "Ag Adi";
    const char* password = "Ag Parolasi";
    const int led = 2;
{% endhighlight %}

Ssid ve password kısmına bulunduğunuz ortamdaki ağın adını ve parolasını giriniz. Esp32 üzerindeki led  GPIO2 pinine bağlı olduğu için led isminde değişkeni bu pine atadık.

<br />

{% highlight cpp %}
    const char index_html[] PROGMEM = 
{% endhighlight %}

Progmem anahtar sözcüğü normalde Esp32'nin Sram'ine kaydetmesi gerekirken Flash hafızasına kaydetmesini sağlar. Esp32'nin Flash hafızası 4MB'dır.

<br />

{% highlight cpp %}
    void notFound(AsyncWebServerRequest *request) {
    request->send(404, "text/plain", "Sayfa Bulunamdadi");
}
{% endhighlight %}
NotFound fonksiyonu sayfa bulunamadığında 404 hata kodunu ve sayfa bulunamadı mesajını gönderecek. 

<br />

{% highlight cpp %}
    AsyncWebServer server(80);
{% endhighlight %}

AsyncWebServer sınıfından server adlı bir nesne oluşturduk ve parametre olarak 80 verdik. Girilen parametre portu temsil ediyor.

<br />

{% highlight cpp %}
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  if (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Wifi agina Baglanmadi!");
    return;
  }
  Serial.println();
  Serial.print("ESP IP Address: http://");
  Serial.println(WiFi.localIP());
{% endhighlight %}

Bu kod bloğunda sırasıyla; 
* <span style="font-size:18px">Seri haberleşmeyi başlattık ve baudrate hızını 115200 olarak ayarladık.</span>  
* <span style="font-size:18px">Wifi modunu STA olarak ayarladık. STA İstasyon modudur. Esp32'yi erişim noktası tarafından kurulan bir WiFi ağına bağlamak için kullanılır.</span> 
* <span style="font-size:18px">Begin metodu ile parametresi girilen wifi ağına bağlanır.</span> 
* <span style="font-size:18px">İf bloğu wifi ağına bağlanıp bağlanmadığını kontrol ediyor. Ağa bağlanmadıysa seri monitöre "Wifi agina Baglanmadi!" yazdırır. Eğer bağlandıysa esp32'nin sunmuş olduğu web sayfasının linkini seri monitöre yazdırır.</span> 

<br />

{% highlight cpp %}
  pinMode(led, OUTPUT);
  digitalWrite(led, LOW);
{% endhighlight %}
Led adlı değişkenin bulunduğu pine(GPIO2) çıkış verdik ve başlangıçtaki durumunu kapalı olarak belirledik.

<br />

{% highlight cpp %}
server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/html", index_html);
  });
{% endhighlight %}
Gelen isteğe karşılık web sayfasını istemciye gönderir.

<br />

{% highlight cpp %}
server.on("/ac", HTTP_GET, [] (AsyncWebServerRequest *request) {
    digitalWrite(output, HIGH);
    request->send(200, "text/plain", "ac sinyali gonderildi.");
  });
{% endhighlight %}
Gelen "/ac" isteğine karşılık ledi açık konuma getirir ve istemciye "ac sinyali gonderildi." mesajını iletir.

<br />

{% highlight cpp %}
server.on("/kapat", HTTP_GET, [] (AsyncWebServerRequest *request) {
    digitalWrite(output, LOW);
    request->send(200, "text/plain", "kapat sinyali gönderildi.");
  });
{% endhighlight %}
Gelen "/kapat" isteğine karşılık ledi kapalı konuma getirir ve istemciye "kapat sinyali gonderildi." mesajını iletir.

<br />

{% highlight cpp %}
server.onNotFound(notFound);
server.begin();
{% endhighlight %}
Bu kod bloğunda sırasıyla:
* <span style="font-size:18px">Eğer sayfa bulunamadıysa onNotFound metodu çalıştırılacak.
</span> 
* <span style="font-size:18px">begin metodu web serverı başlatır.</span> 

<br />


{% highlight cpp %}
#include <WiFi.h>
#include <AsyncTCP.h>
#include <ESPAsyncWebServer.h>


const char* ssid = "Ag Adi";
const char* password = "Ag Parolasi";

const int led = 2;

const char index_html[] PROGMEM = R"rawliteral(<!DOCTYPE Html>

<head>
    <meta charset="utf-8">
    <title> Web Sayfasi </title>
    <style>
        body {
            margin: 0;
            text-align: center;
        }
        
        button {
            width: 200px;
            height: 100px;
            border-radius: 3px;
        }
    </style>
</head>

<body>
    <h2>Esp32 İle Asenkron Web Server</h2>
    <br>
    <button style="background-color: #77d772;" id="ac" onclick="myfunction('ac')"><b>Aç</b></button>
    <button style="background-color: red;" id="kapat" onclick="myfunction('kapat')"><b>Kapat</b></button>
    <h4>Led Durum:
        <h4 id="leddurum">Led Kapalı</h4>
    </h4>
    <script>
        function myfunction(value) {
            var xhr = new XMLHttpRequest();
            if (value == 'ac') {
                document.getElementById("leddurum").innerHTML = "Led Açık";
                xhr.open("GET", "/ac", true);
                xhr.send();
            } else {
                document.getElementById("leddurum").innerHTML = "Led Kapalı";
                xhr.open("GET", "/kapat", true);
                xhr.send();
            }
        }
    </script>
</body>

</Html>)rawliteral";



void notFound(AsyncWebServerRequest *request) {
  request->send(404, "text/plain", "Sayfa Bulunamdadi");
}

AsyncWebServer server(80);

void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  if (WiFi.waitForConnectResult() != WL_CONNECTED) {
    Serial.println("Wifi agina Baglanmadi!");
    return;
  }
  Serial.println();
  Serial.print("ESP IP Address: http://");
  Serial.println(WiFi.localIP());
  
  pinMode(led, OUTPUT);
  digitalWrite(led, LOW);
  
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send_P(200, "text/html", index_html);
  });

  
  server.on("/ac", HTTP_GET, [] (AsyncWebServerRequest *request) {
    digitalWrite(output, HIGH);
    request->send(200, "text/plain", "ac sinyali gonderildi.");
  });

  
  server.on("/kapat", HTTP_GET, [] (AsyncWebServerRequest *request) {
    digitalWrite(output, LOW);
    request->send(200, "text/plain", "kapat sinyali gönderildi.");
  });
  
  server.onNotFound(notFound);
  server.begin();
}

void loop() {
 
}
{% endhighlight %}