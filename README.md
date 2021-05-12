# pencil-soft

### TASK 1 ###

Имеется база со следующими таблицами:
CREATE TABLE `users` (
    `id`         INT(11) NOT NULL AUTO_INCREMENT,
    `name`       VARCHAR(255) DEFAULT NULL,
    `gender`     INT(11) NOT NULL COMMENT '0 - не указан, 1 - мужчина, 2 -
женщина.',
    `birth_date` INT(11) NOT NULL COMMENT 'Дата в unixtime.',
    PRIMARY KEY (`id`)
);
CREATE TABLE `phone_numbers` (
    `id`      INT(11) NOT NULL AUTO_INCREMENT,
    `user_id` INT(11) NOT NULL,
    `phone`   VARCHAR(255) DEFAULT NULL,
    PRIMARY KEY (`id`)
);
Напишите запрос, возвращающий имя и число указанных телефонных номеров девушек в возрасте от
18 до 22 лет.
Оптимизируйте таблицы и запрос при необходимости.

# Sol:

SELECT users.name, count(phone_numbers.phone) AS phone_nums
FROM users
LEFT JOIN phone_numbers
ON users.id = phone_numbers.user_id
WHERE TIMESTAMPDIFF(YEAR, FROM_UNIXTIME(users.birth_date), NOW()) BETWEEN 18 AND 22

*****

### TASK 2 ###

Имеется строка:
https://www.somehost.com/test/index.html?param1=4&amp;param2=3&amp;param3=2&amp;param4=1&amp;param5=3
Напишите функцию, которая:
1. удалит параметры со значением “3”;
2. отсортирует параметры по значению;
3. добавит параметр url со значением из переданной ссылки без параметров (в примере:
/test/index.html);
4. сформирует и вернёт валидный URL на корень указанного в ссылке хоста.
В указанном примере функцией должно быть возвращено:
https://www.somehost.com/?param4=1&amp;param3=2&amp;param1=4&amp;url=%2Ftest%2Findex.html

# Sol:

/**
 * task 2
 */
function task2()
{
    $current_host = (isset($_SERVER['HTTPS']) && $_SERVER['HTTPS'] === 'on' ? "https" : "http") . "://$_SERVER[HTTP_HOST]";

    $php_self = 'url=' . urlencode($_SERVER['PHP_SELF']);

    foreach ($_GET as $key => $param) {
        if ($param == 3) {
            unset($_GET[$key]);
        }
    }

    asort($_GET);

    $request = '';

    foreach ($_GET as $key => $param) {
        $request .= $key . '=' . $param . '&';
    }

    $url = $current_host .'/?'. $request . $php_self;

    return $url;
}

*****

### TASK 3 ###

# Sol:

Class User
{
    /**
     * tables relations: User could have many posts
     * @return Has Many
     */
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}

Class Post
{
    /**
     * tables relations: Post belongs to one User 
     * @return Belongs To
     */
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}

Class PostController
{
    /**
     * Create new Post
     * @return mixed
     */
    public function create()
    {
        //
    }
    
    /**
     * Gets User that created this post (Author of Article)
     * @return mixed
     */
    public function getUser()
    {
        //
    }

    /**
     * Gets all User's Posts
     * @return mixed
     */
    public function getUsersPosts()
    {
        //
    }

    /**
     * Changes Post's User
     * @return mixed
     */
    public function changePostsUser()
    {
        //
    }

}


***

### TASK 4 ###

# Sol:

function load_users_data($user_ids)
{
    $db_host       = 'localhost';
    $db_name       = 'database';
    $db_username   = 'root';
    $db_password   = '123123';
    $db_charset    = 'utf8';

    $db = "mysql:host=$db_host; dbname=$db_name; charset=$db_charset";

    $db_options = [
        PDO::ATTR_ERRMODE            => PDO::ERRMODE_EXCEPTION,
        PDO::ATTR_DEFAULT_FETCH_MODE => PDO::FETCH_ASSOC,
        PDO::ATTR_EMULATE_PREPARES   => false,
    ];

    try {
        $pdo = new PDO ($db, $db_username, $db_password, $db_options);
    } catch (Exception $e) {
        exit($e->getMessage());
    }

    $statement = $pdo->prepare('SELECT id, name FROM users WHERE id IN (?)');
    $statement->execute($user_ids);
    $users = $statement->fetchAll();
    if (!$users) exit('users with specified id`s was not found');
    $statement = null;
    return $users;
}

$data = load_users_data($_GET['user_ids']);
foreach ($data as $user_id => $name) {
    echo "<a href=\"/show_user.php?id=$user_id\">$name</a>";
}

1. Исходный вариант уязвим к SQL-инъекциям
2. Выполнение запросов в цикле - плохая практика





