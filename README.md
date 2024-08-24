@XmlAccessorType(XmlAccessType.PROPERTY)
public class PymBaseDTO {

    @SuppressWarnings("rawtypes")
    public String toString() {

        StringBuilder tabGap = new StringBuilder("  ");

        StringBuilder sb = new StringBuilder();

        try {
            Class clz = this.getClass();

            sb.append("\n").append("(").append(clz.getName()).append(":");

            boolean isFirstObjectFirstField = true;

            while (clz != null && clz != PymBaseDTO.class) {
                Field[] fields = clz.getDeclaredFields();

                for (int i = 0; i < fields.length; i++) {

                    if (isFirstObjectFirstField) {
                        sb.append("\n").append(tabGap).append(tabGap);

                        isFirstObjectFirstField = false;
                    } else {
                        sb.append("\n").append(tabGap).append(", ");
                    }

                    boolean isFieldAccessible = fields[i].isAccessible();

                    if (!isFieldAccessible) {
                        ReflectionUtils.makeAccessible(fields[i]);
                    }

                    Field field = fields[i];

                    Class<?> clazz = field.getType();

                    Object obj = field.get(this);

                    if (obj != null) {
                        if (clazz.isPrimitive()) {
                            sb.append(new StringBuilder().append(field.getName()).append(": ").append(obj.toString())
                                    .toString());
                        } else {
                            if (obj instanceof ArrayList || obj instanceof List) {
                                ArrayList<?> list = (ArrayList<?>) obj;
                                sb.append(new StringBuilder().append(field.getName()).append(": ").toString());
                                if (!CollectionUtils.isEmpty(list)) {
                                    for (int j = 0; j < list.size(); j++) {
                                        if (list != null && list.get(j) != null) {
                                            if (list.get(j).getClass().isPrimitive()) {
                                                sb.append(list.toString());
                                            } else {
                                                String innerObjectString = list.get(j).toString().replace("\n",
                                                        "\n    ");

                                                sb.append(innerObjectString);
                                            }
                                        }
                                    }
                                }
                            } else {
                                String innerObjectString = obj.toString().replace("\n", "\n    ");

                                sb.append(new StringBuilder().append(field.getName()).append(": ")
                                        .append(innerObjectString).toString());
                            }
                        }
                    } else {
                        sb.append(new StringBuilder().append(field.getName()).append(": ").toString()).append("null");
                    }

                    if (!isFieldAccessible) {
                        ReflectionUtils.makeAccessible(fields[i]);
                    }
                }

                clz = clz.getSuperclass();
            }

            sb.append("\n").append(")");

            return sb.toString();

        } catch (Exception e) {
            /** Is this method should be empty? */
        }
        return "\n";
    }

}
