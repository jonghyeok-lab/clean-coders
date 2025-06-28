### 목표
- IDE를 활용한 리팩토링
- OOP
- TDD를 이용한 새로운 기능 개발


### 함수
#### 빌더 패턴
- Constructor에 많은 파라미터를 넘겨야 할 경우 Java Bean 패턴(Getter, Setter) 사용
  - 그러나, 강사님은 필수 파라미터를 넘겨주는 빌더 패턴 선호 (동의)
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