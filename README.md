Dungeon Crawler RL

   Bu proje, bir zindan keşfi oyununa ait özel bir Pekiştirmeli Öğrenme (Reinforcement Learning) ortamıdır. Ajan; lav çukurlarından kaçınırken zindanda gezinmeyi, ganimet toplamayı, mini-bossları ve ana bossu yenmeyi ve prensesi kurtarmayı öğrenir. Gymnasium ve Pygame kullanılarak, oyun dinamiklerini simüle eden bir ortam oluşturulmuştur. Basit ızgara dünyalarının (A noktasından B noktasına gitme) aksine, bu ortam durum bağımlılıkları ve savaş mantığını içerir.

- Ortam (ENV) Tasarımı:

````
```
MAP = [
    "+-------------+",
    "|M: : : : :B|F|",
    "| : : : : : : |",
    "|M: |-:-: : |B|",
    "|R:B| : : :-| |",
    "|-:-| : :M: :B|",
    "| : : :-:-: :-|",
    "|P: : : :R:M:L|",
    "+-------------+",
]
```
````

Ortam 7x7'lik bir ızgara üzerinde kuruludur ve aşağıdakileri içerir:

-   Duvarlar (`|`, `-`): Geçilemeyen engeller.
-   Kısıtlı Alanlar (R): "Lava" karoları.
-   Varlıklar:
    -   Oyuncu (P): Öğrenen ajan.
    -   Ganimet (L): Ana boss'a zarar verebilmek için gerekli silah.
    -   Mini-Boss (M): Yenildiğinde güç artışı sağlar.
    -   Ana Boss (B): Oyunun bitiş koşulunu engelleyen ana düşman.
    -   Kısıtlı Alan (R): Oyuncunun geçemeyeceği "lav" karoları.
    -   Bitiş Noktası (F): Prensesin bulunduğu çıkış.

Durum (State):

Oyunda 25.088 olası durum bulunmaktadır:

-   Oyuncu Pozisyonu: 49
-   Sağlık Seviyeleri: 4
-   Mini-Boss Pozisyonu: 4
-   Ana Boss Pozisyonu: 4
-   Ganimet Durumu: 2
-   Mini-Boss Durumu: 2
-   Ana Boss Durumu: 2

Toplam: 49 × 4 × 4 × 4 × 2 × 2 × 2 = 25.088

  ![res1](dungeon_animation.gif)

-Ödül Sistemi:

 -   Her adım: -1
 -   Ganimet toplama: +10
 -   Mini-boss yenme: +10
 -   Ana boss yenme: +20
 -   Prensesi kurtarma: +30
 -   Boşa saldırı: -2
 -   Gereksiz savaş başlatma: -15
 -   Ana boss ölmeden bitişe gitme: -20
 -   Ölme: -20
 - Shaping Reward: Bu, oyuncunun pozisyonuna göre ödül veren ve onu hedefe doğru iten bir fonksiyondur. Bu fonksiyon olmadan kodum saatlerce çalışıyordu.
     o   Bitiş çizgisine yaklaşan her adım: +0.5
     o   Uzaklaşan her adım: -0.6

- Aksiyonlar: 
Toplam 5 aksiyon vardır:

    0: güneye
    1: kuzeye
    2: doğuya
    3: batıya
    4: saldır

- Oyun Mantığı:
    • Hareket: Hedef kare duvar değilse, oyuncu dört yönde (güney, kuzey, doğu, batı) hareket edebilir.
    • Saldırı: Oyuncu, aynı karede bulunan herhangi bir düşmana (mini boss, ana boss) saldırabilir. Ancak başarılı bir saldırı için oyuncunun belirli bir sırayı izlemesi gerekir; önce mini boss'u yenmesine yardımcı olacak bir silah olan ganimeti alması gerekir. Ancak mini boss'u yenip ödülü aldıktan sonra oyuncu ana boss'u yenebilir.
    Oyuncu, ganimeti almadan mini boss'a saldırmaya çalışırsa -15 ceza alır. Aynı durum, mini boss'u almadan ana boss'a saldırmaya çalışırsa da geçerlidir. (Ganimet --> Mini Boss --> Ana Boss --> Bitiriş)

    • Sağlık Yönetimi:
    o Oyuncu, gerekli güçlendirmeler olmadan ana boss'a veya mini boss'a saldırırsa 2 sağlık puanı kaybeder. o Oyuncunun canı 0'a düşerse ölür ve bölüm sona erer.

    • Oyunu Bitirme:
        o Oyuncu, ana boss'u yenmeden bitiş çizgisine ulaşırsa (prenses kurtarırsa), 20 puan ceza alır.
        o Oyuncu, ana boss'u yendikten sonra bitiş çizgisine ulaşırsa (prenses kurtarırsa), 30 puan ödül alır.

- Hyperparameters:
    o Alpha (Learning Rate): 0.1
    o Gamma (Discount Factor): 1.0
    o Epsilon (Exploration Rate): 0.05
    o Number of Episodes: 100,000
    o Max Steps per Episode: 200

- Q-Learning algoritması:
Proje, oyuncuyu eğitmek için Q-Learning algoritmasını kullanır. Algoritma, alınan ödüllere ve oyuncunun eylemlerine göre Q-değerlerini günceller. Q-Tablosu, yeniden eğitime gerek kalmadan gelecekte kullanılmak üzere bir CSV dosyasına kaydedilir.

- Değerlendirme:
Etken, eğitim sırasında her 300 bölümde bir değerlendirilir. Ardından, kümülatif ödüle göre en iyi modeli izler ve kaydeder. Son olarak, kümülatif ödülü zaman içinde ve kaybı zaman içinde çizer.

- Nasıl çalıştırılır:
    Ajanı sıfırdan eğitmek için:

   1. game.ipynb Jupyter Notebook dosyasını açın.
   2. GameEnv sınıfı tanımını ve görselleştirme yöntemlerini içeren hücreleri çalıştırın.
   3. Eğitim hücresini çalıştırın.
        o Bu işlem ajanı 100,000 bölüm boyunca eğitir.
        o En iyi Q-Tablosunu otomatik olarak game_q_table.csv dosyasına kaydeder.
    4. Eğitimden sonra politika performansını test etmek için:
        o Test hücresini çalıştırın.
        o  Bu işlem CSV dosyasından Q-Tablosunu yükler ve ajanı ortamda çalıştırır.
        o Son ödülü ve ajanın yolunun görselleştirmesini görüntüler.

- Sonuçlar:


  ![res1](res1.png)

  ![res2](res2.png)

  Eğitim süreci 100.000 bölüm boyunca gerçekleştirildi. Sonuçlar, ajanın ortamda gezinmek üzere başarıyla eğitildiğini ve 20575. Bölümde 66,9 puanlık en iyi kümülatif ödüle ulaştığını gösteriyor.
