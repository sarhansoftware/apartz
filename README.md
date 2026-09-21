# Apartz

Apartz, apart otel ve günlük kiralık daire ilanları için mobil uyumlu bir web platformudur. İlk sürüm ziyaretçi ilan arama, takip/karşılaştırma, firma paneli, süper admin paneli, paket fiyat yönetimi, havale talebi, abonelik ve ilan onay iş kurallarını içerir.

## Teknoloji

- Next.js App Router uyumlu Vinext çalışma zamanı ve TypeScript
- Tailwind CSS, shadcn tabanlı erişilebilir UI bileşenleri
- PostgreSQL + Prisma ORM
- Zod doğrulama
- iron-session tabanlı güvenli oturum yardımcıları
- MinIO/S3 yapılandırması ve Sharp fotoğraf işleme scripti
- Redis + BullMQ abonelik süresi arka plan işçisi
- Playwright smoke testleri

## Yerel Kurulum

```powershell
npm install
Copy-Item .env.example .env
npm run db:generate
npm run dev
```

Uygulama varsayılan olarak `http://localhost:5173` adresinde açılır.

## PostgreSQL, Redis ve MinIO

`docker-compose.yml` yerel geliştirme için Postgres, Redis ve MinIO servislerini tanımlar:

```powershell
docker compose up -d
npm run db:push
npm run db:seed
```

Bu makinede Docker CLI kurulu olmadığı için konteynerleri burada başlatamadım. Docker kurulduğunda yukarıdaki komutlar veritabanı şemasını uygular ve örnek geliştirme verilerini yükler.

## Önemli Komutlar

```powershell
npm run dev
npm run build
npm run lint
npm test
npm run test:e2e
npm run db:generate
npm run db:push
npm run db:seed
npm run admin:create -- admin@example.com guclu-bir-parola
npm run worker:expire
npm run worker:expire:once
npm run photo:process -- .\ornek.jpg public\processed
```

İlk süper admin oluştururken `.env` içinde `ADMIN_SETUP_TOKEN` gerçek ve tek kullanımlık bir değer olmalıdır. Üretime sabit admin parolası eklenmedi.

## Uygulama Yüzeyleri

Ziyaretçi:

- `/`
- `/ilanlar`
- `/ilan/[slug]`
- `/isletme/[slug]`
- `/takip-listem`
- `/karsilastir`

Firma:

- `/firma`
- `/firma/profil`
- `/firma/ilanlar`
- `/firma/ilanlar/yeni`
- `/firma/ilanlar/[id]/duzenle`
- `/firma/ilan-aktar`
- `/firma/paketler`
- `/firma/abonelik`
- `/firma/odemeler`
- `/firma/odemeler/[id]`

Süper admin:

- `/admin`
- `/admin/firmalar`
- `/admin/ilanlar`
- `/admin/ilan-onaylari`
- `/admin/paketler`
- `/admin/odemeler`
- `/admin/abonelikler`
- `/admin/istatistikler`
- `/admin/aktarimlar`
- `/admin/ayarlar`

## İş Kuralları

- Basic: 3 ilan, 15 gün ücretsiz, doğrulanmış işletme başına tek kullanım.
- Standart, Plus ve Business paket fiyatları süper admin tarafından güncellenir.
- Fiyatlar toplam paket ücretidir; yıllık fiyat aylığın 12 katına zorlanmaz.
- Yıllık fiyat tanımlı değilse yıllık satın alma kapalıdır.
- Ücretli paketlerde sıfır veya negatif fiyat kabul edilmez.
- Havale talepleri tutar, paket, dönem, ilan limiti ve fiyatlandırma snapshotı saklar.
- Mevcut aboneliklerin süre ve hakları fiyat değişikliğinden etkilenmez.
- Ödeme onayı ilan içerik onayını atlamaz.
- İlan içerikleri sürümlenir; eski sürüm onayı güncel içeriği yayınlamaz.
- Aktif ilan düzenlenince ziyaretçiden kalkar ve yeniden onaya gider.
- Paket süresi dolan ilanlar sunucu görünürlük kontrolüyle ziyaretçiye kapanır.
- Firma izolasyonu sunucu iş kurallarında kontrol edilir.
- Admin/firma panelleri robots/noindex kapsamındadır; sitemap yalnızca yayınlanabilir ziyaretçi sayfalarını içerir.

## Dış Servis Yapılandırmaları

Gerçek banka entegrasyonu yoktur; havale kontrolü manuel akış olarak modellenmiştir. Gerçek ortamda aşağıdakiler `.env` üzerinden tanımlanmalıdır:

- `DATABASE_URL`
- `SESSION_PASSWORD`
- `S3_ENDPOINT`, `S3_BUCKET_ORIGINALS`, `S3_BUCKET_PUBLIC`
- `S3_ACCESS_KEY_ID`, `S3_SECRET_ACCESS_KEY`
- `REDIS_URL`
- `MAP_TILE_URL`
- gerçek alıcı şirket ve IBAN ayarları

## Doğrulama

Bu çalışma sırasında çalıştırılan kontroller:

- `npm run db:generate`
- `npm test`
- `npm run lint`
- `npm run build`
- `npm run test:e2e`

Playwright Chromium tarayıcısı indirildikten sonra desktop ve mobil smoke testleri geçti.
