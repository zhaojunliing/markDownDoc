## 一、序列化方法

```java
    public static void test05(List<Person> srcList) {
        try {
            List<Person> destList = deepCopy(srcList);
            PrintUtils.printList(destList);
            srcList.get(0).setAge(100);
            PrintUtils.printList(destList);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

 	public static <T> List<T> deepCopy(List<T> src) {
        @SuppressWarnings("unchecked")
        List<T> dest = null;
        try {
            ByteArrayOutputStream byteOut = new ByteArrayOutputStream();
            ObjectOutputStream out = new ObjectOutputStream(byteOut);
            out.writeObject(src);

            ByteArrayInputStream byteIn = new ByteArrayInputStream(byteOut.toByteArray());
            ObjectInputStream in = new ObjectInputStream(byteIn);
            dest = (List<T>) in.readObject();
        } catch (IOException e) {
            throw new RuntimeException(e);
        } catch (ClassNotFoundException e) {
            throw new RuntimeException(e);
        }
        return dest;
    }
```

