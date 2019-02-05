# java inheritance example
### super class
        public class Super{
          public String name = "super";
          public void print(){
            System.out.println("super class");
          }
        }
### sub class
        public class Sub{
          public String name = "sub";
          public void print(){
            System.out.println("sub class");
          }
        }
### main class
      public class Main {
       public static void main(String[] args) {
        Super a = new Sub();//super 
        System.out.println(a.name);//sub class
        a.print();
        }
      }

모든클래스는 Object 클래스를 상속받는다. 자식클래스 인스턴스로 생성을 했을 때, 부모클래스를 자료형으로 사용할 수 있지만 그 반대는 안된다.  
이 경우 메소드를 호출할 때엔 오버라이딩된 자식클래스의 메소드를 호출  
변수를 호출할 땐 부모 클래스의 변수를 호출한다.

#### 오버라이딩(overriding) vs 오버로딩(overloading)
**overriding** : 위에서 subclass가 print()함수를 재정의 한 것 (함수 재정의)  
**overloading** : print(int n)과 같이 함수 이름은 같지만 인자값이 다를 때
