# 스도쿠 검증 문제(h사 summer internship dev) - java
9*9 스도쿠를 검증하는 문제  
가로, 세로, 네모칸(3*3)의 숫자의 합이 각 45인지 검증한다.  

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int arr[][]= new int[9][9];//스도쿠 입력 받기
        int n = scan.nextInt();//검증할 스도쿠 개수
        //스도쿠 입력
        for(int i = 0; i<n; i++){
            for(int j = 0; j<9;j++){
                for(int k = 0; k<9; k++){
                    arr[j][k] = scan.nextInt();
                }
            }
            //가로, 세로, 네모칸 내의 숫자의 합이 45라면 스도쿠가 맞다.
            if(checkRow(arr) ==0 && checkCol(arr) ==0 && checkMid(arr) == 0){
                System.out.println("YES");
            }else{
                System.out.println("NO");
            }           
        }
    }
    //가로 행 검증
    public static int checkRow(int[][] arr){
        int check=0;
        int sum=0;      
        for(int i = 0;i<9;i++){
            sum = 0;
            for(int j = 0;j<9;j++){
                sum +=arr[i][j];              
            }
            if(sum!=45){
                return 1;
            }
        }
        return check;
    }
    //세로 열 검증
    public static int checkCol(int[][] arr){     
        int check=0;
        int sum=0;       
        for(int i = 0;i<9;i++){
            sum = 0;
            for(int j = 0;j<9;j++){
                sum +=arr[j][i];                
            }
            if(sum!=45){
                return 1;
            }
        }
        return check;
    }
    //3*3 네모칸 검증 아래의 순서로 검증
    //1 2 3
    //4 5 6
    //7 8 9
    public static int checkMid(int[][] arr){
        int check = 0;
        int sum = 0;
        for(int k = 0 ;k<9;k++){
            sum = 0;
            //i=0 3 6만 체크(행)
            for(int i = (k/3)*3; i<((k/3)*3)+3;i++){
                //j는 123/345/678 만 체크(열)
                for(int j = (k%3)*3; j<((k%3)*3)+3;j++){
                    sum +=arr[i][j];
                }
            }
            if(sum!=45){
                return 1;
            }
        }
        return check;
    }
}
```
### testcase  
        6
        1 3 5 4 6 9 2 7 8
        7 8 2 1 3 5 6 4 9
        4 6 9 2 7 8 1 3 5
        3 2 1 5 4 6 8 9 7
        8 7 4 9 1 3 5 2 6
        5 9 6 8 2 7 4 1 3
        9 1 7 6 5 2 3 8 4
        6 4 3 7 8 1 9 5 2
        2 5 8 3 9 4 7 6 1
        1 1 5 4 6 9 2 7 8
        7 8 2 1 3 5 6 4 9
        4 6 9 2 7 8 1 3 5
        3 2 1 5 4 6 8 9 7
        8 7 4 9 1 3 5 2 6
        5 9 6 8 2 7 4 1 3
        9 1 7 6 5 2 3 8 4
        6 4 3 7 8 1 9 5 2
        2 5 8 3 9 4 7 6 1
        3 6 2 5 7 4 9 1 8
        5 1 4 9 8 6 2 3 7
        7 8 9 2 3 1 6 4 5
        4 2 3 7 6 8 5 9 2
        6 5 8 1 2 9 3 7 4
        9 7 1 3 4 5 8 2 6
        8 3 7 6 1 2 4 5 9
        2 4 5 8 9 7 1 6 3
        1 9 6 4 5 3 7 8 1
        3 6 2 5 7 4 9 1 8
        5 1 4 9 8 6 2 3 7
        7 8 9 2 3 1 6 4 5
        4 2 3 7 6 8 5 9 1
        6 5 8 1 2 9 3 7 4
        9 7 1 3 4 5 8 2 6
        8 3 7 6 1 2 4 5 9
        2 4 5 8 9 7 1 6 3
        1 9 6 4 5 3 7 8 2
        1 2 3 5 6 7 4 8 9
        3 4 5 6 1 2 4 7 8
        7 5 8 3 4 2 1 9 6
        1 2 3 5 6 7 4 8 9
        3 4 5 6 1 2 4 7 8
        7 5 8 3 4 2 1 9 6
        1 2 3 5 6 7 4 8 9
        3 4 5 6 1 2 4 7 8
        7 5 8 3 4 2 1 9 6
        3 5 7 9 6 4 2 8 1
        4 6 8 1 2 3 5 7 9
        9 1 2 5 8 7 4 6 3
        6 3 1 7 9 5 8 4 2
        7 2 4 3 1 8 6 9 5
        8 9 5 2 4 6 1 3 7
        1 7 6 4 5 9 3 2 8
        5 8 3 6 7 2 9 1 4
        2 4 9 8 3 1 7 5 6

### 결과  
        yes
        no
        no
        yes
        no
        yes
    
