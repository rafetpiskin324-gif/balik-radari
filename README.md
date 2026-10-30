# Balık Radarı — Render'a yayınlama

## Yerelde çalıştırma
```
npm install
GEMINI_API_KEY=anahtarin node server.js    # Windows PowerShell: $env:GEMINI_API_KEY="anahtarin"; node server.js
```
Tarayıcıda http://localhost:3000 açın. Anahtar olmadan da uygulama çalışır; yalnızca Av Asistanı "yapılandırılmadı" der.

## Render'da yayınlama
1. Klasörü GitHub'da yeni bir (tercihen özel) depoya yükleyin. `.env` ve `node_modules` yüklenmez (.gitignore).
2. Render → New → **Blueprint** → depoyu seçin (render.yaml okunur). Ya da New → Web Service: Build `npm install`, Start `node server.js`.
3. Render → servis → **Environment** → `GEMINI_API_KEY` değişkenine Google AI Studio'dan aldığınız anahtarı yapıştırın. Anahtarı sohbete, koda veya GitHub'a yazmayın.
4. Deploy bitince `https://<servis-adı>.onrender.com/healthz` adresi `{"ok":true,"asistan":true,...}` göstermeli.
5. Anahtar yanlışlıkla bir yere sızdıysa AI Studio'dan silip yenisini oluşturun.

Notlar
- Ücretsiz Render planında servis boşta uyur; ilk açılış ~30-60 sn sürebilir.
- Model adı `GEMINI_MODEL` ile değiştirilir (Google modeli kaldırırsa asistan "yapılandırma hatası" verir).
- Asistan IP başına dakikada 8 istekle sınırlıdır (server.js).
- Üretimde OpenStreetMap'in ücretsiz döşeme sunucusu yoğun kullanım için uygun değildir; anahtarlı bir sağlayıcıya geçin. Open-Meteo ücretsiz sürümü ticari kullanım için lisans gerektirir.

## Canlı balıkçı sohbeti
- Oda başına (Genel + 81 il) canlı sohbet: sunucu → tarayıcı akışı SSE ile, gönderme POST ile çalışır; ek paket gerekmez.
- **Mesajlar bellekte tutulur** (odada en fazla 100, 24 saat). Sunucu yeniden başlayınca veya ücretsiz planda uyuyunca silinir. Kalıcı arşiv ve gerçek hesaplar için veritabanı (ör. Supabase/PostgreSQL) gerekir.
- Korumalar: takma ad zorunlu, mesaj ≤ 300 karakter, 2 sn bekleme, IP başına dakikada 20 mesaj, bağlantı ve telefon numarası engeli, aynı mesaj tekrarı engeli, 3 farklı kişi bildirirse mesaj gizlenir.
- Uygunsuz kelime filtresi için Render'da `BLOCKED_WORDS` değişkenine virgülle ayrılmış kelimeler yazın.
- Tek bir Render örneği (ücretsiz plan dahil) için yeterlidir; birden fazla örneğe ölçeklenirse mesajlar örnekler arasında paylaşılmaz.
- Gerçek kullanıcıya açmadan önce gizlilik/kullanım koşulu metni ve gerekirse yönetici (moderatör) paneli ekleyin.

## Hız
- Hava/deniz verisi sunucuda 10 dakika önbellekte tutulur (herkes için ortak). Sık açılan 12 il arka planda sıcak tutulur. Eski veri anında gösterilir, arka planda yenilenir.
- Metin/JSON yanıtları sıkıştırılır. Harita ve ikon kütüphaneleri sayfayı bloklamaz.
- **En büyük yavaşlık Render ücretsiz planının uyku modudur** (15 dk işlem olmazsa uyur, ilk açılış 30-60 sn sürer). Çözüm: ücretli plan (Starter) veya uptimerobot.com ile `/healthz` adresini 5 dakikada bir çağırtmak.

## Balık fotoğrafları
- Sunucu, Wikipedia/Wikimedia Commons'tan her türün makale fotoğrafını bulur; yazar ve lisansı detay sayfasında gösterilir. Önce `public/images/fish/<tur>.jpg` varsa o kullanılır (kendi fotoğrafınız önceliklidir).
- Türle eşleşmeyi gözle kontrol edin; yanlış fotoğraf çıkarsa doğru fotoğrafı `public/images/fish/` içine koyun (levrek.jpg, cipura.jpg...). `WIKI_CONTACT` değişkenine e-posta/site yazmanız Wikimedia için iyi uygulamadır.
