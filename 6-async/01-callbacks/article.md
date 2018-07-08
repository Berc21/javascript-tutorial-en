

# Giriş: callbacks

JavaScript'teki birçok eylem *senkronize değildir *.

Örneğin, `loadScript (src)` işlevine bir göz atalım:

```js
function loadScript(src) {
  let script = document.createElement('script');
  script.src = src;
  document.head.append(script);
}
```

Fonksiyonun amacı yeni bir komut dosyası yüklemektir. Belgeye `<script src ="… ">` eklendiğinde, tarayıcı onu yükler ve çalıştırır.

Bunu şöyle kullanabiliriz:

```js
// komut dosyasını yükler ve çalıştırır
loadScript('/my/script.js');
```

İşlev "eşzamansız" olarak adlandırılır, çünkü eylem (komut dosyası yükleme) şimdi değil, daha sonra biter.

Çağrı, komut dosyası yüklemesini başlatır, daha sonra yürütme devam eder. Komut dosyası yüklenirken, aşağıdaki kod yürütmeyi bitirebilir ve yükleme zaman alırsa, diğer komut dosyaları da bu arada çalışabilir.

```js
loadScript('/my/script.js');
//loadScript'in altındaki kod, komut dosyasının yüklenmesi için beklemez
// ...
```

Şimdi yeni komut dosyasını yüklendiğinde kullanmak istediğimizi varsayalım. Muhtemelen yeni fonksiyonlar tanımlar, bu yüzden onları çalıştırmak isteriz.

Ama eğer bunu loadScript (…) `den hemen sonra yaparsak, bu işe yaramaz:

```js
loadScript('/my/script.js'); //komut dosyası "newFunction () {…}" işlevine sahip

*!*
newFunction(); // böyle bir fonsiyon yok!
*/!*
```

Doğal olarak, tarayıcı muhtemelen komut dosyasını yüklemek için zamana sahip değildi. Böylece yeni fonksiyona hemen çağrı başarısız oluyor. Şu an itibariyle, `loadScript` işlevi, yükün tamamlanmasını izlemek için bir yol sağlamaz. Komut yüklenir ve sonunda çalışır, hepsi bu. Ancak, ne zaman olacağını, bu komut dosyasındaki yeni işlevleri ve değişkenleri kullanmak isteriz.

Komut dosyası yüklendiğinde çalıştırılması gereken `loadScript` için ikinci bir argüman olarak `callback` fonksiyonunu ekleyelim:

```js
function loadScript(src, *!*callback*/!*) {
  let script = document.createElement('script');
  script.src = src;

*!*
  script.onload = () => callback(script);
*/!*

  document.head.append(script);
}
```
Şimdi senaryodan yeni fonksiyonlar çağırmak istiyorsak, bunu callback'e yazmalıyız:

```js
loadScript('/my/script.js', function() {
  // the callback runs after the script is loaded
  newFunction(); // so now it works
  ...
});
```

Buradaki mantık: ikinci argüman, eylem tamamlandığında çalışan bir işlev (genellikle anonim).

İşte gerçek bir betik (script) ile uygulanabilir bir örnek:

```js run
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(script);
  document.head.append(script);
}

*!*
loadScript('https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js', script => {
  alert(`Cool, the ${script.src} is loaded`);
  alert( _ );// Yüklenen komut dosyasında bildirilen funksiyon
});
*/!*
```

Bu, "callback tabanlı" bir eşzamansız programlama stili olarak adlandırılır. Eşzamansız bir şey yapan bir fonksiyon, işlevi tamamlandıktan sonra çalışmaya koyduğumuz bir `callback' argümanı sağlamalıdır.

İşte bunu loadScript'de yaptık, ama elbette bu genel bir yaklaşım.

## Callback içinde callback
Ardışık olarak iki komut dosyası nasıl yüklenir: Birincisi, sonra da ikincisi?

Beklenen çözüm, ikinci `loadScript` çağrısını geri arama içine koymak olabilir:

```js
loadScript('/my/script.js', function(script) {

  alert(`Harika,  ${script.src} yüklendi, hadi bir tane daha yükleyelim.`);

*!*
  loadScript('/my/script2.js', function(script) {
    alert(`Harika, ikinci komut satırı da yüklendi!`);
  });
*/!*

});
```

Dış `loadScript` tamamlandıktan sonra, iç callback olanı başlatır.

Ya bir tane daha senaryo istiyorsak ...?

```js
loadScript('/my/script.js', function(script) {

  loadScript('/my/script2.js', function(script) {

*!*
    loadScript('/my/script3.js', function(script) {
      // ...continue after all scripts are loaded
    });
*/!*

  })

});
```

Yani, her yeni eylem bir callbak içinde olması. Bazı funksiyonlar için iyi, ancak pek çoğu için iyi değildir, bu yüzden yakında diğer varyasyonları göreceğiz.

## Hatalarla başa çıkma

Yukarıdaki örneklerde hataları dikkate almadık. Komut dosyası yükleme başarısız olursa ne olur? Callback buna tepki gösterebilmelidir.

İşte yükleme hatalarını izleyen geliştirilmiş bir "loadScript" sürümü:

```js run
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

*!*
  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));
*/!*

  document.head.append(script);
}
```

Başarılı yükleme için `callback(null, script)` çağırır.  Aksi durumda `callback(error)`.


Kullanımı
```js
loadScript('/my/script.js', function(error, script) {
  if (error) {
    // hatayı hallet
  } else {
    // komut dosyası başarıyla yüklendi
  }
});
```

Bir kez daha, "loadScript" için kullandığımız tarif aslında oldukça yaygın. Buna "error-first callback" stili denir.

Yaygın kullanım şekli:
1. 'Callback' ilk argümanı, oluştururken hata için ilk argüman ayrılmıştır. Sonra `callback(err)` çağrılır.
2. İkinci argüman (ve gerekirse sonrakiler) başarılı sonuç içindir. Sonra `callback(null, result1, result2…)` çağrılır.

Dolayısıyla, tek `callback` fonksiyonu hem hataları bildirmek hem de sonuçları geri almak için kullanılır.

## Kıyamet Piramidi

İlk bakışta, eşzamansız kodlamanın uygulanabilir bir yolu. Ve gerçekten bir veya iki iç içe callback için iyi görünüyor.

Ancak birbiri ardına gelen çoklu eşzamansız eylemler için aşağıdaki gibi bir kodumuz olacak:
```js
loadScript('1.js', function(error, script) {

  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', function(error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript('3.js', function(error, script) {
          if (error) {
            handleError(error);
          } else {
  *!*
            // ...tüm komut dosyaları yüklendikten sonra devam et (*)
  */!*
          }
        });

      }
    })
  }
});
```

Yukarıdaki kodda:
1. `1.js` yükleriz, sonra hata yoksa.
2. `2.js` yükleriz, sonra hata yoksa.
3. `3.js` yükleriz, sonra hata yoksa. -- başka bir şey yap `(*)`.

Calback'ler daha iç içe geçtikçe, kod daha da derinleşir ve yönetilmesi gittikçe daha zor hale gelir, özellikle '...' yerine gerçek bir koda sahip olursak, daha fazla döngü, koşullu ifade vb. Içerebilir.

Bu bazen "callback cehennemi" ya da "kıyamet piramidi" denir.

![](callback-hell.png)

İç içe geçmiş calback'ler "piramidi" her eşzamansız eylemle sağa doğru büyür. Bir süre sonra kontrolden çıkar.

Yani bu şekilde kodlama çok iyi olduğu söylenemez.

Her eylemi bağımsız bir işleve dönüştürerek sorunu hafifletmeye çalışabiliriz:

```js
loadScript('1.js', step1);

function step1(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', step2);
  }
}

function step2(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('3.js', step3);
  }
}

function step3(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ..tüm komut dosyaları yüklendikten sonra devam et (*)
  }
};
```

Gördünüz mü? Aynı şeyi yapıyor ve şu anda derin bir yuvalama yok, çünkü her eylemi ayrı bir üst düzey işlev haline getirdik.

Kod sorunsuz çalışıyor, ancak kod parçalanmış bir elektronik tabloya benziyor. Okuması zor, muhtemelen bunu farkettin. Okurken parçaların arasında göz atmak gerekir. Bu rahatsız edici bir durumdur, özellikle okuyucunun kodu aşina değil ve nereye atlayacağını bilmiyorsa.

Ayrıca "step*" adlı fonksiyonlar tek bir kullanımdır, sadece "doom piramidi" ni önlemek için oluşturulurlar. Kimse onları eylem zincirinin dışında yeniden kullanamaz. Yani burada karışık bir isim alanı var.

Daha iyi bir şey isteriz.

Neyse ki, bu piramitleri önlemek için başka yollar var. En iyi yollardan biri, bir sonraki bölümde anlatılan "promise" kullanmaktır.
