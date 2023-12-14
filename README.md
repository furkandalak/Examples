# Examples

priority inversion 

Race Condition 

Fiber thread? C# içinde doğrudan kullanılan bir terim değil. Daha hafif iş parçalarına verilen isim. .NET Core 2.1 sonrasında BoundedCapacity (System.Threading.Tasks.DataFlow) konsepti var. 

## Thread Lock 
Ortak kaynağa aynı anda birden fazla iş parçasının erişimin engellemeye yarayan mekanizmadır. 

Eş zamanlılık sorunlarını, Race Condition’ları ve veri bütünlüğü sorunlarını engellemeye yardımcı olur. 

* Locking: İş parçacığı belirli bir kaynağa erişim sağlarken locklamaya çalışır 
* Lock Check: O kaynağa erişilemiyorsa iş parçacığı bu kaynağı kullanabilir veya kilitleyebilir 
* Lock Wait: Eğer kaynağa zaten bir erişim varsa kilit açılana kadar bekler 
* Lock Release: İş parçacığı kullanımı tamamladığında serbest bırakır 

Microsoft 

Locking Example (Detailed) 
 

Thread Pool 
Sistem tarafından kullanılacak olan işlemlerin daha etkili yönetilmesi ve kullanılması için tasarlanmış bir mekanizmadır.  

Bu sistem belirli ve sınırlı sayıda iş parçası oluşturur.  

İlk oluşturmada belirli sayıda iş parçası oluşturur 

İş parçası istendiğinde pooldan mevcut boş iş parçacığı atanır, yoksa oluşturulur 

İş parçacığının görevi tamamlandığında poola geri gönderilir.  

Sınırlı sayıda iş parçacığı tutar ve fazladan iş parçacığı oluşturma önüne geçer 

Avantajlar: 

Performans: İş parçacığının oluşturma ve sonlandırma maaliyetini azaltır. 

Kaynak Yönetimi: İş parçacıkları etkili yönetilebilir ve kaynakları verimli kullanır. 

Asenkron: Task-Based Async/Await operasyonlarını destekler. 

Kuyruk: Kuyruk sistemi içinde düzenler, belirli sırayla önceliklendirme yapılabilir 

 

Wikipedia (Thread Pool) (Embarrassingly Parallel) 
Microsoft 
Örnek 

 

 

Asenkron (Asynchronous) 
İşlemin başka bir işlemin tamamlanmasını beklemeden devam edebilmesi yeteneği 
 
Bir işlemin sonucu beklenirken başka bir işlem devam edebilir. Örn.: Uzun süreli işlemler, dosya okuma, ağ çağrıları/bağlantıları beklenirken program başka yanıtlar verebilir. 
 
Await 
Asenkron görevin tamamlanmasını bekletir. 

 
