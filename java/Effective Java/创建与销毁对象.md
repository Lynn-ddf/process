## 创建和销毁对象
    ### 考虑用静态工厂方法代替构造器
        类可以提供一个共有的静态工厂方法(static factory method)， 它只是一个返回类的实例的静态方法。
        ```java```
            public static Boolean valueOf(boolean b) {
                return b ? Boolean.TRUE : Boolean.FALSE;
            }
        ```
        优势：
         - 静态工厂方法有名称
            如果构造器的参数本身没有确切地描述正被返回的对象，那么具有适当名称的静态工厂会更容易被使用，
            产生的客户端的代码也更容易阅读。例如：构造器BigInteger(int,int,Random) 返回的BigInteger可能是素数，如果名为BigInteger.probablePrime的静态工厂方法来表示，显示更为清楚。
            ```
            Date date0 = new Date();
            Date date1 = new Date(0L);
            Date date2 = new Date("0");
            Date date3 = new Date(1,2,1);
            Date date4 = new Date(1,2,1,1,1);
            Date date5 = new Date(1,2,1,1,1,1);
            ```
            Date 类有很多重载函数，对于开发者来说，假如不是特别熟悉的话，恐怕是需要犹豫一下，才能找到合适的构造函数的。而对于其他的代码阅读者来说，估计更是需要查看文档，才能明白每个参数的含义了。

         - 不用每次被调用时都创建新对象
            有时候外部调用者只需要拿到一个实例，而不关心是否是新的实例；又或者我们想对外提供一个单例时 —— 如果使用工厂方法，就可以很容易的在内部控制，防止创建不必要的对象，减少开销
         - 可以返回原返回类型的子类
            设计模式中的基本原则就是“里氏替换”原则，就是说子类应该能替换父类。
            显然，构造方法只能返回确切的自身类型，静态工厂方法则能够更加灵活，可以根据需要返回它的子类型的实例。
            ```
            class Person {
                public static Person getInstance(){
                    return new Person();
                    // 这里可以改为 return new Player() / Cooker()
                }
            }
            class Player extends Person{
            }
            class Cooker extends Person{
            }
            ```
         - 可以有多个参数相同但名称不同的工厂方法
            构造函数虽然也可以有多个，但是由于函数名已经被固定，所以就要求参数必须有差异时（类型、数量或者顺序）才能够重载了。
            举例：
            class Child{
                int age = 10;
                int weight = 30;
                public Child(int age, int weight) {
                    this.age = age;
                    this.weight = weight;
                }
                public Child(int age) {
                    this.age = age;
                }
            }
            Child 有age 和 weight 两个属性，如代码所示，它已经有两个构造函数：Child(int age, int weight) 和 Child(int age)这时候如果我们想再添加一个指定 wegiht 但不关心 age 的构造函数，一般是这样：
            public Child( int weight) {
                this.weight = weight;
            }
            但要把这个构造函数添加到 Child 类中，我们都知道是行不通的，因为 java 的函数签名是忽略参数名称的，所以 Child(int age) 跟 Child(int weight) 会冲突
            这时候，静态工厂方法就可以登场了
            class Child{
                int age = 10;
                int weight = 30;
                public static Child newChild(int age, int weight) {
                    Child child = new Child();
                    child.weight = weight;
                    child.age = age;
                    return child;
                }
                public static Child newChildWithWeight(int weight) {
                    Child child = new Child();
                    child.weight = weight;
                    return child;
                }
                public static Child newChildWithAge(int age) {
                    Child child = new Child();
                    child.age = age;
                    return child;
                }
            }
            -  可以减少对外暴露的属性
            class Player {
                public static final int TYPE_RUNNER = 1;
                public static final int TYPE_SWIMMER = 2;
                public static final int TYPE_RACER = 3;
                protected int type;
                public Player(int type) {
                    this.type = type;
                }
            }
        缺点：
        - 类如果不含共有的或者受保护的构造器，就不能被子类实例化。
        - 它们与其他静态方法没有任何的区别
    ### 遇到多构造器参数时要考虑用构造器
        适用场景: 当类的构造器或者静态工厂中具有多个参数，设计这个类时，Builder模式就是不错的选择。
    ### 