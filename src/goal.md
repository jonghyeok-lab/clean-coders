### 목표
- IDE를 활용한 리팩토링
- OOP
- TDD를 이용한 새로운 기능 개발


### 함수
#### 빌더 패턴
- Constructor에 많은 파라미터를 넘겨야 할 경우 Java Bean 패턴(Getter, Setter) 사용
  - 그러나, 강사님은 필수 파라미터를 넘겨주는 빌더 패턴 선호 (동의)
  - 항상, 엔티티와 DTO에 @Builder가 많이 있는 이유가 뭘까?를 궁금해 했었다. 빌더 패턴을 사용하면 해당 필드가 null인지 아닌지 체크해야 하는 것 아닐까? 라는 생각을 했기 때문이다.
  - 그러나, DTO에 들어갈 필드는 nullable 한 값이 있으므로 생성자로 해당 객체를 생성하기엔 경우의 수가 너무 많아진다.
  - 차라리 빌더패턴을 통해 nullable한 객체를 생성하는데 많은 생성자의 경우의 수를 줄이고, 빌더 패턴을 통해 이 객체의 필드는 nullable한 값이 있음을 인지 할 수 있다는 것을 알게 되었다.
  - 무심코, 필드가 많아 빌더 패턴을 사용하는게 아닌, 필드가 nullable 함을 알릴 수 있는 수단이 되었다는 것을 알게 되었다.
#### null 반환
- 코드에 null 반환하지 말자. 반환하는 순간 해당 함수를 호출하는 모든 클라이언트 코드에 null 여부를 따져야 한다.
  - 꼭 null 을 반환해야 한다면, 차라리 에러를 터뜨리는 것도 좋다.
  - Optional<T> 도 있으니 더더욱 null 반환할 필요 없다. 

#### 함수들이 순서를 지켜서 호출되어야 할 때.
- TO-BE: 파일을 열고 닫는 상황처럼 클라이언트가 조심해서 함수 순서를 호출해야 할 때!
```angular2html
1. 파일 열고
2. 어떤 로직 실행
3. 파일 닫기
```

- AS-IS
```angular2html
fileCommandTemplate.process(myfile, myfile -> process(myfile)

class FileCommandTemplate {
    public void process(File file, FileCommand command) {
        file.open
        command.process(file);
        file.close();
    }
}
```

- 시트와 헤더를 추가하는 기능, byte[]로 export하는 기능, 행을 추가하는 기능이 있는 엑셀 파일 관련 클래스를 만들었다.
- 해당 기능들을 Service(클라이언트)에서 순서대로 호출을 하도록 했는데 해당 클래스를 추상화 시키고, 템플릿 메서드를 제공하는 것이 더 나은 코드였음을 알게되었다.

#### Tell Don`t Ask
- 가장 좋아보이는 코드는 무엇인가?
```
1번
if(user.isLoggedIn() {
  user.execute();
} else {
  authenticator.promptLogin();
}

2번
try {
  user.execute(command);
} catch (User.NotLoggedIn e) {
  authenticator.promptLogin();
}

3번
user.execute(command, authenticator)
```

- 로그인 여부 상태는 user 객체에 속한다. user상태를 가져다가 user를 대신해서 왜 결정을 해야하는가?
- user가 user상태를 스스로 판단하고 스스로 행위를 하는 것이 옳은 3번이 가장 좋아보인다.

#### return Null;
- stack의 top() 이 스택의 사이즈가 있든 없든 해당 메서드 호출이 실패하는 것을 기대하지 않는다.
  - 이럴 때는 스택의 값이 없을 때, top()을 호출하면 예외를 던지는게 낫다.
- 하지만, stack의 일부 요소를 찾는 find()는 클라이언트가 실패할 기대를 하고 있다.
  - 찾지 못할 때도 있을 테니까! -> 이럴 때는 null 을 반환해도 좋다.