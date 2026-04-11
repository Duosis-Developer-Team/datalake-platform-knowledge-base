**Archived (legacy product naming).** For the current platform map and terminology, see [`../../wiki/99-Glossary-And-Legacy-Names.md`](../../wiki/99-Glossary-And-Legacy-Names.md) and [`../../wiki/00-Platform-Overview.md`](../../wiki/00-Platform-Overview.md).

---

# **ORION Platformu \- Ürün Gereksinim Dokümanı**

**Versiyon:** 1.1

**Tarih:** 19.08.2025

**Durum:** Taslak

### **1\. Giriş ve Vizyon**

#### **1.1. Giriş**

Bu doküman, **ORION Platform** olarak adlandırılan Yapay Zeka-Odaklı (AI-Native) Operasyon ve Analitik Platformu'nun ürün gereksinimlerini, vizyonunu, özelliklerini ve teknik altyapısını tanımlamaktadır. ORION Platform, modern organizasyonların karşılaştığı veri siloları, karmaşık altyapı yönetimi ve gecikmeli karar alma süreçleri gibi temel zorluklara bütünleşik bir çözüm sunmak amacıyla tasarlanmıştır. Platform, veri hacmindeki artış ve yapay zekanın demokratikleşmesi gibi pazar trendlerinin merkezinde konumlanarak, kurumların en değerli varlığı olan veriyi bir maliyet kaleminden stratejik bir güce dönüştürmeyi hedeflemektedir.

#### **1.2. Vizyon**

Platformun vizyonu, verinin ham halden stratejik içgörülere dönüştüğü, altyapı ve Makine Öğrenmesi Operasyonları (MLOps) yaşam döngülerinin bütünüyle otomatize edildiği, kendi kendini yönetebilen otonom bir ekosistem tesis etmektir. ORION Platformu ile teçhiz edilmiş bir organizasyonel yapıda, pazarlama profesyonellerinin anlık sorgularına saniyeler içinde interaktif raporlar üretilmesi, altyapı mühendislerinin ise yeni servisleri yalnızca bir Git taahhüdü ile güvenli biçimde devreye alması mümkün hale gelecektir. ORION Platform, teknik engelleri ortadan kaldırarak her seviyeden kullanıcının verilerle diyalog kurmasını sağlayacak ve kurumların reaktif raporlamadan proaktif karar alma modeline geçişini temin ederek veri odaklı bir kültürün benimsenmesini önemli ölçüde hızlandıracaktır.

### **2\. Ele Alınan Problemler**

Günümüz organizasyonları, dijital dönüşümün sunduğu fırsatları tam olarak değerlendirmelerini engelleyen temel sorunlarla yüzleşmektedir:

* **Dağınık Veri ve Bilgi Siloları:** Veriler, farklı kurumsal sistemlerde (CRM, ERP, sunucu logları), çeşitli formatlarda (yapısal, yarı yapısal, yapısal olmayan) ve farklı lokasyonlarda (şirket içi, bulut) dağınık halde bulunmaktadır. Bu durum, örneğin bir müşteri destek kaydını, ilgili müşterinin satın alma geçmişiyle birleştirerek bütünsel bir perspektif geliştirilmesini olanaksız kılmaktadır.  
* **Yavaş ve Hatalı Raporlama Süreçleri:** Raporlama ve analiz ihtiyaçlarının karşılanması, teknik ekiplere olan bağımlılık nedeniyle haftalar sürebilmektedir. Finans departmanının çeyrek sonu analizi için gereksinim duyduğu verilerin farklı departmanlardan toplanması, temizlenmesi ve konsolide edilmesi süreci, kritik karar anlarının kaçırılmasına neden olmaktadır. Manuel süreçlerin insan kaynaklı hatalara maruz kalması, raporların güvenilirliğini de zayıflatmaktadır.  
* **Teknik Yetkinlik Bariyerleri:** Veriye erişim ve analiz, genellikle SQL veya programlama dilleri gibi ileri düzey teknik yetkinlikler gerektirmektedir. Bir ürün yöneticisinin, uygulamasındaki kullanıcı davranışlarını anlamak amacıyla basit bir A/B testi sonucunu analiz etme ihtiyacı dahi bir veri analistinin veya mühendisin desteğini gerektirmekte, bu durum iş birimlerinin operasyonel çevikliğini ve inovasyon kapasitesini kısıtlamaktadır.  
* **Karmaşık MLOps Yaşam Döngüleri:** Makine öğrenmesi modellerinin geliştirilmesi, test edilmesi, üretim ortamına alınması ve yönetilmesi; karmaşık, zaman alıcı ve uzmanlık gerektiren bir süreçtir. Geliştirilen bir model, üretim ortamındaki veri dağılımının değişmesi ("model drift") nedeniyle zamanla performansını yitirebilmektedir. Bu modelin yeniden eğitilmesi, test edilmesi ve kesintisiz bir şekilde güncellenmesi, özel platformlar olmaksızın yönetilmesi son derece zor bir operasyonel yük teşkil etmektedir.  
* **Verimsiz Altyapı Yönetimi:** Altyapı ve konfigürasyon yönetimi, sıklıkla manuel, standart dışı ("snowflake servers") ve denetlenmesi güç süreçlerle yürütülmektedir. Yeni bir sunucunun veya servisin devreye alınması, farklı ekipler arasında koordinasyon gerektiren uzun bir prosedüre dönüşebilmektedir. Bu durum, güvenlik riskleri (hatalı yapılandırılmış güvenlik duvarları gibi) ve operasyonel verimsizlik (gereksiz kaynak tahsisi gibi) yaratmaktadır.

### **3\. Hedef Kitle ve Kullanıcı Personaları**

* **Veri Analistleri / İş Birimleri:** **Mevcut Zorluklar:** Farklı veri kaynaklarından veri toplama, elektronik tablolarda birleştirme ve saatler süren manuel işlemlerle rapor hazırlama. **ORION Platformunun Sunduğu Değer:** Teknik ekiplere bağımlı olmaksızın, doğal dil sorguları aracılığıyla anlık, interaktif ve güvenilir raporlar ile stratejik içgörüler elde etme imkanı.  
* **Veri Bilimciler / ML Mühendisleri:** **Mevcut Zorluklar:** Zamanlarının önemli bir bölümünü model geliştirmek yerine, veri temizleme, altyapı konfigürasyonu ve modelin üretim ortamına dağıtımı gibi operasyonel görevlere ayırmak zorunda kalmaları. **ORION Platformunun Sunduğu Değer:** Makine öğrenmesi modellerini hızlı bir şekilde geliştirmek, eğitmek, versiyonlamak ve tek bir komutla üretim ortamına almak için sağlam ve otomatize edilmiş bir MLOps altyapısına erişim.  
* **DevOps / Altyapı Ekipleri:** **Mevcut Zorluklar:** Acil konfigürasyon değişikliği talepleri, manuel ve tekrarlayan kurulum prosedürleri ve sistem konfigürasyonlarının takibindeki zorluklar. **ORION Platformunun Sunduğu Değer:** Altyapı konfigürasyonlarını standart, denetlenebilir ve tam otomatize bir şekilde (GitOps) yöneterek operasyonel yükü azaltma ve sistem güvenliğini artırma.  
* **Yöneticiler (C-Level):** **Mevcut Zorluklar:** Stratejik kararlarını, geçmişe dönük ve sıklıkla güncelliğini yitirmiş özet raporlara (gecikmeli göstergeler) dayandırma zorunluluğu. **ORION Platformunun Sunduğu Değer:** Kurumun genel performansı hakkında anlık, veri odaklı ve stratejik bir perspektif kazanarak, proaktif uyarılar ve tahminleme analizleri (öncü göstergeler) ile karar süreçlerini iyileştirme.

### **4\. Çözüm ve Platforma Genel Bakış**

ORION Platformu, belirtilen bu problemlere yönelik bir çözüm olarak, her biri kendi alanında uzmanlaşmış ve bütünleşik bir mimari ile çalışan Yapay Zeka-Odaklı modüller sunmaktadır. Tüm modüllere tek ve tutarlı bir arayüz üzerinden erişim sağlanmaktadır:

1. **HMDL (Host Metadata-Driven Lifecycle):** Platformun temelini oluşturan, altyapı ve konfigürasyon yönetimini GitOps prensipleriyle otomatize eden modül.  
2. **Datalake & MLOps:** Veri toplama, işleme ve makine öğrenmesi yaşam döngülerini yöneten merkezi motor.  
3. **D3M (Data Driven Decision Module):** Platformdaki verilerle diyalog kurmayı, analitik sorgular çalıştırmayı ve akıllı gösterge panelleri (dashboard) geliştirmeyi sağlayan analitik arayüz.  
4. **ORION Copilot:** Platformun kendisinin yönetilmesini ve organize edilmesini sağlayan, Yapay Zeka destekli yardımcı pilot modülü.

Bu modüller, platformun merkezi sinir sistemi olarak görev yapan bir **Controller** tarafından yönetilen, konteyner tabanlı ve ölçeklenebilir bir mimari üzerinde faaliyet göstermektedir. D3M modülü veri ile etkileşimi sağlarken, ORION Copilot platformun kendisi ile etkileşimi mümkün kılmaktadır. Örneğin, bir DevOps mühendisinin HMDL aracılığıyla yaptığı teknik bir değişiklik, ORION Copilot sayesinde bir ürün yöneticisi tarafından "Yeni ürün analizleri için gerekli olan sunucu loglarını platforma ekle" şeklinde basit bir komut aracılığıyla tetiklenebilmektedir. Bu sinerji, platformun reaktif bir araç olmaktan ziyade, proaktif ve bütünleşik bir ekosistem olarak işlev görmesini sağlamaktadır.

### **5\. Temel Özellikler ve Fonksiyonellik**

#### **5.1. HMDL Modülü (GitOps Yönetimi)**

* **Deklaratif Altyapı Yönetimi:** Tüm altyapı ve uygulama konfigürasyonları (örneğin, Kubernetes dağıtım dosyaları, ağ kuralları) Git üzerinde "tek doğru kaynak" olarak versiyonlanmaktadır. Bu, "Altyapıyı Kod Olarak" (Infrastructure as Code) yaklaşımının benimsenmesiyle, altyapının tekrarlanabilir ve tutarlı bir yapıya kavuşturulmasını sağlamaktadır.  
* **Tam Otomasyon:** ArgoCD entegrasyonu ile Git'in ana branch'ine yapılan bir birleştirme (merge) işlemi, otomatik olarak bir Sürekli Entegrasyon/Sürekli Dağıtım (CI/CD) boru hattını tetiklemekte ve değişiklikler kontrollü bir şekilde üretim ortamlarına uygulanmaktadır.  
* **Denetlenebilirlik ve Güvenlik:** Her değişiklik, kimin tarafından, ne zaman ve neden yapıldığı bilgisiyle (commit mesajı) kayıt altına alınmaktadır. Bir sorun anında git revert komutuyla sistem, önceki kararlı konfigürasyona saniyeler içinde geri döndürülebilmektedir.  
* **Bağımsız Kullanım:** Modül, farklı ortamların veya süreçlerin otomasyonu için platformdan bağımsız olarak da kullanılabilmektedir. Örneğin, yalnızca bir mobil uygulamanın CI/CD sürecini yönetmek amacıyla entegre edilebilir.

#### **5.2. Datalake & MLOps Modülü (Veri Motoru)**

* **Dinamik Veri Toplama:** Özel olarak geliştirilmiş Python betikleri, KubeFlow üzerinde birer "boru hattı" (pipeline) olarak çalışarak farklı kaynaklardan (veritabanları, API'ler, log dosyaları) esnek ve ölçeklenebilir veri toplamaktadır. Her boru hattı, veri kaynağının yapısına özel olarak veri doğrulama, temizleme ve zenginleştirme adımlarını içerebilmektedir.  
* **Merkezi Veri Depolama:** Toplanan veriler, standart bir formatta (örneğin, Parquet) tutarlılık sağlanarak PostgreSQL üzerinde depolanmaktadır. Veri kataloğu aracılığıyla tüm verilerin meta verileri (kaynağı, şeması, güncelliği) yönetilmektedir.  
* **Uçtan Uca MLOps:** KubeFlow kullanılarak veri hazırlama, model eğitimi, hiperparametre optimizasyonu, versiyonlama, A/B testi için dağıtım ve üretim ortamında model performansını izleme süreçleri tek bir çatı altında, görsel bir arayüzle veya programatik olarak yönetilmektedir.

#### **5.3. D3M Modülü (Veri Analitik Arayüzü)**

* **Doğal Dilde Sorgulama:** Kullanıcılar, "Geçen ay en çok kaynak tüketen servisler hangileriydi ve bu tüketimin bir önceki ayla karşılaştırmasını göster" gibi karmaşık ve diyalog halinde sorgular çalıştırabilmektedir. Sistem, sorgudaki belirsizlikleri gidermek amacıyla kullanıcıya ek sorular yöneltebilmektedir.  
* **AI Destekli Otonom Görselleştirme:** Platform, sorgu sonucundaki veriyi analiz ederek en anlamlı görselleştirmeyi (zaman serisi verisi için çizgi grafik, kategorik karşılaştırma için çubuk grafik, coğrafi veri için harita vb.) Büyük Dil Modeli (LLM) aracılığıyla otonom olarak oluşturmaktadır. Kullanıcı, "Bu grafiği pasta grafiğine çevir" gibi komutlarla görselleştirmeyi anlık olarak modifiye etme imkanına sahiptir.  
* **Gösterge Paneli Organizasyonu:** Bu arayüz üzerinden geliştirilen gösterge panelleri ve raporların klasörlenmesi, isimlendirilmesi ve yetkilendirilmesi gibi organizasyonel işlemler ORION Copilot aracılığıyla yönetilmektedir.

#### **5.4. ORION Copilot (AI Yardımcı Pilot)**

* **Süreç ve Envanter Yönetimi:** HMDL'in otomatize ettiği karmaşık süreçleri (yeni bir veri toplayıcı ekleme, envantere yeni bir sunucu kaydetme vb.) teknik bilgisi olmayan kullanıcılar için basit, diyalog bazlı bir deneyime dönüştürmektedir.  
* **Adım Adım Planlama ve Onay Mekanizması:** Kullanıcıdan bir talep aldığında (örneğin, "yeni bir veri toplayıcı ekle"), öncelikle mevcut durumu (collector ajanları, HMDL konfigürasyonları) analiz etmektedir. Akabinde, yapılması gerekenleri adım adım, anlaşılır bir dilde kullanıcıya sunmakta ve her adımı gerçekleştirmek için onay talep etmektedir.  
* **Otonom Yürütme (Execution):** Kullanıcı onayı alındıktan sonra, planladığı adımları (örneğin, KubeFlow'da boru hattı oluşturma, Git'e yeni konfigürasyon gönderme) ilgili modüllerin API'lerini kullanarak otonom olarak icra eder.  
* **Kişiselleştirilmiş Deneyim:** Her sayfada bulunan Yapay Zeka Sohbet Robotu (AiChatBot) arayüzü üzerinden erişim sağlanmaktadır. RAG teknolojisi ile kullanıcının geçmiş etkileşimlerinden ve tercihlerinden öğrenerek proaktif önerilerde bulunmakta ve daha akıcı bir kullanıcı deneyimi sağlamaktadır.

### **6\. Teknik Mimari ve Altyapı**

* **Orkestrasyon:** Kubernetes: Konteyner tabanlı iş yüklerinin ölçeklenebilir, dayanıklı ve taşınabilir biçimde yönetilmesini sağlayan endüstri standardı orkestrasyon platformu.  
* **MLOps:** KubeFlow: Kubernetes-native bir MLOps platformu olması, mevcut altyapıyla sorunsuz entegrasyon temin eder.  
* **CI/CD & Konfigürasyon Yönetimi:** Git, ArgoCD (GitOps): Deklaratif, denetlenebilir ve tam otomatize bir altyapı yönetimi felsefesi sunar.  
* **Veri Toplama:** KubeFlow Pipelines üzerinde çalışan özel Python betikleri: Maksimum esneklik ve özelleştirme imkanı tanır.  
* **Veri Depolama:** PostgreSQL (Başlangıç): Güçlü, güvenilir ve yaygın olarak kullanılan bir ilişkisel veritabanı sistemi.  
* **Yapay Zeka & Etkileşim:** RAG (Retrieval-Augmented Generation) ve Fine-Tuning teknikleriyle özelleştirilmiş Büyük Dil Modelleri (LLM): RAG, LLM'in kurumun güncel ve özel verileriyle etkileşim kurmasını sağlarken, Fine-Tuning modelin kuruma özgü terminolojiyi ve süreçleri öğrenmesini mümkün kılar.  
* **Mimari:** Merkezi bir "Controller" (API Gateway) tarafından yönetilen, konteyner tabanlı mikroservis mimarisi: Modüllerin bağımsız olarak geliştirilmesini, dağıtılmasını ve ölçeklenmesini mümkün kılar.

### **7\. Başarı Metrikleri**

Platformun başarısı, aşağıdaki temel performans göstergeleri (KPI) aracılığıyla ölçülecektir:

* **İçgörüye Ulaşma Süresinde Azalma:** Verinin platforma dahil olduğu andan itibaren, karar vericiler için eyleme geçirilebilir bir içgörüye dönüştürülmesine kadar geçen ortalama süre (Ortalama "Time-to-Insight").  
* **Manuel Raporlama Taleplerinde Düşüş:** İş birimlerinden teknik departmanlara iletilen manuel raporlama ve destek taleplerinin sayısındaki nicel azalma.  
* **ML Model Dağıtım Sıklığı:** Yeni bir makine öğrenmesi modelinin geliştirilmesinden üretim ortamına alınmasına kadar geçen sürenin kısalması ve haftalık/aylık model güncelleme sayısındaki artış ("Deployment Frequency" & "Lead Time for Changes").  
* **Konfigürasyon Hata Oranında Azalma:** GitOps sayesinde manuel yapılandırmadan kaynaklanan hataların ve bu hatalar nedeniyle yaşanan kesintilerin ortalama kurtarma süresindeki (Mean Time to Recovery \- MTTR) düşüş.  
* **Kullanıcı Adaptasyon Oranı:** Farklı kullanıcı personaları arasında platformun aktif kullanım oranı ve Net Tavsiye Skoru (NPS) gibi kullanıcı memnuniyet anketleri.

### **8\. Gelecek Vizyonu ve Yol Haritası**

* **Çeşitli Veri Depolama Katmanları İçin Platform Desteğinin Genişletilmesi:** Büyük veri iş yükleri için MinIO gibi S3 uyumlu Nesne Depolama (Object Storage) çözümleri ve gerçek zamanlı analitik için ClickHouse gibi sütun tabanlı veritabanları için destek eklenmesi.  
* **Tam Otonom LLM Destekli Operasyonlara Geçişin Sağlanması:** İnsan müdahalesi gerektiren süreçlerin minimize edilerek, LLM'in anomali tespit ettiğinde otomatik olarak kaynak artırımı gibi düzeltici aksiyonları alıp yalnızca bilgilendirme yaptığı tam otonom operasyonel modellere geçiş.  
* **Bir Uygulama ve Model Pazar Yeri'nin (Marketplace) Tesis Edilmesi:** Farklı veri kaynakları için hazır veri toplayıcıları (connectors) ve sahtekarlık tespiti, müşteri kaybı tahmini gibi yaygın iş problemleri için önceden eğitilmiş makine öğrenmesi modellerinin sunulduğu bir pazar yeri oluşturulması.  
* **Gelişmiş Çoklu Bulut ve Hibrit Ortam Yönetim Yeteneklerinin Eklenmesi:** Platformun, hem kurum içi veri merkezlerinde hem de farklı bulut sağlayıcılarında (AWS, Azure, GCP) çalışan sistemleri tek bir arayüzden yönetebilme yeteneğinin geliştirilmesi.