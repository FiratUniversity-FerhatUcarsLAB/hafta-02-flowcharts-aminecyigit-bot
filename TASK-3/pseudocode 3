BEGIN RandevuAl
    DISPLAY "Hoş geldiniz. Lütfen bilgilerinizi giriniz."
    INPUT hastaAd, hastaSoyad, TCKimlikNo
    DISPLAY "Bölüm Seçiniz: Kardiyoloji, Dahiliye, Ortopedi, Göz, Kulak Burun Boğaz"
    INPUT bolum
    DISPLAY "Tarih seçiniz (gg/aa/yyyy):"
    INPUT randevuTarihi
    DISPLAY "Saat seçiniz (09:00 - 17:00 arası):"
    INPUT randevuSaati

    IF randevuMüsaitMi(randevuTarihi, randevuSaati, bolum) == TRUE THEN
        randevuID ← RandevuKaydet(hastaAd, hastaSoyad, TCKimlikNo, bolum, randevuTarihi, randevuSaati)
        DISPLAY "Randevunuz başarıyla oluşturuldu. Randevu No: " + randevuID
    ELSE
        DISPLAY "Seçtiğiniz saat dolu. Lütfen başka bir saat seçiniz."
    END IF
END

BEGIN TahlilSistemi
    DISPLAY "Tahlil Sistemi'ne Hoş Geldiniz"
    INPUT TCKimlikNo

    IF HastaVarMi(TCKimlikNo) == TRUE THEN
        DISPLAY "Yapılacak tahlili seçiniz: Kan, İdrar, MR, Röntgen"
        INPUT tahlilTuru
        DISPLAY "Tahlil tarihi giriniz (gg/aa/yyyy):"
        INPUT tahlilTarihi

        tahlilID ← TahlilKaydet(TCKimlikNo, tahlilTuru, tahlilTarihi)
        DISPLAY "Tahlil kaydı oluşturuldu. Tahlil ID: " + tahlilID
    ELSE
        DISPLAY "Hasta kaydı bulunamadı. Lütfen önce randevu alınız."
    END IF
END
BEGIN HastaneSistemi
    DISPLAY "1 - Randevu Al"
    DISPLAY "2 - Tahlil Yaptır"
    INPUT secim

    IF secim == 1 THEN
        CALL RandevuAl
    ELSE IF secim == 2 THEN
        CALL TahlilSistemi
    ELSE
        DISPLAY "Geçersiz seçim."
    END IF
END
