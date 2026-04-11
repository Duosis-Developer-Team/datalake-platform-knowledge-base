**Archived (legacy product naming).** For the current platform map and terminology, see [`../../wiki/99-Glossary-And-Legacy-Names.md`](../../wiki/99-Glossary-And-Legacy-Names.md) and [`../../wiki/00-Platform-Overview.md`](../../wiki/00-Platform-Overview.md).

---

# **Modül Dokümanı: ORION Copilot**

Versiyon: 1.0  
Sorumlu Alan: Platform Yönetimi ve Kullanıcı Etkileşimi

### **1\. Analiz**

**ORION Copilot**, platformun operasyonel beyni ve akıllı asistanıdır. D3M modülü "veriyle konuşmayı" sağlarken, Copilot "platformun kendisiyle konuşmayı" mümkün kılar. Temel amacı, ORION Platformu'nun karmaşık envanter, süreç ve organizasyon konfigürasyonlarını, teknik uzmanlığı ne olursa olsun tüm kullanıcılar için basit, diyalog tabanlı ve hataya dayanıklı bir deneyime dönüştürmektir. Copilot, kullanıcı niyetini anlayarak, bu niyeti gerçekleştirmek için gereken teknik adımları planlayan, kullanıcıya şeffaf bir şekilde sunan ve onay aldıktan sonra otonom olarak yürüten bir aracı katmandır. Bu modül, HMDL gibi güçlü otomasyon motorlarının yeteneklerini, güvenli ve kullanıcı dostu bir arayüz aracılığıyla herkesin erişimine açar.

### **2\. Kabiliyetler**

* **Doğal Dilde Niyet Anlama (Intent Recognition):**  
  * "Pazarlama ekibi için yeni bir dashboard klasörü oluştur ve ilgili kişilere yetki ver" veya "Staging ortamındaki app-server-03 için yeni bir veri toplama süreci başlat" gibi operasyonel ve organizasyonel talepleri anlar.  
* **Çok Adımlı Süreç Planlama:**  
  * Aldığı bir talebi gerçekleştirmek için gereken mantıksal adımları (örneğin: 1\. HMDL envanterini kontrol et, 2\. KubeFlow pipeline tanımını hazırla, 3\. Git'e yeni konfigürasyonu gönder, 4\. ArgoCD senkronizasyonunu bekle, 5\. Kullanıcıyı bilgilendir) otonom olarak planlar.  
* **Şeffaf Plan Sunumu ve Onay Mekanizması:**  
  * Gerçekleştireceği eylemleri teknik detaya boğmadan, "Bu işlemi yapmak için şu adımları izleyeceğim: ... Onaylıyor musunuz?" şeklinde, anlaşılır bir dilde kullanıcıya sunar ve devam etmek için onay bekler.  
* **Otonom Yürütme ve Geri Bildirim:**  
  * Onay alındıktan sonra, planladığı adımları HMDL, KubeFlow gibi diğer modüllerin API'lerini kullanarak otonom olarak gerçekleştirir. Sürecin her aşamasında (başladı, devam ediyor, tamamlandı, hata oluştu) kullanıcıya anlık geri bildirimde bulunur.  
* **Kişiselleştirilmiş Deneyim ve Proaktif Öneriler:**  
  * RAG teknolojisi ile kullanıcının geçmiş etkileşimlerini (session bazlı ve uzun vadeli) ve rollerini analiz ederek kişiselleştirilmiş bir deneyim sunar. Örneğin, sıkça aynı türde veri kaynağı ekleyen bir kullanıcıya bu işlemi bir şablon olarak kaydetmeyi önerebilir.

### **3\. Faydalar**

* **Operasyonel Süreçlerin Basitleştirilmesi:** Karmaşık YAML dosyaları veya komut satırı arayüzleri yerine, en karmaşık operasyonel görevlerin bile basit bir sohbet arayüzü üzerinden yapılmasını sağlar.  
* **Artırılmış Operasyonel Verimlilik:** Tekrarlayan ve zaman alıcı görevleri (yeni envanter ekleme, yetkilendirme, organizasyon vb.) otomatize ederek, uzman personelin (DevOps, Platform Adminleri) zamanını stratejik iyileştirmelere odaklamasına olanak tanır.  
* **İnsan Kaynaklı Hataların Azaltılması:** Standartlaştırılmış, planlanmış ve onay mekanizmalı süreçler sayesinde manuel konfigürasyon hatalarını ve bu hataların neden olabileceği sistem kesintilerini minimize eder.  
* **Hızlandırılmış Adaptasyon ve Yetkilendirme:** Yeni kullanıcıların platformu kullanmayı öğrenme sürecini kısaltır ve teknik olmayan personelin bile belirli operasyonel görevleri güvenli bir şekilde kendi başlarına yapabilmelerini sağlar.

### **4\. Teknik Altyapı**

* **Yapay Zeka Modelleri:** LLM'ler, özellikle RAG (Retrieval-Augmented Generation) mimarisi ile zenginleştirilmiştir. RAG, Copilot'un hem platformun anlık durumu (HMDL envanteri, KubeFlow pipeline durumları) hem de kullanıcıya özel geçmiş veriler hakkında bilgi sahibi olmasını sağlar.  
* **API Orkestrasyon Motoru:** Platformun iç (HMDL, D3M) ve potansiyel dış (Jira, Slack) API'lerini yöneten ve çok adımlı iş akışlarını yürüten bir orkestrasyon katmanı (örn: Temporal, Camunda veya özel bir servis).  
* **Durum Yönetimi (State Management):** Kullanıcılarla olan diyalogların ve çok adımlı operasyonların mevcut durumunu takip eden bir veritabanı (örn: Redis, PostgreSQL).  
* **Güvenli Kimlik Bilgisi Yönetimi:** Diğer sistemlerle etkileşim kurarken kullanılacak API anahtarları ve kimlik bilgileri için HashiCorp Vault veya Kubernetes Secrets gibi güvenli depolama çözümleriyle entegrasyon.  
* **Mimari:** Her sayfada erişilebilir bir "chatbot" arayüzü sunan bir frontend bileşeni ve bu arayüzü besleyen, merkezi Controller ile entegre, bağımsız bir mikroservis.