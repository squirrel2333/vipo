# 回顾反射机制

```java
public void ThreeWaysToGetClass() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
    Class<?> carClass1 = Class.forName("com.hqw.reflect.Car");

    Car car = new Car();
    Class carClass2 = car.getClass();

    Class carClass3 = Car.class;
}
```

```java
public void getConstructor() throws ClassNotFoundException, NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class carClass = Car.class;

        // 获得所有public构造方法
        Constructor[] constructors = carClass.getConstructors();
        for(Constructor c : constructors) {
            System.out.println(c.getName() + "p" + c.getParameterCount());
        }

        //获得所有构造方法
        carClass.getDeclaredConstructors();

        //指定有参构造创建对象
        // 构造public
        Constructor constructor1 = carClass.getConstructor(String.class, String.class, String.class);
        Car car1 = (Car) constructor1.newInstance("BYD", "3", "Red");

        // 构造private
        Constructor constructor2 = carClass.getDeclaredConstructor(String.class, String.class, String.class);
        constructor2.setAccessible(true);
        Car car2 = (Car) constructor2.newInstance("Tesla", "2", "White");

}
```

```java
    @Test
    public void getFields() throws NoSuchMethodException, InvocationTargetException, InstantiationException, IllegalAccessException {
        Class carClass = Car.class;
        Car car = (Car) carClass.getDeclaredConstructor().newInstance();

        // 获取public属性
        Field[] fields1 = carClass.getFields();

        // 获取所有属性
        Field[] fields2 = carClass.getDeclaredFields();

        for (Field field : fields2) {
            if (field.getName().equals("name")) {
                field.setAccessible(true);
                field.set(car, "wuling");
            }
            System.out.println(field.getName());
            System.out.println(car);
        }

    }
```

```java
 @Test
    public void getMethod() throws InvocationTargetException, IllegalAccessException {

        Car car = new Car("oo", "ss", "sww");

        Class carClass = car.getClass();

        Method[] methods = carClass.getMethods();
        for (Method method : methods) {
            if (method.getName().equals("toString")) {
                String invoke = (String) method.invoke(car);
                System.out.println("toString invoke"+ invoke);

            }
        }

        // private方法
        Method[] methods1 = carClass.getDeclaredMethods();
        for (Method method : methods1) {
            if (method.getName().equals("run")) {
                method.setAccessible(true);
                method.invoke(car);
            }
        }
    }
```
# 实现步骤分析

