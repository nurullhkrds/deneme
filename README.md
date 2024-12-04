import javax.xml.datatype.XMLGregorianCalendar;
import java.util.Calendar;
import java.util.GregorianCalendar;

private boolean isDueDateValid(XMLGregorianCalendar dueDate) {
    if (dueDate == null) {
        return false;
    }
    
    // Şu anki zamanı milisaniye cinsinden al
    long todayTime = System.currentTimeMillis();

    // XMLGregorianCalendar nesnesini GregorianCalendar'a çevir ve milisaniye cinsinden zamanını al
    long dueDateTime = dueDate.toGregorianCalendar().getTimeInMillis();

    // Son ödeme tarihi bugünden sonra ise true döndür
    return dueDateTime >= todayTime;
}
