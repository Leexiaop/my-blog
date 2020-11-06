---
title: Recoil基础--Selector纯函数
date: 2020-11-4 13:51:18
categories: recoil
tags: recoil
cover: https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1604479338104&di=6a38619b22e455571bfb2e6b7256e26f&imgtype=0&src=http%3A%2F%2F5b0988e595225.cdn.sohucs.com%2Fimages%2F20190818%2Fb11bb9e824134b24ba9baecd6e774671.jpeg
---
# Selectors 纯函数
A **selector** represents a piece of **derived state**. You can think of derived state as the output of passing state to a pure function that modifies the given state in some way.
**selecttor**就是一种派生状态。你可以认为**派生状态**是一种高阶函数的返回值。
Derived state is a powerful concept because it lets us build dynamic data that depends on other data. In the context of our todo list application, the following are considered derived state:
派生状态是一个非常有意义的概念，因为他们能让我们创建依赖于其他数据的动态数据。在我们todo list的例子中，下面的数据被视作为派生状态：
+ **Filtered todo list**: derived from the complete todo list by creating a new list that has certain items filtered out based on some criteria (such as filtering out items that are already completed).
过滤todo List：
+ **Todo list statistics**: derived from the complete todo list by calculating useful attributes of the list, such as the total number of items in the list, the number of completed items, and the percentage of items that are completed.
To implement a filtered todo list, we need to choose a set of filter criteria whose value can be saved in an atom. The filter options we'll use are: "Show All", "Show Completed", and "Show Uncompleted". The default value will be "Show All":
```
const todoListFilterState = atom({
    key: 'todoListFilterState',
    default: 'Show All',
});
```
Using `todoListFilterState` and `todoListState`, we can build a `filteredTodoListState` selector which derives a filtered list:
我们可以使用`todoListFilterState`和`todoListState`来创建一个`filteredTodoListState`Slector来获取过滤的列表：
```
const filteredTodoListState = selector({
    key: 'filteredTodoListState',
    get: ({get}) => {
        const filter = get(todoListFilterState);
        const list = get(todoListState);

        switch (filter) {
        case 'Show Completed':
            return list.filter((item) => item.isComplete);
        case 'Show Uncompleted':
            return list.filter((item) => !item.isComplete);
        default:
            return list;
        }
    },
});
```
The `filteredTodoListState` internally keeps track of two dependencies: `todoListFilterState` and `todoListState` so that it re-runs if either of those change.
`filteredTodoListState`与`todoListFilterState`和`todoListState`有密切关系，只要有其中一个发生变化，就会导致re-runs.
> From a component's point of view, selectors can be read using the same hooks that are used to read atoms. However it's important to note that certain hooks only work with writable state (i.e useRecoilState()). All atoms are writable state, but only some selectors are considered writable state (selectors that have both a get and set property). See the Core Concepts page for more information on this topic.

Displaying our filtered todoList is as simple as changing one line in the `TodoList` component:
展示过滤的todoList列表非常的简单，只需要在`TodoList`组件中修改一行代码就好。
```
function TodoList() {
    // changed from todoListState to filteredTodoListState
    const todoList = useRecoilValue(filteredTodoListState);

    return (
        <>
        <TodoListStats />
        <TodoListFilters />
        <TodoItemCreator />

        {todoList.map((todoItem) => (
            <TodoItem item={todoItem} key={todoItem.id} />
        ))}
        </>
    );
}
```
Note the UI is showing every todo because `todoListFilterState` was given a default value of "Show All". In order to change the filter, we need to implement the `TodoListFilters` component:
注意，从UI层面来看，展示了所有的item,因为`todoListFilterState`被赋予了展示所有的默认值。为了过滤，我们需要一个`TodoListFilters`组件：
```
function TodoListFilters() {
    const [filter, setFilter] = useRecoilState(todoListFilterState);

    const updateFilter = ({target: {value}}) => {
        setFilter(value);
    };

    return (
        <>
        Filter:
        <select value={filter} onChange={updateFilter}>
            <option value="Show All">All</option>
            <option value="Show Completed">Completed</option>
            <option value="Show Uncompleted">Uncompleted</option>
        </select>
        </>
    );
}
```
With a few lines of code we've managed to implement filtering! We'll use the same concepts to implement the `TodoListStats` component.
写了这么几行代码，我们已经可以让过滤生效了。我们可以用同样的概念使`TodoListStats`组件生效。
We want to display the following stats:
我们希望展示以下的统计：
+ Total number of todo items items的总数
+ Total number of completed items 完成状态的item总数
+ Total number of uncompleted items 没有完成状态的item的总数
+ Percentage of items completed 完成状态的item的占比
While we could create a selector for each of the stats, an easier approach would be to create one selector that returns an object containing the data we need. We'll call this selector `todoListStatsState`:
这时我们可以为每一个统计创建一个Selector.一种最简单的方式就是创建一个selectorr然后返回一个包含我们需要的数据的对象。我们将把这个Selector叫做`todoListStatsState`:
```
const todoListStatsState = selector({
    key: 'todoListStatsState',
    get: ({get}) => {
        const todoList = get(todoListState);
        const totalNum = todoList.length;
        const totalCompletedNum = todoList.filter((item) => item.isComplete).length;
        const totalUncompletedNum = totalNum - totalCompletedNum;
        const percentCompleted = totalNum === 0 ? 0 : totalCompletedNum / totalNum;

        return {
            totalNum,
            totalCompletedNum,
            totalUncompletedNum,
            percentCompleted,
        };
    },
});
```
To read the value of `todoListStatsState`, we use `useRecoilValue()` once again:
再用`useRecoilValue()`函数来读取`todoListStatsState`的值：
```
function TodoListStats() {
    const {
        totalNum,
        totalCompletedNum,
        totalUncompletedNum,
        percentCompleted,
    } = useRecoilValue(todoListStatsState);

    const formattedPercentCompleted = Math.round(percentCompleted * 100);

    return (
        <ul>
            <li>Total items: {totalNum}</li>
            <li>Items completed: {totalCompletedNum}</li>
            <li>Items not completed: {totalUncompletedNum}</li>
            <li>Percent completed: {formattedPercentCompleted}</li>
        </ul>
    );
}
```
To summarize, we've created a todo list app that meets all of our requirements:
总结，我们已经创建了满足我们以下需求的todo list应用：
+ Add todo items 增加item
+ Edit todo items 编辑item
+ Delete todo items 删除item
+ Filter todo items 过滤item
+ Display useful stats 展示统计