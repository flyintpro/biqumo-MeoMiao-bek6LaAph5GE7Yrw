
# JavaScript 中栈的运用


在 JavaScript 中，栈（Stack）是一种非常有用的数据结构，它遵循后进先出（Last In First Out，LIFO）的原则。在本文中，我们将深入探讨栈的概念以及在 JavaScript 中的实际运用。


## 一、栈的概念


栈是一种线性数据结构，它只能在一端进行插入（称为入栈或压栈，push）和删除（称为出栈或弹栈，pop）操作。想象一下一摞盘子，你只能从最上面拿盘子（出栈）或者把盘子放在最上面（入栈）。


栈通常具有以下几个基本操作：


1. `push(element)`：将一个元素压入栈顶。
2. `pop()`：弹出栈顶元素并返回它。
3. `peek()`：查看栈顶元素，但不弹出它。
4. `isEmpty()`：判断栈是否为空。


## 二、在 JavaScript 中实现栈


以下是用 JavaScript 实现一个简单栈的代码：



```
class Stack {
    constructor() {
        this.items = [];
    }

    push(element) {
        this.items.push(element);
    }

    pop() {
        if (this.isEmpty()) {
            return "Underflow";
        }
        return this.items.pop();
    }

    peek() {
        if (this.isEmpty()) {
            return null;
        }
        return this.items[this.items.length - 1];
    }

    isEmpty() {
        return this.items.length === 0;
    }

    size() {
        return this.items.length;
    }
}

```

## 三、栈的实际运用


### （一）表达式求值


1. **中缀表达式转后缀表达式**
在计算机科学中，将中缀表达式转换为后缀表达式是栈的一个重要应用。中缀表达式是我们通常使用的算术表达式形式，如`(2 + 3) * 4`。后缀表达式则是将运算符放在操作数之后，例如`2 3 + 4 *`。


算法步骤如下：


* 初始化一个空栈用于存储运算符。
* 从左到右遍历中缀表达式。
* 如果遇到操作数，直接输出。
* 如果遇到左括号，将其压入栈。
* 如果遇到右括号，弹出栈中的运算符并输出，直到遇到左括号，然后丢弃左括号。
* 如果遇到运算符，根据其优先级进行处理。如果栈顶运算符的优先级高于或等于当前运算符，则弹出栈顶运算符并输出；否则，将当前运算符压入栈。
* 遍历结束后，将栈中的剩余运算符依次弹出并输出。


以下是用 JavaScript 实现中缀表达式转后缀表达式的代码：



```
function infixToPostfix(expression) {
    const stack = new Stack();
    let postfix = "";
    const precedence = {
        '+': 1,
        '-': 1,
        '*': 2,
        '/': 2
    };

    for (let char of expression) {
        if (/[0-9]/.test(char)) {
            postfix += char;
        } else if (char === '(') {
            stack.push(char);
        } else if (char === ')') {
            while (!stack.isEmpty() && stack.peek()!== '(') {
                postfix += stack.pop();
            }
            stack.pop(); // 弹出左括号
        } else {
            while (!stack.isEmpty() && precedence[stack.peek()] >= precedence[char]) {
                postfix += stack.pop();
            }
            stack.push(char);
        }
    }

    while (!stack.isEmpty()) {
        postfix += stack.pop();
    }

    return postfix;
}

```

2. **后缀表达式求值**
一旦将中缀表达式转换为后缀表达式，就可以很容易地对后缀表达式进行求值。


算法步骤如下：


* 从左到右遍历后缀表达式。
* 如果遇到操作数，将其压入栈。
* 如果遇到运算符，弹出栈中的两个操作数，进行相应的运算，然后将结果压回栈。
* 遍历结束后，栈中唯一的元素就是表达式的结果。


以下是用 JavaScript 实现后缀表达式求值的代码：



```
function evaluatePostfix(postfix) {
    const stack = new Stack();
    for (let char of postfix) {
        if (/[0-9]/.test(char)) {
            stack.push(parseInt(char));
        } else {
            const operand2 = stack.pop();
            const operand1 = stack.pop();
            switch (char) {
                case '+':
                    stack.push(operand1 + operand2);
                    break;
                case '-':
                    stack.push(operand1 - operand2);
                    break;
                case '*':
                    stack.push(operand1 * operand2);
                    break;
                case '/':
                    stack.push(operand1 / operand2);
                    break;
            }
        }
    }
    return stack.pop();
}

```

### （二）函数调用栈


在 JavaScript 中，当一个函数调用另一个函数时，会在内存中创建一个称为调用栈（Call Stack）的结构。调用栈是一种栈数据结构，它用于跟踪函数的调用顺序。


例如：



```
function functionA() {
    console.log("Inside functionA");
    functionB();
}

function functionB() {
    console.log("Inside functionB");
}

functionA();

```

当`functionA`被调用时，它的执行上下文被压入调用栈。当`functionA`调用`functionB`时，`functionB`的执行上下文也被压入调用栈。当`functionB`执行完毕后，它的执行上下文从调用栈中弹出。然后，`functionA`继续执行，直到它也执行完毕，其执行上下文也从调用栈中弹出。


这种机制确保了函数的正确执行顺序和变量的作用域管理。


### （三）深度优先搜索（DFS）


深度优先搜索是一种图遍历算法，它可以使用栈来实现。


以下是用 JavaScript 实现深度优先搜索的代码：



```
class Graph {
    constructor() {
        this.adjacencyList = {};
    }

    addVertex(vertex) {
        if (!this.adjacencyList[vertex]) {
            this.adjacencyList[vertex] = [];
        }
    }

    addEdge(vertex1, vertex2) {
        this.adjacencyList[vertex1].push(vertex2);
        this.adjacencyList[vertex2].push(vertex1);
    }

    dfs(startVertex) {
        const stack = new Stack();
        const visited = {};
        stack.push(startVertex);
        visited[startVertex] = true;

        while (!stack.isEmpty()) {
            const currentVertex = stack.pop();
            console.log(currentVertex);

            for (let neighbor of this.adjacencyList[currentVertex]) {
                if (!visited[neighbor]) {
                    stack.push(neighbor);
                    visited[neighbor] = true;
                }
            }
        }
    }
}

```

可以使用以下方式调用：



```
const graph = new Graph();
graph.addVertex('A');
graph.addVertex('B');
graph.addVertex('C');
graph.addVertex('D');
graph.addVertex('E');

graph.addEdge('A', 'B');
graph.addEdge('A', 'C');
graph.addEdge('B', 'D');
graph.addEdge('C', 'E');

graph.dfs('A');

```

在这个例子中，深度优先搜索从给定的起始顶点开始，使用栈来存储待访问的顶点。每次从栈中弹出一个顶点，访问它，并将其未访问过的邻居顶点压入栈。


### （四）括号匹配


检查一个字符串中的括号是否匹配是栈的另一个常见应用。


算法步骤如下：


* 初始化一个空栈。
* 遍历字符串中的每个字符。
* 如果遇到左括号，将其压入栈。
* 如果遇到右括号，检查栈是否为空。如果为空，说明右括号没有匹配的左括号，返回 false。如果栈不为空，弹出栈顶元素，检查弹出的左括号是否与当前右括号匹配。如果不匹配，返回 false。
* 遍历结束后，如果栈为空，说明所有括号都匹配，返回 true；否则，返回 false。


以下是用 JavaScript 实现括号匹配的代码：



```
function isBalanced(str) {
    const stack = new Stack();
    for (let char of str) {
        if (char === '(' || char === '[' || char === '{') {
            stack.push(char);
        } else if (char === ')' || char === ']' || char === '}') {
            if (stack.isEmpty()) {
                return false;
            }
            const top = stack.pop();
            if ((char === ')' && top!== '(') || (char === ']' && top!== '[') || (char === '}' && top!== '{')) {
                return false;
            }
        }
    }
    return stack.isEmpty();
}

```

## 四、总结


栈是一种强大的数据结构，在 JavaScript 中有许多实际应用。从表达式求值到函数调用栈，从图的遍历到括号匹配，栈都发挥了重要作用。理解栈的概念和操作，以及如何在 JavaScript 中实现和应用栈，对于编写高效的代码和解决各种编程问题非常有帮助。


 本博客参考[FlowerCloud机场](https://hushicha.org)。转载请注明出处！
