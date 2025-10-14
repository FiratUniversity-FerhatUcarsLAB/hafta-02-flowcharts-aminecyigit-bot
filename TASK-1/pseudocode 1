
// 1. Kart Girişi
EKRANA_YAZ("Lütfen kartınızı takınız.")
BEKLE_KART_GIRISI()

EĞER KART_TAKILDI İSE
    KartID = KARTTAN_OKU_ID()
    HESAP_BILGILERI = HESAP_VERISI_AL(KartID)
    DOGRULANMIS_PIN = HESAP_BILGILERI.PIN_KODU
    BAKIYE = HESAP_BILGILERI.BAKIYE
    PIN_DENEME_SAYISI = 0
    PIN_DOGRULANDI = YANLIS
    
    // 2. PIN Kontrolü (3 Deneme Hakkı)
    DÖNGÜ_SAYACI = 1'den 3'e KADAR ADIM 1
        EKRANA_YAZ("Lütfen PIN kodunuzu giriniz. (" + DÖNGÜ_SAYACI + ". deneme)")
        KULLANICI_PIN = KLAVYEDEN_GIRILEN_DEGER_AL()

        EĞER KULLANICI_PIN EŞİTTİR DOGRULANMIS_PIN İSE
            PIN_DOGRULANDI = DOĞRU
            DÖNGÜDEN_ÇIK
        DEĞİLSE
            EKRANA_YAZ("Hatalı PIN kodu.")
            PIN_DENEME_SAYISI = PIN_DENEME_SAYISI + 1
        SON_EĞER
    SON_DÖNGÜ
    
    EĞER PIN_DOGRULANDI EŞİTTİR YANLIS İSE
        EKRANA_YAZ("PIN 3 kez hatalı girildi. Kartınız bloke edilmiştir.")
        KART_BLOKE_ET(KartID)
        KART_IADE_ET()
        SISTEMDEN_CIK
    SON_EĞER
    
    // PIN Başarılıysa
    EKRANA_YAZ("PIN doğrulandı. İşlem menüsü yükleniyor...")
    
    // 3. Para Çekme İşlemi Başlangıcı
    EKRANA_YAZ("Lütfen çekmek istediğiniz tutarı giriniz.")
    TUTAR_GEÇERLİ = YANLIS

    DÖNGÜ TUTAR_GEÇERLİ EŞİTTİR YANLIS İKEN
        ÇEKME_TUTARI = KLAVYEDEN_GIRILEN_DEGER_AL()

        // Tutar Kontrolü 1: Minimum/Maksimum Limit ve Banknot Kontrolü
        EĞER ÇEKME_TUTARI < MINIMUM_CEKIM_TUTARI İSE
            EKRANA_YAZ("Hata: Çekilebilecek minimum tutar " + MINIMUM_CEKIM_TUTARI + " TL'dir.")
        DEĞİLSE EĞER ÇEKME_TUTARI > MAKSIMUM_CEKIM_TUTARI İSE
            EKRANA_YAZ("Hata: Çekilebilecek maksimum tutar " + MAKSIMUM_CEKIM_TUTARI + " TL'dir.")
        DEĞİLSE EĞER BANKNOT_KONTROLU(ÇEKME_TUTARI) EŞİTTİR YANLIS İSE
            EKRANA_YAZ("Hata: Lütfen 10 veya 20 TL gibi banknotlarla ödenebilecek bir tutar giriniz. (Örn: 50, 100, 120)")
        
        // Tutar Kontrolü 2: Bakiye Kontrolü
        DEĞİLSE EĞER ÇEKME_TUTARI > BAKIYE İSE
            EKRANA_YAZ("Hata: Yetersiz bakiye. Mevcut bakiyeniz: " + BAKIYE + " TL.")
        DEĞİLSE
            // Tüm kontroller başarılı
            TUTAR_GEÇERLİ = DOĞRU
        SON_EĞER
    SON_DÖNGÜ

    // 4. İşlemin Gerçekleştirilmesi
    EKRANA_YAZ("Para çekme işlemi onaylanıyor...")
    
    YENI_BAKIYE = BAKIYE - ÇEKME_TUTARI
    HESAP_BAKIYE_GUNCELLE(KartID, YENI_BAKIYE) // Veritabanı/Sistem güncellemesi
    PARA_DAGITICI_CALISTIR(ÇEKME_TUTARI) // ATM'nin parayı vermesi
    
    EKRANA_YAZ("İşlem başarılı. Lütfen paranızı alınız: " + ÇEKME_TUTARI + " TL")
    EKRANA_YAZ("Güncel bakiyeniz: " + YENI_BAKIYE + " TL")
    
    // 5. Kart İadesi ve Oturumu Kapatma
    EKRANA_YAZ("Lütfen kartınızı alınız.")
    KART_IADE_ET()

DEĞİLSE
    EKRANA_YAZ("Kart algılanmadı. Tekrar deneyiniz.")
SON_EĞER

EKRANA_YAZ("İyi günler dileriz.")
