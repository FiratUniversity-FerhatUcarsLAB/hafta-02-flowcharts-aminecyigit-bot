FONKSIYON E_TICARET_SEPET_VE_ODEME_SISTEMI
BAŞLA
    
    // --- 1. KULLANICI OTURUMU VE SEPET BAŞLANGICI ---
    
    // KOŞUL: Kullanıcı Giriş Kontrolü
    EĞER KULLANICI_OTURUM_ACIK MI İSE
        KullaniciID = OTURUM_ID_AL()
        SEPET = VERITABANINDAN_SEPET_GETIR(KullaniciID)
    DEĞİLSE
        KullaniciID = "MİSAFİR"
        SEPET = YENI_GEÇICI_SEPET_OLUSTUR()
    SON_EĞER
    
    ISLEM_SECIM_TAMAMLANDI = YANLIS

    // --- 2. SEPET YÖNETİM DÖNGÜSÜ ---
    
    DÖNGÜ ISLEM_SECIM_TAMAMLANDI EŞİTTİR YANLIS İKEN
        EKRANA_YAZ("1-Ürün Ekle, 2-Sepeti Düzenle, 3-Ödeme Adımına Geç")
        SECIM = KULLANICIDAN_GIRIS_AL()

        EĞER SECIM EŞİTTİR 1 İSE
            // 2.1. ÜRÜN EKLEME VE STOK KONTROL NOKTASI
            UrunID = KULLANICIDAN_URUN_ID_AL()
            ADET = KULLANICIDAN_ADET_AL()
            STOK = STOK_VERISINI_AL(UrunID)
            
            // KOŞUL: Stok Yeterlilik Kontrolü
            EĞER ADET <= STOK İSE
                SEPET = SEPETE_URUN_EKLE(UrunID, ADET)
                EKRANA_YAZ("Ürün sepete eklendi.")
            DEĞİLSE
                EKRANA_YAZ("HATA: Yeterli stok bulunmamaktadır. Mevcut: " + STOK)
            SON_EĞER

        DEĞİLSE EĞER SECIM EŞİTTİR 2 İSE
            // 2.2. SEPET GÖRÜNTÜLEME VE DÜZENLEME
            SEPET_DETAYLARINI_GOSTER(SEPET)
            // Varsayım: Kullanıcı düzenleme yapabilir ve SEPET güncellenir.

        DEĞİLSE EĞER SECIM EŞİTTİR 3 İSE
            ISLEM_SECIM_TAMAMLANDI = DOĞRU
            EKRANA_YAZ("Ödeme sürecine geçiliyor...")

        DEĞİLSE
            EKRANA_YAZ("Geçersiz seçim. Lütfen tekrar deneyin.")
        SON_EĞER
    SON_DÖNG // SEPET DÖNGÜSÜ BİTTİ
    
    // KOŞUL: Sepet Boş Kontrol Noktası
    EĞER SEPET_BOS_MU(SEPET) İSE
        EKRANA_YAZ("Sepetiniz boş olduğu için ödeme yapılamaz.")
        BITIR
    SON_EĞER


    // --- 3. ÖDEME ADIMLARI VE KONTROLLER ---

    // 3.1. Adres ve Kargo Hesaplama
    ADRES_BILGILERI = KULLANICIDAN_ADRES_AL()
    SEPET_TUTARI = SEPET_TUTARI_HESAPLA(SEPET)
    
    // KONTROL NOKTASI: Kargo Hesaplama
    KARGO_UCRETİ = KARGO_HESAPLA(ADRES_BILGILERI.TeslimatAdresi, SEPET_TUTARI)
    GENEL_TOPLAM = SEPET_TUTARI + KARGO_UCRETİ
    EKRANA_YAZ("Kargo Ücreti: " + KARGO_UCRETİ + " TL")

    // 3.2. İndirim Kodu Kontrol Noktası
    EKRANA_YAZ("İndirim Kodu Giriniz (Varsa):")
    INDIRIM_KODU = KULLANICIDAN_GIRIS_AL()
    
    EĞER INDIRIM_KODU BOŞ DEĞİL İSE
        INDIRIM_BILGI = INDIRIM_KODU_DOGRULA(INDIRIM_KODU, SEPET_TUTARI) 
        
        // KOŞUL: İndirim Kodu Geçerlilik Kontrolü
        EĞER INDIRIM_BILGI.Geçerli İSE
            GENEL_TOPLAM = GENEL_TOPLAM - INDIRIM_BILGI.Tutar
            EKRANA_YAZ("İndirim Uygulandı. İndirim Miktarı: " + INDIRIM_BILGI.Tutar)
        DEĞİLSE
            EKRANA_YAZ("HATA: " + INDIRIM_BILGI.HataMesajı)
        SON_EĞER
    SON_EĞER
    
    // Nihai Tutar Gösterimi
    EKRANA_YAZ("Ödenecek Toplam Tutar: " + GENEL_TOPLAM + " TL")
    ODEME_YONTEMI = KULLANICIDAN_ODEME_YONTEMI_SEC()

    // 3.3. SON STOK KONTROL NOKTASI (Kilitlenme Önleme)
    // KOŞUL: Son Stok Kontrolü
    EĞER STOKLAR_YETERLI_MI(SEPET) EŞİTTİR YANLIS İSE
        EKRANA_YAZ("HATA: Ödeme sırasında bir ürünün stoğu tükendi. Lütfen sepeti düzenleyin.")
        BITIR
    SON_EĞER

    // 3.4. ÖDEME İŞLEMİ KONTROL NOKTASI
    ODEME_BILGILERI = KULLANICIDAN_ODEME_BILGISI_AL(ODEME_YONTEMI)
    EKRANA_YAZ("Ödeme işleniyor...")
    
    ODEME_SONUCU = ODEME_AG_GECIDINE_GÖNDER(ODEME_BILGILERI, GENEL_TOPLAM)
    
    // KOŞUL: Ödeme Başarısı Kontrolü
    EĞER ODEME_SONUCU EŞİTTİR BAŞARILI İSE
        // 3.5. Başarılı İşlem ve Güncelleme
        SİPARİS_ID = SİPARİS_OLUSTUR(KullaniciID, SEPET, ADRES_BILGILERI, GENEL_TOPLAM)
        
        STOKLARI_GUNCELLE(SEPET)      // Kritik İşlem: Stoklar düşülür
        SEPETI_TEMIZLE(KullaniciID)  // Sepet temizlenir
        
        EKRANA_YAZ("Siparişiniz başarıyla alındı! Sipariş No: " + SİPARİS_ID)
    DEĞİLSE
        EKRANA_YAZ("HATA: Ödeme işlemi başarısız. Lütfen bilgilerinizi kontrol edin.")
    SON_EĞER

BITIR
