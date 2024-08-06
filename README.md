package com.ykb.payments.bill.transaction.common.exception;

import com.ykb.architecture.micro.error.exception.data.ExceptionData;

public class PaymentExceptions {

	private PaymentExceptions() {
		throw new IllegalStateException("PaymentExceptions: Constant class");
	}

	public static final String SHORT_APP_NAME = "billpayment-transaction";
	
    public static final ExceptionData SUCCESS = new ExceptionData(SHORT_APP_NAME, 0L, "Başarılı İşlem");


	public static class ValidationExceptions {

        private ValidationExceptions() {
            throw new IllegalStateException("ValidationExceptions: Constant class");
        }

        public static final ExceptionData SERVICE_DEFINITION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2000L, "Kurum Servis Tanımı Bulunamadı");

        public static final ExceptionData TOKEN_RECORD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2001L, "Kurum Token Kaydı Bulunamadı");

        public static final ExceptionData PARAMETER_RECORD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2002L, "İlgili Parametre Kaydı Bulunamadı");

        public static final ExceptionData TOKEN_REFRESH_UNKNOWN_ERROR = new ExceptionData(SHORT_APP_NAME, 2003L, "Yeni Token Alınırken Bilinmiyen Hata Meydana Geldi");

        public static final ExceptionData MINUS_PAYMENT_AMOUNT = new ExceptionData(SHORT_APP_NAME, 2004L, "Ödeme tutarını sıfırdan küçük giremezsiniz");

        public static final ExceptionData INVALID_PAYMENT_AMOUNT_FORMAT = new ExceptionData(SHORT_APP_NAME, 2005L, "Geçersiz ödeme tutarı formatı");

        public static final ExceptionData ACCOUNT_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2006L, "Hesap bulunamadı");

        public static final ExceptionData CREDIT_CARD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2007L, "Kredi Kartı bulunamadı");

        public static final ExceptionData NOT_NULL_ACCOUNT_FOR_CREDIT_CARD_SOURCE = new ExceptionData(SHORT_APP_NAME, 2008L, "Kredi kartından ödeme yöntemi için hesap numarası girilemez");

        public static final ExceptionData NOT_NULL_CREDIT_CARD_FOR_ACCOUNT_SOURCE = new ExceptionData(SHORT_APP_NAME, 2009L, "Hesaptan ödeme yöntemi için kredi kartı numarası girilemez");

        public static final ExceptionData SEQUENCE_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2010L, "Kurumun Sequence Bilgisi Bulunamadı");

        public static final ExceptionData PACKAGE_GROUP_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2011L, "Kurumun Paket Grup Bilgisi Bulunamadı");

        public static final ExceptionData PACKAGES_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2012L, "Kurumun Paket Bilgileri Bulunamadı");

        public static final ExceptionData INQUIRY_TYPE_NOT_SUPPORTED = new ExceptionData(SHORT_APP_NAME, 2013L, "Kurumun Sorgulama Tipi Bulunamadı");

        public static final ExceptionData SALE_CHANNEL_IS_INVALID = new ExceptionData(SHORT_APP_NAME, 2014L, "Kanal Kodu Hatalı");

        public static final ExceptionData CHANNEL_CODE_IS_EMPTY = new ExceptionData(SHORT_APP_NAME, 2015L, "Kanal Kodu Boş Olamaz");

        public static final ExceptionData INSTITUTION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2016L, "Aktif Kurum Tanımı Bulunamadı");

        public static final ExceptionData PAYMENT_METHOD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2017L, "Aktif Ödeme Metodu Tanımı Bulunamadı");

        public static final ExceptionData INSTITUTION_CHANNEL_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2018L, "Aktif Kanal Tanımı Bulunamadı");

        public static final ExceptionData INSTITUTION_CHANNEL_PAYMENT_METHOD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2019L, "İşlem Kanalına Ait Aktif Ödeme Metodu Tanımı Bulunamadı");

        public static final ExceptionData INSTITUTION_PAYMENT_METHOD_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2020L, "Aktif Kurum Ödeme Metodu Tanımı Bulunamadı");

        public static final ExceptionData INSTITUTION_ACCOUNT_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2021L, "Aktif Kurum Hesap Tanımı Bulunamadı");

        public static final ExceptionData PAYMENT_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2022L, "Ödeme Kaydı Bulunamadı");

        public static final ExceptionData PAYMENT_NOTIFICATION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2023L, "Ödeme Bildirim Kaydı Bulunamadı");

        public static final ExceptionData UNSUPPORTED_PAYMENT_METHOD_TYPE = new ExceptionData(SHORT_APP_NAME, 2024L, "İlgili Ödeme Metodu Desteklenmemektedir");

        public static final ExceptionData EXPENSE_CODE_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2025L, "Aktif Kurum Komisyon Kodu Bulunamadı");

        public static final ExceptionData COMMISSION_AMOUNT_NOT_MATCHED = new ExceptionData(SHORT_APP_NAME, 2026L, "Daha Önce Hesaplanan Komisyon Tutarı İle Şimdi Hesaplanan Komisyon Tutarı Eşleşmiyor");

        public static final ExceptionData DEALER_MIN_PAYMENT_AMOUNT = new ExceptionData(SHORT_APP_NAME, 2027L, "Ödeme Tutarı Beklenen Değerin Altında");

        public static final ExceptionData DEALER_MAX_PAYMENT_AMOUNT = new ExceptionData(SHORT_APP_NAME, 2028L, "Ödeme Tutarı Beklenen Değerin Üstünde");

        public static final ExceptionData INSTITUTION_PROCESS_IS_OUT_OF_WORKING_HOURS = new ExceptionData(SHORT_APP_NAME, 2029L, "İşlem Saati Kurumun İşlem Kanalında Tanımlı Saat Aralığına Uygun Değil");

        public static final ExceptionData INSTITUTION_PACKAGE_CODE_EXIST = new ExceptionData(SHORT_APP_NAME, 2029L, "Aynı Kurum Paket Kodu Tanımı Mevcut");

        public static final ExceptionData INSTITUTION_PACKAGE_GROUP_DEFINITION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2030L, "Kurum Paket Grubu Bulunamadı");

        public static final ExceptionData INSTITUTION_PACKAGE_DEFINITION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2031L, "Kurum Paketi Bulunamadı");

        public static final ExceptionData ERROR_MAPPING_EXISTS = new ExceptionData(SHORT_APP_NAME, 2032L, "Aynı Hata Kod Mapping Değerleri Mevcuttur");

        public static final ExceptionData ERROR_MAPPING_NOT_EXISTS = new ExceptionData(SHORT_APP_NAME, 2033L, "Silmek İstediğiniz Mapping Değeri Bulunamadı");

        public static final ExceptionData USER_CODE_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2034L, "Sicil No Boş Geçilemez.");

        public static final ExceptionData ID_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2035L, "Id Boş Geçilemez");

        public static final ExceptionData INSTITUTION_SERVICE_PARAMETER_EXISTS = new ExceptionData(SHORT_APP_NAME, 2036L, "Aynı Kurum Servis Parametre Tanımı Mevcuttur");

        public static final ExceptionData INSTITUTION_SERVICE_PARAMETER_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 2037L, "Kurum Servis Parametre Tanımı Bulunamadı");

    }
   //TODO: EnumBillResult a tasi
    public static class AccountingExceptions {

        private AccountingExceptions() {
            throw new IllegalStateException("AccountingExceptions: Constant class");
        }

        public static final ExceptionData COMMISSION_NOT_FOUND = new ExceptionData(SHORT_APP_NAME, 4000L, "Komisyon Bulunamadı");

        public static final ExceptionData COMMISSION_GENERIC_UNKNOWN_ERROR = new ExceptionData(SHORT_APP_NAME, 4006L, "Komisyon Tutarı Okunamadı");

        public static final ExceptionData MERCHANT_TYPE_IS_INVALID = new ExceptionData(SHORT_APP_NAME, 4008L, "Merchant Tipi Geçersiz");

        public static final ExceptionData UNKNOWN_CREDIT_CARD_PROVISION_VERSION = new ExceptionData(SHORT_APP_NAME, 4009L, "Bilinmeyen Kredi Kart Provizyon Versiyonu");
        
        public static final ExceptionData CREDIT_CARD_PROVISION_ACK_NOTIFICATION_SUCCESS = new ExceptionData(SHORT_APP_NAME, 4010L, "Kredi Kartı Provizyon Ack Bildirimi Başarılı");
        
        public static final ExceptionData CREDIT_CARD_REVERSE_PROVISION_NOTIFICATION_SUCCESS = new ExceptionData(SHORT_APP_NAME, 4011L, "Kredi Kartı Provizyon Reverse Bildirimi Başarılı");


        
    }

}
