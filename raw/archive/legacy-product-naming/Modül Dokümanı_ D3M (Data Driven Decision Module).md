**Archived (legacy product naming).** For the current platform map and terminology, see [`../../wiki/99-Glossary-And-Legacy-Names.md`](../../wiki/99-Glossary-And-Legacy-Names.md) and [`../../wiki/00-Platform-Overview.md`](../../wiki/00-Platform-Overview.md).

---

# **Modül Dokümanı: D3M (Data Driven Decision Module)**

Versiyon: 1.0  
Sorumlu Alan: Veri Analitiği ve Etkileşim

### **1\. Analiz**

**D3M (Data Driven Decision Module)**, ORION Platformu'nun analitik beyni olarak konumlandırılmıştır. Bu modülün temel amacı, organizasyonun farklı kaynaklarda bulunan ham ve karmaşık verileri ile teknik yetkinliği olmayan son kullanıcılar arasında bir köprü kurmaktır. Geleneksel iş zekası (BI) araçlarının aksine, D3M reaktif raporlamanın ötesine geçerek, kullanıcıların veriyle diyalog kurmasını, proaktif içgörüler elde etmesini ve veri odaklı kararları saniyeler içinde almasını sağlayan, Yapay Zeka-odaklı bir etkileşim katmanıdır. Modül, veri analizini teknik bir süreç olmaktan çıkarıp, organizasyon içindeki her bireyin erişebileceği sezgisel bir deneyime dönüştürür.

### **2\. Kabiliyetler**

* **Doğal Dilde Sorgulama (Natural Language Querying \- NLQ):**  
  * İlişkisel veritabanları, NoSQL veritabanları ve API'ler gibi birden çok veri kaynağına bağlanarak, "Son çeyrekte en yüksek performans gösteren ürün kategorimiz hangisiydi?" gibi karmaşık soruları anlar ve ilgili sorguları arka planda otonom olarak oluşturur.  
* **Yapay Zeka Destekli Otonom Görselleştirme:**  
  * Sorgu sonucunda elde edilen veri setinin yapısını (zaman serisi, coğrafi, kategorik vb.) analiz ederek, veriyi en etkili şekilde anlatacak görsel formatı (çizgi grafik, ısı haritası, çubuk grafik vb.) otomatik olarak seçer ve oluşturur.  
* **Keşifsel Veri Analizi (Exploratory Data Analysis \- EDA):**  
  * Kullanıcı talebi üzerine veya proaktif olarak, veri setleri arasındaki gizli korelasyonları, aykırı değerleri (anomalileri) ve istatistiksel olarak anlamlı desenleri tespit eder. Örneğin, "Web sitesi trafiğindeki artış ile satış rakamları arasında bir ilişki var mı?" sorusuna istatistiksel kanıtlarla yanıt verir.  
* **Veri Federasyonu ve Birleştirme:**  
  * Kullanıcının tek bir sorgusuyla, farklı sistemlerde bulunan verileri (örneğin, Zabbix'teki performans metrikleri ile Jira'daki ticket verileri) sanal olarak birleştirerek bütünsel bir analiz sunar.

### **3\. Faydalar**

* **Verinin Demokratikleşmesi:** Teknik uzmanlığa olan bağımlılığı ortadan kaldırarak, organizasyon içindeki her çalışanın verilere birinci elden erişimini ve sorgulama yapabilmesini sağlar.  
* **Karar Süreçlerinin Radikal Hızlanması:** Haftalar veya günler sürebilen manuel veri analizi ve raporlama süreçlerini saniyelere indirerek, organizasyonun pazar dinamiklerine çok daha hızlı yanıt vermesini mümkün kılar.  
* **Derinlemesine ve Proaktif İçgörü:** İnsan gözünün veya geleneksel BI araçlarının gözden kaçırabileceği karmaşık ilişkileri ve anomalileri ortaya çıkararak, reaktif sorun çözümünden proaktif strateji geliştirmeye geçişi destekler.  
* **Operasyonel Verimlilik:** Veri analistleri ve mühendislerinin zamanını, tekrarlayan raporlama görevlerinden alıp daha katma değerli ve stratejik analizlere yönlendirmelerini sağlar.

### **4\. Teknik Altyapı**

* **Yapay Zeka Modelleri:** RAG (Retrieval-Augmented Generation) ve Fine-Tuning teknikleriyle özelleştirilmiş Büyük Dil Modelleri (LLM). RAG, LLM'in anlık ve kuruma özel verilere erişimini sağlarken, Fine-Tuning modelin sektörel terminolojiyi anlamasını sağlar.  
* **Veri Kaynağı Bağlantı Protokolleri:** JDBC/ODBC (İlişkisel Veritabanları için), REST/GraphQL (API'ler için) ve özel konektörler.  
* **Görselleştirme Kütüphaneleri:** D3.js, Vega-Lite veya benzeri, dinamik ve interaktif görselleştirmeler üretebilen modern kütüphaneler.  
* **Analitik Motor:** Python tabanlı bilimsel hesaplama kütüphaneleri (Pandas, NumPy, SciPy, Statsmodels) ve dağıtık hesaplama için Spark entegrasyonu.  
* **Mimari:** Diğer ORION modülleriyle API üzerinden iletişim kuran, bağımsız ve ölçeklenebilir bir mikroservis.