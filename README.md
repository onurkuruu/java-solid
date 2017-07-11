# Java ve SOLID

**SOLID** kuralları anlaşılır, bakımı kolay ve genişletilebilir kodlar yazmamızı sağlayan kurallardır. Bağımlılıkların yönetilmesini ve birbirine **gevşek bağlı(loose coupling)** sınıflar üretmemizi sağlar. Bunları kullanmayabiliriz fakat kullanmazsak zaman içerisinde kodlarımızın çok kötü bir hal aldığını gözlemleyebiliriz.

SOLID kuralları:
* **Single Responsiblity Princible (Tek Sorumluluk Kuralı)**
* **Open-Closed Princible (Açıklık - Kapalılık Kuralı)**
* **Liskov’s Substitution Princible (Liskov’un Yer değiştirme Kuralı)**
* **Interface Segregation Princible (Interface Parçalama Kuralı)**
* **Dependecy Inversion Princible (Bağımlılıkları Tersine Çevirme Kuralı)**

### Single Responsiblity Princible 

Bu kural sınıflarımızın, metotlarımızın ya da modüllerimizin tek sorumluluğu olması gerektiğini söylüyor. Yazdığımız servis sınıfları, metotlar vs. yazılım birimlerinin tek sorumluluğu olmalıdır. 

Örneğin çarpma ve bölme işlemlerini aynı fonksiyon içerisinde yapıyorsak bu fonksiyonu parçalayarak sadece çarpma işlemini ve sadece bölme işlemini  yapan fonksiyonlar oluşturmalıyız. Bu durum kod tekrarını önleyerek kodun tekrar kullanılabilirliğini arttırır.

### Open Closed Princible 

Sınıflarımız modüllerimiz ve fonksiyonlarımız geliştirmeye açık değişime kapalı olmalıdır.

Örneğin **Spring Framework’ünü** ele alırsak, bu frameworkün temel işlevlerini ve mantığını değiştiremeyiz fakat framework içerisindeki sınıfları kalıtım alarak yapıyı genişletebiliriz. Örneğin **Spring Security’nin** varsayılan oturum oluşturma ve oturum yönetme servislerini kullanabileceğimiz gibi bu sınıfları kalıtım alarak oturum oluşturma ve yönetme işlemlerini kendimizde yapabiliriz.

###  Liskov’s Substitution Princible

**OOP’nin** temeli olan bu kural bize oluşturduğumuz alt sınıf nesnelerini üst sınıfların nesneleri ile yer değiştirirsek aynı davranışı sergilemeleri gerektiğini söylüyor. Örnekleyecek olursak **Square(kare)** ve **Rectangle(dikdörtgen)** sınıflarımız olsun ve **“Square is a Rectangle”** ilişkisi kuralım yani Square sınıfını Rectangle sınıfından kalıtım alalım. Kodlarımız şu şekilde olacaktır.

```java

class Rectangle {

    private int width;
    private int height;

    public int getWidth() {
        return width;
    }

    public void setWidth(int width) {
        this.width = width;
    }

    public int getHeight() {
        return height;
    }

    public void setHeight(int height) {
        this.height = height;
    }

    public int getArea() {
        return this.height * this.width;
    }
}

class Square extends Rectangle {

    //Karenin tüm kenarları aynı değerde olması gerektiği için böyle bir yöntem kullanabiliriz.
    @Override
    public void setWidth(int width) {
        super.setHeight(width);
        super.setWidth(width);
    }

    @Override
    public void setHeight(int height) {
        super.setHeight(height);
        super.setWidth(height);
    }
}


```

Buraya kadar kare bir dikdörtgenmiş gibi duruyor fakat kodumuzu test edecek olursak olayları daha ayrıntılı görebiliriz.

```java
class Test {

    public static void main(String args[]) {
        System.out.println(calculateArea(new Rectangle()));
        System.out.println(calculateArea(new Square()));
    }

    static int calculateArea(Rectangle rectangle) {

        rectangle.setHeight(2);
        rectangle.setWidth(4);

        return rectangle.getArea();
    }
}

```

Test kodumuzu çalıştırdığımızda çıktımız **8** ve **16** şeklinde olmaktadır. Burada dikdörtgen sınıfı için bir sıkıntımız yok fakat kare sınıfı için aynı şeyi söyleyemeyiz.

**calculateArea** fonksiyonu içerisinde sırasıyla yükseklik değerini **2**, genişlik değerini **4** atıyoruz ve Square sınıfı içerisinde **override** ettiğimiz set metotları gereği yükseklik ve genişlik değerleri **4** oluyor. Bu yüzden bize dönen sonuç **16** oluyor fakat kural gereği çıktımız her iki nesne için aynı değeri yani ya **8** ya da **16** değerini üretmelidir. **Liskov’un yer değiştirme kuralı** bizden bunu istiyor. calculateArea fonksiyonuna Rectangle nesnesini veya Square nesnesini verdiğimizde bize dönen sonuç aynı olmalıdır. Bu yüzden sınıflar arasında kalıtım özelliğini kullanırken tekrar düşünmeli ve Square sınıfında override ettiğimiz set metotları gibi hataya sebep olacak yapılardan kaçınmalıyız.

### Interface Segregation Princible

Bu özellik tek sorumluluk kuralına benzer şeyler söylemektedir. İçinde her şeyi barındıran interfaceler yerine belli amaca hizmet eden interfaceler üretmeliyiz. Eğer interface parçalama işini iyi yaparsak gereksiz fonksiyonların sınıflar tarafından zorla uygulanmasını önlemiş oluruz.

### Dependency Inversion Principle

Bu kural bize bağımlılıkların soyut yapılara doğru olması gerektiğini söylüyor. Nesneleri direkt olarak birbirine sıkı sıkıya bağlamak yerine bağımlılıkları soyut sınıflara doğru yaparak nesnelerin birbirine **gevşek bağlı(loose coupling)** olması sağlanır.

Sıkça kullanılan **IoC, Dependency Injection ve Dependency Inversion** terimleri aralarında doğrudan ilişki vardır.

**IoC (Inversion of Control)**: Kontrolün ters çevrilmesi, kontrolden vazgeçme gibi bir türkçesi vardır. Nesnelerin yaşam döngülerinin (oluşturulma, aktif olma, pasif olma vb.) bizim kontrolümüzden alınarak frameworklere devredilmesidir. Örneğin **Spring Framework** ile uygulama geliştirdiğimizde oluşturduğumuz **beanlerin** oluşturulması ve ya yok edilmesiyle biz ilgilenmiyoruz. Biz sadece o beani kullanarak bir metot çağırıyoruz.

**Dependency Injection**: Bağımlılıkları yükleme anlamına gelmektedir. Bu kavram **Dependency Inversion** ile doğrudan alakalıdır. **Dependency Inversion** bir prensiptir, **Dependecy Injection** ise bu prensibi gerçekleştirmenin bir yoludur.

Dependency Injection kullanmazsak muhtemelen kodlarımız şu şekilde olacaktır.

```java
 class PDFWriter {
    public void print() {
        System.out.println("PDF Writer");
    }
}

class Writer {

    public PDFWriter pdfWriter;

    Writer() {
        pdfWriter = new PDFWriter();
    }
}
```

Burada **Writer** sınıfımız şuan için sadece **PDF** için çıktı veriyor. Çünkü **PDFWriter** sınıfına doğrudan bağlı. Bu bize genişletilebilir bir yapı sağlamaz. Biz PDF yerine artık **Excel** çıktısı kullanmak istesek, alt sınıflarda yaptığımız ilgili değişiklikler üst sınıfları da etkileyecek. Şu an için sadece etkilenen bir kaç sınıfımız var ama proje genişledikçe bu sayı yüzlere çıkabilir. Biz hepsi için bu değişikliği uygulamalıyız. Bunu önlemek için bağımlılıkları dışarıdan yükleyebiliriz. Bu yöntemin adı **dependency injectiondır**.

Bağımlılıkları 3 farklı yöntem ile yükleyebiliriz:
* **Constructor injection**: Bağımlılık yükleme işleminin kurucu fonksiyonda yapıldığı yöntem.
* **Setter injection**: Bağımlılık yükleme işleminin set fonksiyonlarında yapıldığı yöntem.
* **Interface injection**: Bağımlılıkların interfaceler yardımıyla yüklendiği yöntem.

#### Constructor Injection

Bağımlılıklarımızı kurucu fonksiyona gönderdiğimiz parametre yardımıyla yüklüyoruz. Bir önceki kodda bazı değişiklikler yaparak bir örnek verelim.

```java
interface IWriter {
    void print();
}

class PDFWriter implements IWriter {

    @Override
    public void print() {
        System.out.println("PDF Writer");
    }
}

class ExcelWriter implements IWriter {
    @Override
    public void print() {
        System.out.println("Excel Writer");
    }
}

class Writer {

    public IWriter iWriter;

    Writer(IWriter iWriter) {
        this.iWriter = iWriter;
    }
}
```

**IWriter** interfacesini ekledik ve **PDFWriter** ve **ExcelWriter** sınıflarında bu interfaceyi implemente ettik. **Writer** sınıfında ise direkt olarak **PDFWriter** sınıfından nesne oluşturmak yerine, bağımlılıkları soyut birimlere doğru yaparak IWriter interfacesinden bir referans oluşturduk. **OOP’nin polymorphism** özelliği sayesinde kurucu fonksiyona PDFWriter nesnesi gönderirsek interface referansı o nesneyi işaret edecektir.

Alt sınıflarda ise Excel ya da PDF çıktısı almak için şunları yapmamız yeterlidir.
```java
Writer writer = new Writer(new ExcelWriter());
```
ya da 

```java
Writer writer = new Writer(new PDFWriter());
```
 Alt sınıflarda yaptığımız bu değişiklik üst sınıfları hiç bir şekilde etkilemiyor ve biz yüzlerce yerde değişiklik yapmaktan kurtuluyoruz. Eğer ilerde bizden birde **Word** çıktısı isteyecek olursa yapmamız gereken **Word** çıktısı verebilen bir sınıf oluşturup writer nesnemizin bağımlılığını Word sınıf nesnesi olarak yüklemektir.

```java
class WordWriter implements IWriter {

    @Override
    public void print() {
        System.out.println("Word Writer");
    }
}
```
ve

```java
Writer writer = new Writer(new WordWriter());
```
bu değişiklikleri yapmamız yeterlidir.

#### Setter Injection

Biraz önceki yapıda **IWriter** bağımlılığımızı kurucu metot yerine set metodunda yükleyeceğiz. Önceki yöntemden avantaj olarak her defasında yeni bir nesne oluşturma gereksinimi yerine bir nesne oluşturup set fonksiyonunu kullanarak değişik çıktılar üretmesi sağlanabilir.
 
```java
class Writer {

    private IWriter iWriter;

    public IWriter getiWriter() {
        return iWriter;
    }

    public void setiWriter(IWriter iWriter) {
        this.iWriter = iWriter;
    }
}
```

yükleme yapacağımız kısım ise şu şekilde olacaktır.

```java
Writer writer = new Writer();
writer.setWriter(new PDFWriter());
```

#### Interface Injection

Bu yöntemde bağımlı nesneleri yüklemek için interfacelerden yardım alınır. Bağımlılıkları yüklemek için bir interface oluşturalım.

```java
public interface ServiceSetter {
    public void setService(Service service);
}
```
Artık bağımlılıkları bu arayüz yardımıyla yükleyeceğiz. Bu arada **Service** bir interface ve bu interfaceyi implemente eden değişik servis sınıfları vardır. Örneğin **ListerService, FinderService** vs.

```java
public class Client implements ServiceSetter {
    private Service service;

    @Override
    public void setService(Service service) {
        this.service = service;
    }
}
```
yükleme yapacağımız kısım ise şöyle olmalıdır.

```java

Service service = new ListerService();
Client client = new Client();
client.setService(service);

```

Tüm bu yöntemlerin amacı sınıfların birbirine sıkı bağlanmasını önlemektir. Bu sayede genişletilebilir yapılar kurularak, alt sınıf değişliklerinin üst sınıflara yansıması önlenmiş olur. Geliştiricileri arka plan işlerinden soyutlar ve geliştirici modülü genişletmek isterse ona arayüzler sunarak buna imkan verir.



   
