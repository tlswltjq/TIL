# 문제가 발생한 코드
```java
@Test
@DisplayName("Todo를 삭제할 수 있다.")
void deleteTodo() {
    //given 삭제할 Todo의 Id가 주어지고
    TodoList todoList = todoListService.createTodoList("testList");
    Todo todo = todoService.createTodo("testTodo", todoList.getId());

    //when Id를 이용해 삭제하면
    todoService.deleteTodo(todo.getId());

    //then 검색된 리스트의 todos.size()는 0이다.
    TodoList updatedTodoList = todoListService.getTodoList(todoList.getId());
    List<Todo> result = updatedTodoList.getTodoList();
    Assertions.assertThat(result.size()).isEqualTo(0);
}
```

```java
@Entity
@Builder
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class TodoList {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column
    private String name;

    @OneToMany(mappedBy = "todoList",cascade = CascadeType.ALL, fetch = FetchType.EAGER)
    List<Todo> todoList = new ArrayList<>();
}
```

>실행결과
```java
org.opentest4j.AssertionFailedError: 
expected: 0
 but was: 1
```
`delete()`메소드를 실행해도 객체가 계속 검색되었다.
# 해결
`cascade = CascadeType.ALL`을 지우거나 `cascade = CascadeType.REMOVE`로 변경하니 테스트가 통과되었다.
# 원인