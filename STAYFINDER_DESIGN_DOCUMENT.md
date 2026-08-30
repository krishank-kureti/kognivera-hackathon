# StayFinder - Hackathon Design Document
**Team:** Syntax Terrors  
**Problem Statement:** PS-02 — StayFinder (Hotel Discovery & Booking with AI)  
**Hackathon:** KogniVera 2026 | **Submission Due:** 2 September 2026

---

## Team Roles

| Member | Role | Responsibilities |
|--------|------|------------------|
| **A** | AI/ML Engineer | Natural language search, review summarization, embedding models, RAG pipeline |
| **B** | Backend Engineer | API development, database queries, auth, booking flow, AI integration endpoints |
| **C** | Frontend Engineer | React UI, map integration, search results, hotel details, checkout flow |
| **D** | UX/UI Designer | Wireframes, user journey, visual design, architecture diagrams, demo flow |

---

## 1. Problem Understanding

StayFinder is not just another hotel booking platform. The challenge explicitly demands **AI woven throughout the experience**, not bolted on as a single feature. Most teams will build a standard booking flow with one AI summary feature—that will fail.

**The real problem:** Travelers face information overload (25+ reviews per hotel), language barriers (40% of Indians prefer native languages), and generic search results that ignore their specific needs. Current platforms require manual filtering, English-only queries, and hours of review reading.

**Our solution:** An AI-first hotel discovery platform where:
- You speak naturally in any Indian language ("शांत 4-स्टार होटल पूल के साथ under ₹8000")
- AI instantly summarizes 7,500+ multilingual reviews into actionable pros/cons
- Results are personalized to YOUR travel style (family, business, adventure, luxury)
- An AI concierge answers property-specific questions grounded in real data (no hallucinations)

**Key insight:** The eval_nl_search_set (100 examples) and eval_queries (120 persona-based queries) are training goldmines most teams will ignore. We'll use them to build genuinely intelligent search, not keyword matching.

---

## 2. Scope

### ✅ What We WILL Build (24 Hours)

**Core MVP Flow (One City: Jaipur)**
1. Map-based hotel search with clustering
2. Keyword + natural language search (Hindi, English)
3. Filtered results (price, star, amenities, guest score, distance)
4. Hotel detail page (photos, amenities, policies, room types)
5. Room availability calendar + rate selection
6. Mock cart + checkout with confirmation

**AI Features (3 Live, Grounded in Data)**
1. **Natural Language Search** - Parse Hindi/English queries into structured filters using eval_nl_search_set training data
2. **AI Review Summaries** - Generate pros/cons from 7,500 multilingual reviews, persona-specific (family, business, couple)
3. **AI Concierge** - RAG-based Q&A answering property questions using hotel_policies, amenities, reviews (with citations)

**Multilingual Support**
- UI: English + Hindi (toggle)
- Search: Hindi, English query parsing
- Reviews: Summarize Hindi, Tamil, Telugu, Bengali, English reviews

**Personalization**
- Rank results by user travel_style and budget_band from users table
- Show "Recommended for you" badges with explanations

### ❌ What We Are DELIBERATELY Leaving Out

1. **Real payment gateway** - Mock checkout only (saves 4+ hours)
2. **User authentication** - Pre-seeded test users only (no signup/login flow)
3. **All 60 cities** - Focus on Jaipur only (best data coverage: 50 hotels, 1,250 reviews)
4. **All 4 AI features** - Skip personalized ranking v2 (stretch goal only if time permits)
5. **Admin dashboard** - No hotel management interface
6. **Email confirmations** - Show confirmation page only
7. **Mobile app** - Responsive web only
8. **Advanced map features** - Basic clustering, no route planning or street view
9. **Loyalty programs / wallets** - Out of scope for 24hr MVP
10. **Social sharing / reviews submission** - Read-only reviews

**Why this shows judgment:** We're going deep on 3 AI features with real grounding instead of shallow on all 4. Jaipur focus lets us perfect one city's experience rather than breaking 60 cities. Mock payments save critical hours for AI polish.

---

## 3. User Journey

### End-to-End Flow (Traveler Perspective)

```
┌─────────────────────────────────────────────────────────────────┐
│ SCREEN 1: Home/Search                                           │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │  [Search bar: "शांत 4-स्टार होटल पूल के साथ under ₹8000"]      │ │
│ │  [Map view toggle] [Language: EN/HI]                        │ │
│ │                                                             │ │
│ │  Quick filters: ★★★★★ | ₹₹ | Pool | Free WiFi | Family     │ │
│ │                                                             │ │
│ │  [MAP: Jaipur with clustered hotel pins]                    │ │
│ └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ SCREEN 2: Search Results                                        │
│ ┌──────────────────┐  ┌──────────────────────────────────────┐  │
│ │  Filters Panel   │  │  Hotel Card 1                        │  │
│ │  ☑ Price: 0-8000 │  │  ⭐ Raj Palace Hotel                 │  │
│ │  ☑ Stars: 4-5    │  │  ★★★★☆ (4.2) • ₹6,500/night         │  │
│ │  ☑ Amenities     │  │  🏊 Pool • 📶 WiFi • 🍽️ Restaurant   │  │
│ │     • Pool       │  │  🎯 2.3km from City Palace           │  │
│ │     • WiFi       │  │  ✅ AI: "Great for families, quiet   │  │
│ │  ☑ Guest Score   │  │     area, but breakfast limited"     │  │
│ │     4.0+         │  │  [View Details]                      │  │
│ │                  │  ├──────────────────────────────────────┤  │
│ │  [Apply] [Reset] │  │  Hotel Card 2                        │  │
│ └──────────────────┘  │  ⭐ Amber Heritage                   │  │
│                       │  ...                                 │  │
│                       └──────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ SCREEN 3: Hotel Detail                                          │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │  [Photo Gallery: 5 images]                                  │ │
│ │                                                             │ │
│ │  Raj Palace Hotel • ★★★★☆ • ₹6,500/night                   │ │
│ │  📍 2.3km from City Palace, Jaipur                          │ │
│ │                                                             │ │
│ │  ┌───────────────────────────────────────────────────────┐  │ │
│ │  │ 🤖 AI Summary (Family Persona)                        │  │ │
│ │  │  ✅ PROS: Spacious rooms, friendly staff, clean pool  │  │ │
│ │  │  ✗ CONS: Street noise after 10pm, limited veg options │  │ │
│ │  │  Based on 127 reviews (Hindi, English, Tamil)         │  │ │
│ │  └───────────────────────────────────────────────────────┘  │ │
│ │                                                             │ │
│ │  Amenities: 🏊 Pool 📶 WiFi 🍽️ Restaurant 🅿️ Parking       │ │
│ │  Policies: Check-in 2PM • Check-out 11AM • No pets         │ │
│ │                                                             │ │
│ │  ┌───────────────────────────────────────────────────────┐  │ │
│ │  │ 💬 Ask Concierge                                     │  │ │
│ │  │  "Is there a crib for toddlers?"                      │  │ │
│ │  │  → Yes, baby cribs available on request (free)        │  │ │
│ │  │  Source: Hotel Policies #47                           │  │ │
│ │  └───────────────────────────────────────────────────────┘  │ │
│ │                                                             │ │
│ │  Available Rooms:                                           │ │
│ │  ┌───────────────────────────────────────────────────────┐  │ │
│ │  │ Deluxe Room • ₹6,500 • 2 guests • Free cancellation  │  │ │
│ │  │ [Select Dates Calendar] [Add to Cart]                │  │ │
│ │  └───────────────────────────────────────────────────────┘  │ │
│ └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ SCREEN 4: Cart & Checkout                                       │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │  Your Booking                                               │ │
│ │  Raj Palace Hotel • Deluxe Room                             │ │
│ │  Check-in: 15 Oct 2026 • Check-out: 17 Oct 2026 (2 nights)  │ │
│ │  Guests: 2 Adults, 1 Child                                  │ │
│ │                                                             │ │
│ │  Room: ₹6,500 × 2 = ₹13,000                                │ │
│ │  Taxes & Fees: ₹1,950                                       │ │
│ │  Total: ₹14,950                                             │ │
│ │                                                             │ │
│ │  [Mock Payment Form]                                        │ │
│ │  Card: **** **** **** 4242 (Test)                           │ │
│ │  [Pay Now]                                                  │ │
│ └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
                              ↓
┌─────────────────────────────────────────────────────────────────┐
│ SCREEN 5: Confirmation                                          │
│ ┌─────────────────────────────────────────────────────────────┐ │
│ │  ✅ Booking Confirmed!                                      │ │
│ │  Booking ID: ST-2026-78492                                  │ │
│ │                                                             │ │
│ │  Raj Palace Hotel                                           │ │
│ │  15-17 October 2026 • Deluxe Room                           │ │
│ │                                                             │ │
│ │  Confirmation email sent to traveler@example.com            │ │
│ │  [Download Invoice] [Back to Search]                        │ │
│ └─────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────┘
```

---

## 4. Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           FRONTEND (React + Vite)                           │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────────────┐ │
│ │  Search     │  │  Results    │  │  Hotel      │  │  Checkout &         │ │
│ │  Component  │  │  Component  │  │  Detail     │  │  Confirmation       │ │
│ │             │  │  (Map +     │  │  (Gallery,  │  │                     │ │
│ │  - NL Input │  │   Cards)    │  │   AI Sum,   │  │  - Mock Payment     │ │
│ │  - Filters  │  │             │  │   Concierge)│  │  - Booking Summary  │ │
│ └─────────────┘  └─────────────┘  └─────────────┘  └─────────────────────┘ │
│         │                │                │                    │            │
│         └────────────────┴────────────────┴────────────────────┘            │
│                                    │                                        │
│                            REST API Calls                                   │
└────────────────────────────────────┼────────────────────────────────────────┘
                                     │
                                     ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         BACKEND (FastAPI - Python)                          │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│ │                        API Routes Layer                               │   │
│ │  /search          /hotels/{id}      /reviews/summarize               │   │
│ │  /filters         /rooms/{id}       /concierge/ask                   │   │
│ │  /bookings        /checkout         /users/{id}/preferences          │   │
│ └──────────────────────────────────────────────────────────────────────┘   │
│         │                    │                    │                         │
│         ▼                    ▼                    ▼                         │
│  ┌─────────────┐    ┌─────────────────┐    ┌──────────────────────┐        │
│ │  Database   │    │   AI Services   │    │   External APIs      │        │
│ │  Queries    │    │                 │    │                      │        │
│ │             │    │  ┌───────────┐  │    │  ┌────────────────┐  │        │
│ │  - Hotels   │    │  │ NL Search │  │    │  │ Gemini API     │  │        │
│ │  - Reviews  │◄───┤  │ Engine    │  │    │  │ (Summarization)│  │        │
│ │  - Users    │    │  └───────────┘  │    │  └────────────────┘  │        │
│ │  - Bookings │    │  ┌───────────┐  │    │                      │        │
│ │             │    │  │ Review    │  │    │  OR                  │        │
│ │             │    │  │ Summarizer│  │    │  ┌────────────────┐  │        │
│ │             │    │  └───────────┘  │    │  │ Phi-3-mini     │  │        │
│ │             │    │  ┌───────────┐  │    │  │ (Local LLM)    │  │        │
│ │             │    │  │ Concierge │  │    │  └────────────────┘  │        │
│ │             │    │  │ RAG       │  │    │                      │        │
│ │             │    │  └───────────┘  │    │                      │        │
│ └─────────────┘    └─────────────────┘    └──────────────────────┘        │
│         │                    │                                            │
│         └────────────────────┴────────────────────────────────────────────┘
│                              │
│                              ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         DATA LAYER (SQLite)                                 │
│  ┌──────────────────────────────────────────────────────────────────────┐   │
│ │                     PS-02.db (50K rows, 21 tables)                    │   │
│ │                                                                       │   │
│ │  Core Tables:                                                         │   │
│ │  • hotels (300) • hotel_reviews (7,500) • hotel_amenities (2,400)    │   │
│ │  • hotel_policies (300) • hotel_room_types (1,200)                   │   │
│ │  • inventory_calendar (15,030) • hotel_media (1,500)                 │   │
│ │                                                                       │   │
│ │  AI Training Data:                                                    │   │
│ │  • eval_nl_search_set (100) • eval_queries (120)                     │   │
│ │  • user_interactions (12,339) • users (1,200)                        │   │
│ │                                                                       │   │
│ │  Supporting: cities (60) • amenities (60) • languages (26)            │   │
│ └──────────────────────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Flow Diagram

### Data Flow: Natural Language Search → Personalized Results

```
User Input (Hindi/English)
    │
    ▼
┌─────────────────────────────────┐
│  Frontend: Search Bar           │
│  "शांत 4-स्टार होटल पूल के साथ   │
│   under ₹8000"                  │
└─────────────────────────────────┘
    │
    ▼ POST /api/search/nl
┌─────────────────────────────────┐
│  Backend: NL Search Engine      │
│                                 │
│  1. Detect language (hi/en)     │
│  2. Load eval_nl_search_set     │
│     few-shot examples           │
│  3. Prompt LLM to extract:      │
│     - price_max: 8000           │
│     - star_rating: 4            │
│     - amenities: ["pool"]       │
│     - keywords: ["quiet"]       │
│  4. Return structured filters   │
└─────────────────────────────────┘
    │
    ▼ GET /api/hotels?filters=...
┌─────────────────────────────────┐
│  Backend: Hotel Query           │
│                                 │
│  1. Query hotels table          │
│  2. Join hotel_amenities        │
│  3. Join hotel_reviews (avg)    │
│  4. Filter by city (Jaipur)     │
│  5. Apply extracted filters     │
│  6. Get 50 matching hotels      │
└─────────────────────────────────┘
    │
    ▼ GET /api/users/{id}/preferences
┌─────────────────────────────────┐
│  Backend: Personalization       │
│                                 │
│  1. Load user profile           │
│     travel_style: "family"      │
│     budget_band: "mid"          │
│  2. Load user_interactions      │
│     past clicks, dwell time     │
│  3. Re-rank hotels by:          │
│     - Family-friendly score     │
│     - Budget match              │
│     - Past behavior similarity  │
│  4. Add "Recommended" badges    │
└─────────────────────────────────┘
    │
    ▼ Parallel: AI Review Summaries
┌─────────────────────────────────┐
│  Backend: Review Summarizer     │
│  (For top 10 hotels)            │
│                                 │
│  1. Fetch 25 reviews/hotel      │
│  2. Detect languages (hi,ta,te) │
│  3. Translate to English        │
│  4. Prompt LLM:                 │
│     "Summarize pros/cons for    │
│      family travelers"          │
│  5. Return:                     │
│     pros: ["spacious", "pool"]  │
│     cons: ["noisy", "breakfast"]│
└─────────────────────────────────┘
    │
    ▼ Response to Frontend
┌─────────────────────────────────┐
│  Frontend: Results Page         │
│                                 │
│  - Map with clustered pins      │
│  - Hotel cards with AI summary  │
│  - "Recommended for families"   │
│  - Filters pre-applied          │
└─────────────────────────────────┘
```

---

## 6. Data Model Usage

### Tables We Will Use (18 of 21)

| Table | Rows | Usage |
|-------|------|-------|
| **hotels** | 300 | Core hotel info, location, star rating, contact |
| **hotel_reviews** | 7,500 | AI summarization source (multilingual) |
| **hotel_amenities** | 2,400 | Filter by amenities, concierge Q&A |
| **amenities** | 60 | Amenity names, categories, icons |
| **hotel_policies** | 300 | Concierge grounding (check-in, pets, cribs) |
| **hotel_room_types** | 1,200 | Room selection, capacity, bed types |
| **hotel_rate_plans** | 2,400 | Pricing, cancellation policies |
| **inventory_calendar** | 15,030 | Availability checking, date selection |
| **hotel_media** | 1,500 | Photos for gallery display |
| **cities** | 60 | Jaipur focus, location context |
| **countries** | 30 | Country names, codes |
| **languages** | 26 | Multilingual support mapping |
| **currencies** | 25 | INR display, formatting |
| **users** | 1,200 | Personalization (travel_style, budget_band, locale) |
| **user_interactions** | 12,339 | Behavioral ranking, implicit preferences |
| **eval_nl_search_set** | 100 | **CRITICAL**: NL search training/examples |
| **eval_queries** | 120 | Persona-based query testing |
| **bookings** | 0 → ~50 | Store mock bookings during demo |

### Tables We Will NOT Use (3 of 21)

| Table | Rows | Reason for Exclusion |
|-------|------|---------------------|
| **loyalty_programs** | 300 | Out of scope for 24hr MVP |
| **payment_methods** | 2,400 | Mock payment only, no real integration |
| **notifications** | 0 | No email/SMS system in MVP |

### Extensions (No Renaming)

We will ADD these minimal tables if needed:

```sql
-- Only if time permits (stretch goal)
CREATE TABLE IF NOT EXISTS ai_concierge_logs (
    id INTEGER PRIMARY KEY,
    hotel_id INTEGER,
    question TEXT,
    answer TEXT,
    sources_used TEXT,  -- JSON array of table references
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (hotel_id) REFERENCES hotels(id)
);

-- For demo tracking
CREATE TABLE IF NOT EXISTS demo_sessions (
    id INTEGER PRIMARY KEY,
    user_id INTEGER,
    search_query TEXT,
    hotels_viewed TEXT,  -- JSON array
    booking_completed BOOLEAN,
    timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
);
```

**Principle:** Extend the provided schema minimally. Never rename or modify existing tables—only read from them and add new ones for demo-specific tracking.

---

## 7. AI Features

### Feature 1: Natural Language Search (Hindi + English)

**What it does:** Converts conversational queries into structured filters.

**Example inputs:**
- "शांत 4-स्टार होटल पूल के साथ under ₹8000" → `{price_max: 8000, stars: 4, amenities: ['pool'], keywords: ['quiet']}`
- "family friendly hotel near city palace with free wifi" → `{location: 'city palace', amenities: ['wifi'], persona: 'family'}`
- "not near airport, 5 star, spa" → `{exclude_location: 'airport', stars: 5, amenities: ['spa']}`

**How we ground it:**
- Use `eval_nl_search_set` (100 examples) as few-shot training in prompts
- Each example contains: `query`, `expected_filters`, `difficulty`, `language`
- LLM extracts entities by pattern-matching against these examples
- Validate extracted filters against actual `amenities` table values
- Reject invalid filters (e.g., non-existent amenity) with fallback message

**Data sources:**
- `eval_nl_search_set` (training examples)
- `amenities` (valid amenity names)
- `cities` (landmark validation)
- `hotels` (price range validation)

**How we know it's working:**
- Test on 20 held-out queries from `eval_queries`
- Measure filter extraction accuracy (% of correct fields)
- Target: >85% accuracy on English, >75% on Hindi
- Demo success: User speaks naturally, correct filters auto-apply

**Implementation:**
```python
# Pseudo-code
def parse_nl_query(query: str, language: str):
    # Load few-shot examples from eval_nl_search_set
    examples = db.query("SELECT * FROM eval_nl_search_set WHERE language = ?", [language])
    
    prompt = f"""
    Given these examples of natural language to filter mappings:
    {format_examples(examples[:10])}
    
    Parse this query: "{query}"
    Extract: price_max, star_rating, amenities[], location, keywords[]
    """
    
    response = llm.generate(prompt)
    filters = validate_filters(response, db.amenities, db.cities)
    return filters
```

---

### Feature 2: AI Review Summaries (Multilingual Pros/Cons)

**What it does:** Summarizes 25+ reviews per hotel into balanced pros/cons, tailored to traveler persona.

**Example output:**
```
🤖 AI Summary for Families:
✅ PROS: Spacious rooms (mentioned 34 times), friendly staff (28), 
        clean pool (22), kids menu available (15)
✗ CONS: Street noise after 10pm (19), limited vegetarian breakfast (12),
        no elevator (8)
Based on 127 reviews in Hindi, English, Tamil
```

**How we ground it:**
- Fetch actual reviews from `hotel_reviews` for specific hotel
- Group by language code (`hi`, `ta`, `te`, `bn`, `en`)
- Translate non-English reviews using LLM (or skip if local model)
- Prompt LLM with persona context: "Summarize for family travelers"
- Require citations: "Mentioned in 34 reviews" (count from actual data)
- Include sentiment distribution: 78% positive, 15% neutral, 7% negative

**Data sources:**
- `hotel_reviews` (7,500 reviews, multilingual)
- `users` (persona context: travel_style)
- `languages` (language code mapping)
- `hotel_reviews.sentiment_score` (if available, else infer)

**How we know it's working:**
- Compare AI summary vs human-written summary for 10 hotels
- Measure coverage: Does summary mention top 3 pros/cons from reviews?
- Measure hallucination rate: % of claims not backed by review count
- Target: <5% hallucination rate, >90% coverage of key points
- Demo success: User sees accurate, balanced summary in 3 seconds

**Implementation:**
```python
def summarize_reviews(hotel_id: int, persona: str):
    reviews = db.query("""
        SELECT review_text, language_code, rating, sentiment_score
        FROM hotel_reviews 
        WHERE hotel_id = ? 
        ORDER BY helpful_count DESC 
        LIMIT 25
    """, [hotel_id])
    
    # Group by language, translate if needed
    translated = []
    for r in reviews:
        if r.language_code != 'en':
            translated.append(translate(r.review_text, r.language_code))
        else:
            translated.append(r.review_text)
    
    prompt = f"""
    Summarize these {len(translated)} reviews for {persona} travelers.
    
    Format:
    PROS: [list with mention counts]
    CONS: [list with mention counts]
    
    Reviews: {translated}
    
    Only include points mentioned in at least 3 reviews.
    Cite the number of reviews for each point.
    """
    
    summary = llm.generate(prompt)
    return parse_summary(summary)
```

---

### Feature 3: AI Concierge (Grounded Property Q&A)

**What it does:** Answers property-specific questions using ONLY hotel data, with citations.

**Example interactions:**
```
Q: "Is there airport pickup?"
A: "Yes, airport shuttle available for ₹800 per vehicle. 
    Source: Hotel Policies #47, Amenities: Airport Shuttle"

Q: "Is it good for a family with a toddler?"
A: "Yes, highly rated by families. Baby cribs available (free), 
    kids menu in restaurant, shallow pool section. 
    89% of family reviewers rated 4+ stars.
    Sources: Policies #47, Reviews (45 family mentions)"

Q: "Step-free access for wheelchair?"
A: "Partial. Ground floor rooms accessible, but no elevator to upper floors.
    Source: Amenities: Partial Accessibility, Policies #47"
```

**How we ground it (RAG - Retrieval Augmented Generation):**
1. **Retrieve:** Search hotel_policies, hotel_amenities, hotel_reviews for relevant info
2. **Augment:** Build context from retrieved documents with source IDs
3. **Generate:** LLM answers using ONLY provided context, cites sources
4. **Validate:** Check answer against original data (no contradictions)

**Data sources:**
- `hotel_policies` (check-in, pets, cribs, smoking, parking)
- `hotel_amenities` (pool, wifi, elevator, restaurant, accessibility)
- `hotel_reviews` (guest experiences, family mentions, noise complaints)
- `amenities` (amenity descriptions, categories)
- `hotel_room_types` (room features, bed types, capacity)

**How we know it's working:**
- Test on 30 predefined questions from `eval_queries`
- Measure grounding accuracy: % of answers with valid source citations
- Measure hallucination rate: % of claims not in retrieved context
- Target: 100% citation rate, <2% hallucination rate
- Demo success: User asks specific question, gets accurate answer with source

**Implementation:**
```python
def concierge_answer(hotel_id: int, question: str):
    # Retrieve relevant documents
    policies = db.query("SELECT * FROM hotel_policies WHERE hotel_id = ?", [hotel_id])
    amenities = db.query("""
        SELECT a.name, a.description 
        FROM hotel_amenities ha 
        JOIN amenities a ON ha.amenity_id = a.id 
        WHERE ha.hotel_id = ?
    """, [hotel_id])
    reviews = db.query("""
        SELECT review_text, tags 
        FROM hotel_reviews 
        WHERE hotel_id = ? AND tags LIKE '%family%'
        LIMIT 10
    """, [hotel_id])
    
    context = f"""
    Hotel Policies: {policies}
    Amenities: {amenities}
    Family Reviews: {reviews}
    """
    
    prompt = f"""
    Answer this question using ONLY the context below.
    If the answer is not in the context, say "I don't have that information."
    Always cite your source (e.g., "Source: Policy #47").
    
    Question: {question}
    Context: {context}
    """
    
    answer = llm.generate(prompt)
    
    # Validate: Check answer doesn't contradict context
    if not validate_grounding(answer, context):
        return "I'm not certain about that. Let me connect you with the hotel directly."
    
    return answer
```

---

### Feature 4 (Stretch): Personalized Ranking

**What it does:** Re-ranks search results based on user's travel style, budget, and past behavior.

**How we ground it:**
- Load user profile from `users` table (travel_style, budget_band, locale)
- Analyze `user_interactions` for past clicks, dwell time, bookings
- Compute similarity score between user profile and hotel attributes
- Re-rank results with explanation: "Recommended because you prefer adventure travel"

**Data sources:**
- `users` (travel_style, budget_band, locale)
- `user_interactions` (searches, clicks, dwell_time, bookings)
- `hotels` (star_rating, avg_price, amenities)
- `hotel_reviews` (guest_type tags, sentiment by traveler type)

**How we know it's working:**
- A/B test: Show personalized vs random ranking to 5 test users
- Measure click-through rate improvement
- Target: 30% higher CTR on personalized results
- Demo success: User sees "Recommended for you" badge, clicks recommended hotel

**Note:** This is a stretch goal. Implement only if Features 1-3 are complete by Hour 18.

---

## 8. Business Benefits

### Value to Travelers

| Problem | Our Solution | Impact |
|---------|-------------|--------|
| **Language barrier** | Hindi/Tamil/Telugu search & reviews | 40% of Indians can search in native language |
| **Review overload** | AI summaries (25 reviews → 3 bullet points) | Save 15+ minutes per hotel research |
| **Generic results** | Personalized ranking by travel style | 3x more relevant first-page results |
| **Uncertainty** | AI concierge with grounded answers | Instant answers to specific questions |
| **Filter fatigue** | Natural language search | No manual filter selection needed |

### Value to Business (Hotel Platform)

| Metric | Improvement | Why |
|--------|-------------|-----|
| **Conversion rate** | +25% | Faster decision-making with AI summaries |
| **Average order value** | +15% | Personalized upselling (room upgrades, amenities) |
| **Customer retention** | +40% | Personalization creates stickiness |
| **Support costs** | -60% | AI concierge handles 80% of common queries |
| **Market expansion** | +50% users | Multilingual support unlocks Tier 2/3 cities |

### Competitive Differentiation

- **MakeMyTrip/Booking.com:** Generic filters, English-only, no AI summaries
- **Our advantage:** Native language search, AI concierge, personalized ranking
- **Moat:** Proprietary training on eval_nl_search_set (100 examples competitors won't have)

---

## 9. Tech Stack

| Component | Technology | Why |
|-----------|-----------|-----|
| **Frontend** | React 18 + Vite | Fast dev, hot reload, component reusability |
| **Maps** | Leaflet + OpenStreetMap | Free, no API keys, lightweight vs Google Maps |
| **Backend** | FastAPI (Python) | Async support, auto docs, easy AI integration |
| **Database** | SQLite (PS-02.db) | Provided, zero setup, fast for 50K rows |
| **LLM (Primary)** | Google Gemini API | Best multilingual support (Hindi, Tamil, Telugu), cheap for hackathon |
| **LLM (Fallback)** | Phi-3-mini (local) | Offline backup if API fails, runs on laptop |
| **Embeddings** | sentence-transformers (multilingual) | For RAG retrieval, supports 50+ languages |
| **State Management** | Zustand (React) | Simpler than Redux, perfect for 24hr build |
| **Styling** | Tailwind CSS | Rapid UI development, responsive out-of-box |
| **Deployment** | Vercel (frontend) + Render (backend) | Free tiers, 5-min deploy, no DevOps overhead |
| **Version Control** | Git + GitHub | Standard collaboration, rollback safety |

**Why not...**
- ❌ Next.js: Overkill for 24hr, extra config time
- ❌ PostgreSQL: SQLite already provided, no migration needed
- ❌ LangChain: Too abstract, faster to write direct prompts
- ❌ Docker: Adds complexity, Render/Vercel handle containerization

---

## 10. 24-Hour Plan

### Phase 1: Foundation (Hours 0-6)

| Hour | Task | Owner(s) | Deliverable |
|------|------|----------|-------------|
| **0-1** | Setup: Clone repo, install deps, verify DB queries work | A, B | Working dev environment, DB connection test |
| **1-2** | Backend: Create FastAPI skeleton, health check, DB models | B | `/health`, `/hotels` endpoints working |
| **2-3** | Frontend: Initialize React app, routing, basic layout | C | Home, Search, Results pages scaffolded |
| **3-4** | Backend: Implement `/hotels` with filters (price, stars, amenities) | B | Filtered hotel list API |
| **4-5** | Frontend: Build search bar, filter panel, results grid | C | Functional search UI (keyword only) |
| **5-6** | Backend: Add `/hotels/{id}` detail endpoint with joins | B | Hotel detail API (amenities, policies, rooms) |
| **6** | **CHECKPOINT:** Can search Jaipur hotels, see details? | All | ✅ Core search → detail flow works |

### Phase 2: AI Integration (Hours 6-18)

| Hour | Task | Owner(s) | Deliverable |
|------|------|----------|-------------|
| **6-8** | AI Feature 1: NL Search engine using eval_nl_search_set | A | `/search/nl` endpoint parses Hindi/English queries |
| **8-10** | Frontend: Integrate NL search, show auto-applied filters | C | User types Hindi, filters update automatically |
| **10-12** | AI Feature 2: Review summarizer (top 10 hotels) | A | `/reviews/summarize?hotel_id=X&persona=family` |
| **12-14** | Frontend: Display AI summaries on hotel cards & detail page | C | Pros/cons visible on results & detail |
| **14-16** | AI Feature 3: Concierge RAG pipeline | A, B | `/concierge/ask?hotel_id=X&question=Y` |
| **16-17** | Frontend: Concierge chat UI on hotel detail page | C | Ask questions, get grounded answers |
| **17-18** | Testing: Run eval_queries, measure accuracy, fix edge cases | A, B | >85% NL search accuracy, <5% hallucination rate |
| **18** | **CHECKPOINT:** All 3 AI features live and tested? | All | ✅ AI features working end-to-end |

### Phase 3: Booking Flow + Polish (Hours 18-22)

| Hour | Task | Owner(s) | Deliverable |
|------|------|----------|-------------|
| **18-19** | Backend: Room availability, rate plans, cart endpoints | B | `/rooms/{id}/availability`, `/cart`, `/checkout` |
| **19-20** | Frontend: Date picker, room selection, cart, checkout form | C | Complete booking UI flow |
| **20-21** | Backend: Mock booking confirmation, generate booking ID | B | `/checkout` returns confirmation |
| **21-22** | Frontend: Confirmation page, invoice download (mock) | C | Booking confirmed screen |
| **22** | **CHECKPOINT:** Full search → book flow works? | All | ✅ MVP complete |

### Phase 4: Demo Prep (Hours 22-24)

| Hour | Task | Owner(s) | Deliverable |
|------|------|----------|-------------|
| **22-23** | Demo script: Write exact user journey, prepare test data | D | 5-min demo script with backup paths |
| **23-23:30** | Rehearse: Run through demo 3x, time it, fix bottlenecks | All | Smooth demo under 5 minutes |
| **23:30-24** | Backup plan: Record demo video, prepare screenshots | D | Video backup if live demo fails |
| **24** | **SUBMISSION READY** | All | ✅ Deployed, tested, rehearsed |

### Work Distribution Summary

| Team Member | Hours 0-6 | Hours 6-12 | Hours 12-18 | Hours 18-24 |
|-------------|-----------|------------|-------------|-------------|
| **A (AI)** | DB setup, eval data analysis | NL Search engine | Review summarizer, Concierge RAG | Testing, accuracy metrics |
| **B (Backend)** | API skeleton, hotel endpoints | NL search API, caching | Concierge API, personalization | Booking API, mock checkout |
| **C (Frontend)** | React setup, search UI | NL search integration | AI summary display, concierge UI | Booking flow, confirmation |
| **D (Design)** | Wireframes, architecture diagram | User flow refinement | Demo script v1, visual polish | Final demo script, rehearsal, backup video |

**Critical Rule:** Stop building at Hour 22. Last 2 hours are ONLY for demo prep. A broken demo = lost hackathon, even with perfect code.

---

## 11. Risks and Fallbacks

### Risk 1: LLM API Fails or Rate-Limited

**Probability:** Medium (30%)  
**Impact:** High (all AI features break)

**Fallback Plan:**
1. **Immediate:** Switch to Phi-3-mini local model (pre-downloaded)
2. **Degraded mode:** Show cached summaries for top 10 hotels (pre-generated Hour 12)
3. **Last resort:** Mock AI responses with disclaimer ("Demo mode: AI temporarily unavailable")

**Prevention:**
- Cache all AI responses during development (Hour 12)
- Pre-generate summaries for Jaipur's top 20 hotels
- Keep Phi-3-mini model downloaded and tested Hour 2

---

### Risk 2: Natural Language Search Accuracy <70%

**Probability:** Medium (25%)  
**Impact:** High (core AI feature fails demo)

**Fallback Plan:**
1. **Hybrid approach:** Combine LLM extraction with regex fallbacks for common patterns
2. **Simplified queries:** Support only 5 query templates (price + stars + amenity + location + keyword)
3. **Manual override:** Show extracted filters, let user adjust before search

**Prevention:**
- Test on 20 eval_queries every hour from Hour 8
- If accuracy <80% at Hour 14, activate hybrid approach
- Prepare 10 pre-tested Hindi/English queries for demo

---

### Risk 3: Booking Flow Takes Too Long, Cut Into AI Time

**Probability:** High (40%)  
**Impact:** Medium (MVP incomplete)

**Fallback Plan:**
1. **Simplify checkout:** Remove date picker, use hardcoded dates (Oct 15-17)
2. **Skip cart:** Direct "Book Now" from hotel detail
3. **Mock harder:** One-click booking with pre-filled user data

**Prevention:**
- Time-box booking flow to 4 hours max (Hours 18-22)
- At Hour 20, if booking not 80% done, activate simplified flow
- Remember: Judges care more about AI than perfect checkout

---

### Additional Mitigation Strategies

| Risk | Prevention | Detection | Response |
|------|------------|-----------|----------|
| Team burnout | 10-min breaks every 2 hours | Slower coding, mistakes | Force 15-min break, rotate tasks |
| Merge conflicts | Git branch per feature, merge hourly | Conflict errors | B resolves immediately, pair program |
| Demo environment fails | Test deployment Hour 20 | Deployment errors | Have local demo ready, screenshots |
| Time overrun | Hourly checkpoints, visible timer | Behind schedule | Cut scope (skip Feature 4, simplify UI) |

---

## 12. Multilingual Approach

### Supported Languages

| Language | Code | Where Used | Implementation |
|----------|------|------------|----------------|
| **Hindi** | hi | Search queries, UI labels, review summaries | Gemini API translation + summarization |
| **English** | en | Default UI, all features | Native LLM support |
| **Tamil** | ta | Review summarization (read-only) | Translate → summarize → display English |
| **Telugu** | te | Review summarization (read-only) | Translate → summarize → display English |
| **Bengali** | bn | Review summarization (read-only) | Translate → summarize → display English |

### Implementation Strategy

**Phase 1 (MVP - Hours 0-12):**
- UI: English only (labels, buttons, messages)
- Search: Hindi + English query parsing
- Reviews: Summarize all languages, display in English

**Phase 2 (Stretch - Hours 12-18, if time permits):**
- UI: Hindi toggle (all labels translated)
- Search: Display filters in user's language
- Reviews: Show summary in same language as majority of reviews

### Technical Approach

```python
# Language detection
from langdetect import detect

def detect_and_translate(text: str, target_lang='en'):
    source_lang = detect(text)
    if source_lang == target_lang:
        return text
    
    prompt = f"Translate from {source_lang} to {target_lang}: {text}"
    return llm.generate(prompt)

# Multilingual summarization
def summarize_multilingual(reviews: List[Review], persona: str):
    # Group by language
    by_lang = defaultdict(list)
    for r in reviews:
        by_lang[r.language_code].append(r.text)
    
    # Translate all to English
    all_english = []
    for lang, texts in by_lang.items():
        if lang != 'en':
            translated = [translate(t, 'en') for t in texts]
            all_english.extend(translated)
        else:
            all_english.extend(texts)
    
    # Summarize
    return summarize(all_english, persona)
```

### Demo Strategy

**Show multilingual capability early:**
1. Start demo with Hindi search query (impressive, differentiates)
2. Point out: "This hotel has reviews in Hindi, Tamil, English—all summarized"
3. Mention: "UI available in Hindi (toggle in header)" if implemented

**If short on time:**
- Prioritize Hindi + English only
- Mention other languages as "supported in backend, UI coming soon"

---

## 13. XR Device Declaration

**Not Applicable**

This problem statement (PS-02 StayFinder) does not require AR/VR functionality. Our solution is a responsive web application accessible on desktop, tablet, and mobile browsers.

**If judges ask:** "XR features are out of scope for this hotel booking challenge. We focused our 24 hours on AI features that solve real problems: language barriers, review overload, and personalized discovery."

---

## 14. Appendix: Demo Script (For Rehearsal)

### 5-Minute Demo Flow

**Minute 0-1: Problem & Solution**
> "Hi, we're Syntax Terrors. Today 40% of Indian travelers struggle with English-only hotel sites and spend 20+ minutes reading reviews. StayFinder solves this with AI woven throughout."

**Minute 1-2: Natural Language Search (Feature 1)**
> [Type in Hindi] "शांत 4-स्टार होटल पूल के साथ under ₹8000"
> [Show filters auto-apply] "Our AI parsed this into structured filters instantly."
> [Show map results] "Results personalized for mid-budget travelers seeking quiet."

**Minute 2-3: AI Review Summaries (Feature 2)**
> [Click hotel] "Instead of reading 127 reviews, see our AI summary."
> [Point to pros/cons] "Families love the pool and staff, but note street noise. Based on Hindi, Tamil, English reviews."

**Minute 3-4: AI Concierge (Feature 3)**
> [Ask] "Is there a crib for toddlers?"
> [Show answer] "Yes, free cribs available. Source: Hotel Policy #47."
> [Ask] "Airport pickup?"
> [Show answer] "Yes, ₹800 per vehicle. Source: Amenities."

**Minute 4-5: Booking & Close**
> [Select dates, room] "Pick dates, choose room, mock checkout."
> [Confirmation] "Booking confirmed in 3 clicks total."
> "We built search → book with 3 live AI features in 24 hours. Thank you!"

### Backup Paths

**If NL Search fails:**
> "Let me show our pre-loaded Hindi query..." [Use saved search]

**If Concierge slow:**
> "Here's a cached response..." [Show pre-generated answer]

**If time running short:**
> Skip booking flow, end at concierge demo

---

## 15. Submission Checklist

Before exporting to PDF:

- [ ] All 14 sections present
- [ ] Scope includes what we're NOT building
- [ ] 24-hour plan has hours AND names assigned
- [ ] Architecture diagram legible at 100% zoom
- [ ] AI features explain grounding and measurement
- [ ] Demo script included (Appendix)
- [ ] Team member names and roles filled in
- [ ] Exported to PDF, reopened to verify rendering
- [ ] File size <25 MB (compress images if needed)
- [ ] Filename: `SyntaxTerrors_Design_v1.pdf`

**Final step:** Have someone outside the team read it. If they can't explain what you're building in 30 seconds, simplify.

---

*Document Version: 1.0*  
*Last Updated: [Date]*  
*Team: Syntax Terrors*  
*Contact: [Team Lead Email]*
