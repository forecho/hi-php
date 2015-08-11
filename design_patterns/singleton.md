## 单例模式

**实现**

1. 保存类唯一实例的静态成员变量
2. 必须私有化构造函数和克隆函数
3. 对外提供唯一可以访问的静态方法

```php
class Db
{
    public $conn;
    public static $sql;

    /**
     * 保存类唯一实例的静态成员变量
     * @var null
     */
    private static $_instance = null;

    /**
     * 必须私有化构造函数
     */
    private function __construct()
    {
        require_once('db.config.php');
        $this->conn = mysql_connect($db['host'], $db['user'], $db['password']);
        if (!mysql_select_db($db['database'], $this->conn)) {
            echo "失败";
        };
        mysql_query('set names utf8', $this->conn);
    }

    /**
     * 私有化克隆函数
     */
    private function __clone()
    {
    }

    /**
     * 对外提供唯一可以访问的静态方法
     * @return Db
     */
    public static function getInstance()
    {
        if (is_null(self::$_instance)) {
            self::$instance = new db;
        }
        return self::$instance;
    }

    /**
     * 查询数据库
     * @param $table
     * @param array $condition
     * @param array $field
     * @return array
     */
    public function select($table, $condition = array(), $field = array())
    {
        $where = '';
        if (!empty($condition)) {
            foreach ($condition as $k => $v) {
                $where .= $k . "='" . $v . "' and ";
            }
            $where = 'where ' . $where . '1=1';
        }
        $fieldstr = '';
        if (!empty($field)) {
            foreach ($field as $k => $v) {
                $fieldstr .= $v . ',';
            }
            $fieldstr = rtrim($fieldstr, ',');
        } else {
            $fieldstr = '*';
        }
        self::$sql = "select {$fieldstr} from {$table} {$where}";
        $result = mysql_query(self::$sql, $this->conn);
        $resuleRow = array();
        $i = 0;
        while ($row = mysql_fetch_assoc($result)) {
            foreach ($row as $k => $v) {
                $resuleRow[$i][$k] = $v;
            }
            $i++;
        }
        return $resuleRow;
    }

    public function insert($table, $data)
    {
        # code...
    }


}

$db = DB::getInstance();

$list = $db->select('test', array('name' => 'tom', 'password' => 'ds'), array('name', 'password'));
```