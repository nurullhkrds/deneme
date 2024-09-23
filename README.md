public class BillExceptionsUI {

    private static final	String APP_NAME = "PAYMENTS.bill-ui";


    private BillExceptionsUI() {
        throw new IllegalStateException("BillExceptionsUI: Exception Constant Class");
    }

    public static final ExceptionData SUCCESS = new ExceptionData(APP_NAME, 0L, "Başarılı İşlem");

    public static final ExceptionData NOT_IMPLEMENTED = new ExceptionData(APP_NAME, 1L, "İlgili Servis Henüz Hazır Değil");

    public static final ExceptionData SERVICE_CALL_EXCEPTION = new ExceptionData(APP_NAME, 998L, "Servis Çagrımında Hata Oluştu");

    public static final ExceptionData UNKNOWN_ERROR_OCCURRED = new ExceptionData(APP_NAME, 999L, "Bilinmeyen Hata Meydana Geldi");




    public static class ValidationExceptions {

        private ValidationExceptions() {
            throw new IllegalStateException("ValidationExceptions: Constant class");
        }

        public static final ExceptionData SERVICE_DEFINITION_NOT_FOUND = new ExceptionData(APP_NAME, 2000L, "Kurum Servis Tanımı Bulunamadı");


        public static final ExceptionData PARAMETER_RECORD_NOT_FOUND = new ExceptionData(APP_NAME, 2001L, "İlgili Parametre Kaydı Bulunamadı");

        public static final ExceptionData CONVERSION_FAILED = new ExceptionData(APP_NAME, 2002L, "Dönüştürme işlemi başarısız !");

        public static final ExceptionData DATA_NOT_FOUND = new ExceptionData(APP_NAME, 2003L, "Veri Bulunamadı !");

        public static final ExceptionData RETURN_MAP_EXIST = new ExceptionData(APP_NAME, 2004L, "Aynı dönüş kod  değeri mevcuttur");
        public static final ExceptionData RETURN_MAP_DEFINITION_NOT_FOUND = new ExceptionData(APP_NAME, 2005L, "Dönüş Kodu Tanımı Bulunamadı");
        public static final ExceptionData RETURN_MAP_AND_INSTITUTION_EXIST = new ExceptionData(APP_NAME, 2006L, "Bu Dönüş Kodu ve Kurum Koduna ait kayıt mevcuttur");
        public static final ExceptionData DUPLICATE_INSTITUTION_PRODUCT = new ExceptionData(APP_NAME, 2006L, "Bu kurum koduna  ve bu ürün koduna ait bir kayıt zaten mevcut.");

        public static final ExceptionData INSTITUTION_NOT_FOUND = new ExceptionData(APP_NAME, 2007L, "Kurum bulunamadı.");



        public static final ExceptionData OWNER_DEPARTMENT_NOT_FOUND = new ExceptionData(APP_NAME, 2008L, "Sahip departman bulunamadı.");
        public static final ExceptionData PRODUCT_NOT_FOUND = new ExceptionData(APP_NAME, 2009L, "Ürün bulunamadı.");


        public static final ExceptionData DUPLICATE_INSTITUTION_FEATURE = new ExceptionData(APP_NAME, 2010L, "Bu Kuruma ve bu Özellik koduna  ait bir kayıt zaten mevcut.");
        public static final ExceptionData INSTITUTION_FEATURE_NOT_FOUND = new ExceptionData(APP_NAME, 2011L, "Kurum özelliği bulunamadı.");
        public static final ExceptionData DUPLICATE_INSTITUTION_DEBT_TYPE = new ExceptionData(APP_NAME, 2012L, "Bu kurum ve bu debt type ait bir kayıt zaten mevcut.");

        public static final ExceptionData INSTITUTION_DEBT_TYPE_NOT_FOUND = new ExceptionData(APP_NAME, 2013L, "Kurum borç tipi bulunamadı.");

        public static final ExceptionData FEATURE_NOT_FOUND = new ExceptionData(APP_NAME, 2014L, "Özellik bulunamadı.");


    }
}
