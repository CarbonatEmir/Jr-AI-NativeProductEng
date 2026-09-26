# Jr-AI-NativeProductEng
Markdown# 🚀 Lasersan AI - Kurumsal RAG ve OSINT Asistanı

Sistem Genel Görünümü
<img width="1999" height="1453" alt="Ekran görüntüsü 2026-06-10 020613" src="https://github.com/user-attachments/assets/f17a69d6-55a3-4706-b90b-8d2cbb351188" />

> *Sistem arayüzü ve genel kullanım ekranı.*

Lasersan AI, savunma ve optik ürün katalogları üzerine inşa edilmiş, RAG tabanlı Soru-Cevap ve OSINT Rakip Keşfi yeteneklerine sahip gelişmiş bir yapay zeka asistanıdır. Sistem, iç ekipler için Streamlit tabanlı bir arayüz ve kurumsal web entegrasyonları için FastAPI tabanlı bir Headless API sunar.

---

##  Proje Kazanımları ve İş Değeri

Sistemin canlıya alınmasıyla birlikte sağlanan temel faydalar:

*    **%80 Bilgiye Erişim Hız Artışı:** Şirket ürün kataloğu üzerinde çalışan çift aşamalı RAG mimarisi sayesinde, ürün bazlı özellik arama ve bilgiye erişim süresi %80 oranında kısaltıldı.
*    **Otomasyon ve Rekabet İstihbaratı:** Mevcut chatbot ekosistemine global pazar rakip analizi, karşılaştırma ve keşif modülleri entegre edilerek şirketin rekabet istihbaratı süreçleri uçtan uca otomatikleştirildi.
*    **Maliyet ve Performans Optimizasyonu:** İleri seviye prompt mühendisliği, optimum model seçimi ve önbellekleme (caching) stratejileri uygulanarak sistem, minimum işlem/token maliyeti ile canlıya alındı.

---

## 📌 Özellikler ve Sistem Mimarisi

Sistem, yapay zeka halüsinasyonlarını engellemek amacıyla doğrudan vektör veritabanından (Qdrant) getirilen gerçek ürün bağlamlarına dayanarak çalışır.

<img width="1999" height="1448" alt="Ekran görüntüsü 2026-06-10 020703" src="https://github.com/user-attachments/assets/bf62322e-922e-40e2-a995-991f2bbd2460" />
<img width="2550" height="1472" alt="Ekran görüntüsü 2026-06-10 023305" src="https://github.com/user-attachments/assets/b7356453-d1cf-431f-a4b3-6746be11b1f8" />
<img width="1996" height="1452" alt="Ekran görüntüsü 2026-06-10 020650" src="https://github.com/user-attachments/assets/2a205698-3541-41e1-bb23-988977e1ed89" />
<img width="1990" height="1454" alt="Ekran görüntüsü 2026-06-10 020754" src="https://github.com/user-attachments/assets/3a18c0bf-b5a4-4dc4-b235-d55cff9564fe" />


### 1. RAG Tabanlı Chatbot (Soru-Cevap Hattı)
Kullanıcı sorguları, veritabanındaki ürün bilgileriyle zenginleştirilerek dil modeline iletilir.

```text
Soru 
 └─→ Niyet Sınıflandırma (5 yönlü router)
     └─→ Sorgu Ayrıştırma (Decompose)
         └─→ Hibrit Arama (Qdrant: Dense + Sparse/BM25)
             └─→ Yeniden Sıralama (Reranker)
                 └─→ Bağlam İnşası
                     └─→ LLM ile Yanıt Üretimi (Gemini / Ollama)
2. OSINT Rakip Keşif Hattı (Discovery)İnternet üzerindeki rakip ürünleri otomatik keşfeder ve analiz eder.PlaintextÜrün Adı 
 └─→ Qdrant'tan ürün payload'ı çekilir
     └─→ Gemini ile OSINT arama sorguları üretilir
         └─→ Paralel Arama (Serper → Tavily yedek)
             └─→ URL Temizleme & Tekilleştirme
                 └─→ Gemini ile adayların skorlanması
                     └─→ En iyi N Aday + Cache (Redis)
🧱 İkili Mimari (Frontend + Backend)KatmanTeknolojiPortKullanım AmacıFrontendStreamlit (app.py)8501İç ekip ve demolar için görsel sohbet paneli.BackendFastAPI (api.py)8000Kurumsal sistemler için saf JSON/SSE API.🛠 Kullanılan TeknolojilerBackend & API: Python 3.12, FastAPI, Uvicorn, Pydantic v2Arayüz: StreamlitYapay Zeka: Google Gemini, Ollama (qwen2.5:14b)Veritabanı & Cache: Qdrant, PostgreSQL 14+, Redis, DockerOSINT & Arama: Serper, Tavily, Firecrawl⚙️ Kurulum Adımları1. Projeyi Klonlama ve Sanal OrtamBashgit clone [https://github.com/KULLANICI_ADINIZ/lasersan-ai.git](https://github.com/KULLANICI_ADINIZ/lasersan-ai.git)
cd lasersan-ai

# Windows
py -3.12 -m venv .venv
.\.venv\Scripts\activate

# Mac/Linux
python3.12 -m venv .venv
source .venv/bin/activate
2. Bağımlılıklar ve Veritabanı (Docker)Bashpip install -r requirements.txt

# Qdrant'ı Başlatma
docker run -d --name lasersan-qdrant -p 6333:6333 -v qdrant_storage:/qdrant/storage qdrant/qdrant
🚀 Sistemi ÇalıştırmaA. Headless API SunucusuBashuvicorn api:app --reload --host 0.0.0.0 --port 8000
B. Görsel Arayüz (Streamlit)Bashstreamlit run app.py
📡 API Endpoint Kullanımıİsteklerde X-API-Key header'ı zorunludur.EndpointMetotAçıklama/healthGETCanlılık kontrolü./api/v1/chatPOSTRAG Chatbot (SSE streaming)./api/v1/discoverPOSTOSINT rakip keşfi.
