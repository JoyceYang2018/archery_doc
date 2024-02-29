### 用户登录接口

- **URL**: `/auth/login`
- **Method**: `POST`
- **Auth required**: No

#### Request Parameters (JSON Body)

| Parameter    | Type   | Required | Description                 |
|--------------|--------|----------|-----------------------------|
| `username`   | String | Yes      | 用户的用户名。               |
| `password`   | String | Yes      | 用户的密码。                 |
| `role_name`  | String | No       | 请求登录的角色名称（可选）。 |

#### Success Response

- **Code**: `200 OK`
- **Content example**

```json
{
    "access_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

#### Error Response

- **Condition**: 如果请求体不是JSON格式
  - **Code**: `400 Bad Request`
  - **Content**: `{"msg": "Missing JSON in request"}`

- **Condition**: 如果缺少`username`或`password`
  - **Code**: `400 Bad Request`
  - **Content**: `{"msg": "Missing username, password"}`

- **Condition**: 如果用户名或密码不正确
  - **Code**: `401 Unauthorized`
  - **Content**: `{"msg": "Bad username or password"}`

#### Description

此接口用于用户登录，用户需要提供`username`和`password`。可选地，用户可以提供`role_name`来指定希望登录的角色，例如"admin"或"super_admin"。成功登录后，接口将返回一个JWT令牌，可用于后续请求的身份验证。

</br>

---

</br>

### 获取管理员信息

- **URL**: `/admin`
- **Method**: `GET`
- **Auth required**: Yes
- **Required Role**: `admin`

#### Query Parameters

| Parameter   | Type   | Required | Description                                    |
|-------------|--------|----------|------------------------------------------------|
| `username`  | String | No       | 用户名。                                        |
| `id`        | Int    | No       | 用户ID。                                        |
| `name`      | String | No       | 名字。                                          |
| `role_name` | String | No       | 角色名（默认查询`super_admin`和`admin`）。      |

#### Success Response

- **Code**: `200 OK`
- **Content example**

```json
[
    {
        "id": 1,
        "username": "adminUser",
        "name": "Admin",
        "phone_number": "123456789",
        "email": "admin@example.com",
        "role_name": "admin"
    }
]
```

#### Error Response

- **Condition**: 如果角色不存在
  - **Code**: `404 Not Found`
  - **Content**: `{"msg": "Role not found"}`

#### Description

此接口用于获取管理员信息，支持通过`username`、`id`、`name`或`role_name`查询特定管理员。需要`admin`角色权限。

### 添加管理员

- **URL**: `/admin`
- **Method**: `POST`
- **Auth required**: Yes
- **Required Role**: `super_admin`

#### Request Body (JSON)

| Parameter     | Type   | Required | Description                 |
|---------------|--------|----------|-----------------------------|
| `phone_number`| String | Yes      | 电话号码。                   |
| `password`    | String | Yes      | 密码。                       |
| `username`    | String | Yes      | 用户名。                     |
| `name`        | String | No       | 名字。                       |
| `role_name`   | String | No       | 角色名（默认为`admin`）。    |

#### Success Response

- **Code**: `200 OK`

#### Error Response

- **Condition**: 如果角色名无效或用户已是管理员
  - **Code**: `400 Bad Request`
  - **Content**: `{"msg": "Invalid role name or user already an admin"}`

- **Condition**: 如果电话号码重复
  - **Code**: `401 Unauthorized`
  - **Content**: `{"msg": "Duplicate phone number"}`

#### Description

此接口用于添加管理员。需要`super_admin`角色权限。请求必须包含`phone_number`、`password`和`username`，可选地包含`name`和`role_name`。

### 编辑管理员信息

- **URL**: `/admin/<int:admin_id>`
- **Method**: `PUT`
- **Auth required**: Yes
- **Required Role**: `super_admin`

#### Request Body (JSON)

| Parameter     | Type   | Required | Description   |
|---------------|--------|----------|---------------|
| `role_id`     | Int    | Yes      | 角色ID。       |
| `phone_number`| String | Yes      | 电话号码。     |
| `email`       | String | Yes      | 电子邮箱。     |

#### Success Response

- **Code**: `200 OK`

#### Error Response

- **Condition**: 如果管理员ID不存在
  - **Code**: `404 Not Found`
  - **Content**: `{"msg": "Admin not found"}`

#### Description

此接口用于编辑管理员信息，包括角色ID、电话号码和电子邮箱。需要`super_admin`角色权限。

### 删除管理员

- **URL**: `/admin/<int:admin_id>`
- **Method**: `DELETE`
- **Auth required**: Yes
- **Required Role**: `super_admin`

#### Path Parameters

| Parameter  | Type | Required | Description   |
|------------|------|----------|---------------|
| `admin_id` | Int  | Yes      | 管理员的ID。   |

#### Success Response

- **Code**: `200 OK`

#### Error Response

- **Condition**: 如果管理员ID不存在
  - **Code**: `404 Not Found`
  - **Content**: `{"msg": "Admin not found"}`

#### Description

此接口用于删除管理员。需要`super_admin`角色权限。只有超级管理员可以删除管理员账户。

### 管理员权限更改

- **URL**: `/admin/<int:admin_id>/role`
- **Method**: `PUT`
- **Auth required**: Yes
- **Required Role**: `super_admin`

#### Path Parameters

| Parameter  | Type | Required | Description   |
|------------|------|----------|---------------|
| `admin_id` | Int  | Yes      | 管理员的ID。   |

#### Request Body (JSON)

| Parameter  | Type   | Required | Description    |
|------------|--------|----------|----------------|
| `role_id`  | Int    | Yes      | 新的角色ID。    |

#### Success Response

- **Code**: `200 OK`

#### Error Response

- **Condition**: 如果管理员ID不存在
  - **Code**: `404 Not Found`
  - **Content**: `{"msg": "Admin not found"}`

- **Condition**: 如果角色ID无效
  - **Code**: `400 Bad Request`
  - **Content**: `{"msg": "Invalid role ID"}`

#### Description

此接口用于更改管理员的权限。需要`super_admin`角色权限。通过指定新的角色ID来更新管理员的角色。这允许超级管理员提升或降低其他管理员的权限级别。

</br>

---

</br>

### 1. 创建俱乐部

- **URL:** `/club/`
- **方法:** `POST`
- **请求参数:**

  | 字段          | 类型     | 描述         |
  | ------------- | -------- | ------------ |
  | club_name     | string   | 俱乐部名称     |
  | phone_number  | string   | 联系电话       |
  | password      | string   | 密码          |
  | city_name     | string   | 所在城市       |
  | location      | string   | 具体位置       |
  | description   | string   | 俱乐部描述     |

- **请求示例:**

  ```json
  {
    "club_name": "Test Club",
    "phone_number": "1234567890",
    "password": "password",
    "city_name": "Test City",
    "location": "Test Location",
    "description": "This is a test club."
  }
  ```

- **响应示例:**

  ```json
  {
    "message": "Club created successfully.",
    "club_id": 1
  }
  ```

- **错误响应:**

  - 400 Bad Request: 请求参数不完整或格式错误。
  
### 2. 获取俱乐部列表

- **URL:** `/club/`
- **方法:** `GET`
- **请求参数:** 无

- **响应示例:**

  ```json
  [
    {
      "id": 1,
      "name": "Test Club",
      "city_name": "Test City",
      "location": "Test Location",
      "description": "This is a test club."
    }
  ]
  ```

### 3. 获取单个俱乐部信息

- **URL:** `/club/<club_id>`
- **方法:** `GET`
- **请求参数:** 无

- **响应示例:**

  ```json
  {
    "id": 1,
    "name": "Test Club",
    "city_name": "Test City",
    "location": "Test Location",
    "description": "This is a test club."
  }
  ```

### 4. 更新俱乐部信息

- **URL:** `/club/<club_id>`
- **方法:** `PUT`
- **请求参数:**

  | 字段          | 类型     | 描述         |
  | ------------- | -------- | ------------ |
  | club_name     | string   | 俱乐部名称     |
  | description   | string   | 俱乐部描述     |

- **请求示例:**

  ```json
  {
    "club_name": "Updated Club Name",
    "description": "Updated description."
  }
  ```

- **响应示例:**

  ```json
  {
    "message": "Club updated successfully."
  }
  ```

### 5. 删除俱乐部

- **URL:** `/club/<club_id>`
- **方法:** `DELETE`
- **请求参数:** 无

- **响应示例:**

  ```json
  {
    "message": "Club deleted successfully."
  }
  ```

- **错误响应:**

  - 404 Not Found: 未找到指定的俱乐部。

</br>

---

</br>


### 创建协会

- **URL:** `/association/`
- **方法:** `POST`
- **认证要求:** 是

#### 请求参数

| 参数名          | 类型   | 必填 | 描述     |
|---------------|------|----|--------|
| `association_name` | string | 是 | 协会名称 |
| `name`            | string | 否 | 用户真实姓名 |
| `username`        | string | 是 | 用户名   |
| `phone_number`    | string | 是 | 手机号   |
| `password`        | string | 是 | 密码     |
| `description`     | string | 否 | 协会描述 |

#### 成功响应

- **代码:** `201 Created`
- **内容示例:**

```json
{
  "id": "协会ID",
  "name": "协会名称",
  "description": "协会描述"
}
```

#### 错误响应

- **代码:** `400 Bad Request`
- **内容:** 协会名已存在或必填字段缺失。


### 获取协会列表

- **URL:** `/association/`
- **方法:** `GET`
- **认证要求:** 是

#### 查询参数

| 参数名            | 类型   | 必填 | 描述          |
|-----------------|------|----|-------------|
| `association_name` | string | 否 | 协会名称，用于搜索 |
| `association_id`   | string | 否 | 协会ID，用于获取特定协会 |
| `name`            | string | 否 | 用户名，用于搜索    |
| `phone_number`    | string | 否 | 手机号，用于搜索    |

#### 成功响应

- **代码:** `200 OK`
- **内容示例:**

```json
[
  {
    "id": "协会ID",
    "name": "协会名称",
    "description": "协会描述"
  }
]
```
###  获取社团信息

- **请求URL:** `/association/<int:association_id>`
- **请求方法:** `GET`
- **URL参数:**

  | 参数名          | 类型 | 必须 | 说明     |
  |-----------------|------|------|----------|
  | `association_id` | int  | 是   | 社团的ID |

- **成功响应示例:**

  ```json
  {
    "id": 1,
    "name": "社团名称",
    "description": "社团描述",
    "admins": [
      {
        "name": "管理员姓名",
        "user_id": 2,
        "role_id": 3,
        "phone_number": "12345678901"
      }
    ]
  }
  ```

- **响应状态码:** `200 OK`

###  更新社团信息

- **请求URL:** `/association/<int:association_id>`
- **请求方法:** `PUT`
- **请求体(JSON):**

  | 字段名          | 类型    | 必须 | 说明               |
  |-----------------|---------|------|--------------------|
  | `user_id`       | int     | 是   | 用户ID             |
  | `role_id`       | int     | 是   | 角色ID             |
  | `association_name` | string  | 否   | 新的社团名称       |
  | `name`          | string  | 否   | 新的管理员姓名     |
  | `phone_number`  | string  | 否   | 新的手机号码       |
  | `password`      | string  | 否   | 新密码             |
  | `description`   | string  | 否   | 社团新描述         |
  | `open`          | boolean | 否   | 社团是否开放新成员加入 |

- **成功响应示例:**

  ```json
  {
    "id": 1,
    "name": "新社团名称",
    "description": "新描述"
  }
  ```

- **响应状态码:** `200 OK`

###  更新社团管理员

- **请求URL:** `/association/<int:association_id>/admin`
- **请求方法:** `PUT`
- **请求体(JSON):**

  | 字段名         | 类型   | 必须 | 说明         |
  |----------------|--------|------|--------------|
  | `user_id`      | int    | 是   | 用户ID       |
  | `role_id`      | int    | 是   | 角色ID       |
  | `name`         | string | 否   | 新管理员姓名 |
  | `phone_number` | string | 否   | 新手机号码   |
  | `password`     | string | 否   | 新密码       |

- **成功响应示例:**

  ```json
  {
    "message": "管理员更新成功"
  }
  ```

- **响应状态码:** `200 OK`

###  删除社团

- **请求URL:** `/association/<int:association_id>`
- **请求方法:** `DELETE`
- **无请求参数**

- **成功响应示例:**

  ```json
  {
    "message": "Association deleted"
  }
  ```

- **响应状态码:** `200 OK`

### 删除社团管理员

- **请求URL:** `/association/<int:association_id>/admin`
- **请求方法:** `DELETE`
- **请求体(JSON):**

  | 字段名    | 类型 | 必须 | 说明     |
  |-----------|------|------|----------|
  | `user_id` | int  | 是   | 用户ID   |
  | `role_id` | int  | 是   | 角色ID   |

- **成功响应示例:**

  ```json
  {
    "message": "Association admin deleted"
  }
  ```

- **响应状态码:** `200 OK`

</br>

---

</br>

### 组别管理接口文档

#### 获取所有组别项

- **URL**: `/group/`
- **方法**: `GET`
- **权限**: 无
- **参数**: 无

**成功响应**:

- **Code**: `200 OK`
- **Content**:
  
```json
{
  "弓种": [
    {
      "id": 1,
      "name": "反曲弓"
    },
    {
      "id": 2,
      "name": "复合弓"
    }
  ],
  "年龄段": [
    {
      "id": 3,
      "name": "成年"
    },
    {
      "id": 4,
      "name": "青少年"
    }
  ]
}
```

#### 添加组别项

- **URL**: `/group/`
- **方法**: `POST`
- **权限**: 需要管理员权限
- **参数 (JSON body)**:

| 参数名          | 类型   | 必须 | 说明                 |
|-----------------|--------|------|----------------------|
| `category_name` | string | 是   | 组别类别名称         |
| `name`          | string | 是   | 组别项名称           |
| `description`   | string | 否   | 组别项描述（可选项） |

**成功响应**:

- **Code**: `201 Created`
- **Content**:
  
```json
{
  "message": "Category item added successfully."
}
```

**失败响应**:

- **Code**: `404 Not Found`
- **Content**:
  
```json
{
  "error": "Category not found."
}
```

#### 编辑组别项

- **URL**: `/group/<int:item_id>`
- **方法**: `PUT`
- **权限**: 需要管理员权限
- **URL参数**:

| 参数名    | 类型 | 必须 | 说明       |
|-----------|------|------|------------|
| `item_id` | int  | 是   | 组别项的ID |

- **请求体 (JSON body)**:

| 参数名        | 类型   | 必须 | 说明               |
|---------------|--------|------|--------------------|
| `name`        | string | 否   | 新的组别项名称     |
| `description` | string | 否   | 新的组别项描述     |

**成功响应**:

- **Code**: `200 OK`
- **Content**:
  
```json
{
  "message": "Category item updated successfully."
}
```

**失败响应**:

- **Code**: `404 Not Found`
- **Content**:
  
```json
{
  "error": "Category item not found."
}
```

#### 删除组别项

- **URL**: `/group/<int:item_id>`
- **方法**: `DELETE`
- **权限**: 需要管理员权限
- **URL参数**:

| 参数名    | 类型 | 必须 | 说明       |
|-----------|------|------|------------|
| `item_id` | int  | 是   | 组别项的ID |

**成功响应**:

- **Code**: `200 OK`
- **Content**:
  
```json
{
  "message": "Category item del successfully."
}
```

**失败响应**:

- **Code**: `404 Not Found`
- **Content**:
  
```json
{
  "error": "Category item not found."
}
```

</br>

---

</br>


### 获取赛事列表

- **URL**: `/events/`
- **Method**: `GET`
- **Auth Required**: Yes (JWT Token)
- **Permissions Required**: None

#### Query Parameters

| Parameter        | Type  | Required | Description                          |
|------------------|-------|----------|--------------------------------------|
| `page`           | int   | No       | 请求的页码，默认为1。                 |
| `per_page`       | int   | No       | 每页显示的赛事数量，默认为10。         |
| `name`           | str   | No       | 根据赛事名称进行过滤。                 |
| `association_id` | int   | No       | 根据协会ID进行过滤。                   |
| `status`         | int   | No       | 根据赛事状态进行过滤。                 |

#### Response

```json
{
    "events": [
        {
            "id": 1,
            "name": "赛事名称",
            "location": "赛事地点",
            "start_time": "开始时间",
            "end_time": "结束时间",
            // 更多赛事信息
        }
    ],
    "total": 100,
    "pages": 10,
    "page": 1
}
```

---

### 获取单个赛事详情

- **URL**: `/events/<int:event_id>`
- **Method**: `GET`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

| Parameter  | Type | Required | Description |
|------------|------|----------|-------------|
| `event_id` | int  | Yes      | 赛事的ID。   |

#### Response

```json
{
    "id": 1,
    "name": "赛事名称",
    "location": "赛事地点",
    "start_time": "开始时间",
    "end_time": "结束时间",
    // 更多赛事信息
}
```

---

### 创建赛事

- **URL**: `/events/`
- **Method**: `POST`
- **Auth Required**: Yes (JWT Token)
- **Permissions Required**: `association_main_admin`, `association_admin`

#### Request Body

```json
{
    "name": "新赛事",
    "location": "赛事地点",
    "start_time": "开始时间",
    "end_time": "结束时间",
    "description": "赛事描述",
    "organizer_contact": "组织者联系方式",
    "promo_image": "宣传图链接",
    "registration_limit": 100,
    "individual_registration_deadline": "个人报名截止时间",
    "team_registration_deadline": "团队报名截止时间",
    "group_info": "组别信息"
}
```

#### Response

```json
{
    "message": "Event created successfully.",
    "event_id": 1
}
```

---

### 更新赛事

- **URL**: `/events/<int:event_id>`
- **Method**: `PUT`
- **Auth Required**: Yes (JWT Token)
- **Permissions Required**: `association_main_admin`, `association_admin`

#### URL Parameters

| Parameter  | Type | Required | Description |
|------------|------|----------|-------------|
| `event_id` | int  | Yes      | 赛事的ID。   |

#### Request Body

```json
{
    "name": "更新后的赛事名称",
    "location": "更新后的赛事地点",
    // 更多可更新的赛事信息
}
```

#### Response

```json
{
    "message": "Event updated successfully."
}
```

---

### 删除赛事

- **URL**: `/events/<int:event_id>`
- **Method**: `DELETE`
- **Auth Required**: Yes (JWT Token)
- **Permissions Required**: `association_main_admin`, `association_admin`

#### URL Parameters

| Parameter  | Type | Required | Description |
|------------|------|----------|-------------|
| `event_id` | int  | Yes      | 赛事的ID。   |

#### Response

```json
{
    "message": "Event deleted successfully."
}
```
</br>

---

</br>


### 获取工作人员列表

- **URL**: `/events/<int:event_id>/workers`
- **Method**: `GET`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

- `event_id`: 赛事的ID。

#### Query Parameters

| Parameter      | Type  | Required | Description            |
|----------------|-------|----------|------------------------|
| `name`         | str   | No       | 工作人员的姓名。         |
| `phone_number` | str   | No       | 工作人员的电话号码。     |
| `type`         | int   | No       | 工作人员的类型（1-主裁判，2-助理裁判，3-工作人员）。|

#### Response

```json
[
    {
        "worker_id": 1,
        "user_id": 1,
        "name": "张三",
        "phone_number": "13800001111",
        "type": 1,
        "note": "主裁判"
    }
]
```


### 添加工作人员

- **URL**: `/events/<int:event_id>/workers`
- **Method**: `POST`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

- `event_id`: 赛事的ID。

#### Request Body

```json
{
    "phone_number": "13800001111",
    "name": "张三",
    "type": 1,
    "note": "主裁判"
}
```

#### Response

```json
{
    "message": "Worker added successfully."
}
```


### 删除工作人员

- **URL**: `/events/<int:event_id>/workers/<int:worker_id>`
- **Method**: `DELETE`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

- `event_id`: 赛事的ID。
- `worker_id`: 工作人员的ID。

#### Response

```json
{
    "message": "Worker deleted successfully."
}
```


### 添加工作人员角色

- **URL**: `/events/<int:event_id>/worker_role`
- **Method**: `POST`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

- `event_id`: 赛事的ID。

#### Request Body

```json
{
    "phone_number": "13800001111",
    "password": "newpassword"
}
```

#### Response

```json
{
    "message": "Worker role added successfully."
}
```

---

### 编辑工作人员信息

- **URL**: `/events/<int:event_id>/workers/<int:worker_id>`
- **Method**: `PUT`
- **Auth Required**: No
- **Permissions Required**: None

#### URL Parameters

- `event_id`: 赛事的ID。
- `worker_id`: 工作人员的ID。

#### Request Body

```json
{
    "note": "更新后的备注",
    "type": 2
}
```

#### Response

```json
{
    "message": "Worker edited successfully."
}
```


</br>

---

</br>

### 显示赛事注册详情

- **URL**: `/registration/<int:event_id>`
- **方法**: GET
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**:

| 参数名    | 类型 | 必须 | 描述           |
|---------|----|----|--------------|
| event_id | int | 是  | 赛事的唯一标识符 |

- **成功响应**:

**Code**: 200  
**Content**:
```json
{
  "event": {
    "id": 1,
    "name": "赛事名称",
    "location": "赛事地点",
    ...
  }
}
```

- **错误响应**:

**赛事未找到**:  
**Code**: 404  
**Content**: `{"message": "Event not found"}`

**注册未开放**:  
**Code**: 403  
**Content**: `{"message": "Registration is not open"}`


### 获取所有注册信息

- **URL**: `/registration/<int:event_id>`
- **方法**: GET
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**: 无

- **查询参数**:

| 参数名            | 类型   | 必须 | 描述                   |
|-----------------|------|----|----------------------|
| page            | int  | 否 | 页码，默认为1             |
| per_page        | int  | 否 | 每页数量，默认为10          |
| name            | str  | 否 | 参赛者姓名                 |
| gender          | str  | 否 | 性别                    |
| club_id         | int  | 否 | 俱乐部ID                 |
| id_type         | str  | 否 | 证件类型                  |
| id_number       | str  | 否 | 证件号码                  |
| phone_number    | str  | 否 | 电话号码                  |
| registration_time | str  | 否 | 注册时间                  |
| type            | int  | 否 | 参赛类型                  |
| pay_status      | int  | 否 | 支付状态                  |
| confirm_status  | int  | 否 | 确认状态                  |
| group_combinations | str  | 否 | 参赛组别ID，逗号分隔          |

- **成功响应**:

**Code**: 200  
**Content**:
```json
{
  "registrations": [
    {
      "id": 1,
      "user_name": "参赛者姓名",
      "gender": "性别",
      ...
    }
  ],
  "total": 100,
  "pages": 10,
  "current_page": 1
}
```

- **错误响应**:

**赛事未找到**:  
**Code**: 404  
**Content**: `{"message": "Event not found"}`

**注册未开放**:  
**Code**: 403  
**Content**: `{"message": "Registration is not open"}`


### 获取有效注册信息

- **URL**: `/registration/<int:event_id>/valid`
- **方法**: GET
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**: 无

- **查询参数**:

| 参数名            | 类型   | 必须 | 描述                   |
|-----------------|------|----|----------------------|
| page            | int  | 否 | 页码，默认为1             |
| per_page        | int  | 否 | 每页数量，默认为10          |
| name            | str  | 否 | 参赛者姓名                 |
| gender          | str  | 否 | 性别                    |
| club_id         | int  | 否 | 俱乐部ID                 |
| id_type         | str  | 否 | 证件类型                  |
| id_number       | str  | 否 | 证件号码                  |
| phone_number    | str  | 否 | 电话号码                  |
| registration_time | str  | 否 | 注册时间                  |
| type            | int  | 否 | 参赛类型                  |
| pay_status      | int  | 否 | 支付状态                  |
| confirm_status  | int  | 否 | 确认状态                  |
| group_combinations | str  | 否 | 参赛组别ID，逗号分隔          |

- **成功响应**:

**Code**: 200  
**Content**:
```json
{
  "registrations": [
    {
      "id": 1,
      "user_name": "参赛者姓名",
      "gender": "性别",
      ...
    }
  ],
  "total": 100,
  "pages": 10,
  "current_page": 1
}
```

- **错误响应**:

**赛事未找到**:  
**Code**: 404  
**Content**: `{"message": "Event not found"}`

**注册未开放**:  
**Code**: 403  
**Content**: `{"message": "Registration is not open"}`


### 用户添加赛事注册

- **URL**: `/registration/<int:event_id>`
- **方法**: POST
- **权限**: 无（用户公开访问）
- **URL 参数**:

| 参数名    | 类型 | 必须 | 描述           |
|---------|----|----|--------------|
| event_id | int | 是  | 赛事的唯一标识符 |

- **请求体参数** (JSON Body):

| 参数名               | 类型   | 必须 | 描述                          |
|---------------------|------|----|-----------------------------|
| name                | str  | 是  | 参赛者姓名                      |
| gender              | str  | 是  | 性别                           |
| id_type             | str  | 是  | 证件类型                        |
| id_number           | str  | 是  | 证件号码                        |
| phone_number        | str  | 是  | 电话号码                        |
| birth_date          | date | 是  | 出生日期，格式为YYYY-MM-DD       |
| selected_group_ids  | list | 是  | 用户选择的组别ID列表             |
| club_id             | int  | 否  | 俱乐部ID（如果参赛者代表某个俱乐部）|

- **成功响应**:

**Code**: 201  
**Content**:
```json
{
  "message": "Registration successful",
  "username": "生成的用户名",
  "password": "生成的密码（如果有）"
}
```

- **错误响应**:

**赛事未找到**:  
**Code**: 404  
**Content**: `{"message": "Event not found"}`

**注册未开放**:  
**Code**: 403  
**Content**: `{"message": "Registration is not open"}`

**手机号码无效**:  
**Code**: 400  
**Content**: `{'error': 'Invalid phone number'}`


### 管理员添加赛事注册

- **URL**: `/registration/<int:event_id>/manage`
- **方法**: POST
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**:

| 参数名    | 类型 | 必须 | 描述           |
|---------|----|----|--------------|
| event_id | int | 是  | 赛事的唯一标识符 |

- **请求体参数** (JSON Body):

| 参数名               | 类型   | 必须 | 描述                          |
|---------------------|------|----|-----------------------------|
| name                | str  | 是  | 参赛者姓名                      |
| gender              | str  | 是  | 性别                           |
| id_type             | str  | 是  | 证件类型                        |
| id_number           | str  | 是  | 证件号码                        |
| phone_number        | str  | 是  | 电话号码                        |
| birth_date          | date | 是  | 出生日期，格式为YYYY-MM-DD       |
| selected_group_ids  | list | 是  | 用户选择的组别ID列表             |
| club_id             | int  | 否  | 俱乐部ID（如果参赛者代表某个俱乐部）|

- **成功响应**:

**Code**: 201  
**Content**:
```json
{
  "message": "Registration successful",
  "username": "生成的用户名",
  "password": "生成的密码（如果有）"
}
```

- **错误响应**:

**赛事未找到**:  
**Code**: 404  
**Content**: `{"message": "Event not found"}`

**注册未开放**:  
**Code**: 403  
**Content**: `{"message": "Registration is not open"}`

**手机号码无效**:  
**Code**: 400  
**Content**: `{'error': 'Invalid phone number'}`


### 删除赛事注册

- **URL**: `/registration/<int:registration_id>`
- **方法**: DELETE
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**:

| 参数名            | 类型 | 必须 | 描述             |
|-----------------|----|----|----------------|
| registration_id | int | 是  | 注册记录的唯一标识符 |

- **成功响应**:

**Code**: 200  
**Content**:
```json
{
  "message": "Registration deleted successfully"
}
```

- **错误响应**:

**注册记录未找到**:  
**Code**: 404  
**Content**: `{"message": "Registration not found"}`



### 导出赛事注册信息到Excel

- **URL**: `/registration/<int:event_id>/export`
- **方法**: GET
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**:

| 参数名          | 类型 | 必须 | 描述             |
|---------------|----|----|----------------|
| event_id      | int | 是  | 赛事的唯一标识符 |

- **查询参数**:

| 参数名             | 类型   | 必须 | 描述                              |
|-------------------|------|----|---------------------------------|
| registration_ids  | str  | 否  | 特定的注册ID列表，逗号分隔            |
| name              | str  | 否  | 参赛者姓名                          |
| gender            | str  | 否  | 性别                               |
| club_id           | int  | 否  | 俱乐部ID                          |
| id_type           | str  | 否  | 证件类型                            |
| id_number         | str  | 否  | 证件号码                            |
| phone_number      | str  | 否  | 电话号码                            |
| registration_time | date | 否  | 注册时间，格式为YYYY-MM-DD          |
| group_combinations| str  | 否  | 用户选择的组别ID列表，逗号分隔        |

- **成功响应**:

**Code**: 200  
**Content**: Excel文件下载

- **错误响应**:

**赛事未找到**:
**Code**: 404  
**Content**: `{"message": "Event not found"}`


### 更新赛事注册信息

- **URL**: `/registration/<int:registration_id>`
- **方法**: PUT
- **权限**: 需要"association_main_admin"或"association_admin"角色
- **URL 参数**:

| 参数名            | 类型 | 必须 | 描述             |
|-----------------|----|----|----------------|
| registration_id | int | 是  | 注册记录的唯一标识符 |

- **请求体参数** (JSON Body):

| 参数名                 | 类型   | 必须 | 描述                             |
|-----------------------|------|----|--------------------------------|
| event_id              | int  | 否  | 赛事ID，如果更改赛事                 |
| group_combination_id  | int  | 否  | 组别ID，如果更改参赛组别             |
| pay_status            | int  | 否  | 支付状态（例如：0未支付，1已支付）    |
| confirm_status        | int  | 否  | 确认状态（例如：0未确认，1已确认）    |

- **成功响应**:

**Code**: 200  
**Content**:
```json
{
  "message": "Registration updated successfully"
}
```

- **错误响应**:

**注册记录未找到**:  
**Code**: 404  
**Content**: `{"error": "Registration not found"}`

**赛事未找到**:  
**Code**: 404  
**Content**: `{"error": "Event not found"}`

**选择的组别不被赛事支持**:  
**Code**: 400  
**Content**: `{'error': 'Selected group is not supported by this event'}`

</br>

---

</br>
