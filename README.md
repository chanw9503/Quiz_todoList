### [과제] 숙련주차 과제 답

## Problem(1) :

추가하기 버튼을 클릭해도 추가한 아이템이 화면에 표시되지 않음.

## Solution :

onSubmit 시, 호출되는 함수 내에 action을 add로 갖는 함수를 dispatch 해준다.

```
const dispatch = useDispatch();//이부분 추가.
...

const onSubmitHandler = (event) => {
    event.preventDefault();
    if (todo.title.trim() === '' || todo.body.trim() === '') return;

    setTodo({
      id: 0,
      title: '',
      body: '',
      isDone: false,
    });
    dispatch(addTodo(todo)); //이부분 추가.
  };

```

---

## Problem(2) :

추가하기 버튼 클릭 후 기존에 존재하던 아이템들이 사라짐.

## Solution :

todos reducer에서 action에 대한 정보를 담을 때 덮어쓰기를 하고 있었다.
todos에 내용을 넣을 때, 이전에 있던 값도 같이 넣을 수 있도록 스프레드 기법을 사용하여 추가하였다.

### 기존

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [action.payload],
      };
```

### 변경

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
				...state,
        todos: [...state.todos, action.payload],
      };
  };
```

---

## Problem(3)

:삭제 기능이 동작하지 않음.

## solution(3)

:기존 reducer에 삭제 action 에대한
reducer가 만들어져 있지 않아서, 정보를 삭제할 수 없었다. 그래서 해당 내용을 추가해주었다.

### 기존

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };



    case TOGGLE_STATUS_TODO:
      return {
        ...state,
        todos: state.todos.map((todo) => {
          if (todo.id === action.payload) {
            return {
              ...todo,
              isDone: !todo.isDone,
            };
          } else {
            return todo;
          }
        }),
      };

    case GET_TODO_BY_ID:
      return {
        ...state,
        todo: state.todos.find((todo) => {
          return todo.id === action.payload;
        }),
      };
    default:
      return state;
  }
```

### 변경

```
const todos = (state = initialState, action) => {
  switch (action.type) {
    case ADD_TODO:
      return {
        ...state,
        todos: [...state.todos, action.payload],
      };
		/////////////////////추가된 부분     ///////////////////////
    case DELETE_TODO:
      return {
        ...state,
        todos: state.todos.filter((todo) => todo.id !== action.payload),
      };
		/////////////////////////////////////////////////////
    case TOGGLE_STATUS_TODO:
      return {
        ...state,
        todos: state.todos.map((todo) => {
          if (todo.id === action.payload) {
            return {
              ...todo,
              isDone: !todo.isDone,
            };
          } else {
            return todo;
          }
        }),
      };

    case GET_TODO_BY_ID:
      return {
        ...state,
        todo: state.todos.find((todo) => {
          return todo.id === action.payload;
        }),
      };
    default:
      return state;
  }
};
```

---

## Problem(4)

:상세 페이지에 진입 하였을 때 데이터가 업데이트 되지 않음.

## solution(4)

:상세 페이지 클릭 시, store에 있는 객체 정보중 todo 정보를 setting 해줘야한다.

현재는 상세정보 click 시 해당 action에 대한 dispatch가 없으므로 정보가 입력되지 않았다.

Link 태그의 onclick event 발생 시, 해당 action을 dispatch 해줌으로써 해결했다.

```
import {
  deleteTodo,
  getTodoByID, // 수정 부분
  toggleStatusTodo,
} from '../../../redux/modules/todos.js';

...
const onGetTodoById = (id) => {
    dispatch(getTodoByID(id));
  };

...

<StLink
  onClick={() => onGetTodoById(todo.id)}
  to={`/${todo.id}`}
  key={todo.id}
  >
  <div>상세보기</div>
 </StLink>
```

---

## Problem(5)

:완료된 카드의 상세 페이지에 진입 하였을 때 올바른 데이터를 불러오지 못함.

## solution(5)

: 데이터 업데이트 문제 해결 후 완료

---

## Problem(6)

:취소 버튼 클릭시 기능이 작동하지 않음.

## solution(6)

calback 함수 호출 방식이 잘못되어 있어서 바꾸어 주었다.
클릭 이벤트 발생 시, 함수를 호출 해주어야 되는데
기존 방식은 함수을 객체로 넘기는 거라, 정보만 넘어갈뿐 실행되지는 않는다.

### 기존

```
<StButton
   borderColor="green"
   onClick={onToggleStatusTodo}
   >
  {todo.isDone ? "취소!" : "완료!"}
</StButton>
```

### 변경

```
<StButton
  borderColor="green"
  onClick={() => onToggleStatusTodo(todo.id)}
  >
  {todo.isDone ? '취소!' : '완료!'}
</StButton>
```

---
