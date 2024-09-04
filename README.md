import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;

import java.util.ArrayList;
import java.util.List;

import static org.junit.jupiter.api.Assertions.assertTrue;

class BaseDTOTest {

    private TestDTO testDTO;
    private SubDTO subDTO;

    @BeforeEach
    void setUp() {
        testDTO = new TestDTO();
        subDTO = new SubDTO();
    }

    @Test
    void testToString_withNonNullFields() {
        // Non-null alanların test edilmesi
        testDTO.setField1("Test Field");
        testDTO.setField2(100);

        String result = testDTO.toString();

        assertTrue(result.contains("field1: Test Field"));
        assertTrue(result.contains("field2: 100"));
    }

    @Test
    void testToString_withNullFields() {
        // Alanların null olması durumu
        String result = testDTO.toString();

        assertTrue(result.contains("field1: null"));
        assertTrue(result.contains("field2: null"));
    }

    @Test
    void testToString_withListField() {
        // List alanının test edilmesi
        List<String> list = new ArrayList<>();
        list.add("item1");
        list.add("item2");

        testDTO.setListField(list);

        String result = testDTO.toString();

        assertTrue(result.contains("listField:"));
        assertTrue(result.contains("item1"));
        assertTrue(result.contains("item2"));
    }

    @Test
    void testToString_withInheritedFields() {
        // Miras alınan alanların test edilmesi
        subDTO.setSubField("Sub Field Test");

        String result = subDTO.toString();

        assertTrue(result.contains("subField: Sub Field Test"));
    }

    @Test
    void testToString_withEmptyListField() {
        // Boş listelerin test edilmesi
        testDTO.setListField(new ArrayList<>());

        String result = testDTO.toString();

        assertTrue(result.contains("listField: null"));
    }
}





import java.util.List;

class TestDTO extends BaseDTO {
    private String field1;
    private int field2;
    private List<String> listField;

    // Getter ve Setter'lar
    public String getField1() {
        return field1;
    }

    public void setField1(String field1) {
        this.field1 = field1;
    }

    public int getField2() {
        return field2;
    }

    public void setField2(int field2) {
        this.field2 = field2;
    }

    public List<String> getListField() {
        return listField;
    }

    public void setListField(List<String> listField) {
        this.listField = listField;
    }
}




class SubDTO extends BaseDTO {
    private String subField;

    // Getter ve Setter'lar
    public String getSubField() {
        return subField;
    }

    public void setSubField(String subField) {
        this.subField = subField;
    }
}
