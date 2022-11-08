# DomCrawler 

## Docs
> Cài đặt 
```sh
  composer require symfony/dom-crawler
  composer require symfony/css-selector
```

> Xem node chính 
```sh
use Symfony\Component\DomCrawler\Crawler;

$html = <<<'HTML'
<!DOCTYPE html>
<html>
    <body>
        <p class="message">Hello World!</p>
        <p>Hello Crawler!</p>
    </body>
</html>
HTML;

$crawler = new Crawler($html);

foreach ($crawler as $domElement) {
    var_dump($domElement->nodeName);
}
```

> Lọc node 
```sh 
    $crawler = $crawler->filterXPath('descendant-or-self::body/p');$crawler = $crawler->filterXPath('descendant-or-self::body/p');$crawler = $crawler->filterXPath('descendant-or-self::body/p');
    
    // Cssselector componentt 
    $crawler = $crawler->filter('body > p');
```
```sh 
// Lọc phức tạp hơn 
  $crawler = $crawler
    ->filter('body > p')
    ->reduce(function (Crawler $node, $i) {
        // filters every other node
        return $i == 0;
    });
```

>XML namespaces
```sh 
 <?xml version="1.0" encoding="UTF-8" ?>
<entry
    xmlns="http://www.w3.org/2005/Atom"
    xmlns:media="http://search.yahoo.com/mrss/"
    xmlns:yt="http://gdata.youtube.com/schemas/2007"
>
    <id>tag:youtube.com,2008:video:kgZRZmEc9j4</id>
    <yt:accessControl action="comment" permission="allowed"/>
    <yt:accessControl action="videoRespond" permission="moderated"/>
    <media:group>
        <media:title type="plain">Chordates - CrashCourse Biology #24</media:title>
        <yt:aspectRatio>widescreen</yt:aspectRatio>
    </media:group>
</entry>
```
```sh
    $crawler = $crawler->filterXPath('//default:entry/media:group//yt:aspectRatio');
    $crawler = $crawler->filter('default|entry media|group yt|aspectRatio');
  
  
    // Đăng ký tên namspce 
    $crawler->registerNamespace('m', 'http://search.yahoo.com/mrss/');
    $crawler = $crawler->filterXPath('//m:group//yt:aspectRatio');
```

> Kiểm tra xem node hiện tại có khớp không 
```sh
  $crawler->matches('div');
```

> Lấy các node 
```sh
  $crawler->eq(Vị trí các node trong array node);
  $crawler->first(); // Lấy ra node ở vị trí đầu tiên
  $crawler->last(); // Lấy ra node ỏ vị trí thứ 2 
  $crawler->filter('body>div>a')->siblings() // Chỉ lấy các node ngoài node được chọn hiện tại cùng cấp 
  $crawler->filter('body>div>a')->nextAll() // Chỉ lấy các node sau node được chọn hiện tại cùng cấp 
  $crawler->filter('body>div>a')->previousAll() // Chỉ lấy các node trước node được chọn hiện tại cùng cấp 
  $crawler->filter('body>div>a')->children() // Chỉ lấy các node con 
  $crawler->filter('body>div>a')->ancestors() // Chỉ lấy các node cha 
  $crawler->filter('body>div>a')->closest('body') // Lấy các node gần nhất 
  
```

> Truy cập giá trị 
```sh
  $crawler->first()->nodeName() // In ra tên của node hiện tại 
  $crawler->first()->text('Text default nếu node không tồn tại' , bool khoảng trằng ) // In ra text của node hiện tại 
  $crawler->first()->innerText() // In ra text của node hiện tại không bao gồm node con như text()
  $crawler->first()->extract(['_name','class','id']) // Lấy ra các giá trị trong mảng attr của node hiện tại 
  $crawler->first()->attr('class') // Lấy ra các giá trị  attr của node hiện tại 
    
  $crawler = $crawler->filter('body>div>a')->each(function ($node,$i) {
        return $node->text();
    });  // Lấy ra mảng các text của các node 
   
```


> Truy cập con của each của filterXPath
```sh
  $crawler = $crawler->filter('body>div>a')->each(function ($node,$i) {
        // Sẽ không thể tru cập 
        $subCrawler = $node->filterXPath('a');
        
        //Truy cập tốt 
        $subCrawler = $node->filterXPath('node()/a');
        $subCrawler = $node->filterXPath('div/a');
        return $node->text();
    });
   
```


> Thêm nội dung 
```sh
    $crawler = new Crawler('<html><body/></html>');

    $crawler->addHtmlContent('<html><body/></html>');
    $crawler->addXmlContent('<root><node/></root>');
    
    $crawler->addContent('<html><body/></html>');
    $crawler->addContent('<root><node/></root>', 'text/xml');
    
    $crawler->add('<html><body/></html>');
    $crawler->add('<root><node/></root>');
```




> Tương tác với các DomDocument , DomNodelist , DomNode
```sh
    $domDocument = new \DOMDocument();
    $domDocument->loadXml('<root><node/><node/></root>');
    $nodeList = $domDocument->getElementsByTagName('node');
    $node = $domDocument->getElementsByTagName('node')->item(0);
    
    $crawler->addDocument($domDocument);
    $crawler->addNodeList($nodeList);
    $crawler->addNodes([$node]);
    $crawler->addNode($node);
    $crawler->add($domDocument);
```

> Thu thập thông tin 
```sh
 $html = '';

foreach ($crawler as $domElement) {
    $html .= $domElement->ownerDocument->saveHTML($domElement);
}

// or 
$html = $crawler->html();
$html = $crawler->outerHtml();
```
 
```sh
    // Tìm các thẻ span có id tồn tại string 'article-'
    // Và trả về một mảng với giá trị của id sau string "-"
    $data = $crawler->filterXPath('//span[contains(@id, "article-")]')
        ->evaluate('substring-after(@id, "-")');
        
    // Tìm cái đầu tiên do không có filterXpath     
    $crawler->evaluate('substring-after(//span[contains(@id, "article-")]/@id, "-")');
    
    // Trả về tổng các thẻ span có class là "article"
    $crawler->evaluate('count(//span[@class="article"])');
```


> Link 

```sh
    // Khởi tạo một node link 
    $link = $crawler->link();
    //or
    $link = $crawler->selectLink('Log in')->link();
    
    // Lấy url của link đó 
    $uri = $link->getUri();
```


> Hình ảnh 

```sh
    // Khởi tạo một node ảnh  
    $link = $crawler->image();
    //or
    $link = $crawler->selectImage('Image')->image();
    
    // Lấy url của link đó 
    $uri = $link->getUri();
```



> Form 

```sh
    // Khởi tạo một node form 
    $crawler->selectButton('My button')->form();
    
    // or 
    // Khởi tao bằng class form 
    $crawler->filter('.form-vertical')->form();
    // Trường form có thể nhập dữ liệu 
    form(['name' => 'Taylor','email' => 'taylor@example.xml']) 

    // Get uri 
    $form->getUri();
    // Get method 
    $form->getMethod();
    // Get name
    $form->getName();
    
    // Lưu nhiều giá trị string , ....
    $form->setValues([
    
    ])
    
    // Lấy giá trị mảng phẳng 
    $form->getValues() 
    
    // Lấy giá trị format php 
    $form->getPhpValues();
    
    // Tich check box 
    $form['checkbox']->tich()
    $form['checkbox']->untich()
    
    // Select option và nhiều option 
    $form['select']->select(value or array[])
    
    // File upload 
    $form['file']->upload('path')

    // Get value php 
    $values = $form->getPhpValues();
    // Get file php 
    $values = $form->getPhpFiles();
    
    // Như thường 
    $values = $form->getValues();
    $files = $form->getFiles();
```





























