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


### Locking Örneği
```
private object lockObject = new object();

public void ExampleMethod()
{
    // Kilit alma işlemi
    lock (lockObject)
    {
        // Kritik bölgeye ait işlemler
    }
    // Kilit serbest bırakma işlemi
}
```

[Detaylı Örnek](https://github.com/furkandalak/Examples/blob/main/Locking%20Example)
[Microsoft](https://learn.microsoft.com/en-us/dotnet/csharp/language-reference/statements/lock#example)

## Thread Pool 
Sistem tarafından kullanılacak olan işlemlerin daha etkili yönetilmesi ve kullanılması için tasarlanmış bir mekanizmadır.  

Bu sistem belirli ve sınırlı sayıda iş parçası oluşturur  
1. İlk oluşturmada belirli sayıda iş parçası oluşturur 
2. İş parçası istendiğinde pooldan mevcut boş iş parçacığı atanır, yoksa oluşturulur 
3. İş parçacığının görevi tamamlandığında poola geri gönderilir  
4. Sınırlı sayıda iş parçacığı tutar ve fazladan iş parçacığı oluşturma önüne geçer 

Avantajlar: 

- Performans: İş parçacığının oluşturma ve sonlandırma maaliyetini azaltır. 
- Kaynak Yönetimi: İş parçacıkları etkili yönetilebilir ve kaynakları verimli kullanır. 
- Asenkron: Task-Based Async/Await operasyonlarını destekler. 
- Kuyruk: Kuyruk sistemi içinde düzenler, belirli sırayla önceliklendirme yapılabilir 

Wikipedia [Thread Pool](https://en.wikipedia.org/wiki/Thread_pool#:~:text=In%20computer%20programming%2C%20a%20thread,execution%20by%20the%20supervising%20program.) [Embarrassingly Parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel)
[Microsoft](https://learn.microsoft.com/tr-tr/dotnet/api/system.threading.threadpool?view=net-8.0#examples)
[Örnek](https://github.com/furkandalak/Examples/blob/main/Thread%20Pool)

 

 

Asenkron (Asynchronous) 
İşlemin başka bir işlemin tamamlanmasını beklemeden devam edebilmesi yeteneği 
 
Bir işlemin sonucu beklenirken başka bir işlem devam edebilir. Örn.: Uzun süreli işlemler, dosya okuma, ağ çağrıları/bağlantıları beklenirken program başka yanıtlar verebilir. 
 
Await 
Asenkron görevin tamamlanmasını bekletir. 

 
