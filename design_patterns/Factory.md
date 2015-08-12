## 简单工厂模式

没有用设计模式的实现

```php
/**
 * 计算类
 */
class Calc
{
    /**
     * @param $num1
     * @param $num2
     * @param $operator 操作符
     */
    public function calculate($num1, $num2, $operator)
    {
        try {
            $result = 0;
            switch ($operator) {
                case '+':
                    $result = $num1 + $num2;
					break;

                case '-':
                    $result = $num1 - $num2;
					break;

                case '*':
                    $result = $num1 * $num2;
					break;

                # code...

                default:
                    # code...
                    break;
            }
            echo $result;
        } catch (Exception $e) {
            echo "您输入有误：" . $e->getMessage();
        }
    }
}
```

使用工厂模式之后的代码：

```php

/**
 * 计算接口
 * Interface Calc
 */
interface Calc
{
    public function getResult();
}

/**
 * 操作类
 * Class Operation
 */
class Operation
{
    protected $num1 = 0;
    protected $num2 = 0;
    protected $result = 0;

    public function setNum($num1, $num2)
    {
        $this->num1 = $num1;
        $this->num2 = $num2;
    }
}

class OperationAdd extends Operation implements Calc
{
    public function getResult()
    {
        return $this->result = $this->num1 + $this->num2;
    }
}

class OperationSub extends Operation implements Calc
{
    public function getResult()
    {
        return $this->result = $this->num1 - $this->num2;
    }
}

class OperationMul extends Operation implements Calc
{
    public function getResult()
    {
        return $this->result = $this->num1 * $this->num2;
    }
}

class OperationDiv extends Operation implements Calc
{
    public function getResult()
    {
        if (intval($this->num2) == 0) {
            return $this->result = 'Division by zero';
        }
        return $this->result = ($this->num1 / $this->num2);
    }
}

/**
 * 计算工厂
 * Class OperationFactory
 */
class OperationFactory
{
    private static $obj;

    public static function createOperation($type)
    {
        try {
            switch ($type) {
                case '+':
                    self::$obj = new OperationAdd();
                    break;

                case '-':
                    self::$obj = new OperationSub();
                    break;

                case '*':
                    self::$obj = new OperationMul();
                    break;

                case '/':
                    self::$obj = new OperationDiv();
                    break;

                default:
                    throw new Exception('I don\'t know type');
                    break;
            }
            return self::$obj;
        } catch (Exception $e) {
            echo "您输入有误：" . $e->getMessage();
        }
    }
}
// 使用
$obj = OperationFactory::createOperation('*');
$obj->setNum(1,2);
echo $obj->getResult();
```

**总结**

使用工厂模式之后代码变多了，但是为了解耦，扩展方便，更加灵活。业务变的复杂之后，增加代码的复用。