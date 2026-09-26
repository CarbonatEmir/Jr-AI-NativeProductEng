# Jr-AI-NativeProductEng

Markdown# 🚀 Lasersan AI - Kurumsal RAG ve OSINT Asistanı

![Sistem Genel Görünümü]
<img width="1999" height="1453" alt="Ekran görüntüsü 2026-06-10 020613" src="https://github.com/user-attachments/assets/b5f07b0b-5214-4844-abfc-bfd6102ef9f8" />

> *Sistem arayüzü ve genel kullanım ekranı.*

Lasersan AI, savunma ve optik ürün katalogları üzerine inşa edilmiş, **iki ana yeteneğe** (RAG tabanlı Soru-Cevap ve OSINT Rakip Keşfi) sahip gelişmiş bir yapay zeka asistanıdır. Sistem, iç ekipler için Streamlit tabanlı bir arayüz ve kurumsal web entegrasyonları için FastAPI tabanlı bir Headless API (SSE Streaming destekli) sunar.

---

## 📌 Özellikler ve Sistem Mimarisi

Sistem, yapay zeka halüsinasyonlarını (uydurma) engellemek amacıyla doğrudan vektör veritabanından (Qdrant) getirilen gerçek ürün bağlamlarına dayanarak çalışır. 

<img width="2550" height="1472" alt="Ekran görüntüsü 2026-06-10 023305" src="https://github.com/user-attachments/assets/b6544c5d-9108-48e2-976f-2403ae234b44" />
<img width="1999" height="1448" alt="Ekran görüntüsü 2026-06-10 020703" src="https://github.com/user-attachments/assets/1e1d0b42-4b3d-4b94-9870-06b3562578ce" />
<img width="1990" height="1454" alt="Ekran görüntüsü 2026-06-10 020754" src="https://github.com/user-attachments/assets/0832ca2a-0936-41e8-8281-a3a9689fc7bd" />




### 1. RAG Tabanlı Chatbot (Soru-Cevap Hattı)
Kullanıcı sorguları, veritabanındaki ürün bilgileriyle zenginleştirilerek dil modeline (LLM) iletilir.

```text
Soru 
 └─→ Niyet Sınıflandırma (5 yönlü router)
     └─→ Sorgu Ayrıştırma (Decompose)
         └─→ Hibrit Arama (Qdrant: Dense + Sparse/BM25)
             └─→ Yeniden Sıralama (Reranker)
                 └─→ Bağlam İnşası
                     └─→ LLM ile Yanıt Üretimi (Gemini / Ollama)
2. OSINT Rakip Keşif Hattı (Discovery)İnternet üzerindeki rakip ürünleri otomatik olarak keşfeder, analiz eder ve skorlar.PlaintextÜrün Adı 
 └─→ Qdrant'tan ürün payload'ı çekilir
     └─→ Gemini ile OSINT arama sorguları üretilir
         └─→ Paralel Arama (Serper → Tavily yedek)
             └─→ URL Temizleme & Tekilleştirme
                 └─→ Gemini ile adayların skorlanması
                     └─→ En iyi N Aday + Cache (Redis)
🧱 İkili Mimari (Frontend + Backend)KatmanTeknolojiPortKullanım AmacıFrontendStreamlit (app.py)8501İç ekip ve demolar için görsel sohbet/yönetim paneli.BackendFastAPI (api.py)8000Kurumsal sistemlerin tüketeceği saf JSON/SSE API.Not: API ve arayüz aynı çekirdek mantığı (rag_service.py, llm_engine.py) paylaşır. Arayüz ve API her zaman birebir aynı sonucu üretir.🛠 Kullanılan TeknolojilerSistemde kullanılan temel teknolojiler ve entegrasyonlar.Backend & API: Python 3.12, FastAPI, Uvicorn, Pydantic v2Arayüz: StreamlitYapay Zeka & LLM: Google Gemini (LLM + Embedding), Ollama (Yerel LLM alternatifi - qwen2.5:14b)Vektör Veritabanı: Qdrant (Hibrit arama destekli), Dockerİlişkisel Veritabanı & Önbellek: PostgreSQL 14+, Redis (Opsiyonel), SQLAlchemyOSINT & Arama: Serper (Google SERP), Tavily (AI-native arama), Firecrawl (Web/PDF kazıma)⚙️ Kurulum Adımları1. Ön KoşullarPython 3.12.x (3.13/3.14 sürümlerinde bazı bağımlılık uyuşmazlıkları olabilir)Docker Desktop (Qdrant için)PostgreSQL (14+)Git2. Projeyi Klonlama ve Sanal OrtamBashgit clone [https://github.com/KULLANICI_ADINIZ/lasersan-ai.git](https://github.com/KULLANICI_ADINIZ/lasersan-ai.git)
cd lasersan-ai

# Windows için sanal ortam oluşturma ve aktif etme
py -3.12 -m venv .venv
.\.venv\Scripts\activate

# Mac/Linux için sanal ortam oluşturma ve aktif etme
python3.12 -m venv .venv
source .venv/bin/activate
3. Bağımlılıkların YüklenmesiBashpip install --upgrade pip
pip install -r requirements.txt
4. Çevresel Değişkenler (.env)Proje kök dizininde bir .env dosyası oluşturun ve gerekli API anahtarlarını (Gemini, Serper, Qdrant URL vb.) tanımlayın. Sistem tüm ayarlarını otomatik olarak bu dosyadan okur.5. Vektör Veritabanını Başlatma (Docker)Bashdocker run -d --name lasersan-qdrant -p 6333:6333 -v qdrant_storage:/qdrant/storage qdrant/qdrant
🚀 Sistemi ÇalıştırmaNot: Çalıştırmadan önce sanal ortamın (.venv) aktif, .env dosyasının dolu ve Qdrant container'ının çalışır durumda olduğundan emin olun.A. Headless API Sunucusu (FastAPI)Bashuvicorn api:app --reload --host 0.0.0.0 --port 8000
Swagger API Dokümantasyonu: http://localhost:8000/docsSağlık Kontrolü: http://localhost:8000/healthÜretim Ortamı İçin Öneri: uvicorn api:app --host 0.0.0.0 --port 8000 --workers 4B. Görsel Arayüz (Streamlit)Bashstreamlit run app.py
Arayüz Erişimi: http://localhost:8501📡 API KullanımıFastAPI Swagger arayüzü üzerinden endpoint testleri.Tüm korumalı endpoint'ler isteklerde X-API-Key header'ını bekler.EndpointMetotKorumaAçıklama/healthGETAçıkCanlılık ve modül durumu kontrolü./api/v1/chatPOST🔐 API KeyRAG Chatbot (SSE streaming ile daktilo efekti)./api/v1/discoverPOST🔐 API KeyOSINT rakip keşfi (JSON formatında yanıt döner).⚠️ Sık Karşılaşılan Sorunlar (Troubleshooting)401 Unauthorized Hatası: .env dosyasında API anahtarının doğru tanımlandığından ve isteklerde Authorization yerine X-API-Key header'ının kullanıldığından emin olun.Streaming (Daktilo Efekti) Çalışmıyor: Yanıt tek seferde geliyorsa, araya giren bir ters proxy'nin (reverse proxy) tampon belleği (buffering) açık kalmış olabilir. Proxy ayarlarından buffering'i kapatın.503 Service Unavailable: Qdrant'ın kapalı olup olmadığını docker ps ile kontrol edin. DISCOVERY_ENABLED ayarının .env dosyasında true olduğundan emin olun.SSL Sertifika Hatası (pip install): Kurumsal ağlarda yaşıyorsanız:Bashpip install --trusted-host pypi.org --trusted-host files.pythonhosted.org -r requirements.txt
📎 Hızlı Komut Özeti (Cheat Sheet)Bash# Veritabanını Başlat (Qdrant)
docker run -d --name lasersan-qdrant -p 6333:6333 qdrant/qdrant

# API'yi Başlat (Port 8000)
uvicorn api:app --reload --host 0.0.0.0 --port 8000

# Arayüzü Başlat (Port 8501)
streamlit run app.py

# API Canlılık Testi
curl http://localhost:8000/health


