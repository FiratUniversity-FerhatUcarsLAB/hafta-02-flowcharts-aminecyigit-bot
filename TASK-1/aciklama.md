İsim - Soy isim: Amine Ceren Yiğit
Öğrenci No:250541048

sistemin kısa açıklaması (maks. 5-6 satır)




FONKSIYON ATM_PARA_CEKME_SISTEMI
BASLA

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

BITIR


digraph ATM_PARA_CEKME_SISTEMI {
    // Graf ayarları
    rankdir=TB; // Yukarıdan aşağıya akış
    node [shape=box, style="filled", fillcolor="lightblue"];
    
    // Şekil tanımları
    start_node [shape=oval, label="BAŞLA", fillcolor="palegreen"];
    end_node [shape=oval, label="BİTİR", fillcolor="red"];
    
    // 1. Kart Girişi
    input_kart [shape=box, label="EKRANA_YAZ('Kartınızı takınız')\nBEKLE_KART_GİRİŞİ()"];
    decision_kart_takildi [shape=diamond, label="KART_TAKILDI mı?", fillcolor="yellow"];
    process_kart_oku [shape=box, label="KartID, PIN, Bakiye AL\nPIN_DENEME_SAYISI = 0"];
    process_kart_yok [shape=box, label="EKRANA_YAZ('Kart algılanmadı')"];

    // 2. PIN Kontrolü (Döngü)
    loop_pin_kontrol [shape=diamond, label="PIN_DENEME_SAYISI < 3 ve PIN_DOGRULANDI == YANLIŞ?", fillcolor="yellow"];
    input_pin [shape=box, label="KULLANICI_PIN AL"];
    decision_pin_dogru [shape=diamond, label="KULLANICI_PIN == DOGRULANMIS_PIN?", fillcolor="yellow"];
    process_pin_dogru [shape=box, label="PIN_DOGRULANDI = DOĞRU"];
    process_pin_yanlis [shape=box, label="EKRANA_YAZ('Hatalı PIN')\nPIN_DENEME_SAYISI++"];
    
    // Bloke Etme
    process_bloke [shape=box, label="EKRANA_YAZ('Kart bloke edildi')\nKART_BLOKE_ET()\nKART_İADE_ET()", fillcolor="salmon"];
    
    // 3. Para Çekme İşlemi Hazırlığı
    process_pin_basarili [shape=box, label="EKRANA_YAZ('PIN doğrulandı')"];
    
    // Tutar Kontrolü Döngüsü
    loop_tutar_gecerli [shape=diamond, label="TUTAR_GEÇERLİ == YANLIŞ İKEN", fillcolor="yellow"];
    input_tutar [shape=box, label="ÇEKME_TUTARI AL"];
    
    // Tutar Kontrol Zinciri (Kararlar)
    decision_min_tutar [shape=diamond, label="TUTAR < MİN_CEKİM?", fillcolor="yellow"];
    decision_max_tutar [shape=diamond, label="TUTAR > MAKS_CEKİM?", fillcolor="yellow"];
    decision_banknot [shape=diamond, label="BANKNOT_KONTROLU(TUTAR) == YANLIS?", fillcolor="yellow"];
    decision_bakiye [shape=diamond, label="TUTAR > BAKİYE?", fillcolor="yellow"];
    
    // Hata Mesajları
    process_hata_tekrar [shape=box, label="EKRANA_YAZ('Hata:...')"];
    process_tutar_gecerli [shape=box, label="TUTAR_GEÇERLİ = DOĞRU"];

    // 4. İşlemin Gerçekleştirilmesi
    process_guncelle [shape=box, label="YENİ_BAKİYE = BAKİYE - TUTAR\nHESAP_BAKİYE_GUNCELLE()"];
    process_para_ver [shape=record, label="{İşlem Başarılı|{PARA_DAĞITICI_ÇALIŞTIR(Tutar)|EKRANA_YAZ('Güncel bakiye...')}}", fillcolor="lightgreen"];
    
    // 5. Kapanış
    process_kart_iade [shape=box, label="KART_İADE_ET()\nEKRANA_YAZ('İyi günler dileriz.')"];

    // Bağlantılar (Akış)

    start_node -> input_kart;
    input_kart -> decision_kart_takildi;
    
    // Kart Kontrolü Akışı
    decision_kart_takildi -> process_kart_oku [label="Evet"];
    decision_kart_takildi -> process_kart_yok [label="Hayır"];
    process_kart_yok -> end_node;
    
    // PIN Kontrolü Akışı (Döngü)
    process_kart_oku -> loop_pin_kontrol;
    
    loop_pin_kontrol -> input_pin [label="Evet"];
    loop_pin_kontrol -> process_bloke [label="Hayır"]; // Bloke Durumu (3 deneme doldu VEYA PIN_DOGRULANDI = DOĞRU)
    
    input_pin -> decision_pin_dogru;
    
    decision_pin_dogru -> process_pin_dogru [label="Evet"];
    decision_pin_dogru -> process_pin_yanlis [label="Hayır"];
    
    process_pin_dogru -> process_pin_basarili; // Başarılı PIN -> Sonraki Adım
    process_pin_yanlis -> loop_pin_kontrol; // Yanlış PIN -> Döngü Başına
    
    // Bloke Akışı
    process_bloke -> end_node;
    
    // İşlem Akışı
    process_pin_basarili -> loop_tutar_gecerli;
    
    // Tutar Kontrolü Döngüsü Başlangıcı
    loop_tutar_gecerli -> input_tutar [label="Evet (Tutar Geçersiz)"];
    loop_tutar_gecerli -> process_guncelle [label="Hayır (Tutar Geçerli)"]; 
    
    input_tutar -> decision_min_tutar;

    // Tutar Kontrolleri ve Hatalar
    decision_min_tutar -> process_hata_tekrar [label="Evet (Min Hata)"];
    decision_min_tutar -> decision_max_tutar [label="Hayır"];

    decision_max_tutar -> process_hata_tekrar [label="Evet (Maks Hata)"];
    decision_max_tutar -> decision_banknot [label="Hayır"];
    
    decision_banknot -> process_hata_tekrar [label="Evet (Banknot Hata)"];
    decision_banknot -> decision_bakiye [label="Hayır"];

    decision_bakiye -> process_hata_tekrar [label="Evet (Bakiye Hata)"];
    decision_bakiye -> process_tutar_gecerli [label="Hayır"];
    
    // Hata durumlarında döngü başına dönme
    process_hata_tekrar -> loop_tutar_gecerli;
    
    // Tutar Geçerli ve İşlem Başlatma
    process_tutar_gecerli -> loop_tutar_gecerli; // Döngüyü kırmak için koşul tekrar kontrol edilecek

    // İşlemi Tamamlama
    process_guncelle -> process_para_ver;
    process_para_ver -> process_kart_iade;
    process_kart_iade -> end_node;
}
