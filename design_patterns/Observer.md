## 观察者模式（发布/订阅模式）

> 在此种模式中，一个目标对象管理所有相依于它的观察者对象，并且在它本身的状态改变时主动发出通知。这通常透过呼叫各观察者所提供的方法来实现。此种模式通常被用来实作事件处理系统。

### 关键点

定义了一对多的依赖，一个类的状态改变时，它所有的依赖全部都收到通知，并且自动「更新」。

- 被观察者 -> 追加观察者 -> 满足条件时通知观察者
- 观察者 -> 接受观察条件

### 场景

- 注册之后发邮件
- 下单之后发通知
...

### 实现

```php
function bug($user)
{
    echo "buy success\n";
    return true;
}

class User
{
    private $_observers = [];

    public function register($sub)
    {
        // 注册观察者
        $this->_observers[] = $sub;
    }

    public function trigger()
    {
        // 外部统一访问
        if (!empty($this->_observers)) {
            foreach ($this->_observers as $observer) {
                $observer->update();
            }
        }
    }
}

/**
 * 观察者要实现的接口
 * Interface ObserverAble
 */
interface ObserverAble
{
    public function update();
}

class Sms implements ObserverAble
{
    public function update()
    {
        echo "Sms notify\n";
    }
}

class Email implements ObserverAble
{
    public function update()
    {
        echo "Email notify\n";
    }
}

// 使用
$user = new User();
$user->register(new Sms());
$user->register(new Email());

if (bug($user)) {
    $user->trigger();
}
```