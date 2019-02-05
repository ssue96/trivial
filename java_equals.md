## equals() vs ==
**equals()** : 객체의 내용 비교  
**==** : 객체가 참조하는 주소 값이 같은지 비교

    public class Main {
      public static void main(String[] argv) {
        String a = "hello";
        String b = "hello";
        String c = a;
        String d = new String("hello");
        System.out.println(a==b);//true
        System.out.println(a==c);//true
        System.out.println(a==d);//flase
        System.out.println(a.equals(d));//true
      }
    }
