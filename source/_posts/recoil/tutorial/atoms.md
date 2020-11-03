---
title: Recoil基础--Atoms共享状态
date: 2020-11-2 18:25:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604384484174&di=4d4caf7078c87ee70b7fce56d767103c&imgtype=0&src=http%3A%2F%2Fimg.xshuma.com%2F201308%2F122808130808350945.jpg
---
# Atoms共享状态
Atoms contain the source of truth for our application state. In our todo-list, the source of truth will be an array of objects, with each object representing a todo item.
Atoms包含了应用的状态资源，在我们的todo-list的例子里，数据来源是一个包含了多个对象的数组，每一个对象就是一个todo item项.
We'll call our list atom `todoListState` and create it using the `atom()` function:
用 `atom()`方法创建列表的Atom，并调用`todoListState`:
```
const todoListState = atom({
    key: 'todoListState',
    default: [],
});
```
We give our atom a unique `key` and set the `default` value to an empty array. To read the contents of this atom, we can use the `useRecoilValue()` hook in our TodoList component:
给atom添加一个唯一的`标示`，设置一个空数组作为`默认值`。我们可以在组件中使用用`useRecoilValue()`钩子函数：
```
function TodoList() {
    const todoList = useRecoilValue(todoListState);

    return (
            <>
                {/* <TodoListStats /> */}
                {/* <TodoListFilters /> */}
                <TodoItemCreator />

                {todoList.map((todoItem) => (
                    <TodoItem key={todoItem.id} item={todoItem} />
                ))}
            </>
    );
}
```
The commented-out components will be implemented in the sections that follow.
注释部分的组件将会在接下来的部分讲解。
To create new todo items, we need to access a setter function that will update the contents of the `todoListState`. We can use the `useSetRecoilState()` hook to get a setter function in our `TodoItemCreator` component:
创建新的todo items项，需要创建一个新的setter函数来更新`todoListState`的内容。那么在`TodoItemCreator`组件中，我们可以通过`useSetRecoilState()`钩子函数来创建这个setter函数。
```
function TodoItemCreator() {
    const [inputValue, setInputValue] = useState('');
    const setTodoList = useSetRecoilState(todoListState);

    const addItem = () => {
        setTodoList((oldTodoList) => [
        ...oldTodoList,
        {
            id: getId(),
            text: inputValue,
            isComplete: false,
        },
        ]);
        setInputValue('');
    };

    const onChange = ({target: {value}}) => {
        setInputValue(value);
    };

    return (
        <div>
        <input type="text" value={inputValue} onChange={onChange} />
        <button onClick={addItem}>Add</button>
        </div>
    );
    }

// utility for creating unique Id
let id = 0;
function getId() {
    return id++;
}
```
Notice we use the **updater** form of the setter function so that we can create a new todo list based on the old todo list.
注意我们使用了setter函数中新的更新器，以至于我们能基于之前的todo list来创建新的todo list.
The `TodoItem` component will display the value of the todo item while allowing you to change its text and delete the item. We use `useRecoilState()` to read `todoListState` and to get a setter function that we use to update the item text, mark it as completed, and delete it:
当更改`TodoItem`组件的内容或者删除其中一个item的时候，`TodoItem`组件就展示todo item项的内容。我们通过使用`useRecoilState()`来读取`todoListState`并且通过setter函数得到更新的item内容，这意味着更新的完成，并且已经删除。
```
function TodoItem({item}) {
  const [todoList, setTodoList] = useRecoilState(todoListState);
  const index = todoList.findIndex((listItem) => listItem === item);

  const editItemText = ({target: {value}}) => {
        const newList = replaceItemAtIndex(todoList, index, {
            ...item,
            text: value,
        });

        setTodoList(newList);
  };

  const toggleItemCompletion = () => {
        const newList = replaceItemAtIndex(todoList, index, {
            ...item,
            isComplete: !item.isComplete,
        });

        setTodoList(newList);
  };

  const deleteItem = () => {
        const newList = removeItemAtIndex(todoList, index);

        setTodoList(newList);
  };

    return (
        <div>
            <input type="text" value={item.text} onChange={editItemText} />
            <input
                type="checkbox"
                checked={item.isComplete}
                onChange={toggleItemCompletion}
            />
            <button onClick={deleteItem}>X</button>
        </div>
    );
}

function replaceItemAtIndex(arr, index, newValue) {
    return [...arr.slice(0, index), newValue, ...arr.slice(index + 1)];
}

function removeItemAtIndex(arr, index) {
    return [...arr.slice(0, index), ...arr.slice(index + 1)];
}
```
And with that we've got a fully functional todo list! In the next section we'll see how we can use selectors to take our list to the next level.
这样我们就得到了完整的todolist 组件。在下一个章节，我们将看看selectors是如何将我们的组件提升到一个新的高度的。