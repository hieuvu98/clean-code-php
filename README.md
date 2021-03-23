# Clean Code PHP
## Table of Contents

1. [Introduction](#introduction)
2. [Variables](#variables)
  * [Use meaningful and pronounceable variable names](#use-meaningful-and-pronounceable-variable-names)
  * [Use the same vocabulary for the same type of variable](#use-the-same-vocabulary-for-the-same-type-of-variable)
  * [Use searchable names (part 1)](#use-searchable-names-part-1)
  * [Use searchable names (part 2)](#use-searchable-names-part-2)
  * [Use explanatory variables](#use-explanatory-variables)
  * [Avoid nesting too deeply and return early (part 1)](#avoid-nesting-too-deeply-and-return-early-part-1)
  * [Avoid nesting too deeply and return early (part 2)](#avoid-nesting-too-deeply-and-return-early-part-2)
  * [Avoid Mental Mapping](#avoid-mental-mapping)
  * [Don't add unneeded context](#dont-add-unneeded-context)
  * [Use default arguments instead of short circuiting or conditionals](#use-default-arguments-instead-of-short-circuiting-or-conditionals)
3. [Comparison](#comparison)
  * [Use identical comparison](#use-identical-comparison)
  * [Null coalescing operator](#null-coalescing-operator)
4. [Functions](#functions)
  * [Function arguments (2 or fewer ideally)](#function-arguments-2-or-fewer-ideally)
  * [Function names should say what they do](#function-names-should-say-what-they-do)
  * [Functions should only be one level of abstraction](#functions-should-only-be-one-level-of-abstraction)
  * [Don't use flags as function parameters](#dont-use-flags-as-function-parameters)
  * [Avoid Side Effects](#avoid-side-effects)
  * [Don't write to global functions](#dont-write-to-global-functions)
  * [Don't use a Singleton pattern](#dont-use-a-singleton-pattern)
  * [Encapsulate conditionals](#encapsulate-conditionals)
  * [Avoid negative conditionals](#avoid-negative-conditionals)
  * [Avoid conditionals](#avoid-conditionals)
  * [Avoid type-checking (part 1)](#avoid-type-checking-part-1)
  * [Avoid type-checking (part 2)](#avoid-type-checking-part-2)
  * [Remove dead code](#remove-dead-code)
5. [Objects and Data Structures](#objects-and-data-structures)
  * [Use object encapsulation](#use-object-encapsulation)
  * [Make objects have private/protected members](#make-objects-have-privateprotected-members)
6. [Classes](#classes)
  * [Prefer composition over inheritance](#prefer-composition-over-inheritance)
  * [Avoid fluent interfaces](#avoid-fluent-interfaces)
  * [Prefer final classes](#prefer-final-classes)
7. [SOLID](#solid)
  * [Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
  * [Open/Closed Principle (OCP)](#openclosed-principle-ocp)
  * [Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
  * [Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
  * [Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)
8. [Don’t repeat yourself (DRY)](#dont-repeat-yourself-dry)
9. [Translations](#translations)

## Introduction

Các nguyên tắc kỹ thuật phần mềm, trích từ cuốn sách [*Clean Code*](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
của Robert C. Martin's, thích hợp cho ngôn ngữ PHP. Sách không hướng dẫn cách viết code. Nó hướng dẫn cách làm phần mềm 
dễ đọc, có thể sử dụng lại và có thể tái cấu trúc trong PHP.

Không có bất cư nguyên tắc nào ở đây phải tuân thủ một cách nghiêm ngặt và thậm chí ít hơn sẽ được thống nhất chung. Đây
chỉ là hướng dẫn và không có bất kỳ điều gì khác, nhưng chúng là những thứ được hệ thống hóa qua nhiều năm kinh nghiệm của
các tác giả.

Not every principle herein has to be strictly followed, and even fewer will be universally
agreed upon. These are guidelines and nothing more, but they are ones codified over many
years of collective experience by the authors of *Clean Code*.

Lấy cảm hứng từ [clean-code-javascript](https://github.com/ryanmcdermott/clean-code-javascript).

Lưu ý: Mặc dù nhiều lập trình viên còn sử dụng PHP 5, nhưng nhiều ví dụ trong đây chỉ chạy được trên PHP 7.1+.

## Biến (Variables)

### Sử dụng tên biến có ý nghĩa và dễ đọc

**Bad:**

```php
declare(strict_types=1);

$ymdstr = $moment->format('y-m-d');
```

**Good:**

```php
declare(strict_types=1);

$currentDate = $moment->format('y-m-d');
```

**[⬆ back to top](#table-of-contents)**

### Sử dụng cùng một từ vựng cho một loại biến

**Bad:**

```php
declare(strict_types=1);

getUserInfo();
getUserData();
getUserRecord();
getUserProfile();
```

**Good:**

```php
declare(strict_types=1);

getUser();
```

**[⬆ back to top](#table-of-contents)**

### Sử dụng tên có thể tìm kiếm (part 1)

Chúng ta thường đọc code nhiều hơn là viết code. Điều quan trọng là code của chúng ta viết ra dễ đọc và dễ tìm kiếm. Nếu 
không đặt tên biến có ý nghĩa và làm chương chình dễ hiểu, chúng ra gây khó cho người đọc. Làm cho tên biến có thể tìm 
kiếm được:

**Bad:**

```php
declare(strict_types=1);

// What the heck is 448 for?
$result = $serializer->serialize($data, 448);
```

**Good:**

```php
declare(strict_types=1);

$json = $serializer->serialize($data, JSON_UNESCAPED_SLASHES | JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE);
```

### Sử dụng tên có thể tìm kiếm (part 2)

**Bad:**

```php
declare(strict_types=1);

class User
{
    // What the heck is 7 for?
    public $access = 7;
}

// What the heck is 4 for?
if ($user->access & 4) {
    // ...
}

// What's going on here?
$user->access ^= 2;
```

**Good:**

```php
declare(strict_types=1);

class User
{
    public const ACCESS_READ = 1;

    public const ACCESS_CREATE = 2;

    public const ACCESS_UPDATE = 4;

    public const ACCESS_DELETE = 8;

    // User as default can read, create and update something
    public $access = self::ACCESS_READ | self::ACCESS_CREATE | self::ACCESS_UPDATE;
}

if ($user->access & User::ACCESS_UPDATE) {
    // do edit ...
}

// Deny access rights to create something
$user->access ^= User::ACCESS_CREATE;
```

**[⬆ back to top](#table-of-contents)**

### Đặt tên biến dễ hiểu
 
**Bad:**

```php
declare(strict_types=1);

$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches[1], $matches[2]);
```

**Not bad:**

Tốt hơn một chút, nhưng vẫn còn phụ thuộc nhiều vào regex.
```php
declare(strict_types=1);

$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(.+?)\s*(\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

[, $city, $zipCode] = $matches;
saveCityZipCode($city, $zipCode);
```

**Good:**

Đã giảm phụ thuộc vào regex bằng "naming subpatterns".

```php
declare(strict_types=1);

$address = 'One Infinite Loop, Cupertino 95014';
$cityZipCodeRegex = '/^[^,]+,\s*(?<city>.+?)\s*(?<zipCode>\d{5})$/';
preg_match($cityZipCodeRegex, $address, $matches);

saveCityZipCode($matches['city'], $matches['zipCode']);
```

**[⬆ back to top](#table-of-contents)**

### Tránh lồng quá nhiều và return sớm (part 1)

Quá nhiều if-else sẽ khiến code của bạn khó theo dõi. Làm rõ rang tốt hơn là ngầm định.

**Bad:**

```php
declare(strict_types=1);

function isShopOpen($day): bool
{
    if ($day) {
        if (is_string($day)) {
            $day = strtolower($day);
            if ($day === 'friday') {
                return true;
            } elseif ($day === 'saturday') {
                return true;
            } elseif ($day === 'sunday') {
                return true;
            }
            return false;
        }
        return false;
    }
    return false;
}
```

**Good:**

```php
declare(strict_types=1);

function isShopOpen(string $day): bool
{
    if (empty($day)) {
        return false;
    }

    $openingDays = ['friday', 'saturday', 'sunday'];

    return in_array(strtolower($day), $openingDays, true);
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh lồng quá nhiều và return sớm (part 2)

**Bad:**

```php
declare(strict_types=1);

function fibonacci(int $n)
{
    if ($n < 50) {
        if ($n !== 0) {
            if ($n !== 1) {
                return fibonacci($n - 1) + fibonacci($n - 2);
            }
            return 1;
        }
        return 0;
    }
    return 'Not supported';
}
```

**Good:**

```php
declare(strict_types=1);

function fibonacci(int $n): int
{
    if ($n === 0 || $n === 1) {
        return $n;
    }

    if ($n >= 50) {
        throw new Exception('Not supported');
    }

    return fibonacci($n - 1) + fibonacci($n - 2);
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh hack não người đọc

Don’t force the reader of your code to translate what the variable means.
Explicit is better than implicit.

**Bad:**

```php
$l = ['Austin', 'New York', 'San Francisco'];

for ($i = 0; $i < count($l); $i++) {
    $li = $l[$i];
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    // Wait, what is `$li` for again?
    dispatch($li);
}
```

**Good:**

```php
declare(strict_types=1);

$locations = ['Austin', 'New York', 'San Francisco'];

foreach ($locations as $location) {
    doStuff();
    doSomeOtherStuff();
    // ...
    // ...
    // ...
    dispatch($location);
}
```

**[⬆ back to top](#table-of-contents)**

### Đừng thêm nội dung không cần thiết

Nếu tên class/object của bạn nói cho bạn một vài thứ, đừng lặp lại chúng trong tên biến.

**Bad:**

```php
declare(strict_types=1);

class Car
{
    public $carMake;

    public $carModel;

    public $carColor;

    //...
}
```

**Good:**

```php
declare(strict_types=1);

class Car
{
    public $make;

    public $model;

    public $color;

    //...
}
```

**[⬆ back to top](#table-of-contents)**

### Sử dụng đối số mặc định thay vì phải kiểm tra bằng biểu thức điều kiện

**Not good:**

Chưa tốt bởi vì `$breweryName` có thể `NULL`.

```php
function createMicrobrewery($breweryName = 'Hipster Brew Co.'): void
{
    // ...
}
```

**Not bad:**

Ý kiến này dễ hiểu hơn phiên bản trước, nhưng nó kiểm soát tốt hơn giá trị của biến.

```php
function createMicrobrewery($name = null): void
{
    $breweryName = $name ?: 'Hipster Brew Co.';
    // ...
}
```

**Good:**

Bạn có thể sử dụng [type hinting](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)
và chắc chắn <code>$breweryName</code> sẽ không bị NULL.

```php
function createMicrobrewery(string $breweryName = 'Hipster Brew Co.'): void
{
    // ...
}
```

**[⬆ back to top](#table-of-contents)**

## Comparison

### Use [identical comparison](http://php.net/manual/en/language.operators.comparison.php)

**Not good:**

So sánh đơn giản sẽ chuyển đổi string -> integer

```php
declare(strict_types=1);

$a = '42';
$b = 42;

if ($a != $b) {
    // The expression will always pass
}
```

Phép so sánh `$a != $b` returns `FALSE` nhưng trong thực tế kết quả phải là `TRUE`!
Chuỗi `'42'` thì phải khác số `42`.

**Good:**

Sử dụng identical comparison sẽ so sánh kiểu dữ liệu và giá trị.

```php
declare(strict_types=1);

$a = '42';
$b = 42;

if ($a !== $b) {
    // The expression is verified
}
```

**[⬆ back to top](#table-of-contents)**

### Toán tử liên kết null

Liên kết null là một toán tử [giới thiệu trong PHP 7](https://www.php.net/manual/en/migration70.new-features.php). Toán tử liên kết null `??` được thêm vào như một đường cú pháp đối với trường hợp phổ biến là cần sử dụng toán tử ba ngôi kết hợp với `isset()`. Nó sẽ trả về toán hạng đầu tiên nếu nó tồn ttaij và không `null`

**Bad:**

```php
declare(strict_types=1);

if (isset($_GET['name'])) {
    $name = $_GET['name'];
} elseif (isset($_POST['name'])) {
    $name = $_POST['name'];
} else {
    $name = 'nobody';
}
```

**Good:**
```php
declare(strict_types=1);

$name = $_GET['name'] ?? $_POST['name'] ?? 'nobody';
```

**[⬆ back to top](#table-of-contents)**

## Hàm

### Tham số của hàm (lý tưởng là ít hơn hoặc bằng 2)

Việc giới hạn số lượng tham số của hàm vô cùng quan trọng vì nó giúp kiểm trả hàm của bạn dễ dàng hơn. Nhiều hơn 3 đối số dẫn đến có nhiều tổ hợp, nơi bạn kiểm trả hàng tấn các trường hợp khác nhau với mỗi đối số riêng.

Không có đối số là một trường hợp lý tưởng. Một hoặc hai đối số là OK và ba đối số nên tránh. Bất kỳ những cái gì nhiều hơn thế nên được củng cố lại.

Thông thường nếu bạn có nhiều hơn hai đối số thì hàm của bạn đang cố găng thưc hiện quá nhiều. Truong trường hơp không, hầu hết một object higher-level sẽ đủ như là một đối số.

**Bad:**

```php
declare(strict_types=1);

class Questionnaire
{
    public function __construct(
        string $firstname,
        string $lastname,
        string $patronymic,
        string $region,
        string $district,
        string $city,
        string $phone,
        string $email
    ) {
        // ...
    }
}
```

**Good:**

```php
declare(strict_types=1);

class Name
{
    private $firstname;

    private $lastname;

    private $patronymic;

    public function __construct(string $firstname, string $lastname, string $patronymic)
    {
        $this->firstname = $firstname;
        $this->lastname = $lastname;
        $this->patronymic = $patronymic;
    }

    // getters ...
}

class City
{
    private $region;

    private $district;

    private $city;

    public function __construct(string $region, string $district, string $city)
    {
        $this->region = $region;
        $this->district = $district;
        $this->city = $city;
    }

    // getters ...
}

class Contact
{
    private $phone;

    private $email;

    public function __construct(string $phone, string $email)
    {
        $this->phone = $phone;
        $this->email = $email;
    }

    // getters ...
}

class Questionnaire
{
    public function __construct(Name $name, City $city, Contact $contact)
    {
        // ...
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Tên hàm phải nói lên nhưng gì chúng ra làm

**Bad:**

```php
class Email
{
    //...

    public function handle(): void
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// What is this? A handle for the message? Are we writing to a file now?
$message->handle();
```

**Good:**

```php
class Email
{
    //...

    public function send(): void
    {
        mail($this->to, $this->subject, $this->body);
    }
}

$message = new Email(...);
// Clear and obvious
$message->send();
```

**[⬆ back to top](#table-of-contents)**

### Các hàm chỉ nên có một cấp độ trừu tượng

Khi bạn có nhiều hơn một cập độ trừu tượng, hàm của bạn thường hoạt động quá nhiều. Chia nhỏ các chức năng dẫn đến việc tái sử dụng và kiểm tra dễ dàng hơn.

**Bad:**

```php
declare(strict_types=1);

function parseBetterPHPAlternative(string $code): void
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            // ...
        }
    }

    $ast = [];
    foreach ($tokens as $token) {
        // lex...
    }

    foreach ($ast as $node) {
        // parse...
    }
}
```

**Bad too:**

Chúng ra đã thực hiện nhưng hàm `parseBetterPHPAlternative()` vẫn rất phức tạp và không thể kiểm tra được.

```php
function tokenize(string $code): array
{
    $regexes = [
        // ...
    ];

    $statements = explode(' ', $code);
    $tokens = [];
    foreach ($regexes as $regex) {
        foreach ($statements as $statement) {
            $tokens[] = /* ... */;
        }
    }

    return $tokens;
}

function lexer(array $tokens): array
{
    $ast = [];
    foreach ($tokens as $token) {
        $ast[] = /* ... */;
    }

    return $ast;
}

function parseBetterPHPAlternative(string $code): void
{
    $tokens = tokenize($code);
    $ast = lexer($tokens);
    foreach ($ast as $node) {
        // parse...
    }
}
```

**Good:**

Giải pháp tôt nhất là loại bỏ các dependencies khỏi hàm `parseBetterPHPAlternative()`.

```php
class Tokenizer
{
    public function tokenize(string $code): array
    {
        $regexes = [
            // ...
        ];

        $statements = explode(' ', $code);
        $tokens = [];
        foreach ($regexes as $regex) {
            foreach ($statements as $statement) {
                $tokens[] = /* ... */;
            }
        }

        return $tokens;
    }
}

class Lexer
{
    public function lexify(array $tokens): array
    {
        $ast = [];
        foreach ($tokens as $token) {
            $ast[] = /* ... */;
        }

        return $ast;
    }
}

class BetterPHPAlternative
{
    private $tokenizer;
    private $lexer;

    public function __construct(Tokenizer $tokenizer, Lexer $lexer)
    {
        $this->tokenizer = $tokenizer;
        $this->lexer = $lexer;
    }

    public function parse(string $code): void
    {
        $tokens = $this->tokenizer->tokenize($code);
        $ast = $this->lexer->lexify($tokens);
        foreach ($ast as $node) {
            // parse...
        }
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Đừng sử dụng cờ như một đối số của hàm

Cờ nói cho người dùng của bạn biết rằng hàm này làm nhiều hơn một việc. Các hàm nên làm một việc. Tách hàm của bạn ra nếu chúng đi theo các luồng khác dựa trên boolean.

**Bad:**

```php
declare(strict_types=1);

function createFile(string $name, bool $temp = false): void
{
    if ($temp) {
        touch('./temp/' . $name);
    } else {
        touch($name);
    }
}
```

**Good:**

```php
declare(strict_types=1);

function createFile(string $name): void
{
    touch($name);
}

function createTempFile(string $name): void
{
    touch('./temp/' . $name);
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh tác dụng phụ

Một hàm tạo ra các hiệu ứng phụ nếu nó làm bất kỳ điều gì ngoài việc lấy giá trị và trả về một giá trị khác. Tác dụng phụ có thể là viết vào một file nào đó, sử dụng sửa đối biến global, hoặc vô tình chuyển hết tiền của bạn cho ai đó.

Bây giờ, bạn cần làm việc với các tác dụng phụ đó trong chương trình. Giống như ví dụ trước, bạn có thể cần ghi vào một file. Điều bạn cần làm là tập trung vào nơi bạn làm việc với nó. Đừng làm một vài hàm, class để ghi vào một file cụ thể. Hãy tạo một service để làm việc đó. Một và chỉ một.

Điểm chính là tránh lỗi phổ biến như: chia sẻ trạng thái object mà không tuân theo cấu trúc nào, sử dụng các kiểu dữ liệu có thể thay đổi/bị thay đổi bởi bất cứ thứ gì, và không tổng hợp các tác dụng phụ có thể xảy ra khi viết hàm.

**Bad:**

```php
declare(strict_types=1);

// Global variable referenced by following function.
// If we had another function that used this name, now it'd be an array and it could break it.
$name = 'Ryan McDermott';

function splitIntoFirstAndLastName(): void
{
    global $name;

    $name = explode(' ', $name);
}

splitIntoFirstAndLastName();

var_dump($name);
// ['Ryan', 'McDermott'];
```

**Good:**

```php
declare(strict_types=1);

function splitIntoFirstAndLastName(string $name): array
{
    return explode(' ', $name);
}

$name = 'Ryan McDermott';
$newName = splitIntoFirstAndLastName($name);

var_dump($name);
// 'Ryan McDermott';

var_dump($newName);
// ['Ryan', 'McDermott'];
```

**[⬆ back to top](#table-of-contents)**

### Đừng viết hàm global

Dùng nhiều hàm global là một thực hành động tệ với nhiều ngôn ngữu bởi vì bạn có thể gây xung đột với thư viện và người dùng api của bạn không hề biết gì cho đến khi họ nhận được rồi của sản phẩm. Hãy suy nghĩ về một vài ví dụ: Điều gì xảy ra nếu bạn muốn cấu hình một mảng? Bạn có thể viêt một làm global giống như `config()` nhưng nó có thể bị xung đột với các thư viện khác.

**Bad:**

```php
declare(strict_types=1);

function config(): array
{
    return [
        'foo' => 'bar',
    ];
}
```

**Good:**

```php
declare(strict_types=1);

class Configuration
{
    private $configuration = [];

    public function __construct(array $configuration)
    {
        $this->configuration = $configuration;
    }

    public function get(string $key): ?string
    {
        // null coalescing operator
        return $this->configuration[$key] ?? null;
    }
}
```

Load configuration và tạo instance của lớp `Configuration`

```php
declare(strict_types=1);

$configuration = new Configuration([
    'foo' => 'bar',
]);
```

**[⬆ back to top](#table-of-contents)**

### Đừng sử dụng Singleton pattern

Singleton là một [anti-pattern](https://en.wikipedia.org/wiki/Singleton_pattern). Trích đoạn từ Brian Button:
 1. Chúng thường được sử dụng như là một **global instance**, tại sao nó tệ? Bởi vì **bạn ẩn dependencies** của ứng dụng bên trong code của bạn, thay vì thể hiện chúng qua interfaces [code smell](https://en.wikipedia.org/wiki/Code_smell).
 2. Chúng vi phạm [single responsibility principle](#single-responsibility-principle-srp): theo thực tế là **chúng điều khiển những gì tạo chúng ra và vòng đời của nó**.
 3. Chúng vốn dĩ khiến code kết hợp chặt chẽ với nhau [coupled](https://en.wikipedia.org/wiki/Coupling_%28computer_programming%29). Điều này làm cho việc làm giả chúng gây **khó khăn để kiểm tra** trong nhiều trường hợp.
 4. Chúng giữ trạng thái trong suốt vòng đời của ứng dụng. Một điểm khác đối với quá trình thử nghiệm **bạn có thể gặp phải phải mà các unit test cần phải được sắp xếp** đây là một điều không tốt đối với các unit test. Tại sao? Bởi vì mỗi unit test nên đọc lập với các unit test khác.

Đây cũng là suy nghĩ tốt của [Misko Hevery](http://misko.hevery.com/about/) về [root of problem](http://misko.hevery.com/2008/08/25/root-cause-of-singletons/).

**Bad:**

```php
declare(strict_types=1);

class DBConnection
{
    private static $instance;

    private function __construct(string $dsn)
    {
        // ...
    }

    public static function getInstance(): self
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    // ...
}

$singleton = DBConnection::getInstance();
```

**Good:**

```php
declare(strict_types=1);

class DBConnection
{
    public function __construct(string $dsn)
    {
        // ...
    }

    // ...
}
```

Tạo instance lớp `DBConnection` và cấu hình chúng với [DSN](http://php.net/manual/en/pdo.construct.php#refsect1-pdo.construct-parameters).

```php
declare(strict_types=1);

$connection = new DBConnection($dsn);
```

Và bây giờ bạn phải sử dụng instance `DBConnection` cho ứng dụng của bạn.

**[⬆ back to top](#table-of-contents)**

### Đóng gói điều kiện

**Bad:**

```php
declare(strict_types=1);

if ($article->state === 'published') {
    // ...
}
```

**Good:**

```php
declare(strict_types=1);

if ($article->isPublished()) {
    // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh các điều kiện phủ định

**Bad:**

```php
declare(strict_types=1);

function isDOMNodeNotPresent(DOMNode $node): bool
{
    // ...
}

if (! isDOMNodeNotPresent($node)) {
    // ...
}
```

**Good:**

```php
declare(strict_types=1);

function isDOMNodePresent(DOMNode $node): bool
{
    // ...
}

if (isDOMNodePresent($node)) {
    // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh dụng điều kiện

Điều này có vẻ không khả thi. Khi lần đầu nghe điều này hầu hết mọi người nói "làm thế nào mà tôi muốn làm một vài thứ gì đó mà không có `if`". Câu trả lời là bạn có thể sử dụng đa hình để hoàn thành việc đó trong nhiều trường hợp. Câu hỏi thứ hai luôn luôn là "Ồ, thật tuyệt nhưng tại sao tôi lại muốn làm điều đó?" Câu trả lời là  một khái niệm clean code trước đây mà chúng ra đã học: một làm chỉ nên làm một việc. Khi bạn có các lớp và hàm có câu lệnh `if`. Bạn đang nói với chúng rằng hàm của bạn sẽ thực hiện nhiều hơn một việc

**Bad:**

```php
declare(strict_types=1);

class Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        switch ($this->type) {
            case '777':
                return $this->getMaxAltitude() - $this->getPassengerCount();
            case 'Air Force One':
                return $this->getMaxAltitude();
            case 'Cessna':
                return $this->getMaxAltitude() - $this->getFuelExpenditure();
        }
    }
}
```

**Good:**

```php
declare(strict_types=1);

interface Airplane
{
    // ...

    public function getCruisingAltitude(): int;
}

class Boeing777 implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude() - $this->getPassengerCount();
    }
}

class AirForceOne implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude();
    }
}

class Cessna implements Airplane
{
    // ...

    public function getCruisingAltitude(): int
    {
        return $this->getMaxAltitude() - $this->getFuelExpenditure();
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh kiểm trả kiểu dữ kiệu (part 1)

PHP là ngôn ngữ không ràng buộc kiểu dữ kiệu, có nghĩa là hàm của bạn có thể nhận bất kỳ kiểu tham số nào. Thỉnh thoàng bạn cảm thấy khó chịu bởi sự tự dò này, và nó là điều kiện để ta kiểm tra dữ liệu trong hàm. Có nhiều cách để tranh phải làm vỉevggg
PHP is untyped, which means your functions can take any type of argument.
Sometimes you are bitten by this freedom and it becomes tempting to do
type-checking in your functions. There are many ways to avoid having to do this.
The first thing to consider is consistent APIs.

**Bad:**

```php
declare(strict_types=1);

function travelToTexas($vehicle): void
{
    if ($vehicle instanceof Bicycle) {
        $vehicle->pedalTo(new Location('texas'));
    } elseif ($vehicle instanceof Car) {
        $vehicle->driveTo(new Location('texas'));
    }
}
```

**Good:**

```php
declare(strict_types=1);

function travelToTexas(Vehicle $vehicle): void
{
    $vehicle->travelTo(new Location('texas'));
}
```

**[⬆ back to top](#table-of-contents)**

### Tránh kiểu dữ liệu (part 2)

Nếu bạn đang làm việc với các giá trị nguyên thủy như string, int, array và bạn sử dụng PHP 7+ và bạn không thể sử dụng tính đa hình nhưng bạn vẫn cảm thấy thoải mái thấy cần phải kiểm tra kiểu dữ liệu, bạn nên xem [type declaration](http://php.net/manual/en/functions.arguments.php#functions.arguments.type-declaration)
hoặc strict mode. Nó cung cấp cho bạn kiểu static của PHP syntax. Vấn đề với việc kiểm tra kiểu thủ công là việc thực hiện nó đòi hỏi chi tiết. Hãy giữ cho PHP clean, viết test tốt và review code. Nếu không hãy làm tất cả nhứng điều đó với strict type hoặc strict mode.

**Bad:**

```php
declare(strict_types=1);

function combine($val1, $val2): int
{
    if (! is_numeric($val1) || ! is_numeric($val2)) {
        throw new Exception('Must be of type Number');
    }

    return $val1 + $val2;
}
```

**Good:**

```php
declare(strict_types=1);

function combine(int $val1, int $val2): int
{
    return $val1 + $val2;
}
```

**[⬆ back to top](#table-of-contents)**

### Remove dead code

Dead code is just as bad as duplicate code. There's no reason to keep it in
your codebase. If it's not being called, get rid of it! It will still be safe
in your version history if you still need it.

**Bad:**

```php
declare(strict_types=1);

function oldRequestModule(string $url): void
{
    // ...
}

function newRequestModule(string $url): void
{
    // ...
}

$request = newRequestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**Good:**

```php
declare(strict_types=1);

function requestModule(string $url): void
{
    // ...
}

$request = requestModule($requestUrl);
inventoryTracker('apples', $request, 'www.inventory-awesome.io');
```

**[⬆ back to top](#table-of-contents)**


## Objects and Data Structures

### Use object encapsulation

In PHP you can set `public`, `protected` and `private` keywords for methods.
Using it, you can control properties modification on an object.

* When you want to do more beyond getting an object property, you don't have
to look up and change every accessor in your codebase.
* Makes adding validation simple when doing a `set`.
* Encapsulates the internal representation.
* Easy to add logging and error handling when getting and setting.
* Inheriting this class, you can override default functionality.
* You can lazy load your object's properties, let's say getting it from a
server.

Additionally, this is part of [Open/Closed](#openclosed-principle-ocp) principle.

**Bad:**

```php
declare(strict_types=1);

class BankAccount
{
    public $balance = 1000;
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->balance -= 100;
```

**Good:**

```php
class BankAccount
{
    private $balance;

    public function __construct(int $balance = 1000)
    {
      $this->balance = $balance;
    }

    public function withdraw(int $amount): void
    {
        if ($amount > $this->balance) {
            throw new \Exception('Amount greater than available balance.');
        }

        $this->balance -= $amount;
    }

    public function deposit(int $amount): void
    {
        $this->balance += $amount;
    }

    public function getBalance(): int
    {
        return $this->balance;
    }
}

$bankAccount = new BankAccount();

// Buy shoes...
$bankAccount->withdraw($shoesPrice);

// Get balance
$balance = $bankAccount->getBalance();
```

**[⬆ back to top](#table-of-contents)**

### Make objects have private/protected members

* `public` methods and properties are most dangerous for changes, because some outside code may easily rely on them and you can't control what code relies on them. **Modifications in class are dangerous for all users of class.**
* `protected` modifier are as dangerous as public, because they are available in scope of any child class. This effectively means that difference between public and protected is only in access mechanism, but encapsulation guarantee remains the same. **Modifications in class are dangerous for all descendant classes.**
* `private` modifier guarantees that code is **dangerous to modify only in boundaries of single class** (you are safe for modifications and you won't have [Jenga effect](http://www.urbandictionary.com/define.php?term=Jengaphobia&defid=2494196)).

Therefore, use `private` by default and `public/protected` when you need to provide access for external classes.

For more informations you can read the [blog post](http://fabien.potencier.org/pragmatism-over-theory-protected-vs-private.html) on this topic written by [Fabien Potencier](https://github.com/fabpot).

**Bad:**

```php
declare(strict_types=1);

class Employee
{
    public $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }
}

$employee = new Employee('John Doe');
// Employee name: John Doe
echo 'Employee name: ' . $employee->name;
```

**Good:**

```php
declare(strict_types=1);

class Employee
{
    private $name;

    public function __construct(string $name)
    {
        $this->name = $name;
    }

    public function getName(): string
    {
        return $this->name;
    }
}

$employee = new Employee('John Doe');
// Employee name: John Doe
echo 'Employee name: ' . $employee->getName();
```

**[⬆ back to top](#table-of-contents)**

## Classes

### Prefer composition over inheritance

As stated famously in [*Design Patterns*](https://en.wikipedia.org/wiki/Design_Patterns) by the Gang of Four,
you should prefer composition over inheritance where you can. There are lots of
good reasons to use inheritance and lots of good reasons to use composition.
The main point for this maxim is that if your mind instinctively goes for
inheritance, try to think if composition could model your problem better. In some
cases it can.

You might be wondering then, "when should I use inheritance?" It
depends on your problem at hand, but this is a decent list of when inheritance
makes more sense than composition:

1. Your inheritance represents an "is-a" relationship and not a "has-a"
relationship (Human->Animal vs. User->UserDetails).
2. You can reuse code from the base classes (Humans can move like all animals).
3. You want to make global changes to derived classes by changing a base class.
(Change the caloric expenditure of all animals when they move).

**Bad:**

```php
declare(strict_types=1);

class Employee
{
    private $name;

    private $email;

    public function __construct(string $name, string $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    // ...
}

// Bad because Employees "have" tax data.
// EmployeeTaxData is not a type of Employee

class EmployeeTaxData extends Employee
{
    private $ssn;

    private $salary;

    public function __construct(string $name, string $email, string $ssn, string $salary)
    {
        parent::__construct($name, $email);

        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}
```

**Good:**

```php
declare(strict_types=1);

class EmployeeTaxData
{
    private $ssn;

    private $salary;

    public function __construct(string $ssn, string $salary)
    {
        $this->ssn = $ssn;
        $this->salary = $salary;
    }

    // ...
}

class Employee
{
    private $name;

    private $email;

    private $taxData;

    public function __construct(string $name, string $email)
    {
        $this->name = $name;
        $this->email = $email;
    }

    public function setTaxData(EmployeeTaxData $taxData): void
    {
        $this->taxData = $taxData;
    }

    // ...
}
```

**[⬆ back to top](#table-of-contents)**

### Avoid fluent interfaces

A [Fluent interface](https://en.wikipedia.org/wiki/Fluent_interface) is an object
oriented API that aims to improve the readability of the source code by using
[Method chaining](https://en.wikipedia.org/wiki/Method_chaining).

While there can be some contexts, frequently builder objects, where this
pattern reduces the verbosity of the code (for example the [PHPUnit Mock Builder](https://phpunit.de/manual/current/en/test-doubles.html)
or the [Doctrine Query Builder](http://docs.doctrine-project.org/projects/doctrine-dbal/en/latest/reference/query-builder.html)),
more often it comes at some costs:

1. Breaks [Encapsulation](https://en.wikipedia.org/wiki/Encapsulation_%28object-oriented_programming%29).
2. Breaks [Decorators](https://en.wikipedia.org/wiki/Decorator_pattern).
3. Is harder to [mock](https://en.wikipedia.org/wiki/Mock_object) in a test suite.
4. Makes diffs of commits harder to read.

For more informations you can read the full [blog post](https://ocramius.github.io/blog/fluent-interfaces-are-evil/)
on this topic written by [Marco Pivetta](https://github.com/Ocramius).

**Bad:**

```php
declare(strict_types=1);

class Car
{
    private $make = 'Honda';

    private $model = 'Accord';

    private $color = 'white';

    public function setMake(string $make): self
    {
        $this->make = $make;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setModel(string $model): self
    {
        $this->model = $model;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function setColor(string $color): self
    {
        $this->color = $color;

        // NOTE: Returning this for chaining
        return $this;
    }

    public function dump(): void
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = (new Car())
    ->setColor('pink')
    ->setMake('Ford')
    ->setModel('F-150')
    ->dump();
```

**Good:**

```php
declare(strict_types=1);

class Car
{
    private $make = 'Honda';

    private $model = 'Accord';

    private $color = 'white';

    public function setMake(string $make): void
    {
        $this->make = $make;
    }

    public function setModel(string $model): void
    {
        $this->model = $model;
    }

    public function setColor(string $color): void
    {
        $this->color = $color;
    }

    public function dump(): void
    {
        var_dump($this->make, $this->model, $this->color);
    }
}

$car = new Car();
$car->setColor('pink');
$car->setMake('Ford');
$car->setModel('F-150');
$car->dump();
```

**[⬆ back to top](#table-of-contents)**

### Prefer final classes

The `final` should be used whenever possible:

1. It prevents uncontrolled inheritance chain.
2. It encourages [composition](#prefer-composition-over-inheritance).
3. It encourages the [Single Responsibility Pattern](#single-responsibility-principle-srp).
4. It encourages developers to use your public methods instead of extending the class to get access on protected ones.
5. It allows you to change your code without any break of applications that use your class.

The only condition is that your class should implement an interface and no other public methods are defined.

For more informations you can read [the blog post](https://ocramius.github.io/blog/when-to-declare-classes-final/) on this topic written by [Marco Pivetta (Ocramius)](https://ocramius.github.io/).

**Bad:**

```php
declare(strict_types=1);

final class Car
{
    private $color;

    public function __construct($color)
    {
        $this->color = $color;
    }

    /**
     * @return string The color of the vehicle
     */
    public function getColor()
    {
        return $this->color;
    }
}
```

**Good:**

```php
declare(strict_types=1);

interface Vehicle
{
    /**
     * @return string The color of the vehicle
     */
    public function getColor();
}

final class Car implements Vehicle
{
    private $color;

    public function __construct($color)
    {
        $this->color = $color;
    }

    public function getColor()
    {
        return $this->color;
    }
}
```

**[⬆ back to top](#table-of-contents)**

## SOLID

**SOLID** is the mnemonic acronym introduced by Michael Feathers for the first five principles named by Robert Martin, which meant five basic principles of object-oriented programming and design.

 * [S: Single Responsibility Principle (SRP)](#single-responsibility-principle-srp)
 * [O: Open/Closed Principle (OCP)](#openclosed-principle-ocp)
 * [L: Liskov Substitution Principle (LSP)](#liskov-substitution-principle-lsp)
 * [I: Interface Segregation Principle (ISP)](#interface-segregation-principle-isp)
 * [D: Dependency Inversion Principle (DIP)](#dependency-inversion-principle-dip)

### Single Responsibility Principle (SRP)

As stated in Clean Code, "There should never be more than one reason for a class
to change". It's tempting to jam-pack a class with a lot of functionality, like
when you can only take one suitcase on your flight. The issue with this is
that your class won't be conceptually cohesive and it will give it many reasons
to change. Minimizing the amount of times you need to change a class is important.
It's important because if too much functionality is in one class and you modify a piece of it,
it can be difficult to understand how that will affect other dependent modules in
your codebase.

**Bad:**

```php
declare(strict_types=1);

class UserSettings
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function changeSettings(array $settings): void
    {
        if ($this->verifyCredentials()) {
            // ...
        }
    }

    private function verifyCredentials(): bool
    {
        // ...
    }
}
```

**Good:**

```php
declare(strict_types=1);

class UserAuth
{
    private $user;

    public function __construct(User $user)
    {
        $this->user = $user;
    }

    public function verifyCredentials(): bool
    {
        // ...
    }
}

class UserSettings
{
    private $user;

    private $auth;

    public function __construct(User $user)
    {
        $this->user = $user;
        $this->auth = new UserAuth($user);
    }

    public function changeSettings(array $settings): void
    {
        if ($this->auth->verifyCredentials()) {
            // ...
        }
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Open/Closed Principle (OCP)

As stated by Bertrand Meyer, "software entities (classes, modules, functions,
etc.) should be open for extension, but closed for modification." What does that
mean though? This principle basically states that you should allow users to
add new functionalities without changing existing code.

**Bad:**

```php
declare(strict_types=1);

abstract class Adapter
{
    protected $name;

    public function getName(): string
    {
        return $this->name;
    }
}

class AjaxAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'ajaxAdapter';
    }
}

class NodeAdapter extends Adapter
{
    public function __construct()
    {
        parent::__construct();

        $this->name = 'nodeAdapter';
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): Promise
    {
        $adapterName = $this->adapter->getName();

        if ($adapterName === 'ajaxAdapter') {
            return $this->makeAjaxCall($url);
        } elseif ($adapterName === 'httpNodeAdapter') {
            return $this->makeHttpCall($url);
        }
    }

    private function makeAjaxCall(string $url): Promise
    {
        // request and return promise
    }

    private function makeHttpCall(string $url): Promise
    {
        // request and return promise
    }
}
```

**Good:**

```php
declare(strict_types=1);

interface Adapter
{
    public function request(string $url): Promise;
}

class AjaxAdapter implements Adapter
{
    public function request(string $url): Promise
    {
        // request and return promise
    }
}

class NodeAdapter implements Adapter
{
    public function request(string $url): Promise
    {
        // request and return promise
    }
}

class HttpRequester
{
    private $adapter;

    public function __construct(Adapter $adapter)
    {
        $this->adapter = $adapter;
    }

    public function fetch(string $url): Promise
    {
        return $this->adapter->request($url);
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Liskov Substitution Principle (LSP)

This is a scary term for a very simple concept. It's formally defined as "If S
is a subtype of T, then objects of type T may be replaced with objects of type S
(i.e., objects of type S may substitute objects of type T) without altering any
of the desirable properties of that program (correctness, task performed,
etc.)." That's an even scarier definition.

The best explanation for this is if you have a parent class and a child class,
then the base class and child class can be used interchangeably without getting
incorrect results. This might still be confusing, so let's take a look at the
classic Square-Rectangle example. Mathematically, a square is a rectangle, but
if you model it using the "is-a" relationship via inheritance, you quickly
get into trouble.

**Bad:**

```php
declare(strict_types=1);

class Rectangle
{
    protected $width = 0;

    protected $height = 0;

    public function setWidth(int $width): void
    {
        $this->width = $width;
    }

    public function setHeight(int $height): void
    {
        $this->height = $height;
    }

    public function getArea(): int
    {
        return $this->width * $this->height;
    }
}

class Square extends Rectangle
{
    public function setWidth(int $width): void
    {
        $this->width = $this->height = $width;
    }

    public function setHeight(int $height): void
    {
        $this->width = $this->height = $height;
    }
}

function printArea(Rectangle $rectangle): void
{
    $rectangle->setWidth(4);
    $rectangle->setHeight(5);

    // BAD: Will return 25 for Square. Should be 20.
    echo sprintf('%s has area %d.', get_class($rectangle), $rectangle->getArea()) . PHP_EOL;
}

$rectangles = [new Rectangle(), new Square()];

foreach ($rectangles as $rectangle) {
    printArea($rectangle);
}
```

**Good:**

The best way is separate the quadrangles and allocation of a more general subtype for both shapes.

Despite the apparent similarity of the square and the rectangle, they are different.
A square has much in common with a rhombus, and a rectangle with a parallelogram, but they are not subtype.
A square, a rectangle, a rhombus and a parallelogram are separate shapes with their own properties, albeit similar.

```php
interface Shape
{
    public function getArea(): int;
}

class Rectangle implements Shape
{
    private $width = 0;
    private $height = 0;

    public function __construct(int $width, int $height)
    {
        $this->width = $width;
        $this->height = $height;
    }

    public function getArea(): int
    {
        return $this->width * $this->height;
    }
}

class Square implements Shape
{
    private $length = 0;

    public function __construct(int $length)
    {
        $this->length = $length;
    }

    public function getArea(): int
    {
        return $this->length ** 2;
    }
}

function printArea(Shape $shape): void
{
    echo sprintf('%s has area %d.', get_class($shape), $shape->getArea()).PHP_EOL;
}

$shapes = [new Rectangle(4, 5), new Square(5)];

foreach ($shapes as $shape) {
    printArea($shape);
}
```

**[⬆ back to top](#table-of-contents)**

### Interface Segregation Principle (ISP)

ISP states that "Clients should not be forced to depend upon interfaces that
they do not use."

A good example to look at that demonstrates this principle is for
classes that require large settings objects. Not requiring clients to set up
huge amounts of options is beneficial, because most of the time they won't need
all of the settings. Making them optional helps prevent having a "fat interface".

**Bad:**

```php
declare(strict_types=1);

interface Employee
{
    public function work(): void;

    public function eat(): void;
}

class HumanEmployee implements Employee
{
    public function work(): void
    {
        // ....working
    }

    public function eat(): void
    {
        // ...... eating in lunch break
    }
}

class RobotEmployee implements Employee
{
    public function work(): void
    {
        //.... working much more
    }

    public function eat(): void
    {
        //.... robot can't eat, but it must implement this method
    }
}
```

**Good:**

Not every worker is an employee, but every employee is a worker.

```php
declare(strict_types=1);

interface Workable
{
    public function work(): void;
}

interface Feedable
{
    public function eat(): void;
}

interface Employee extends Feedable, Workable
{
}

class HumanEmployee implements Employee
{
    public function work(): void
    {
        // ....working
    }

    public function eat(): void
    {
        //.... eating in lunch break
    }
}

// robot can only work
class RobotEmployee implements Workable
{
    public function work(): void
    {
        // ....working
    }
}
```

**[⬆ back to top](#table-of-contents)**

### Dependency Inversion Principle (DIP)

This principle states two essential things:
1. High-level modules should not depend on low-level modules. Both should
depend on abstractions.
2. Abstractions should not depend upon details. Details should depend on
abstractions.

This can be hard to understand at first, but if you've worked with PHP frameworks (like Symfony), you've seen an implementation of this principle in the form of Dependency
Injection (DI). While they are not identical concepts, DIP keeps high-level
modules from knowing the details of its low-level modules and setting them up.
It can accomplish this through DI. A huge benefit of this is that it reduces
the coupling between modules. Coupling is a very bad development pattern because
it makes your code hard to refactor.

**Bad:**

```php
declare(strict_types=1);

class Employee
{
    public function work(): void
    {
        // ....working
    }
}

class Robot extends Employee
{
    public function work(): void
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage(): void
    {
        $this->employee->work();
    }
}
```

**Good:**

```php
declare(strict_types=1);

interface Employee
{
    public function work(): void;
}

class Human implements Employee
{
    public function work(): void
    {
        // ....working
    }
}

class Robot implements Employee
{
    public function work(): void
    {
        //.... working much more
    }
}

class Manager
{
    private $employee;

    public function __construct(Employee $employee)
    {
        $this->employee = $employee;
    }

    public function manage(): void
    {
        $this->employee->work();
    }
}
```

**[⬆ back to top](#table-of-contents)**

## Don’t repeat yourself (DRY)

Try to observe the [DRY](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) principle.

Do your absolute best to avoid duplicate code. Duplicate code is bad because
it means that there's more than one place to alter something if you need to
change some logic.

Imagine if you run a restaurant and you keep track of your inventory: all your
tomatoes, onions, garlic, spices, etc. If you have multiple lists that
you keep this on, then all have to be updated when you serve a dish with
tomatoes in them. If you only have one list, there's only one place to update!

Often you have duplicate code because you have two or more slightly
different things, that share a lot in common, but their differences force you
to have two or more separate functions that do much of the same things. Removing
duplicate code means creating an abstraction that can handle this set of different
things with just one function/module/class.

Getting the abstraction right is critical, that's why you should follow the
SOLID principles laid out in the [Classes](#classes) section. Bad abstractions can be
worse than duplicate code, so be careful! Having said this, if you can make
a good abstraction, do it! Don't repeat yourself, otherwise you'll find yourself
updating multiple places any time you want to change one thing.

**Bad:**

```php
declare(strict_types=1);

function showDeveloperList(array $developers): void
{
    foreach ($developers as $developer) {
        $expectedSalary = $developer->calculateExpectedSalary();
        $experience = $developer->getExperience();
        $githubLink = $developer->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}

function showManagerList(array $managers): void
{
    foreach ($managers as $manager) {
        $expectedSalary = $manager->calculateExpectedSalary();
        $experience = $manager->getExperience();
        $githubLink = $manager->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}
```

**Good:**

```php
declare(strict_types=1);

function showList(array $employees): void
{
    foreach ($employees as $employee) {
        $expectedSalary = $employee->calculateExpectedSalary();
        $experience = $employee->getExperience();
        $githubLink = $employee->getGithubLink();
        $data = [$expectedSalary, $experience, $githubLink];

        render($data);
    }
}
```

**Very good:**

It is better to use a compact version of the code.

```php
declare(strict_types=1);

function showList(array $employees): void
{
    foreach ($employees as $employee) {
        render([$employee->calculateExpectedSalary(), $employee->getExperience(), $employee->getGithubLink()]);
    }
}
```

**[⬆ back to top](#table-of-contents)**

## Translations

This is also available in other languages:

* :cn: **Chinese:**
   * [php-cpm/clean-code-php](https://github.com/php-cpm/clean-code-php)
* :ru: **Russian:**
   * [peter-gribanov/clean-code-php](https://github.com/peter-gribanov/clean-code-php)
* :es: **Spanish:**
   * [fikoborquez/clean-code-php](https://github.com/fikoborquez/clean-code-php)
* :brazil: **Portuguese:**
   * [fabioars/clean-code-php](https://github.com/fabioars/clean-code-php)
   * [jeanjar/clean-code-php](https://github.com/jeanjar/clean-code-php/tree/pt-br)
* :thailand: **Thai:**
   * [panuwizzle/clean-code-php](https://github.com/panuwizzle/clean-code-php)
* :fr: **French:**
   * [errorname/clean-code-php](https://github.com/errorname/clean-code-php)
* :vietnam: **Vietnamese**
   * [viethuongdev/clean-code-php](https://github.com/viethuongdev/clean-code-php)
* :kr: **Korean:**
   * [yujineeee/clean-code-php](https://github.com/yujineeee/clean-code-php)
* :tr: **Turkish:**
   * [anilozmen/clean-code-php](https://github.com/anilozmen/clean-code-php)

**[⬆ back to top](#table-of-contents)**
