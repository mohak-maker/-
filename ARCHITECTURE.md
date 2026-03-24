# Mizan Legal AI Platform

منصة قانونية عربية تجمع بين:
- **ميزان (Mizan):** محرك فهم الأنظمة واللوائح.
- **فاحص (Fahs):** محرك تحليل العقود واكتشاف المخاطر.

> الهدف: إنتاج تحليل قانوني موثّق بالمصادر، قابل للتدقيق، ومناسب للاستخدام المؤسسي.

---

## 1) الرؤية والتميّز

### المشكلة
- صعوبة الوصول إلى إجابة قانونية دقيقة بسرعة.
- قراءة العقود يدويًا تستغرق وقتًا وتعرّض الأعمال لمخاطر غير مرئية.
- فجوة بين "فهم النص النظامي" و"تطبيقه على بنود العقد".

### الحل
- **ميزان** يجيب على الأسئلة القانونية من مصادر نظامية موثوقة.
- **فاحص** يحلل العقود بندًا بندًا ويحدّد المخاطر.
- **الربط الذكي** بينهما يقيّم قانونية كل بند مع إسناد المادة النظامية.

### القيمة
- تقليل المخاطر التعاقدية.
- تسريع الفحص القانوني.
- رفع الثقة عبر **Citation-first legal AI**.

---

## 2) المعمارية عالية المستوى

```text
[ Web / Mobile / Admin Portal ]
              |
              v
-------------------------------------------------
|                 API LAYER                     |
| API Gateway | Auth | Rate Limit | Audit APIs  |
| Legal Q&A API | Contract Upload API           |
| Report API | Search API | Admin API           |
-------------------------------------------------
              |
              v
-------------------------------------------------
|                  AI LAYER                     |
| Mizan Engine | Fahs Engine                    |
| Legal Reasoning Service                       |
| Arabic NLP Processing                         |
| Summarization | Risk Detection | Classification|
| LLM Orchestrator | Prompt Manager             |
-------------------------------------------------
              |
              v
-------------------------------------------------
|                 RAG LAYER                     |
| Chunker | Embedding Service | Vector Search   |
| Retrieval Orchestrator | Re-ranking           |
| Citation Builder | Indexing Pipeline          |
-------------------------------------------------
              |
              v
-------------------------------------------------
|                 DATA LAYER                    |
| Saudi Laws DB | Contract Repository           |
| Vector DB | Metadata DB | OCR Output DB       |
| Audit Logs | Reports Storage | Object Storage |
-------------------------------------------------
```

---

## 3) API Layer

### 3.1 API Gateway
المهام:
- Routing.
- JWT/OAuth2 token verification.
- Rate limiting + IP throttling.
- Request/response logging.
- API versioning (v1/v2).

### 3.2 Identity & Access
- **RBAC Roles**:
  - `user`
  - `legal_reviewer`
  - `enterprise_admin`
  - `platform_admin`
- دعم MFA للحسابات الإدارية.
- Tenant isolation على مستوى المؤسسة.

### 3.3 Core APIs
1. `POST /v1/legal/qa`
   - إدخال: سؤال قانوني + نطاق اختياري.
   - إخراج: answer, explanation, sources, confidence.
2. `POST /v1/contracts/upload`
   - رفع PDF/DOCX/Images.
   - إنشاء job async للتحليل.
3. `GET /v1/reports/{id}`
   - جلب التقرير النهائي (JSON/PDF).
4. `POST /v1/search`
   - بحث keyword + semantic.
5. `POST /v1/admin/reindex`
   - إعادة فهرسة المستندات (إداري فقط).

---

## 4) AI Layer

### 4.1 Mizan Engine (Legal Knowledge Engine)
- تصنيف السؤال (عمل/إيجار/تجارة/شركات...).
- تحليل intent والسياق.
- توليد إجابة قانونية مبسطة + أساس نظامي.
- دعم شرح متعدد المستويات (مختصر/تفصيلي).

### 4.2 Fahs Engine (Contract Analysis Engine)
- استخراج النص من العقد.
- تقسيم البنود (Clause Segmentation).
- اكتشاف الالتزامات/الجزاءات/الغرامات.
- تقييم المخاطر لكل بند (Risk score).
- اقتراح تعديلات عملية.

### 4.3 Legal Reasoning Service
- يمنع الاستدلال خارج المصادر المسترجعة.
- يفرض schema إلزامي:
  - Summary
  - Legal Basis
  - Risk Level
  - Recommendation
  - Sources

### 4.4 LLM Orchestrator
- Route by complexity:
  - Simple Q&A → local model.
  - Complex reasoning → stronger fallback model.
- Chunked analysis for long contracts.
- Retry/fallback policy + timeout budgets.

### 4.5 Arabic NLP Processing
- Normalization (أ/إ/آ، ى/ي، ه/ة حسب القاعدة).
- Diacritics removal.
- Sentence splitting / tokenization.
- Domain-specific cleanup (legal terms).

---

## 5) RAG Layer

### 5.1 Indexing Pipeline
1. ingest
2. OCR (if scanned)
3. text cleaning
4. legal-domain classification
5. chunking
6. embeddings
7. write to vector DB + metadata DB

### 5.2 Retrieval Pipeline
1. query rewriting
2. retrieval from legal corpora
3. re-ranking by authority/relevance/recency
4. citation assembly
5. pass grounded context to LLM

### 5.3 Citation Builder
مخرجات موحّدة مثل:
- نظام العمل — المادة (80)
- اللائحة التنفيذية — الفقرة (2)
- بند (4.3) من العقد المرفوع

---

## 6) Data Layer

### قواعد البيانات المقترحة
- **PostgreSQL**: users, reports, workflows, findings.
- **Vector DB (Qdrant/Weaviate)**: embeddings.
- **Object Storage (S3-compatible)**: files and generated PDFs.
- **OCR Output Store**: extracted text + confidence + page map.
- **Audit Logs Store**: immutable compliance logs.

### كيانات أساسية (نماذج)
- `law_document`
- `law_article`
- `contract`
- `contract_clause`
- `analysis_report`
- `risk_finding`
- `source_reference`

---

## 7) تكامل ميزان + فاحص (قيمة المنصة)

### Workflow تكاملي
1. المستخدم يرفع العقد.
2. فاحص يستخرج البنود ويصنف المخاطر.
3. لكل بند عالي الخطورة، يرسل استعلامًا إلى ميزان:
   - "هل هذا البند متوافق مع النظام؟"
4. ميزان يرجع المادة النظامية + الحكم + تفسير مختصر.
5. التقرير النهائي يوحّد:
   - البند
   - مستوى المخاطر
   - التوافق النظامي
   - التوصية العملية
   - المراجع القانونية

---

## 8) Enterprise Services

### Security
- encryption at rest / in transit.
- KMS-managed keys.
- RBAC + ABAC (اختياري للمؤسسات الكبيرة).
- PII masking في logs.

### Observability
- API latency, error rates.
- OCR failure rate.
- retrieval precision proxies.
- token and cost telemetry per tenant.

### Job Processing
- Queue (analysis jobs).
- async report generation.
- retry with dead-letter queue.

### Governance
- prompt versioning.
- source trust ranking.
- document approval workflow.
- legal reviewer sign-off (اختياري).

---

## 9) Microservices Blueprint

- `api-gateway-service`
- `auth-service`
- `mizan-service`
- `fahs-service`
- `retrieval-service`
- `embedding-service`
- `indexing-service`
- `ocr-service`
- `report-service`
- `audit-service`
- `admin-service`

> للتشغيل السريع (MVP): يمكن بدء المشروع كـ **modular monolith** ثم فصل الخدمات تدريجيًا.

---

## 10) Stack مقترح

- **Backend/API:** FastAPI (Python) أو Node.js (NestJS).
- **RAG:** LlamaIndex أو LangChain + custom orchestrator.
- **Vector DB:** Qdrant.
- **Relational DB:** PostgreSQL.
- **Cache:** Redis.
- **Object Storage:** MinIO/S3.
- **OCR:** PaddleOCR / Tesseract + تحسينات عربية.
- **Models:** Ollama local + cloud fallback.
- **Infra:** Docker + Kubernetes + OpenTelemetry + Prometheus + Grafana.

---

## 11) إطار جودة المخرجات القانونية

### Guardrails
- لا إجابة بدون مصادر.
- منع الجزم عند انخفاض الثقة.
- إظهار تنبيه: "ليس استشارة قانونية ملزمة".

### Quality KPIs
- citation coverage ≥ 95%.
- hallucination rate ≤ 2% (via audit sampling).
- retrieval hit@k on benchmark legal set.
- contract risk precision/recall.

---

## 12) خطة تنفيذ مرحلية

### Phase 1 (MVP - 8 إلى 12 أسبوع)
- Legal Q&A baseline (ميزان).
- Contract upload + basic clause/risk extraction (فاحص).
- تقرير موحّد مع citations.

### Phase 2
- advanced risk models by contract type.
- enterprise auth + tenant controls.
- Admin governance dashboard.

### Phase 3
- human-in-the-loop legal review.
- Arabic legal benchmark suite.
- explainability dashboards + SLA tiers.

---

## 13) مخرجات واجهة المستخدم النهائية

1. **Legal Answer Card (ميزان)**
   - الإجابة المختصرة.
   - التفسير.
   - النص النظامي المرتبط.
   - درجة الثقة.

2. **Contract Risk Report (فاحص)**
   - ملخص تنفيذي.
   - قائمة البنود الحرجة.
   - توصيات صياغة.
   - الروابط النظامية الداعمة.

3. **Unified Compliance Report**
   - توافق العقد مع النظام.
   - خريطة مخاطر بالأولويات.
   - خطوات تعديل عملية قبل التوقيع.

---

## 14) ملاحظات قانونية وتشغيلية

- المنصة أداة دعم قرار وليست بديلًا عن المحامي.
- يلزم تحديث دوري لمصادر الأنظمة واللوائح.
- يجب الاحتفاظ بسجل تدقيق كامل لكل تحليل.

