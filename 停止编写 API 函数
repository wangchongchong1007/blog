如果你正在开发一个前端程序，后端使用的 RESTFUL API，那你必须停止为每一个接口编写函数。

RESTFUL API 通常提供在不同实体上执行增删改查（CRUD）操作的一组接口。我们通常在我们的前端项目中为这些每一个接口提供一个函数，这些函数的功能非常的相似，只是为了服务于不用的实体。举个例子，假设我们有这些函数。

```js
// api/users.js

// 创建
export function createUser(userFormValues) {
  return fetch('users', { method: 'POST', body: userFormValues });
}

// 查询
export function getListOfUsers(keyword) {
  return fetch(`/users?keyword=${keyword}`);
}

export function getUser(id) {
  return fetch(`/users/${id}`);
}

// 更新
export updateUser(id, userFormValues) {
  return fetch(`/users/${is}`, { method: 'PUT', body: userFormValues });
}

// 删除
export function removeUser(id) {
  return fetch(`/users/${id}`, { method: 'DELETE' });
}
```

类似的功能可能存在于其他实体，例如：城市、产品、类别...但是我们可以用一个简单的函数调用来代替这些函数：

```js
// apis/users.js
export const users = crudBuilder('/users');

// apis/cities.js
export const cities = crudBuilder('/regions/cities');

```

然后像这样去使用：

```js
users.create(values);
users.show(1);
users.list('john');
users.update(values);
users.remove(1);
```

# 你可能会问为什么？有一些很好的理由：

- 减少了代码行数：你编写的代码，和当你离开公司时其他人维护的代码
- 强制执行 API 函数的命名约定，这可以增加代码的可读性和可维护性。例如你已经见过的函数名称: ```getListOfUsers```, ```getCities```, ```getAllProducts```, ```productIndex```, ```fetchCategories```等, 他们都在做相同的事情，那就是“获取实体列表”。使用这种方法，你将始终拥有```entityName.list()```函数，并且团队中的每个人都知道这一点。

所以，让我们创建```crudBuilder()```函数，然后再添加一些糖。

# 一个非常简单的 CRUD 构造器

对于上边的简单示例，```crudBuilder()```函数将非常简单：

```js
export function crudBuilder(baseRoute) {
  function list(keyword) {
    return fetch(`${baseRoute}?keyword=${keyword}`);
  }
  function show(id) {
    return fetch(`${baseRoute}/${id}`);
  }
  function create(formValues) {
    return fetch(baseRoute, { method: 'POST', body: formValues });
  }
  function update(id, formValues) {
    return fetch(`${baseRoute}/${id}`, { method: 'PUT', body: formValues });
  }
  function remove(id) {
    return fetch(`${baseRoute}/${id}`, { method: 'DELETE' });
  }

  return {
    list,
    show,
    create,
    update,
    remove
  };
}
```

假设约定 API 路径并且给相应实体提供一个路径前缀，他将返回该实体上调用 CRUD 操作所需的所有方法。

但老实说，我们知道现实世界的应用程序并不会那么简单。在将这种方法应用于我们的项目时，有很多事情需要考虑：

- 过滤：列表 API 通常会提供许多过滤器参数
- 分页：列表 API 总是分页的
- 转换：API 返回的值在实际使用之前可能需要进行一些转换
- 准备：```formValues```对象在发送给 API 之前需要做一些准备工作
- 自定义接口：更新特定项的接口不总是```${baseRoute}/${id}```

因此，我们需要可以处理更多复杂场景的 CRUD 构造器。

# 高级 CRUD 构造器

让我们通过上述方法来构建一些日常中我们真正使用的东西。

## 过滤

首先，我们应该在 ```list```输出函数中处理更加复杂的过滤。每个实体列表可能有不同的过滤器并且用户可能应用了其中的一些过滤器。因此，我们不能对应用过滤器的形状和值有任何假设，但是我们可以假设任何列表过滤都可以产生一个对象，该对象为不同的过滤器名称指定了一些值。例如，我们可以过滤一些用户：

```js
const filters = {
  keyword: 'john',
  createdAt: new Date('2020-02-10')
};
```

另一方面，我们不知道这些过滤器应该如何传递给 API，但是我们可以假设（跟 API 提供方进行约定）每一个过滤器在列表 API 中都有一个相应的参数，可以以```'key=value'```URL 查询参数的形式被传递。

因此我们需要知道如何将应用的过滤器转换成相对应的 API 参数来创建我们的 ```list``` 函数。这可以通过将 ```transformFilters``` 参数传递给 ```crudBuilder()``` 来完成。举一个用户的例子：

```js
function transformUserFilters(filters) {
  const params = [];
  if (filters.keyword) {
    params.push(`keyword=${filters.keyword}`);
  }
  if (filters.createdAt) {
    params.push(`create_at=${dateUtility.format(filters.createdAt)}`);
  }

  return params;
}
```

现在我们可以使用这个参数来创建 ```list``` 函数了。

```js
export function crudBuilder(baseRoute, transformFilters) {
  function list(filters) {
    let params = transformFilters(filters)?.join('&');
    if (params) {
      params += '?';
    }

    return fetch(`${baseRoute}${params}`);
  }
}
```

## 转换和分页

从 API 接收的数据可能需要进行一些转换才能在我们的应用程序中使用。例如，我们可能需要将 ```snake_case``` 转换成驼峰命名或将一些日期字符串转换成用户时区。

此外，我们还需要处理分页。

我们假设来自 API 的分页数据都按照如下格式（与 API 提供者约定）：

```js
{
  data: [], // 实体对象列表
  pagination: {...} // 分页信息
}
```

因此，我们需要知道如何转换单个实体对象。然后我们可以遍历列表对象来转换他们。为此，我们需要一个 ```transformEntity``` 函数作为 ```crudBuilder``` 的参数。

```js
export function crudBuilder(baseRoute, transformFilters, transformEntity, ) {
  function list(filters) {
    const params = transformFilters(filters)?.join('&');
    return fetch(`${baseRoute}?${params}`)
      .then((res) => res.json())
      .then((res) => ({
        data: res.data.map((entity) => transformEntity(entity)),
        pagination: res.pagination
      }));
  }
}
```

```list()``` 函数我们就完成了。

## 准备

对于 ```create``` 和 ```update``` 函数，我们需要将 ```formValues``` 转换成 API 需要的格式。例如，假设我们在表单中有一个 ```City``` 的城市选择对象。但是 ```create``` API 只需要 ```city_id```。因此，我们需要一个执行以下操作的函数：

```js
const prepareValue = formValue => ({city_id: formValues.city.id});
```

这个函数会根据用例返回普通对象或者 ```FormData```，并且可以将数据传递给 API：

```js
export function crudBuilder(baseRoute, transformFilters, transformEntity, prepareFormValues) {
  function create(formValues) {
    return fetch(baseRoute, {
      method: 'POST',
      body: prepareFormValues(formValues)
    });
  }
}
```

## 自定义接口

在一些少数情况下，对实体执行某些操作的 API 接口不遵循相同的约定。例如，我们不能使用 ```/users/${id}``` 来编辑用户，而是使用 ```/edit-user/${id}```。对于这些情况，我们应该指定一个自定义路径。

在这里我们允许覆盖 crud builder 中使用的任何路径。注意，展示、更新、移除操作的路径可能取决于具体实体对象的信息，因此我们必须使用函数并传递实体对象来获取路径。

我们需要在对象中获取这些自定义路径，如果没有指定，就退回到默认路径。像这样：

```js
const paths = {
  list: 'list-of-users',
  show: (userId) => `users/with/id/${userId}`,
  create: 'users/new',
  update: (user) => `users/update/${user.id}`,
  remove: (user) => `delete-user/${user.id}`
};
```

# 最终的 BRUD 构造器

这是创建 CRUD 函数的最终代码。

```js
export function crudBuilder(baseRoute, transformFilters, transformEntity, prepareFormValues, paths) {
  function list (filters) {
    const path = paths.list || baseRoute;
    let params = transformFilters(filters)?.join('&');
    if (params) {
      params += '?';
    }

    return fetch(`${path}${params}`)
      .then((res) => res.json())
      .then(() => ({
        data: res.data.map(entity => transformEntity(entity)),
        pagination: res.pagination
      }));
  }
  function show(id) {
    const path = paths.show?.(id) || `${baseRoute}/${id}`;

    return fetch(path)
      .then((res) => res.json())
      .then((res => transformEntity(res)));
  }
  function create(formValues) {
    const path = paths.create || baseRoute;

    return fetch(path, { method: 'POST', body: prepareFormValues(formValues) });
  }
  function update(id, formValues) {
    const path = paths.update?.(id) || `${baseRoute}/${id}`;

    return fetch(path, { method: 'PUT', body: formValues });
  }
  function remove(id) {
    const path = paths.remove?.(id) || `${baseRoute}/${id}`;

    return fetch(path, { method: 'DELETE' });
  }
  return {
    list,
    show,
    create,
    update,
    remove
  }
}
```

> [Saeed Mosavat: Stop writing API functions](https://dev.to/saeedmosavat/stop-writing-api-functions-3693)
