# lin-cms-tp-validate-core
LinCms TP 的反射参数中间件封装，含路由参数验证中间件，方法注释参数提取器，基于`wangyu/reflex-core`扩展

# `composer`安装

```bash
composer require lin-cms-tp/param-core
```

# 默认参数
| 名称 | 注释 | 默认值 |
| :-: |:-:|:-:|
| **config('lin.validate_root_path')** | 验证器根目录 | **api/validate** |

# 反射参数验证

- 需要在系统`config`配置`middleware.php`

```php
<?php
return [
    // 默认中间件命名空间
    'default_namespace' => 'app\\http\\middleware\\',
    'linRouteParam' => LinCmsTp\Param::Class
];
```

- 需要在接口类`注释`设置`@middleware`

```php
/**
 * Class Book
 * @route('v1/book')
 * @middleware('linRouteParam')
 * @package app\api\controller\v1
 */
class Book
{}
```

# 配置方法注释参数验证，有两种方式

## 使用@validate('验证器的名称，不带后缀名),进行方法验证
  
> 例如：`@validate('LoginForm') `相当于调用的`\app\api\validate\user\LoginForm`去验证
    
```php
/**
 * 账户登陆
 * @route('cms/user/login','post')
 * @param Request $request
 * @validate('LoginForm')
 * @return array
 * @throws \think\Exception
 */
public function login(Request $request)
{
    (new LoginForm())->goCheck();
    $params = $request->post();

    $user = LinUser::verify($params['nickname'], $params['password']);
    $result = Token::getToken($user);

    logger('登陆领取了令牌', $user['id'], $user['nickname']);

    return $result;
}
```

## 使用@param('参数名','参数注释','参数规则')，进行单个参数验证

> '参数规则' 对应TP的验证规则，例如：@param('id','ID','require|max:1000|min:1')

```php
/**
 * 查询指定bid的图书
 * @route('v1/book/:bid','get')
 * @param Request $bid
 * @param('bid','bid的图书','require')
 * @return mixed
 */
public function getBook($bid)
{
    $result = BookModel::get($bid);
    return $result;
}
```

# 联系我们

- QQ: `354007048` 
- Email: `china_wangyu@aliyun.com`