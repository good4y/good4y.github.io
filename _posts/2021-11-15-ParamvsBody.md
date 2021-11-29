## @RequestParam vs @RequestBody

두 어노테이션은 컨트롤러에서 데이터를 인자에 할당하는 대표적인 방법이다.  
일단 결론은, Url에서 데이터를 전달하는 경우(Form 태그를 이용한 데이터 처리 등)는 변수별로 데이터를 받을 수 있는 @RequestParam을 이용한다  
그리고 나머지 상황에는 객체를 생성하여 데이터를 저장 할 수 있는 @RequestBody를 이용한다.  

## 예시

- Form 태그를 붙여 Post 할 때 Url옆에 'name=jun&age=13'이런 식의 String이 전달된다.
