# Getter-Setter
## **1. 기본구조**
// User.java - 틀
```Java
public class User{
    // 클래스 선언
    String name;
    int age;
    String hobby;

    // 클래스의 생성자
    public User(String _name, int _age, String _hobby) {
        this.name = _name;
        this.age = _age;
        this.hobby = _hobby;
    } 
}
```

// Main.java - 활용
```Java
pulic class Main{

    public static void main(Strind[] args){
        // 생성자 파라미터(매개변수)에 맞게 인자를 삽입
        User user = new User("홍길동", 28, "프로그래밍"); // 클래스 생성 
    }
}
```
<br>

## **2. 생성자 자동 생성 단축키**

* Alt + insert → Generate → Constructor
```Java
// 클래스의 생성자
public User(String _name, int _age, String _hobby) {
    this.name = _name;
    this.age = _age;
    this.hobby = _hobby;
} 

```
## **3. Getter/Setter 자동 생성 단축키**
* Alt + insert → Generate → Getter and Setter<br>
    : 값을 한번에 넣지 않고 따로따로 그때 그때 넣어주고 싶을 때 사용

// User.java - 틀
```Java
public class User{
    // 클래스 선언
    String name;
    int age;
    String hobby;

    // 디폴트 생성자
    public User() {} 

    /* 게터 세터 영역 */
    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge(){
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public String getHobby() {
        return hobby;
    }

    public void setHobby(String hobby) {
        this.hobby = hobby;
    }
}
```
// Main.java - 활용
```Java
pulic class Main{
    public static void main(String[] args) {
        User user = new User();

        user.setName("홍길동");
        user.setAge(27);
        user.setHobby("프로그래밍")

        System.out.println(user.getName);
        System.out.println(user.getAge);
        System.out.println(user.getHobby);
    }
}
```
