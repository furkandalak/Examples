# Examples
- [ ] Semaphore
- [ ] Mutex
- [x] Thread Pool
- [x] Fiber Thread
- [x] Thread/Task.Run()
- [x] Async Wait
- [x] Thread Lock


priority inversion 


## Fiber Thread 
C# içinde doğrudan kullanılan bir terim değil. Daha hafif iş parçalarına verilen isim. .NET Core 2.1 sonrasında 
```BoundedCapacity (System.Threading.Tasks.DataFlow)``` konsepti var. 

[Microsoft](https://learn.microsoft.com/en-us/windows/win32/procthread/fibers)

## Thread Lock 
Ortak kaynağa aynı anda birden fazla iş parçasının erişimin engellemeye yarayan mekanizmadır. 

Eş zamanlılık sorunlarını, Race Condition’ları ve veri bütünlüğü sorunlarını engellemeye yardımcı olur. 

* Locking: İş parçacığı belirli bir kaynağa erişim sağlarken locklamaya çalışır 
* Lock Check: O kaynağa erişilemiyorsa iş parçacığı bu kaynağı kullanabilir veya kilitleyebilir 
* Lock Wait: Eğer kaynağa zaten bir erişim varsa kilit açılana kadar bekler 
* Lock Release: İş parçacığı kullanımı tamamladığında serbest bırakır 

### Race Condition
Birden fazla iş parcacığının aynı anda bir kaynağa erişmeye çalıştığı durumu ifade eder. Bu durum kontrol edilmeze veri bütünlüğü sorunları ortaya çıkabilir.

Wikipedia [Örnek](https://en.wikipedia.org/wiki/Race_condition#Example)

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

![](https://github.com/furkandalak/Examples/blob/main/400px-Thread_pool.svg.png)

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

Wikipedia [Thread Pool](https://en.wikipedia.org/wiki/Thread_pool#:~:text=In%20computer%20programming%2C%20a%20thread,execution%20by%20the%20supervising%20program.), [Embarrassingly Parallel](https://en.wikipedia.org/wiki/Embarrassingly_parallel)

[Microsoft](https://learn.microsoft.com/tr-tr/dotnet/api/system.threading.threadpool?view=net-8.0#examples)

[Örnek](https://github.com/furkandalak/Examples/blob/main/Thread%20Pool) 

## Asenkron (Asynchronous) 
İşlemin başka bir işlemin tamamlanmasını beklemeden devam edebilmesi yeteneği 
 
Bir işlemin sonucu beklenirken başka bir işlem devam edebilir. Örn.: Uzun süreli işlemler, dosya okuma, ağ çağrıları/bağlantıları beklenirken program başka yanıtlar verebilir. 
 
### Await 
Asenkron görevin tamamlanmasını bekletir. 
```
async Task<int> LongRunningOperationAsync()
{
    // Uzun süren bir işlem simülasyonu
    await Task.Delay(2000);
    
    return 42;
}

async void MyAsyncMethod()
{
    Console.WriteLine("İşlem başlıyor...");

    int result = await LongRunningOperationAsync();

    Console.WriteLine("İşlem tamamlandı. Sonuç: " + result);
}
```

## Task.Run()
C#'da Task Parallel Library ([TPL](https://learn.microsoft.com/en-us/dotnet/standard/parallel-programming/task-parallel-library-tpl)) kapsamında sunulan bir yöntemdir.

```
Task.Run(() =>
{
    // Asenkron çalışacak kod bloğu
});
```

- Asenkron Olay Başlatma: İçine aldığı metodu asenkron olarak başlatır ve bir "Task" döndürür.
- Task Döndürme: Bir "Task" döndürür ve bu sayede operasyonun tamamlanma durumu takip etmek ve sonucu ele almak mümkündür.
- Kullanım: Async/Await anahtar kelimeleri ile kullanılarak okunurluk artırılır.
- Thread Pool: İş parçacığı havuzunu kullanarak operasyonları çalıştırır.

> [!WARNING]
> ```Task.Run()``` her zaman daha iyi performans vermeyebilir. Küçük ve hızlı işlemler i.in doğrudan asenkron yöntemler tercih edilebilir.

Detaylı Örnek [1](https://github.com/furkandalak/Examples/blob/main/Task%20Run%201), [2](https://github.com/furkandalak/Examples/blob/main/Task%20Run%202)

## Task.Run() VS Thread

1. Thread
   - Doğrudan iş parçacığı oluşturmayı sağlar.
   - İş parçacığı oluşturmak. yönetmek ve sonlandırmak direkt geliştiricinin sorumluluğundadır.
   - Düşük seviyelidir. İş parçacığını manuel kontrol etme imkanı verir.
   - Genellikle düşük seviyeli senkronizasyon mekanizmalarını gerektirir. Örnek: Lock, Mutex.
2. Task.Run()
   - .NET Task Parallel Library tarafından sağlanan yüksek seviyeli API'dır.
   - .NET çalışma zamanı thread pool'u yönetimi ve iş parçacığı oluşturma gibi karmaşık görevleri otomatik yönetir.
   - Asenkron programlamayı destekler ve Async/Await kullanımını kolaylaştırır.
   - İş parçacıkları arasında veri paylaşımını kontrol etmeyi sağlayan yüksek seviyeli senkronizasyon özelliklerini içerir.
  

### Thread:
#### Avantajlar
- Düşük seviyeli kontrol
- Özelleştirilmiş senkronizasyon durumlarına uygun
- Doğrudan iş parçacığı oluşturmak/kontrol etmek isteyen geliştiricilere esneklik
#### Dezavantajlar
- Daha fazla manuel yönetim ve potansiyel hatalar
- Daha fazla kaynak tüketebilir, iş parçacığı oluşturup yönetme maliyeti
### Task.Run():
#### Avantajlar
- Yüksek seviyeli bir API sunar ve iş parçacığı kontrolünü otomatikleştirir
- Asenkron programlamayı destekler, verimli ve okunabilir kod yazmaya yardımcı olur
- Task'ler thread pool'u etkili kullanarak performans artırabilir
#### Dezavantajlar
- Daha fazla soyutlamayla gelir, geliştiricinin kontrolünü niraz kaybetmesine neden olabilir
- Daha karmaşık senkronizasyon senaryolarında kullanımı zor olabilir

Tercih, uygulamanın ihtiyaçlarına, ölçeklerine, gereksinimlerine ve geliştiricinin tercihine bağlıdır. Genellikle modern C# uygulamalarında Task.Run() ve Task-Based asenkron programlama daha yaygındır.

## Ekstra
Pathfinder olayı [TR](https://medium.com/@gokhansengun/mars-ke%C5%9Fif-arac%C4%B1-pathfinderdaki-i%CC%87lgin%C3%A7-yaz%C4%B1l%C4%B1m-problemi-5b6ebe771d55), [EN](https://www.rapitasystems.com/blog/what-really-happened-software-mars-pathfinder-spacecraft)
