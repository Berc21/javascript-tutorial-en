# JavaScript'te giriş

JavaScript ile ilgili neyin özel olduğunu, bununla neleri başarabileceğimizi, başka hangi teknolojilerle iyi işler başardığına bir göz atalım.

## What is JavaScript?

*JavaScript* was initially created to *"make webpages alive"*.

* JavaScript *başlangıçta "web sayfalarını interaktif bir hale getirmek"* için oluşturuldu.

Bu dilde programlar *komut dosyaları (scripts)* denir. Bunlar doğrudan HTML’de yazılabilir ve sayfa yüklendikçe otomatik olarak çalıştırılabilir.

Komut dosyaları düz metin olarak sunulur ve yürütülür. Çalıştırmak için özel bir hazırlık ya da derleme gerektirmez.

Bu açıdan, JavaScript, [Java](http://en.wikipedia.org/wiki/Java) adlı dilden çok farklıdır. 

```smart header="Neden <u>Java</u>Script?"
JavaScript oluşturulduğunda, başlangıçta başka bir ad vardı: "LiveScript". Ancak Java dili o zamanlar çok popülerdi, bu yüzden yeni bir dili Java'nın “küçük erkek kardeşi” olarak konumlandırmanın yardımcı olacağına karar verildi.

Ancak, geliştikçe, JavaScript, [ECMAScript] (http://en.wikipedia.org/wiki/ECMAScript) adı verilen kendi tanımlamasıyla tam bağımsız bir dil haline geldi. Java ile hiç bir ilişkisi yoktur.
```

Şu anda, JavaScript sadece tarayıcıda değil, aynı zamanda sunucuda veya [JavaScript motoru] (https://en.wikipedia.org/wiki/JavaScript_engine) adlı özel bir programın bulunduğu herhangi bir cihazda da çalışabilir.

Tarayıcı gömülü bir motora sahiptir, bazen "JavaScript sanal makinesi" olarak da adlandırılır.

Farklı motorların farklı "kod adları" vardır, örneğin:

- [V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) -- in Chrome and Opera.
- [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey) -- in Firefox.
- ...IE'nin farklı sürümleri için "Trident", "Chakra", Microsoft Edge için "ChakraCore", Safari için "Nitro" ve "SquirrelFish" gibi başka kod adları da vardır.

Yukarıdaki terimler hatırlamakta fayda vardır, çünkü bunlar internetteki geliştirici makalelerinde kullanılmaktadır. Biz de kullanacağız. Örneğin, "bir özellik X V8 tarafından destekleniyorsa", muhtemelen Chrome ve Opera'da çalışır.

```smart header="Motorlar nasıl çalışır?"

Motorlar karmaşıktır. Ama temel olarak kolaydır.

1. Motor (bir tarayıcıya gömülü ise eğer) komut dosyasını okur ("ayrıştırır").
2. Sonra, betiği(script) makine diline dönüştürür ("derler").
3. Ve sonra makine kodu çok hızlı bir şekilde çalışır.

Motor, sürecin her aşamasında optimizasyonlar uygular. Çalıştırıldığı haliyle derlenmiş betiği (script) izler, içinden geçen verileri analiz eder ve bu bilgiye dayanarak makine koduna optimizasyonlar uygular. Sonunda, komut dosyaları oldukça hızlı bir hale gelir.
```

## Tarayıcı içindeki JavaScript neler yapabilir?

Modern JavaScript, "güvenli" bir programlama dilidir. Başlangıçta bellek gerektirmeyen tarayıcılar için oluşturulduğundan, bellek veya CPU'ya düşük seviyeli erişim sağlamaz.

Kapasitesi, JavaScript çalıştıran ortama büyük ölçüde bağlıdır. Örneğin, [Node.JS] (https://wikipedia.org/wiki/Node.js), JavaScript'in dilsemsel (rastgele) dosyaları okuma / yazma, ağ istekleri vb. Gerçekleştirmesine izin veren işlevleri destekler.

Tarayıcıda JavaScript, web sayfası manipülasyonu, kullanıcı ve web sunucusu ile etkileşim ile ilgili her şeyi yapabilir.

Örneğin, tarayıcı içi JavaScript şunları yapabilir:

- Sayfaya yeni HTML ekler, mevcut içeriği değiştirir, stilleri (css) değiştirebilir.
- Kullanıcı eylemlerine, fare tıklaması, işaretçi (pointer) hareketlerine, tuş basışlarına tepki verir.
- Ağ üzerinden istekleri uzak sunuculara gönderir, dosyaları indirir ve yükler ([AJAX] (https://en.wikipedia.org/wiki/Ajax_ (programlama)) ve [COMET] (https://en.wikipedia).org/wiki/Comet_(programlama)) teknolojileri).
- Get and set cookies, ask questions to the visitor, show messages.
- Remember the data on the client-side ("local storage").

## Tarayıcı içindeki JavaScript neler yapaMAZ?

Tarayıcıdaki JavaScript kapasitesi, kullanıcının güvenliği açısından sınırlıdır. Amaç, kötü bir web sayfasının özel bilgilere erişmesini veya kullanıcının verilerinin zarar görmesini engellemektir.

Bu tür kısıtlamaların örnekleri:

- Bir web sayfasındaki JavaScript, sabit diskte rastgele dosyaları okuyamaz / yazamaz, kopyalayamaz veya programları yürütmeyebilir. İşletim sistemi sistem işlevlerine doğrudan erişimi yoktur.

    Modern tarayıcılar dosyalarla çalışmasına izin verir, ancak erişim sınırlıdır ve yalnızca kullanıcı bir tarayıcı penceresine "bırakma" veya bir "<input>" etiketi ile seçerek "belirli eylemler yaparsa" sağlanır.

    Kamera / mikrofon ve diğer cihazlarla etkileşimde bulunmanın yolları vardır, ancak kullanıcının açık izni gerekir. Dolayısıyla, JavaScript etkinleştirilmiş bir sayfa gizlice web kamerasını etkinleştiremeyebilir, çevreyi gözlemleyemez ve bilgileri [NSA] 'ya gönderemez (https://en.wikipedia.org/wiki/National_Security_Agency).
- Farklı sekmeler / pencereler genellikle birbirlerini bilmez. Bazen, örneğin bir pencere diğerini açmak için JavaScript kullandığında bilebilir. Ancak bu durumda bile, bir sayfanın JavaScript'i farklı sitelerden geliyorsa (başka bir alandan, protokolden veya porttan) diğerine erişemeyebilir.

    Buna "Aynı Kaynak Politikası" denir. Bu konuda çalışmak için, *her iki sayfa da" veri alışverişini gerçekleştiren özel bir JavaScript kodu içermelidir.

    Sınırlama yine kullanıcının güvenliği içindir. Bir kullanıcının açtığı http: // anysite.com`dan bir sayfa, http: // gmail.com` ile başka bir tarayıcı sekmesine erişememeli ve oradan bilgi çalmamalıdır.
- JavaScript, ağ üzerinden mevcut sayfanın geldiği sunucuya kolayca iletişim kurabilir. Ancak diğer sitelerden / alanlardan veri alma yeteneği sakattır. Mümkün olsa da, uzak taraftan açık bir anlaşma (HTTP header diye ifade edilir) gerektirir. Bir kez daha berlirtmek gerekirse, bu güvenlik sınırlamalarıyla ilgilidir.

![](limitations.png)

Javascript tarayıcı dışında, örneğin bir sunucuda kullanılıyorsa bu sınırlar mevcut değildir. Modern tarayıcılar, genişletilmiş izinler alabilecek eklenti / eklentilerin yüklenmesine de izin verir.

## JavaScript'i benzersiz kılan nedir?

JavaScript hakkında en az * üç * harika şey vardır:

```compare
+ HTML / CSS ile tam entegrasyon.
+ Basit şeyler basitçe yapılır.
+ Tüm önemli tarayıcılar tarafından desteklenir ve varsayılan olarak etkinleştirilmiştir.
```

Kombine olmuş, bu üç şey sadece JavaScript’te vardır ve başka hiçbir tarayıcı teknolojisinde yoktur.

JavaScript'i benzersiz kılan budur. Bu yüzden tarayıcı arayüzleri oluşturmak için en yaygın araçtır.

Yeni bir teknoloji öğrenmeyi planlarken, onun bakış açılarını kontrol etmek yararlıdır. Bu yüzden yeni diller ve tarayıcı yetenekleri içeren modern trendlere göz atalım.


## JavasCript üzerinden diller

JavaScript'in sözdizimi herkesin ihtiyaçlarına uygun değildir. Farklı insanlar farklı özellikler isterler.

Bu beklenen bir şeydir, çünkü projeler ve gereksinimler herkes için farklıdır.

Yakın zamanda, tarayıcıda çalıştırılmadan önce JavaScript'e *dönüştürülür* çok sayıda yeni dil çıktı.

Modern araçlar, derlemeyi (transpilation) çok hızlı ve şeffaf hale getirerek, kullanıcıların başka bir dilde kodlamalarını ve "arka planda" otomatik olarak dönüştürmelerini sağlar.

Bu tür dillerin örnekleri:

- [CoffeeScript](http://coffeescript.org/), JavaScript için "sözdizimsel güzellik" katar, daha net ve açık kodlar yazılmasına izin veren daha kısa bir sözdizimi sunar. Ruby geliştiricileri genellikle bunu dili sever.
- [TypeScript](http://www.typescriptlang.org/) karmaşık sistemlerin geliştirilmesini ve desteklenmesini basitleştirmek için "statik veri yazımı" eklemeye odaklıdır. Microsoft tarafından geliştirilmiştir.
- [Dart](https://www.dartlang.org/), tarayıcı olmayan ortamlarda (mobil uygulamalar gibi) çalışan kendi motoru olan bağımsız bir dildir. Başlangıçta Google tarafından JavaScript'in yerini alması için önerilmiştir. Ancak, şu an itibariyle tarayıcılar, yukarıdakiler gibi JavaScript'e derlenmesi gerekiyor.

Bu örnekleri artırmak mümkün. Tabii ki bu dillerden birini kullansak bile, ne yaptığımızı gerçekten anlamak için JavaScript'i de bilmeliyiz.

## Özet

- JavaScript başlangıçta tarayıcıya özel bir dil olarak oluşturuldu, ancak şimdi başka birçok ortamda kullanılıyor.
- Şu anda, JavaScript, HTML / CSS ile tam entegrasyona sahip, en çok kullanılan tarayıcı dili olarak benzersiz bir konuma sahip.
- JavaScript'e "derlenebilen" ve belirli özellikler sağlayan birçok dil var. JavaScript'e hakim olduktan sonra, en azından kısaca bir göz atmanız önerilir.
