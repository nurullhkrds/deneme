                                    ┌─────────────────────────────────┐
                                    │          İŞLEM BAŞLANGICI        │
                                    │         (Girişçi Talebi)         │
                                    └─────────────────┬───────────────┘
                                                       │
                                                       ▼
                                    ┌─────────────────────────────────┐
                                    │         VALİDASYONLAR           │
                                    │  • Format kontrolü              │
                                    │  • Zorunlu alan kontrolü        │
                                    │  • Mükerrerlik kontrolü         │
                                    └─────────────────┬───────────────┘
                                                       │
                                          ┌────────────┴────────────┐
                                         OK                        NOK
                                          │                          │
                                          │                          ▼
                                          │             ┌────────────────────────┐
                                          │             │         ❌ ERROR        │
                                          │             │   Validasyon Hatası     │
                                          │             │   İşlem Sonlandı        │
                                          │             └────────────────────────┘
                                          │
                                          ▼
                                    ┌─────────────────────────────────┐
                                    │          FLOWX START             │
                                    └─────────────────┬───────────────┘
                                                       │
                                                       ▼
                                    ┌─────────────────────────────────┐
                                    │        teller_process            │
                                    │      STATUS = IN_PROGRESS        │
                                    └─────────────────┬───────────────┘
                                                       │
                                                       ▼
                                    ┌─────────────────────────────────┐
                                    │       LİMİT ÜSTÜ KONTROL        │
                                    │   İşlem tutarı > limit mi?      │
                                    └─────────────────┬───────────────┘
                                                       │
                                          ┌────────────┴────────────┐
                                        HAYIR                      EVET
                                   (Limit Dahili)             (Limit Üstü)
                                          │                          │
                                          ▼                          ▼
                             ┌────────────────────┐    ┌────────────────────────┐
                             │     FLOWX END       │    │     FLOWX DEVAM        │
                             └─────────┬──────────┘    └────────────┬───────────┘
                                       │                             │
                                       ▼                             ▼
                             ┌────────────────────┐    ┌────────────────────────┐
                             │   teller_process    │    │     teller_process     │
                             │  STATUS = SUCCESS   │    │  STATUS =              │
                             │                     │    │  BRANCH_WAITING_       │
                             │  ✅ İşlem Bitti      │    │     APPROVAL           │
                             └────────────────────┘    └────────────┬───────────┘
                                                                     │
                                                                     ▼
                                                        ┌────────────────────────┐
                                                        │    🔔 ONAYCI EKRANI     │
                                                        │  Onaycıya Bildirim     │
                                                        │  Gönderildi            │
                                                        └────────────┬───────────┘
                                                                     │
                                                                     │
                                              ┌──────────────────────┼──────────────────────┐
                                              │                       │                      │
                                           KABUL                   REDDET                  İADE
                                              │                       │                      │
                                              ▼                       ▼                      ▼
                                ┌─────────────────────┐  ┌───────────────────────┐  ┌───────────────────────┐
                                │   teller_process     │  │    teller_process     │  │    teller_process     │
                                │   STATUS =           │  │    STATUS =           │  │    STATUS =           │
                                │ BRANCH_OPERATION_    │  │  BRANCH_OPERATION_    │  │  BRANCH_OPERATION_    │
                                │    APPROVAL          │  │      REJECT           │  │      RETURN           │
                                └──────────┬──────────┘  └───────────┬───────────┘  └───────────┬───────────┘
                                           │                          │                           │
                                           ▼                          ▼                           ▼
                                ┌─────────────────────┐  ┌───────────────────────┐  ┌───────────────────────┐
                                │  🔔 GİRİŞÇİ EKRANI   │  │   teller_process      │  │  🔔 GİRİŞÇİ EKRANI    │
                                │  Onay geldi          │  │   STATUS = REJECTED   │  │  İade geldi           │
                                │  Ödeme yapılabilir   │  │                       │  │  Düzeltme yapılabilir │
                                └──────────┬──────────┘  │   ❌ İşlem Reddedildi  │  └───────────┬───────────┘
                                           │             │      Akış Bitti        │              │
                                           ▼             └───────────────────────┘              │
                                ┌─────────────────────┐                                         │
                                │   GİRİŞÇİ ÖDEMEYİ  │                                         │
                                │      YAPIYOR         │                                         │
                                └──────────┬──────────┘                                         │
                                           │                                                     │
                                           ▼                                                     │
                                ┌─────────────────────┐                                         │
                                │   teller_process     │                                         │
                                │  STATUS = COMPLETED  │                                         │
                                │                      │                                         │
                                │  ✅ İşlem Tamamlandı │                                         │
                                └─────────────────────┘                                         │
                                                                                                 │
                                                                                                 ▼
                                                                                  ┌──────────────────────────┐
                                                                                  │   GİRİŞÇİ İNCELER &      │
                                                                                  │   DÜZELTİR               │
                                                                                  │   Tekrar Onaya Gönderir  │
                                                                                  └──────────────┬───────────┘
                                                                                                 │
                                                                                                 ▼
                                                                                  ┌──────────────────────────┐
                                                                                  │      teller_process       │
                                                                                  │      STATUS =             │
                                                                                  │   BRANCH_WAITING_         │
                                                                                  │      APPROVAL             │
                                                                                  └──────────────┬───────────┘
                                                                                                 │
                                                                                                 │
                                                                                    ◄────────────┘
                                                                               ONAYCI EKRANINA GERİ DÖNER
                                                                                    (Döngü Devam Eder)



















                                                                                                        IN_PROGRESS
                         │
              ┌──────────┴──────────┐
              │                     │
           SUCCESS        BRANCH_WAITING_APPROVAL ◄──────────────────┐
         (Limit OK)                 │                                 │
                        ┌───────────┼────────────┐                   │
                        │           │            │                   │
                     KABUL       REDDET        İADE                  │
                        │           │            │                   │
               BRANCH_OPERATION_ BRANCH_    BRANCH_OPERATION_        │
                  APPROVAL      OPERATION_    RETURN                  │
                        │        REJECT          │                   │
                        │           │            │                   │
                     COMPLETED   REJECTED    Girişçi Düzeltir ───────┘
