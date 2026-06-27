# İSGCep Gizlilik Politikası

**Son güncelleme:** 27 Haziran 2026

Bu Gizlilik Politikası, İSGCep mobil uygulamasını ("Uygulama", "İSGCep") kullanırken hangi bilgilerin toplandığını, nasıl kullanıldığını ve korunduğunu açıklamaktadır. Uygulamayı kullanarak bu politikayı kabul etmiş olursunuz.

---

## 1. Veri Sorumlusu

İSGCep'in işletmecisi: **Ali Özben** (gerçek kişi)
İletişim: **isgcepp@gmail.com**

> Not: İSGCep şu aşamada bir şirket çatısı olmadan, gerçek kişi (şahıs) sıfatıyla işletilmektedir. İleride bir şahıs/limited şirket açılırsa bu alan şirket unvanıyla güncellenmelidir.

---

## 2. Toplanan Veriler

### 2.1 Hesap Bilgileri (Supabase Auth üzerinden)
- E-posta adresi
- Kullanıcı kimliği (UUID) — sistem tarafından otomatik oluşturulur, kişisel bilgi içermez
- Şifre (şifrelenmiş/hash'lenmiş olarak saklanır, biz okuyamayız)

### 2.2 Kullanım ve Performans Verileri
- Sınav/deneme sonuçları, yanlış cevaplanan sorular, çalışma geçmişi
- Sertifika sınıfı tercihi (DSP/A/B/C) ve varsa hedef sınav tarihi
- Flashcard tekrar (spaced repetition) ilerleme verisi

Bu veriler, size kişiselleştirilmiş bir öğrenme deneyimi sunmak (yanlış soru havuzu, ilerleme takibi) amacıyla işlenir.

### 2.3 Satın Alma Bilgileri (RevenueCat üzerinden)
- Abonelik durumu (aktif/pasif, paket türü)
- Satın alma geçmişi ve işlem kimlikleri

**Önemli:** Kredi kartı bilgileriniz bize veya RevenueCat'e hiçbir zaman ulaşmaz; ödemeler doğrudan Apple App Store veya Google Play altyapısı üzerinden işlenir.

### 2.4 Cihaz Üzerinde Saklanan Veriler (Hive)
Uygulama, çevrimdışı kullanım için bazı verileri (sınav içerikleri, geçici oturum durumu, önbelleğe alınmış abonelik durumu) cihazınızda yerel olarak saklar. Bu veriler sunucularımıza otomatik olarak iletilmez; senkronizasyon yalnızca internet bağlantısı olduğunda ve ilgili işlemler sırasında gerçekleşir.

### 2.5 Teknik Veriler
- IP adresi, cihaz/işletim sistemi bilgisi, uygulama sürümü (hata ayıklama ve güvenlik amaçlı, varsa crash-reporting aracı üzerinden)

> Not: Eğer Firebase Crashlytics, Sentry veya benzeri bir araç eklerseniz bu alt başlığı genişletin ve ilgili aracın adını belirtin.

---

## 3. Verilerin Kullanım Amaçları

- Hesabınızı oluşturmak ve oturum açmanızı sağlamak
- Sınav/deneme sonuçlarınızı kaydetmek, yanlış soru havuzunu ve flashcard ilerlemenizi yönetmek
- Abonelik durumunuzu doğrulamak ve premium özelliklere erişiminizi yönetmek
- Uygulamanın teknik olarak düzgün çalışmasını sağlamak, hataları tespit edip gidermek
- Yasal yükümlülüklerin yerine getirilmesi

---

## 4. Verilerin Üçüncü TarAFlarla PaylaşıLmasI

Verileriniz aşağıdaki hizmet sağlayıcılarla, yalnızca belirtilen amaçlarla paylaşılır:

| Hizmet Sağlayıcı | Amaç | Paylaşılan Veri |
|---|---|---|
| Supabase | Hesap yönetimi, veritabanı barındırma | E-posta, kullanıcı ID, kullanım verisi |
| RevenueCat | Abonelik/satın alma yönetimi | Satın alma geçmişi, kullanıcı ID |
| Apple App Store / Google Play | Ödeme işleme | Ödeme bilgileri (bizim tarafımızdan görülmez) |

Verileriniz, yasal zorunluluk haricinde reklam amacıyla satılmaz veya kiralanmaz. **İSGCep'te üçüncü taraf reklam ağı kullanılmamaktadır** (kullanmaya başlarsanız bu politika güncellenmelidir).

---

## 5. Verilerin Saklanma Süresi

- Hesap verileri, hesabınızı silene kadar saklanır
- Hesap silme talebinde bulunduğunuzda, yasal saklama zorunlulukları (örn. fatura kayıtları) dışındaki tüm kişisel verileriniz makul bir süre içinde silinir

---

## 6. Kullanıcı Hakları

Aşağıdaki haklara sahipsiniz:
- Verilerinize erişim talep etme
- Yanlış/eksik verilerin düzeltilmesini isteme
- Hesabınızın ve ilişkili verilerinizin silinmesini talep etme
- Veri işlemeye itiraz etme

Bu talepleri **isgcepp@gmail.com** üzerinden iletebilirsiniz.

---

## 7. Veri Güvenliği

Verileriniz, Supabase'in sunduğu Row Level Security (RLS) politikaları ile korunur — bu, bir kullanıcının yalnızca kendi verilerine erişebilmesini, başka kullanıcıların verilerine erişememesini sağlar. Şifreleriniz hash'lenmiş olarak saklanır.

---

## 8. Çocukların Gizliliği

İSGCep, meslek sınavlarına hazırlanan yetişkin bireylere yönelik bir uygulamadır ve bilerek 18 yaş altı bireylerden veri toplamaz.

---

## 9. Politika Değişiklikleri

Bu politika zaman zaman güncellenebilir. Önemli değişikliklerde uygulama içinde bilgilendirme yapılacaktır.

---

## 10. İletişim

Sorularınız için: **isgcepp@gmail.com**

---

*Bu belge, İSGCep'in T.C. Çalışma ve Sosyal Güvenlik Bakanlığı veya ÖSYM ile bir bağlantısı olmadığını, eğitim ve sınav hazırlık amaçlı bağımsız bir uygulama olduğunu beyan eder.*
