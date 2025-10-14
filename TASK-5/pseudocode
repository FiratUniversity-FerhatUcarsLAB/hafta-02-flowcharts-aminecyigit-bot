BEGIN AkilliEvGuvenlikSistemi

    IF SistemAktifMi() == FALSE THEN
        DISPLAY "Sistem pasif. Güvenlik izleme başlatılmadı."
        EXIT
    END IF

    WHILE TRUE DO
        sensorVerileri ← SensorOku()  // hareket, kapı, pencere

        IF HareketVar(sensorVerileri) == TRUE THEN
            DISPLAY "Hareket algılandı."
        END IF

        IF KapiPencereAcik(sensorVerileri) == TRUE THEN
            DISPLAY "Kapı/Pencere açık!"
        END IF

        IF HareketVar(sensorVerileri) OR KapiPencereAcik(sensorVerileri) THEN
            KameraAktifEt()
            IF EvSahibiEvdeMi() == TRUE THEN
                DISPLAY "Yanlış alarm. Ev sahibi evde."
            ELSE
                alarmSeviyesi ← AlarmSeviyesiBelirle(sensorVerileri)
                BildirimGonder(alarmSeviyesi)
            END IF
        END IF

        DISPLAY "Bekleniyor... Yeni sensör verisi kontrol edilecek."
        WAIT(10)  // 10 saniye bekle

        IF AlarmDurumu() == "reset" THEN
            DISPLAY "Alarm sıfırlandı."
        ELSE
            DISPLAY "Alarm devam ediyor."
        END IF
    END WHILE

END
