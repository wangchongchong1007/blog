# JavaScript LocalStorage 解析

在互联网早期，你需要一台服务器存储数据。但是现在，通过 LocalStorage 就可以将数据存储在浏览器和应用程序上，无需与后端服务器通信。

在这篇文章中，你将了解到 LocalStorage 在 JavaScript 中的理论知识和实际应用。

# 浅谈 Web 存储

在进入今天真正的话题之前，我们先来了解一下 Web 存储的定义。Web 存储是 HTML5 重要特性之一，它允许 Web 应用程序在用户浏览器中本地存储数据。

两种最常见的 Web 存储形式是 LocalStorage 和 SessionStorage。这两种形式的 Web 存储之间主要的区别在于，保存在 ```LocalStorage``` 中的数据永远保存在浏览器，直到手动将其删除。相反的，只要关闭了浏览器的选项卡或者窗口，保存在 ```SessionStorage``` 中的数据就会被删除。

# LocalStorage 是什么

如前所述，LocalStorage 是一种浏览器提供的 Web 存储形式，它允许 Web 应用将数据本地存储在用户的浏览器中，并且不会有过期时间。这样，即使关闭了浏览器选项卡或窗口，存储的数据依旧可用。

不过请注意，存储在 LocalStorage 中的数据只保存于用户访问站点设备的浏览器中。因此，如果用户稍后使用不同的浏览器或者其他设备访问同一个站点，是不能访问存储的数据的。

# LocalStorage 使用案例

下面介绍一些 LocalStorage 的日常使用案例。

## 1. 存储部分提交的表单数据

如果用户在线填写长表单，LocalStorage 可以是存储用户输入的有用资源。在这种情况下，即使在填写表单提交之前互联网断开，用户也不会丢失他们的输入，并且可以从离开的地方继续填写。

## 2. 缓存

缓存是指在用户设备上存储例如登录的信息，以便将来对这些数据的请求可以更快的处理。你也可以使用 LocalStorage 缓存整个站点，以便用户即使在离线的状态下也可以访问站点。

## 3. 基础应用数据库

如前所述，起初，数据存储只能通过后端与数据库进行通信。但是现在，使用 LocalStorage 可以在前端存储数据，而不需要数据库。因此，LocalStorage 有时可以作为基本应用程序的小型数据库。

# 如何访问 LocalStorage

访问 LocalStorage 非常的简单，只需要几个步骤：

1. 访问任意一个站点或 Web 应用，然后通过键盘上的 ```F12``` 打开浏览器控制台。

2. 接着，点击 **Application** 选项卡，然后在左侧的菜单中，就可以看到 **Storage** 选项卡下边的 ```LocalStorage``` 了，如下所示：

![LocalStorage 浏览器示例](https://github.com/wangchongchong1007/blog/blob/main/images/localStorage.png)

3. 最后点击 ```LocalStorage``` 下拉菜单并进一步点击下拉菜单项。

![LocalStorage 浏览器示例](https://github.com/wangchongchong1007/blog/blob/main/images/localStorage2.png)

如你所见，有两列，分别是 ```key``` 和 ```value```。这就是 LocalStorage 存储你保存的每一个数据的地方。

# 如何使用 LocalStorage

在 LocalStorage 中可以使用如下方法来管理数据：


| 方法 | 描述 |
|--|--|
| ```setItem()``` | 往 LocalStorage 中存储数据 |
| ```getItem()``` | 从 LocalStorage 中获取数据 |
| ```removeItem()``` | 根据 key 删除 LocalStorage 中的数据 |
|```key()```| 从指定索引处检索 LocalStorage 中的数据 |

## setItem()

此方法用于往 LocalStorage 中存储数据。这个方法接受 ```key```作为第一个参数，```value``` 作为第二个参数。

使用下面代码往 LocalStorage 中添加数据：

```js
localStorage.setItem('name', 'Mandy');
// 这里，`name` 是 key，`Mandy` 是 value
```

如前所述，LocalStorage 将数据存储为字符串，因此如果需要存储对象和数组等数据，那么就需要使用 ```JSON.stringify()``` 将数据转换成字符串。

看看这是如何工作的：

```js
// 将 Object 对象存储到 LocalStorage
const user = {
  name: 'Mandy',
  age: 23
};
localStorage.setItem('user-info', JSON.stringify(user));

// 将数组存储到 LocalStorage
const names = ['John', 'Jake', 'Vanessa'];
localStorage.setItem('names', JSON.stringify(names));
```

## getItem()

从 LocalStorage 中读取数据使用 ```getItem()```。方法只接受一个参数，那就是存数据时使用的 ```key```。

例如，要获取上述用户 ```user``` 的数据，可以使用如下代码：

```js
localStorage.getItem('user-info');
```

上边的代码会返回一个如下所示的 JSON 字符串：

```js
'{"name": "Mandy", "age": 23}'
```

这是应该使用 ```JSON.parse()``` 方法将其转换成对象：

```js
JSON.parse(localStorage.getItem('user-info'));
```

## removeItem()

从 LocalStorage 中移除数据使用 ```removeItem()``` 方法。接受一个 ```key``` 作为参数。

例如，使用下面的代码将 ```user``` 对象从 LocalStorage 中删除：

```js
localStorage.removeItem('user-info');
```

## key()

使用 ```key(index)``` 方法从 LocalStorage 中读取数据，这里的 ```index``` 你想要读取的数据下标。

```js
localStorage.key(2);
```

## clear()

使用 ```clear()``` 方法清除或删除 LocalStorage 中的所有数据。

```js
localStorage.clear();
```

# 项目示例

我们已经了解了用于管理 LocalStorage 中数据的主要方法，现在让我们动手创建一个用户可以使用的 Web 应用程序。

- 向 LocalStorage 中存储数据
- 从 LocalStorage 中获取数据
- 使用 ```key``` 移除或删除数据
- 从 LocalStorage 中删除所有数据

现在我们首先新建一个 folder 并使用编辑器打开。接着创建三个文件 ```index.html```，```style.css```，```main.js```。

## 开始编码吧

```index.html``` 文件中包含 Web 应用程序的 HTML 标签。HTML 代码中包含一个带有按钮和输入字段的表单。

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>Local Storage</title>
    <link rel="stylesheet" href="style.css" />
  </head>
  <body>
    <div id="form">
      <form id="userForm">
        <h1>LocalStorage Application</h1>
        <label for="userName">User</label>
        <input type="text" id="userName" placeholder="Enter user name" required autofocus />
        <br/>
        <label for="age">Age</label>
        <input type="number" id="age" placeholder="Enter user age" required />
        <br/>
        <label for="key">Key</label>
        <input type="text" id="key" placeholder="Enter key" required />
        <br/>

        <button type="submit">Save User</button>
      </form>
      <br/>

      <label for="retrieveKey">Enter Key to retrieve user</label>
      <input type="text" id="retrieveKey" placeholder="Enter key to access user" required />
      <br/>
      <button id="retrieveButton">Retrieve User</button>
      <br/>

      <div id="userData"></div>
      <br/>

      <label for="removeKey">Enter Key to delete user</label>
      <input type="text" id="removeKey" placeholder="remove key" required />
      <br/>
      <button id="removeButton">Delete User</button>
      <br/>

      <button id="clearButton">Delete all users</button>
    </div>
    <script type="text/javascript" scr="main.js"></script>
  </body>
</html>
```

这里是样式代码：

```css
/* 基础样式 */
html {
  font-size: 67.5%;
}
body {
  font-size: 1.6rem;
  padding: 0;
  margin: 0;
}

/* form */
#form {
  margin-left: 2rem;
}
```

```main.js``` 主要包含从 LocalStorage 中进行数据存储，获取以及删除的所有函数。

```js
// 往 LocalStorage 中存储用户数据
function store() {
  let userName = document.getElementById('userName').value;
  let age = document.getElementById('age').value;
  let key = document.getElementById('key').value;

  const user = { userName, age };

  localStorage.setItem(key, JSON.stringify(user));
}

// 从 LocalStorage 中获取用户数据
function retrieveUserData() {
  var key = document.getElementById('retrieveKey').value;
  let userData = localStorage.getItem(key);

  let paragraph = document.createElement('p');
  let info = document.createTextNode(userData);
  paragraph.appendChild(info);
  let element = document.getElementById('userData');
  element.appendChild(paragraph);
  key.value = '';
}

// 从 LocalStorage 中删除用户数据
function removeUserData() {
  var removeKey = document.getElementById('removeKey').value;
  localStorage.removeItem(removeKey);
  removeKey.value = '';
}

// 从 LocalStorage 中删除所有用户数据
function deleteAllUserData() {
  localStorage.clear();
}
```

# 关于 LocalStorage 的一些要点

- LocalStorage 没有数据保护，存储敏感数据并不安全，因为任何人都可以访问。
- LocalStorage 最多只能在浏览器中存储 5MB 的数据。
