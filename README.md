import javax.xml.bind.annotation.XmlElement;
import javax.xml.bind.annotation.XmlRootElement;

@XmlRootElement
class Araba {
    private String hız;
    private String renk;

    @XmlElement
    public String getHız() {
        return hız;
    }

    public void setHız(String hız) {
        this.hız = hız;
    }

    @XmlElement
    public String getRenk() {
        return renk;
    }

    public void setRenk(String renk) {
        this.renk = renk;
    }
}

@XmlRootElement
class BMW extends Araba {
    private String marka;
    private String tip;

    @XmlElement
    public String getMarka() {
        return marka;
    }

    public void setMarka(String marka) {
        this.marka = marka;
    }

    @XmlElement
    public String getTip() {
        return tip;
    }

    public void setTip(String tip) {
        this.tip = tip;
    }
}

 <dependency>
        <groupId>jakarta.xml.bind</groupId>
        <artifactId>jakarta.xml.bind-api</artifactId>
        <version>4.0.0</version>
    </dependency>
    
    <!-- JAXB Implementasyonu -->
    <dependency>
        <groupId>org.glassfish.jaxb</groupId>
        <artifactId>jaxb-runtime</artifactId>
        <version>4.0.0</version>
    </dependency>
    
    <!-- JAXB için ek gereksinim (Java 9+ için gerekli) -->
    <dependency>
        <groupId>javax.activation</groupId>
        <artifactId>activation</artifactId>
        <version>1.1.1</version>
    </dependency>

public class Main {
    public static void main(String[] args) {
        try {
            // BMW nesnesi oluşturma
            BMW bmw = new BMW();
            bmw.setMarka("BMW");
            bmw.setTip("Sedan");
            bmw.setHız("200");
            bmw.setRenk("Siyah");

            // JAXBContext ve Marshaller ile XML'e dönüştürme
            JAXBContext jaxbContext = JAXBContext.newInstance(BMW.class);
            Marshaller marshaller = jaxbContext.createMarshaller();
            marshaller.setProperty(Marshaller.JAXB_FORMATTED_OUTPUT, true);

            // String'e dönüştürme
            StringWriter stringWriter = new StringWriter();
            marshaller.marshal(bmw, stringWriter);

            // Sonucu string olarak al
            String xmlString = stringWriter.toString();
            System.out.println(xmlString);
        } catch (JAXBException e) {
            e.printStackTrace();
        }
    }
}
