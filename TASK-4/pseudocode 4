BEGIN DersKayitSistemi

    DISPLAY "Öğrenci Girişi"
    INPUT ogrenciNo, sifre

    IF GirisKontrol(ogrenciNo, sifre) == TRUE THEN

        dersListesi ← DersleriGetir()
        DISPLAY "Açık Dersler:"
        FOR ders IN dersListesi DO
            DISPLAY ders.ad + " (" + ders.kredi + " kredi, kontenjan: " + ders.kontenjan + ")"
        END FOR

        toplamKredi ← 0
        secilenDersler ← []
        
        WHILE KayıtDevam == TRUE DO
            INPUT dersKodu, islemTuru // ekle veya çıkar

            IF islemTuru == "ekle" THEN
                IF KontenjanUygun(dersKodu) == TRUE AND
                   OnKosulSaglandi(dersKodu, ogrenciNo) == TRUE AND
                   ZamanCakismasiYok(dersKodu, secilenDersler) == TRUE AND
                   (toplamKredi + DersKredi(dersKodu)) <= 35 THEN

                    secilenDersler.ADD(dersKodu)
                    toplamKredi ← toplamKredi + DersKredi(dersKodu)
                    DISPLAY "Ders eklendi."

                ELSE
                    DISPLAY "Ders eklenemedi: Kontrol koşullarından biri sağlanmadı."
                END IF

            ELSE IF islemTuru == "çıkar" THEN
                IF dersKodu IN secilenDersler THEN
                    secilenDersler.REMOVE(dersKodu)
                    toplamKredi ← toplamKredi - DersKredi(dersKodu)
                    DISPLAY "Ders çıkarıldı."
                ELSE
                    DISPLAY "Bu dersi seçmemişsiniz."
                END IF
            END IF

            DISPLAY "Ders ekleme/çıkarma işlemi devam etsin mi? (E/H)"
            INPUT cevap
            IF cevap == "H" THEN
                KayıtDevam ← FALSE
            END IF
        END WHILE

        GPA ← GetirNotOrtalamasi(ogrenciNo)
        IF GPA < 2.5 THEN
            DISPLAY "Danışman onayı gereklidir."
        ELSE
            DISPLAY "Danışman onayı gerekli değil."
        END IF

        DISPLAY "Kayıt Özeti:"
        FOR ders IN secilenDersler DO
            DISPLAY ders
        END FOR
        DISPLAY "Toplam Kredi: " + toplamKredi
        DISPLAY "Kayıt onaylansın mı? (E/H)"
        INPUT onay

        IF onay == "E" THEN
            KaydetKayit(ogrenciNo, secilenDersler)
            DISPLAY "Kayıt başarıyla tamamlandı."
        ELSE
            DISPLAY "Kayıt iptal edildi."
        END IF

    ELSE
        DISPLAY "Giriş bilgileri hatalı!"
    END IF

END
