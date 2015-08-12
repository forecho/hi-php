## 策略模式

> 策略模式作为一种软件设计模式，指对象有某个行为，但是在不同的场景中，该行为有不同的实现算法。比如每个人都要「交个人所得税」，但是「在美国交个人所得税」和「在中国交个人所得税」就有不同的算税方法。

### 关键点

- 定义一个业务规则
- 封装每个算法
- 互相替换算法

### 场景

- 搜索-根据不同的算法返回不同的结果

### 实现

```php
interface IStrategy
{
    public function filter($record);
}

/**
 * 某字母之后的结果
 * Class FindAfterStrategy
 */
class FindAfterStrategy implements IStrategy
{
    private $_name;


    public function __construct($name)
    {
        $this->_name = $name;
    }

    public function filter($record)
    {
        return strcmp($this->_name, $record) <= 0;
    }
}

/**
 * 随机
 * Class RandomStrategy
 */
class RandomStrategy implements IStrategy
{
    public function filter($record)
    {
        return rand(0, 1) <= 0.5;
    }
}

/**
 * 包含某字母的结果
 * Class FindNameWith
 */
class FindNameWith implements IStrategy
{
    /**
     * @var 小写字母
     */
    private $_alphabet;

    public function __construct($alphabet)
    {
        $this->_alphabet = $alphabet;
    }

    public function filter($record)
    {
        $record = strtolower($record);
        if (strpos($record, $this->_alphabet) !== false) {
            return true;
        }
        return false;
    }
}

class UserList
{
    private $_list = [];

    public function __construct($names)
    {
        if ($names != null) {
            foreach ($names as $name) {
                $this->_list[] = $name;
            }
        }
    }

    public function add($name)
    {
        $this->_list[] = $name;
    }

    public function find($filter)
    {
        $recs = [];
        foreach ($this->_list as $user) {
            if ($filter->filter($user)) {
                $recs[] = $user;
            }
        }
        return $recs;

    }
}

// 使用
$userList = new UserList(['Andy', 'Jack', 'Lori', 'Echo']);
$find1 = $userList->find(new FindAfterStrategy('J'));
print_r($find1);

$find2 = $userList->find(new RandomStrategy());
print_r($find2);

$find3 = $userList->find(new FindNameWith('a'));
print_r($find3);
```