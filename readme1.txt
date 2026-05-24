# JENU-GUMPU ANDROID APP - COMPLETE SOP (Standard Operating Procedure)

## PROJECT: Honey Producer's Collective App with GenAI Integration

---

## TABLE OF CONTENTS

1. Executive Summary
2. Technology Stack
3. Frontend Architecture & Screen Specifications
4. Backend Architecture & API Specifications
5. Database Schema
6. GenAI Integration Details
7. Screen-by-Screen Detailed Specifications
8. Navigation Flow & Screen Connections
9. Development Phases
10. Testing Requirements
11. Deployment Guidelines

---

## 1. EXECUTIVE SUMMARY

**App Name:** Jenu-Gumpu  
**Platform:** Android (Minimum SDK: 24, Target SDK: 34)  
**Primary Language:** Kotlin  
**Architecture:** MVVM (Model-View-ViewModel)  
**Primary Users:** Tribal and rural honey hunters  
**Core Objective:** Empower honey producers through technology, AI-driven insights, and collective bargaining power

---

## 2. TECHNOLOGY STACK

### FRONTEND TECHNOLOGIES
```
- Language: Kotlin
- UI Framework: Jetpack Compose / XML Layouts
- Architecture: MVVM with LiveData/Flow
- Navigation: Jetpack Navigation Component
- Dependency Injection: Hilt/Dagger
- Image Loading: Coil/Glide
- Charts: MPAndroidChart
- Language Support: Android String Resources (Kannada + English)
```

### BACKEND TECHNOLOGIES
```
- Primary: Firebase (Authentication, Firestore, Storage)
- Local Database: Room Database
- API Communication: Retrofit + OkHttp
- GenAI Integration: 
  * Google Gemini API / OpenAI API
  * ML Kit for on-device processing
- Real-time Updates: Firebase Cloud Messaging
- Analytics: Firebase Analytics
```

### ADDITIONAL TOOLS
```
- Version Control: Git
- Build System: Gradle
- Testing: JUnit, Espresso, Mockito
- CI/CD: GitHub Actions / Firebase App Distribution
```

---

## 3. FRONTEND ARCHITECTURE

### APP STRUCTURE
```
JenuGumpu/
├── app/
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/jenugumpu/
│   │   │   │   ├── data/
│   │   │   │   │   ├── local/
│   │   │   │   │   │   ├── dao/
│   │   │   │   │   │   ├── database/
│   │   │   │   │   │   └── entities/
│   │   │   │   │   ├── remote/
│   │   │   │   │   │   ├── api/
│   │   │   │   │   │   └── dto/
│   │   │   │   │   └── repository/
│   │   │   │   ├── domain/
│   │   │   │   │   ├── model/
│   │   │   │   │   └── usecase/
│   │   │   │   ├── presentation/
│   │   │   │   │   ├── screens/
│   │   │   │   │   │   ├── auth/
│   │   │   │   │   │   ├── dashboard/
│   │   │   │   │   │   ├── harvest/
│   │   │   │   │   │   ├── grading/
│   │   │   │   │   │   ├── pricing/
│   │   │   │   │   │   ├── batch/
│   │   │   │   │   │   ├── collective/
│   │   │   │   │   │   ├── calculator/
│   │   │   │   │   │   └── profile/
│   │   │   │   │   ├── viewmodel/
│   │   │   │   │   └── components/
│   │   │   │   ├── di/
│   │   │   │   └── utils/
│   │   │   ├── res/
│   │   │   │   ├── layout/
│   │   │   │   ├── drawable/
│   │   │   │   ├── values/
│   │   │   │   ├── values-kn/
│   │   │   │   └── navigation/
```

### DESIGN PRINCIPLES
- **Material Design 3** guidelines
- **Accessibility-first** approach with large touch targets
- **Icon-heavy** interface for low-literacy users
- **Minimal text**, maximum visual guidance
- **Offline-first** architecture with sync capabilities

---

## 4. BACKEND ARCHITECTURE

### FIREBASE STRUCTURE

#### **Authentication**
```
- Phone Number Authentication (Primary)
- Email/Password (Optional)
- Anonymous Sign-in (for testing)
```

#### **Firestore Collections**
```
/users/{userId}
  - name: String
  - phone: String
  - location: GeoPoint
  - groupId: String
  - language: String (en/kn)
  - createdAt: Timestamp
  - profileImage: String

/harvests/{harvestId}
  - userId: String
  - date: Timestamp
  - location: GeoPoint
  - quantity: Number (kg)
  - qualityGrade: String
  - moistureLevel: Number
  - batchId: String
  - images: Array<String>
  - aiSuggestions: Map

/batches/{batchId}
  - batchNumber: String
  - userId: String
  - harvestIds: Array<String>
  - totalQuantity: Number
  - averageGrade: String
  - createdAt: Timestamp
  - status: String (active/sold)

/groups/{groupId}
  - name: String
  - members: Array<String>
  - totalStock: Number
  - location: String
  - createdAt: Timestamp

/prices/{priceId}
  - date: Timestamp
  - localPrice: Number
  - cityRetailPrice: Number
  - wholesalePrice: Number
  - location: String
  - source: String

/ai_logs/{logId}
  - userId: String
  - query: String
  - response: String
  - timestamp: Timestamp
  - type: String (grading/pricing/help)
```

#### **Cloud Storage Structure**
```
/users/{userId}/profile/
/harvests/{harvestId}/images/
/batches/{batchId}/labels/
```

---

## 5. DATABASE SCHEMA (ROOM DATABASE - LOCAL)

### ENTITIES

#### **UserEntity**
```kotlin
@Entity(tableName = "users")
data class UserEntity(
    @PrimaryKey val userId: String,
    val name: String,
    val phone: String,
    val location: String,
    val groupId: String?,
    val language: String,
    val syncStatus: Boolean
)
```

#### **HarvestEntity**
```kotlin
@Entity(tableName = "harvests")
data class HarvestEntity(
    @PrimaryKey val harvestId: String,
    val userId: String,
    val date: Long,
    val locationLat: Double,
    val locationLng: Double,
    val quantity: Float,
    val qualityGrade: String?,
    val moistureLevel: Float?,
    val batchId: String?,
    val imagePaths: String, // JSON array
    val aiSuggestions: String?, // JSON
    val syncStatus: Boolean
)
```

#### **BatchEntity**
```kotlin
@Entity(tableName = "batches")
data class BatchEntity(
    @PrimaryKey val batchId: String,
    val batchNumber: String,
    val userId: String,
    val totalQuantity: Float,
    val averageGrade: String,
    val createdAt: Long,
    val status: String,
    val syncStatus: Boolean
)
```

#### **PriceEntity**
```kotlin
@Entity(tableName = "prices")
data class PriceEntity(
    @PrimaryKey val priceId: String,
    val date: Long,
    val localPrice: Float,
    val cityRetailPrice: Float,
    val wholesalePrice: Float,
    val location: String,
    val lastUpdated: Long
)
```

---

## 6. GenAI INTEGRATION DETAILS

### AI FEATURES IMPLEMENTATION

#### **1. Quality Grading Assistant**
```
API: Google Gemini Pro Vision / OpenAI GPT-4 Vision
Input: Honey image + metadata
Output: 
  - Quality grade (A+, A, B, C)
  - Color classification
  - Texture analysis
  - Improvement suggestions
  - Estimated market value
```

**Prompt Template:**
```
Analyze this honey sample image and provide:
1. Quality grade based on color, clarity, and texture
2. Estimated moisture content (if visible)
3. Suggested retail price category
4. Quality improvement tips
Consider: Image shows [color description], collected from [location], quantity [X kg]
Respond in JSON format with fields: grade, color, clarity, suggestions, priceCategory
```

#### **2. Pricing Insights**
```
API: Gemini Pro / GPT-4
Input: Location, quantity, grade, season
Output:
  - Current market trends
  - Price comparison (local vs. city)
  - Best selling time
  - Negotiation tips
```

**Prompt Template:**
```
Provide honey pricing insights for:
- Location: [location]
- Quality Grade: [grade]
- Quantity: [quantity] kg
- Season: [current season]

Suggest:
1. Fair market price range
2. Best selling strategy
3. Price negotiation points
4. Demand forecast
Format response as JSON with fields: priceRange, strategy, tips, forecast
```

#### **3. AI Chatbot Helper**
```
API: Gemini Pro / GPT-3.5
Purpose: Answer farmer queries
Topics:
  - Honey collection best practices
  - Quality improvement
  - Storage guidelines
  - Market information
  - App usage help
```

**System Prompt:**
```
You are an AI assistant for tribal honey collectors in Karnataka, India.
Provide helpful, simple advice on:
- Honey harvesting techniques
- Quality improvement
- Fair pricing
- Storage methods
Use simple language, be culturally sensitive, and focus on practical solutions.
Prefer local/traditional knowledge when relevant.
```

---

## 7. SCREEN-BY-SCREEN DETAILED SPECIFICATIONS

### SCREEN 1: SPLASH SCREEN
**File:** `SplashScreen.kt`

**Purpose:** App loading and initialization

**UI Elements:**
- App logo (Honey drop with bee icon)
- App name "Jenu-Gumpu" in Kannada and English
- Tagline: "ಜೇನು ಉತ್ಪಾದಕರ ಸಮೂಹ" / "Honey Producer's Collective"
- Loading indicator

**Logic:**
```kotlin
- Check network connectivity
- Verify user authentication status
- Load local database
- Sync pending data with Firebase
- Initialize GenAI SDK
- Navigate to Login or Dashboard after 2 seconds
```

**Navigation:**
- → Login Screen (if not authenticated)
- → Dashboard (if authenticated)

---

### SCREEN 2: LOGIN / REGISTRATION SCREEN
**File:** `AuthScreen.kt`

**Purpose:** User authentication and registration

**UI Components:**

**Tab 1 - Login:**
- Phone number input field (with country code +91)
- OTP input field (appears after sending OTP)
- "Send OTP" button
- "Verify & Login" button
- Language toggle (English/ಕನ್ನಡ)

**Tab 2 - Register:**
- Full name input
- Phone number input
- Location selector (dropdown with districts)
- Profile photo upload (optional)
- "Register" button

**Logic:**
```kotlin
class AuthViewModel : ViewModel() {
    fun sendOTP(phoneNumber: String)
    fun verifyOTP(otp: String)
    fun registerUser(name: String, phone: String, location: String)
    fun uploadProfileImage(imageUri: Uri)
}
```

**Firebase Integration:**
```kotlin
// Phone Auth
FirebaseAuth.getInstance()
    .verifyPhoneNumber(phoneNumber, callbacks)

// User Creation
FirebaseFirestore.getInstance()
    .collection("users")
    .document(userId)
    .set(userMap)
```

**Navigation:**
- → Dashboard (after successful authentication)

**Error Handling:**
- Invalid phone number format
- OTP timeout
- Network failure (show retry option)

---

### SCREEN 3: DASHBOARD (HOME SCREEN)
**File:** `DashboardScreen.kt`

**Purpose:** Central hub for all app features

**UI Layout:**

**Header Section:**
- Welcome message: "ಸ್ವಾಗತ, [User Name]"
- Profile icon (top right)
- Notification bell icon
- Current date and location

**Quick Stats Cards:**
```
Card 1: Total Harvests This Month
  - Icon: Honey pot
  - Value: XX kg
  - Trend indicator

Card 2: Current Market Price
  - Icon: Rupee symbol
  - Value: ₹XXX/kg
  - Price change percentage

Card 3: My Total Batches
  - Icon: Box
  - Value: XX batches
  - Status: Active/Sold

Card 4: Collective Stock
  - Icon: Group
  - Value: XXX kg
  - Group name
```

**Main Action Buttons (Icon + Text):**
1. 📝 **Log Harvest** (Primary - Large, Green)
2. ⭐ **Grade Honey** (Secondary)
3. 💰 **Check Prices** (Secondary)
4. 📦 **Create Batch** (Secondary)
5. 👥 **Collective Stock** (Secondary)
6. 🧮 **Calculate Profit** (Secondary)
7. 🤖 **AI Helper** (Floating action button)

**Bottom Navigation Bar:**
- 🏠 Home (Active)
- 📊 My Records
- 💬 Community
- 👤 Profile

**Logic:**
```kotlin
class DashboardViewModel : ViewModel() {
    val userStats: LiveData<UserStats>
    val recentHarvests: LiveData<List<Harvest>>
    val currentPrices: LiveData<PriceData>
    val collectiveStock: LiveData<Float>
    
    fun loadDashboardData()
    fun syncWithFirebase()
}
```

**Navigation:**
- → Harvest Log Screen (Log Harvest button)
- → Grading Screen (Grade Honey button)
- → Price Monitor Screen (Check Prices button)
- → Batch Creation Screen (Create Batch button)
- → Collective Stock Screen (Collective Stock button)
- → Profit Calculator Screen (Calculate Profit button)
- → AI Chat Screen (AI Helper FAB)
- → My Records Screen (Bottom nav)
- → Profile Screen (Profile icon)

---

### SCREEN 4: HARVEST LOG SCREEN
**File:** `HarvestLogScreen.kt`

**Purpose:** Record new honey collection

**UI Components:**

**Form Fields:**
1. **Date Picker**
   - Default: Today
   - Icon: Calendar
   - Format: DD/MM/YYYY

2. **Location Input**
   - Auto-detect GPS button
   - Manual entry option
   - Map preview (optional)
   - Fields: Area name, District

3. **Quantity Input**
   - Number input (kg)
   - Large numeric keypad
   - Unit displayed: "ಕೆಜಿ / kg"

4. **Photo Upload**
   - Camera button (primary)
   - Gallery button (secondary)
   - Multiple image support (up to 3)
   - Image preview thumbnails

5. **Notes (Optional)**
   - Text area
   - Voice input button
   - Placeholder: "ಹೆಚ್ಚುವರಿ ಮಾಹಿತಿ / Additional notes"

**Action Buttons:**
- "💾 Save Harvest" (Primary, Green)
- "🤖 Get AI Suggestions" (Secondary)
- "❌ Cancel" (Tertiary)

**Logic:**
```kotlin
class HarvestLogViewModel : ViewModel() {
    fun saveHarvest(harvest: HarvestData)
    fun getCurrentLocation(): Location
    fun uploadImages(imageUris: List<Uri>): List<String>
    fun getAISuggestions(images: List<String>, metadata: HarvestMetadata)
}

// Save to Room DB
harvestDao.insert(harvestEntity)

// Upload to Firebase
firestoreRef.collection("harvests").add(harvestMap)

// Call GenAI for suggestions
geminiAPI.analyzeHoney(imageUrls, metadata)
```

**Validation:**
- Quantity must be > 0
- Date cannot be in future
- At least one image required for AI grading

**Navigation:**
- → Grading Screen (if "Get AI Suggestions" clicked)
- → Dashboard (after saving)

**Background Operations:**
- Auto-save draft every 30 seconds
- Queue upload if offline
- Compress images before upload

---

### SCREEN 5: HONEY GRADING SCREEN
**File:** `GradingScreen.kt`

**Purpose:** Assess honey quality and get AI recommendations

**UI Sections:**

**Section 1 - Visual Guide**
- Color reference chart
  - Extra Light Amber (A+)
  - Light Amber (A)
  - Amber (B)
  - Dark Amber (C)
- Clarity indicators
- Texture examples

**Section 2 - Manual Grading**
- Color selector (Radio buttons with color swatches)
- Clarity slider (Clear ↔ Cloudy)
- Texture selector (Liquid / Creamy / Crystallized)
- Moisture estimation (Mock slider: 15-25%)

**Section 3 - AI Analysis**
- Upload/Capture image button
- Image preview
- "🤖 Analyze with AI" button
- Results display area:
  ```
  AI Grade: A+
  Confidence: 95%
  
  Color: Light Golden
  Clarity: Excellent
  Estimated Moisture: 18%
  
  Suggestions:
  • Store in cool, dry place
  • Suitable for premium market
  • Estimated price: ₹450-550/kg
  ```

**Section 4 - Save Grade**
- Link to harvest dropdown (select existing harvest)
- "Save Grade" button

**Logic:**
```kotlin
class GradingViewModel : ViewModel() {
    fun analyzeImage(imageUri: Uri): GradingResult
    fun saveGrade(harvestId: String, grade: GradeData)
    fun getGradingHistory(): List<GradeData>
}

// GenAI Call
suspend fun analyzeHoneyQuality(imageUrl: String): AIGradingResult {
    val prompt = """
    Analyze this honey sample and provide:
    - Quality grade (A+, A, B, C)
    - Color classification
    - Clarity rating (1-10)
    - Estimated moisture content
    - Market price category
    - Quality improvement tips
    """
    
    return geminiVisionAPI.generateContent(
        prompt = prompt,
        image = imageUrl
    ).parseJSON()
}
```

**Navigation:**
- → Harvest List (select harvest to grade)
- → Dashboard (after saving)
- → Price Monitor (view suggested prices)

---

### SCREEN 6: PRICE MONITORING SCREEN
**File:** `PriceMonitorScreen.kt`

**Purpose:** Display current honey market prices and trends

**UI Components:**

**Header:**
- Last updated timestamp
- Refresh button
- Location selector (My Area / Karnataka / India)

**Price Cards:**

**Card 1 - Local Market**
```
🏪 Local Market Price
₹XXX per kg
↑ 5% from last week
Source: Local traders
Updated: Today
```

**Card 2 - City Retail**
```
🏙️ City Retail Price
₹XXX per kg
Range: ₹XXX - ₹XXX
City: Bangalore
Updated: 2 days ago
```

**Card 3 - Wholesale**
```
📦 Wholesale Price
₹XXX per kg
Bulk (100+ kg)
Updated: Today
```

**Price Comparison Chart:**
- Line graph showing price trends (last 30 days)
- X-axis: Dates
- Y-axis: Price (₹)
- Multiple lines: Local, City, Wholesale

**Grade-wise Pricing Table:**
```
Grade | Local  | City   | Wholesale
A+    | ₹XXX   | ₹XXX   | ₹XXX
A     | ₹XXX   | ₹XXX   | ₹XXX
B     | ₹XXX   | ₹XXX   | ₹XXX
C     | ₹XXX   | ₹XXX   | ₹XXX
```

**AI Price Insights:**
- "🤖 Ask AI about pricing" button
- Shows: Best time to sell, negotiation tips, demand forecast

**Logic:**
```kotlin
class PriceViewModel : ViewModel() {
    val currentPrices: LiveData<PriceData>
    val priceHistory: LiveData<List<PricePoint>>
    val aiInsights: LiveData<String>
    
    fun refreshPrices()
    fun getPriceComparison(grade: String): PriceComparison
    fun getAIPriceInsights(grade: String, quantity: Float)
}

// Firestore Query
firestore.collection("prices")
    .orderBy("date", Query.Direction.DESCENDING)
    .limit(30)
    .get()

// GenAI Price Insights
suspend fun getPricingAdvice(context: PricingContext): String {
    val prompt = """
    Current market data:
    - Grade: ${context.grade}
    - Quantity: ${context.quantity} kg
    - Location: ${context.location}
    - Season: ${context.season}
    
    Provide:
    1. Fair price range
    2. Best selling strategy
    3. Market demand analysis
    4. Negotiation tips
    """
    return geminiAPI.generateText(prompt)
}
```

**Navigation:**
- → AI Chat (for detailed pricing advice)
- → Dashboard

**Auto-refresh:**
- Pull to refresh
- Auto-refresh every 6 hours

---

### SCREEN 7: BATCH CREATION SCREEN
**File:** `BatchCreationScreen.kt`

**Purpose:** Combine harvests into batches for selling

**UI Sections:**

**Section 1 - Batch Information**
- Auto-generated Batch Number: "JG-YYYYMMDD-XXX"
- Date: Auto-filled (today)
- Editable batch name field

**Section 2 - Select Harvests**
- List of available (unbatched) harvests
- Checkboxes for selection
- Each item shows:
  ```
  ☐ Harvest on DD/MM/YYYY
    Location: [Area]
    Quantity: X kg
    Grade: A+
  ```
- "Select All" option
- Search/filter by date or grade

**Section 3 - Batch Summary**
```
📦 Batch Summary
Total Quantity: XX.X kg
Average Grade: A
Harvests included: X
Estimated Value: ₹XX,XXX
```

**Section 4 - Batch Label Generation**
- "Generate Label" button
- Preview:
  ```
  ╔══════════════════════════╗
  ║   JENU-GUMPU HONEY      ║
  ║   Batch: JG-20240115-001 ║
  ║   Grade: A+              ║
  ║   Quantity: 25 kg        ║
  ║   Producer: [Name]       ║
  ║   Location: [Area]       ║
  ║   Date: 15/01/2024       ║
  ╚══════════════════════════╝
  ```
- Download/Share label button

**Action Buttons:**
- "✅ Create Batch" (Primary)
- "📄 Preview Label" (Secondary)
- "❌ Cancel"

**Logic:**
```kotlin
class BatchViewModel : ViewModel() {
    val availableHarvests: LiveData<List<Harvest>>
    val selectedHarvests: MutableLiveData<List<String>>
    
    fun createBatch(batchData: BatchData): String
    fun generateBatchNumber(): String
    fun calculateAverageGrade(harvestIds: List<String>): String
    fun generateLabel(batchId: String): Bitmap
}

// Create Batch
fun createBatch(harvests: List<String>): String {
    val batchId = UUID.randomUUID().toString()
    val batch = BatchEntity(
        batchId = batchId,
        batchNumber = generateBatchNumber(),
        userId = currentUser.id,
        totalQuantity = harvests.sumOf { it.quantity },
        averageGrade = calculateAvgGrade(harvests),
        createdAt = System.currentTimeMillis(),
        status = "active"
    )
    
    // Update harvests with batch ID
    harvests.forEach { harvest ->
        harvestDao.updateBatchId(harvest.id, batchId)
    }
    
    batchDao.insert(batch)
    syncWithFirebase(batch)
    
    return batchId
}
```

**Navigation:**
- → My Batches Screen (after creation)
- → Dashboard

---

### SCREEN 8: COLLECTIVE STOCK SCREEN
**File:** `CollectiveStockScreen.kt`

**Purpose:** View and manage group's combined honey stock

**UI Components:**

**Header:**
- Group name
- Total members count
- "Invite Member" button

**Total Stock Card:**
```
👥 Collective Stock
━━━━━━━━━━━━━━━━━━
Total: XXX kg
Grade A+: XX kg
Grade A: XX kg
Grade B: XX kg
Grade C: XX kg

Est. Value: ₹X,XX,XXX
```

**Member Contributions:**
- List view of all members
- Each item:
  ```
  👤 [Member Name]
  Contribution: XX kg (XX%)
  Grade breakdown
  Last updated: DD/MM/YYYY
  ```
- Sort by: Contribution / Name / Date

**Group Analytics:**
- Pie chart (contribution by member)
- Bar chart (grade distribution)

**Collective Bargaining Tool:**
```
💬 Bargaining Power
With XXX kg total stock:
• Minimum asking price: ₹XXX/kg
• Fair group price: ₹XXX/kg
• Target bulk price: ₹XXX/kg

🤖 AI Negotiation Tips:
[AI-generated suggestions]
```

**Action Buttons:**
- "📊 View Detailed Report"
- "🤝 Start Group Sale"
- "💬 Group Chat"

**Logic:**
```kotlin
class CollectiveViewModel : ViewModel() {
    val groupData: LiveData<GroupData>
    val memberContributions: LiveData<List<MemberStock>>
    val totalStock: LiveData<Float>
    val gradeDistribution: LiveData<Map<String, Float>>
    
    fun loadGroupData(groupId: String)
    fun calculateCollectiveValue(): Float
    fun getAIBargainingTips(totalQuantity: Float, avgGrade: String)
}

// Firestore aggregation
fun getGroupStock(groupId: String): GroupStock {
    val members = firestore.collection("users")
        .whereEqualTo("groupId", groupId)
        .get()
    
    val totalStock = members.documents.sumOf { user ->
        getBatches(user.id).sumOf { it.totalQuantity }
    }
    
    return GroupStock(
        groupId = groupId,
        totalQuantity = totalStock,
        memberCount = members.size(),
        gradeBreakdown = calculateGradeDistribution(members)
    )
}
```

**Navigation:**
- → Member Profile (tap on member)
- → Group Chat Screen
- → Dashboard

---

### SCREEN 9: PROFIT CALCULATOR SCREEN
**File:** `ProfitCalculatorScreen.kt`

**Purpose:** Calculate profit margins and ROI

**UI Layout:**

**Input Section:**

**Cost Inputs:**
1. Collection Cost (₹/kg)
   - Labor
   - Equipment
   - Transportation

2. Processing Cost (₹/kg)
   - Filtering
   - Packaging
   - Storage

3. Total Quantity (kg)

**Selling Price Input:**
- Selling price per kg (₹)
- Or select from current market prices

**Calculation Display:**
```
━━━━━━━━━━━━━━━━━━━━━━━━━
📊 CALCULATION RESULTS
━━━━━━━━━━━━━━━━━━━━━━━━━

Total Cost:        ₹XX,XXX
Total Revenue:     ₹XX,XXX
─────────────────────────
Gross Profit:      ₹XX,XXX
Profit Margin:     XX%
ROI:               XX%

Per kg Analysis:
Cost per kg:       ₹XXX
Selling price:     ₹XXX
Profit per kg:     ₹XXX
```

**Comparison Tool:**
```
💡 Price Scenarios

Scenario 1 (Current):
Sell at ₹XXX/kg → Profit: ₹XX,XXX

Scenario 2 (City Retail):
Sell at ₹XXX/kg → Profit: ₹XX,XXX (+XX%)

Scenario 3 (Collective Sale):
Sell at ₹XXX/kg → Profit: ₹XX,XXX (+XX%)
```

**AI Recommendations:**
- "🤖 Get AI profit optimization tips"
- Shows: Best pricing strategy, cost reduction ideas

**Action Buttons:**
- "💾 Save Calculation"
- "📤 Share Report"
- "🔄 Reset"

**Logic:**
```kotlin
class ProfitCalculatorViewModel : ViewModel() {
    fun calculateProfit(
        quantity: Float,
        costPerKg: Float,
        sellingPrice: Float
    ): ProfitResult
    
    fun generateScenarios(baseData: CalculationData): List<Scenario>
    fun getOptimizationTips(data: CalculationData): String
}

data class ProfitResult(
    val totalCost: Float,
    val totalRevenue: Float,
    val grossProfit: Float,
    val profitMargin: Float,
    val roi: Float,
    val profitPerKg: Float
)

// Calculation
fun calculateProfit(inputs: Inputs): ProfitResult {
    val totalCost = inputs.quantity * inputs.costPerKg
    val totalRevenue = inputs.quantity * inputs.sellingPrice
    val profit = totalRevenue - totalCost
    val margin = (profit / totalRevenue) * 100
    val roi = (profit / totalCost) * 100
    
    return ProfitResult(
        totalCost = totalCost,
        totalRevenue = totalRevenue,
        grossProfit = profit,
        profitMargin = margin,
        roi = roi,
        profitPerKg = profit / inputs.quantity
    )
}
```

**Navigation:**
- → Dashboard
- → Price Monitor (to update selling price)

---

### SCREEN 10: AI CHAT HELPER SCREEN
**File:** `AIChatScreen.kt`

**Purpose:** Interactive AI assistant for guidance

**UI Components:**

**Chat Interface:**
- Message list (RecyclerView/LazyColumn)
- Message types:
  - User message (right-aligned, blue)
  - AI response (left-aligned, green)
  - System message (centered, gray)

**Input Area:**
- Text input field
- Voice input button (microphone icon)
- Send button
- Suggested questions chips (below input)

**Suggested Questions:**
```
"How to improve honey quality?"
"Best time to harvest?"
"Current market prices?"
"Storage tips"
"How to grade honey?"
"Collective selling benefits?"
```

**Message Examples:**

**User:** "How to check honey purity?"

**AI Response:**
```
🐝 Honey Purity Tests:

1. Water Test:
   Drop honey in water. Pure honey 
   settles at bottom.

2. Thumb Test:
   Put drop on thumb. If it spreads,
   it's impure.

3. Paper Test:
   Put on blotting paper. Pure honey
   won't get absorbed.

4. Flame Test:
   Dip matchstick in honey and light.
   Pure honey will burn.

Would you like more details on any test?
```

**Quick Actions in Chat:**
- "📊 Show current prices"
- "📝 Log new harvest"
- "🎯 Grade my honey"

**Logic:**
```kotlin
class AIChatViewModel : ViewModel() {
    val messages: LiveData<List<ChatMessage>>
    
    fun sendMessage(text: String)
    fun processVoiceInput(audioData: ByteArray)
    fun getSuggestedQuestions(): List<String>
}

// GenAI Integration
suspend fun getAIResponse(userMessage: String): String {
    val conversationHistory = chatDao.getRecentMessages(limit = 10)
    
    val systemPrompt = """
    You are a helpful assistant for honey producers in Karnataka.
    Provide practical, simple advice in both English and Kannada when needed.
    Focus on:
    - Quality improvement
    - Fair pricing
    - Best practices
    - Market insights
    Keep responses concise and actionable.
    """
    
    val response = geminiAPI.chat(
        systemPrompt = systemPrompt,
        conversationHistory = conversationHistory,
        userMessage = userMessage
    )
    
    // Save to DB
    chatDao.insert(ChatMessage(
        sender = "user",
        message = userMessage,
        timestamp = System.currentTimeMillis()
    ))
    
    chatDao.insert(ChatMessage(
        sender = "ai",
        message = response,
        timestamp = System.currentTimeMillis()
    ))
    
    return response
}
```

**Features:**
- Message history (last 100 messages)
- Copy message option
- Share conversation
- Clear chat
- Language switching (English ↔ Kannada)

**Navigation:**
- → Dashboard (back button)
- → Relevant screens (via quick actions)

---

### SCREEN 11: MY RECORDS SCREEN
**File:** `RecordsScreen.kt`

**Purpose:** View history of harvests and batches

**UI Tabs:**

**Tab 1 - Harvests**
- List of all harvests (newest first)
- Filter options:
  - By date range
  - By grade
  - By location
  - By batch status (batched/unbatched)

**Harvest Card:**
```
📅 15 Jan 2024
📍 Bandipur Forest
⚖️ 12.5 kg
⭐ Grade: A+
📦 Batch: JG-20240115-001
[View Details →]
```

**Tab 2 - Batches**
- List of all batches
- Filter: Active / Sold / All

**Batch Card:**
```
📦 Batch JG-20240115-001
📅 Created: 15 Jan 2024
⚖️ Total: 25 kg
⭐ Grade: A
💰 Status: Active
[View Details →]
```

**Tab 3 - Analytics**
```
📊 Production Analytics

This Month:
Total Harvested: XX kg
Total Batches: X
Average Grade: A

This Year:
Total: XXX kg
Revenue: ₹XX,XXX
Growth: +XX%

📈 Charts:
- Monthly production (bar chart)
- Grade distribution (pie chart)
- Revenue trend (line chart)
```

**Detail View (on card click):**
- Full harvest/batch information
- Edit button (if not batched/sold)
- Delete button
- Share button
- View on map (for harvest location)

**Logic:**
```kotlin
class RecordsViewModel : ViewModel() {
    val harvests: LiveData<List<Harvest>>
    val batches: LiveData<List<Batch>>
    val analytics: LiveData<AnalyticsData>
    
    fun getHarvests(filter: FilterCriteria): List<Harvest>
    fun getBatches(filter: FilterCriteria): List<Batch>
    fun calculateAnalytics(period: TimePeriod): AnalyticsData
}

// Room queries
@Query("SELECT * FROM harvests ORDER BY date DESC")
fun getAllHarvests(): LiveData<List<HarvestEntity>>

@Query("SELECT * FROM harvests WHERE date BETWEEN :startDate AND :endDate")
fun getHarvestsByDateRange(startDate: Long, endDate: Long): List<HarvestEntity>

// Analytics calculation
fun calculateMonthlyStats(userId: String): MonthlyStats {
    val startOfMonth = getStartOfMonth()
    val harvests = harvestDao.getHarvestsByDateRange(startOfMonth, now())
    
    return MonthlyStats(
        totalQuantity = harvests.sumOf { it.quantity },
        totalHarvests = harvests.size,
        averageGrade = calculateAvgGrade(harvests),
        topLocation = findMostProductiveLocation(harvests)
    )
}
```

**Navigation:**
- → Harvest Detail Screen
- → Batch Detail Screen
- → Dashboard

---

### SCREEN 12: PROFILE SCREEN
**File:** `ProfileScreen.kt`

**Purpose:** User profile and app settings

**UI Sections:**

**Profile Header:**
- Profile photo (editable)
- Name
- Phone number
- Location
- Member since date

**Statistics Card:**
```
📊 My Statistics
━━━━━━━━━━━━━━━━━
Total Harvests: XXX
Total Produced: XXX kg
Active Batches: X
Group: [Group Name]
```

**Menu Options:**

1. **👤 Edit Profile**
   - Update name
   - Change photo
   - Update location

2. **👥 My Group**
   - View group details
   - Leave group / Join group
   - Invite members

3. **🌐 Language / ಭಾಷೆ**
   - English / ಕನ್ನಡ toggle

4. **🔔 Notifications**
   - Price alerts ON/OFF
   - Group updates ON/OFF
   - AI tips ON/OFF

5. **💾 Data & Sync**
   - Last synced: [timestamp]
   - Manual sync button
   - Offline mode toggle
   - Clear cache

6. **❓ Help & Support**
   - Tutorial videos
   - FAQs
   - Contact support
   - App version

7. **📄 Legal**
   - Privacy policy
   - Terms of service

8. **🚪 Logout**

**Logic:**
```kotlin
class ProfileViewModel : ViewModel() {
    val userProfile: LiveData<User>
    val userStats: LiveData<UserStats>
    
    fun updateProfile(updates: Map<String, Any>)
    fun changeLanguage(languageCode: String)
    fun updateNotificationSettings(settings: NotificationSettings)
    fun syncData()
    fun logout()
}

// Update profile
fun updateUserProfile(userId: String, updates: Map<String, Any>) {
    // Update Room DB
    userDao.update(userId, updates)
    
    // Update Firestore
    firestore.collection("users")
        .document(userId)
        .update(updates)
}

// Logout
fun logout() {
    FirebaseAuth.getInstance().signOut()
    clearLocalData()
    navigateToLogin()
}
```

**Navigation:**
- → Edit Profile Screen
- → Group Management Screen
- → Settings Screen
- → Help Screen
- → Login Screen (after logout)

---

## 8. NAVIGATION FLOW & SCREEN CONNECTIONS

### NAVIGATION GRAPH
```
graph TD
    A[Splash] --> B{Authenticated?}
    B -->|No| C[Login/Register]
    B -->|Yes| D[Dashboard]
    C --> D
    
    D --> E[Harvest Log]
    D --> F[Grading]
    D --> G[Price Monitor]
    D --> H[Batch Creation]
    D --> I[Collective Stock]
    D --> J[Profit Calculator]
    D --> K[AI Chat]
    D --> L[My Records]
    D --> M[Profile]
    
    E --> F
    E --> H
    F --> G
    H --> L
    
    L --> N[Harvest Detail]
    L --> O[Batch Detail]
    
    M --> P[Edit Profile]
    M --> Q[Settings]
    M --> R[Help]
```

### SCREEN TRANSITION MATRIX

| From Screen | To Screen | Trigger | Animation |
|------------|-----------|---------|-----------|
| Splash | Login | Not authenticated | Fade |
| Splash | Dashboard | Authenticated | Fade |
| Login | Dashboard | Successful login | Slide left |
| Dashboard | Harvest Log | Button click | Slide up |
| Dashboard | Grading | Button click | Slide up |
| Dashboard | Price Monitor | Button click | Slide left |
| Dashboard | Batch Creation | Button click | Slide left |
| Dashboard | Collective Stock | Button click | Slide left |
| Dashboard | Profit Calculator | Button click | Slide up |
| Dashboard | AI Chat | FAB click | Slide up (bottom sheet) |
| Dashboard | My Records | Bottom nav | Fade |
| Dashboard | Profile | Profile icon | Slide left |
| Harvest Log | Grading | "Get AI Suggestions" | Slide left |
| Harvest Log | Dashboard | Save | Slide down |
| Grading | Price Monitor | "View Prices" | Slide left |
| Batch Creation | My Records | Create success | Slide left |
| My Records | Harvest Detail | Card click | Slide left |
| Profile | Edit Profile | Edit button | Slide left |
| Profile | Login | Logout | Fade + Clear stack |
| Any Screen | AI Chat | AI Helper FAB | Bottom sheet |
| Any Screen | Dashboard | Back button | Slide right/down |

---

## 9. DEVELOPMENT PHASES

### PHASE 1: FOUNDATION (Week 1-2)
**Objective:** Set up project structure and basic authentication

**Tasks:**
1. Create Android project with Kotlin
2. Set up MVVM architecture
3. Configure Firebase (Auth, Firestore, Storage)
4. Implement Room Database
5. Create base classes (BaseActivity, BaseViewModel)
6. Set up dependency injection (Hilt)
7. Implement Splash Screen
8. Implement Login/Registration with Phone Auth
9. Create navigation graph
10. Set up string resources (English + Kannada)

**Deliverables:**
- Working authentication flow
- Basic project structure
- Firebase integration complete

---

### PHASE 2: CORE FEATURES (Week 3-5)
**Objective:** Implement main app functionalities

**Tasks:**
1. Build Dashboard UI
2. Implement Harvest Log Screen
   - Form validation
   - Image upload
   - GPS integration
   - Room DB save
3. Implement Grading Screen
   - Visual guides
   - Manual grading
4. Implement Price Monitor Screen
   - Firestore integration
   - Charts (MPAndroidChart)
5. Implement Batch Creation
   - Multi-select harvests
   - Label generation
6. Implement Collective Stock Screen
   - Group data aggregation
7. Implement Profit Calculator
   - Calculation logic
   - Scenarios
8. Implement My Records Screen
   - List views
   - Analytics charts

**Deliverables:**
- All core screens functional
- Local data storage working
- Firebase sync operational

---

### PHASE 3: GenAI INTEGRATION (Week 6-7)
**Objective:** Integrate AI features

**Tasks:**
1. Set up Google Gemini API / OpenAI API
2. Implement image-based grading
   - Image upload to AI
   - Parse JSON response
   - Display results
3. Implement pricing insights
   - Context-based prompts
   - Display recommendations
4. Implement AI Chat Helper
   - Chat UI
   - Conversation history
   - Voice input integration
5. Add AI suggestions throughout app
6. Implement error handling for AI calls
7. Add loading states and placeholders

**Deliverables:**
- Working AI grading
- AI chat functional
- AI pricing insights operational

---

### PHASE 4: POLISH & OPTIMIZATION (Week 8-9)
**Objective:** Improve UX and performance

**Tasks:**
1. Implement offline functionality
   - Offline data access
   - Queue sync when online
2. Add animations and transitions
3. Implement notifications
   - Price alerts
   - Group updates
4. Optimize image handling
   - Compression
   - Caching
5. Performance optimization
   - Lazy loading
   - Pagination
6. Add loading skeletons
7. Implement error handling
   - Network errors
   - Validation errors
   - User-friendly messages
8. Add haptic feedback
9. Accessibility improvements
   - Content descriptions
   - Large text support

**Deliverables:**
- Smooth, polished UI
- Offline mode working
- Optimized performance

---

### PHASE 5: TESTING & DEPLOYMENT (Week 10)
**Objective:** Test thoroughly and deploy

**Tasks:**
1. Unit testing
   - ViewModels
   - Use cases
   - Utils
2. UI testing (Espresso)
   - Critical user flows
3. Integration testing
   - Firebase operations
   - API calls
4. Manual testing
   - Different devices
   - Different screen sizes
   - Different Android versions
5. Fix bugs
6. Prepare release build
7. Generate signed APK/AAB
8. Deploy to Firebase App Distribution (beta)
9. Deploy to Google Play Console (internal testing)
10. Create user documentation

**Deliverables:**
- Tested, stable app
- Beta release
- Documentation

---

## 10. TESTING REQUIREMENTS

### UNIT TESTS

**ViewModels:**
```kotlin
class DashboardViewModelTest {
    @Test
    fun `loadDashboardData should fetch user stats`()
    
    @Test
    fun `syncWithFirebase should update local data`()
}

class HarvestLogViewModelTest {
    @Test
    fun `saveHarvest should validate quantity greater than zero`()
    
    @Test
    fun `getCurrentLocation should return valid coordinates`()
}

class ProfitCalculatorViewModelTest {
    @Test
    fun `calculateProfit should return correct margin`()
    
    @Test
    fun `profit margin of 20 percent when cost 100 and selling 120`()
}
```

**Use Cases:**
```kotlin
class GradeCalculationUseCaseTest {
    @Test
    fun `average grade of A+ and A should be A`()
}

class BatchCreationUseCaseTest {
    @Test
    fun `batch should combine harvests correctly`()
}
```

---

### INTEGRATION TESTS

**Database:**
```kotlin
@Test
fun `insert harvest and retrieve by id`()

@Test
fun `update harvest batch id`()

@Test
fun `delete harvest should cascade to batch`()
```

**Firebase:**
```kotlin
@Test
fun `authenticate user with phone number`()

@Test
fun `upload harvest to Firestore`()

@Test
fun `sync local changes to cloud`()
```

---

### UI TESTS (ESPRESSO)

**Critical Flows:**
```kotlin
@Test
fun `complete harvest log flow`() {
    // Open harvest log
    // Enter quantity
    // Select location
    // Upload image
    // Save
    // Verify saved in list
}

@Test
fun `create batch from multiple harvests`() {
    // Navigate to batch creation
    // Select 3 harvests
    // Create batch
    // Verify batch in list
}

@Test
fun `AI chat interaction`() {
    // Open AI chat
    // Send message
    // Verify response received
    // Check message in history
}
```

---

### MANUAL TESTING CHECKLIST

**Authentication:**
- [ ] Phone number validation
- [ ] OTP send and verify
- [ ] Registration with valid data
- [ ] Login persistence
- [ ] Logout clears session

**Harvest Log:**
- [ ] Form validation
- [ ] Image upload from camera
- [ ] Image upload from gallery
- [ ] GPS location detection
- [ ] Manual location entry
- [ ] Save offline
- [ ] Sync when online

**Grading:**
- [ ] Manual grading selection
- [ ] AI analysis with image
- [ ] Display AI results
- [ ] Save grade to harvest
- [ ] Error handling for failed AI call

**Pricing:**
- [ ] Display current prices
- [ ] Refresh prices
- [ ] Chart rendering
- [ ] Grade-wise prices
- [ ] AI insights generation

**Batch:**
- [ ] Select multiple harvests
- [ ] Calculate totals correctly
- [ ] Generate batch number
- [ ] Create batch
- [ ] Generate label PDF

**Collective:**
- [ ] Display group data
- [ ] Calculate total stock
- [ ] Show member contributions
- [ ] Charts render correctly

**Calculator:**
- [ ] Input validation
- [ ] Calculation accuracy
- [ ] Scenarios display
- [ ] Save calculation

**AI Chat:**
- [ ] Send text message
- [ ] Receive response
- [ ] Voice input
- [ ] Suggested questions work
- [ ] Quick actions navigate correctly

**Profile:**
- [ ] Display user info
- [ ] Edit profile
- [ ] Upload profile photo
- [ ] Change language
- [ ] Notification settings
- [ ] Sync data
- [ ] Logout

**General:**
- [ ] Offline mode works
- [ ] Data syncs when back online
- [ ] Notifications appear
- [ ] Language switching works
- [ ] App doesn't crash on network loss
- [ ] Back button navigation
- [ ] Bottom navigation works

---

## 11. DEPLOYMENT GUIDELINES

### BUILD CONFIGURATION

**build.gradle (app level):**
```gradle
android {
    compileSdk 34
    
    defaultConfig {
        applicationId "com.jenugumpu.app"
        minSdk 24
        targetSdk 34
        versionCode 1
        versionName "1.0.0"
        
        multiDexEnabled true
    }
    
    buildTypes {
        debug {
            applicationIdSuffix ".debug"
            debuggable true
            minifyEnabled false
        }
        
        release {
            minifyEnabled true
            shrinkResources true
            proguardFiles getDefaultProguardFile('proguard-android-optimize.txt'), 'proguard-rules.pro'
            
            // Signing config
            signingConfig signingConfigs.release
        }
    }
    
    buildFeatures {
        compose true
        viewBinding true
    }
}

dependencies {
    // Kotlin
    implementation "org.jetbrains.kotlin:kotlin-stdlib:1.9.0"
    
    // Android X
    implementation "androidx.core:core-ktx:1.12.0"
    implementation "androidx.appcompat:appcompat:1.6.1"
    implementation "androidx.constraintlayout:constraintlayout:2.1.4"
    implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0"
    implementation "androidx.lifecycle:lifecycle-livedata-ktx:2.7.0"
    
    // Navigation
    implementation "androidx.navigation:navigation-fragment-ktx:2.7.6"
    implementation "androidx.navigation:navigation-ui-ktx:2.7.6"
    
    // Room
    implementation "androidx.room:room-runtime:2.6.1"
    implementation "androidx.room:room-ktx:2.6.1"
    kapt "androidx.room:room-compiler:2.6.1"
    
    // Firebase
    implementation platform('com.google.firebase:firebase-bom:32.7.0')
    implementation 'com.google.firebase:firebase-auth-ktx'
    implementation 'com.google.firebase:firebase-firestore-ktx'
    implementation 'com.google.firebase:firebase-storage-ktx'
    implementation 'com.google.firebase:firebase-analytics-ktx'
    implementation 'com.google.firebase:firebase-messaging-ktx'
    
    // Hilt
    implementation "com.google.dagger:hilt-android:2.48"
    kapt "com.google.dagger:hilt-compiler:2.48"
    
    // Retrofit
    implementation "com.squareup.retrofit2:retrofit:2.9.0"
    implementation "com.squareup.retrofit2:converter-gson:2.9.0"
    
    // Image loading
    implementation "io.coil-kt:coil:2.5.0"
    
    // Charts
    implementation "com.github.PhilJay:MPAndroidChart:v3.1.0"
    
    // GenAI
    implementation "com.google.ai.client.generativeai:generativeai:0.1.1"
    
    // Testing
    testImplementation "junit:junit:4.13.2"
    androidTestImplementation "androidx.test.ext:junit:1.1.5"
    androidTestImplementation "androidx.test.espresso:espresso-core:3.5.1"
}
```

---

### RELEASE CHECKLIST

**Pre-Release:**
- [ ] All features tested and working
- [ ] No critical bugs
- [ ] All strings externalized
- [ ] Kannada translations complete
- [ ] Icons and images optimized
- [ ] ProGuard rules configured
- [ ] Signing key generated and secured
- [ ] Version code incremented
- [ ] Version name updated
- [ ] Change log prepared

**Build:**
- [ ] Generate signed AAB (App Bundle)
- [ ] Test release build on multiple devices
- [ ] Verify all features work in release mode
- [ ] Check app size (target < 50 MB)
- [ ] Verify no debug logs in release

**Play Store Assets:**
- [ ] App icon (512x512 PNG)
- [ ] Feature graphic (1024x500)
- [ ] Screenshots (phone: min 2, tablet: min 1)
- [ ] Short description (< 80 characters)
- [ ] Full description
- [ ] Privacy policy URL
- [ ] Content rating questionnaire completed
- [ ] App category selected

**Deployment:**
- [ ] Upload to Internal Testing track
- [ ] Add internal testers
- [ ] Collect feedback
- [ ] Fix critical issues
- [ ] Promote to Closed Beta
- [ ] Beta testing (1-2 weeks)
- [ ] Review feedback
- [ ] Final bug fixes
- [ ] Promote to Production
- [ ] Monitor crash reports
- [ ] Monitor user reviews

---

### MONITORING & ANALYTICS

**Firebase Analytics Events:**
```kotlin
// Track screen views
firebaseAnalytics.logEvent(FirebaseAnalytics.Event.SCREEN_VIEW) {
    param(FirebaseAnalytics.Param.SCREEN_NAME, "dashboard")
    param(FirebaseAnalytics.Param.SCREEN_CLASS, "DashboardActivity")
}

// Track harvest log
firebaseAnalytics.logEvent("harvest_logged") {
    param("quantity", quantity)
    param("has_image", hasImage)
}

// Track AI usage
firebaseAnalytics.logEvent("ai_grading_used") {
    param("grade_result", grade)
}

// Track batch creation
firebaseAnalytics.logEvent("batch_created") {
    param("harvest_count", harvestCount)
    param("total_quantity", totalQuantity)
}
```

**Crashlytics:**
```kotlin
// Log custom events
FirebaseCrashlytics.getInstance().log("Harvest log screen loaded")

// Set user identifier
FirebaseCrashlytics.getInstance().setUserId(userId)

// Record exceptions
try {
    // risky operation
} catch (e: Exception) {
    FirebaseCrashlytics.getInstance().recordException(e)
}
```

---

## 12. API INTEGRATION SPECIFICATIONS

### GENAI API ENDPOINTS

**Google Gemini Integration:**

```kotlin
// Configuration
object GeminiConfig {
    const val API_KEY = "YOUR_GEMINI_API_KEY"
    const val MODEL = "gemini-pro-vision"
    const val BASE_URL = "https://generativelanguage.googleapis.com/v1/"
}

// Service Interface
interface GeminiService {
    @POST("models/{model}:generateContent")
    suspend fun analyzeImage(
        @Path("model") model: String,
        @Body request: GeminiRequest,
        @Query("key") apiKey: String
    ): GeminiResponse
    
    @POST("models/gemini-pro:generateContent")
    suspend fun generateText(
        @Body request: TextRequest,
        @Query("key") apiKey: String
    ): TextResponse
}

// Request/Response Models
data class GeminiRequest(
    val contents: List<Content>,
    val generationConfig: GenerationConfig?
)

data class Content(
    val parts: List<Part>
)

data class Part(
    val text: String? = null,
    val inlineData: InlineData? = null
)

data class InlineData(
    val mimeType: String,
    val data: String // Base64 encoded
)

data class GeminiResponse(
    val candidates: List<Candidate>
)

data class Candidate(
    val content: Content,
    val finishReason: String
)
```

**Implementation:**

```kotlin
class GeminiRepository @Inject constructor(
    private val service: GeminiService
) {
    suspend fun analyzeHoneyQuality(imageUri: Uri): GradingResult {
        val base64Image = encodeImageToBase64(imageUri)
        
        val request = GeminiRequest(
            contents = listOf(
                Content(
                    parts = listOf(
                        Part(text = GRADING_PROMPT),
                        Part(inlineData = InlineData(
                            mimeType = "image/jpeg",
                            data = base64Image
                        ))
                    )
                )
            ),
            generationConfig = GenerationConfig(
                temperature = 0.4,
                topP = 1.0,
                topK = 32,
                maxOutputTokens = 1024
            )
        )
        
        val response = service.analyzeImage(
            model = "gemini-pro-vision",
            request = request,
            apiKey = GeminiConfig.API_KEY
        )
        
        return parseGradingResponse(response)
    }
    
    private companion object {
        const val GRADING_PROMPT = """
            Analyze this honey sample image and provide a detailed quality assessment.
            
            Return your analysis in the following JSON format:
            {
                "grade": "A+/A/B/C",
                "color": "description of color",
                "clarity": "1-10 rating",
                "texture": "liquid/creamy/crystallized",
                "estimatedMoisture": "percentage",
                "suggestions": ["tip1", "tip2", "tip3"],
                "priceCategory": "premium/standard/economy",
                "confidence": "0-100"
            }
            
            Consider factors like:
            - Color uniformity and appeal
            - Clarity and transparency
            - Visible impurities
            - Crystallization state
            - Overall appearance quality
        """
    }
}
```

---

### PRICE DATA API (Mock/Simulated)

Since real-time government APIs might not be available, create a simulated price service:

```kotlin
// Mock Price Service
class PriceDataService {
    fun getCurrentPrices(location: String): PriceData {
        // Simulate API call
        return PriceData(
            localPrice = generateLocalPrice(),
            cityRetailPrice = generateCityPrice(),
            wholesalePrice = generateWholesalePrice(),
            lastUpdated = System.currentTimeMillis(),
            location = location,
            trend = calculateTrend()
        )
    }
    
    private fun generateLocalPrice(): Float {
        // Base price with some randomization
        val basePrice = 250f
        val variation = Random.nextFloat() * 50 - 25 // ±25
        return basePrice + variation
    }
    
    private fun generateCityPrice(): Float {
        val basePrice = 450f
        val variation = Random.nextFloat() * 100 - 50
        return basePrice + variation
    }
    
    private fun generateWholesalePrice(): Float {
        val basePrice = 320f
        val variation = Random.nextFloat() * 60 - 30
        return basePrice + variation
    }
}
```

---

## 13. CODE SAMPLES

### SAMPLE: Harvest Log ViewModel

```kotlin
@HiltViewModel
class HarvestLogViewModel @Inject constructor(
    private val harvestRepository: HarvestRepository,
    private val locationProvider: LocationProvider,
    private val imageUploader: ImageUploader,
    private val geminiRepository: GeminiRepository
) : ViewModel() {

    private val _harvestState = MutableLiveData<HarvestState>()
    val harvestState: LiveData<HarvestState> = _harvestState
    
    private val _location = MutableLiveData<Location>()
    val location: LiveData<Location> = _location
    
    fun getCurrentLocation() {
        viewModelScope.launch {
            _harvestState.value = HarvestState.Loading
            try {
                val location = locationProvider.getCurrentLocation()
                _location.value = location
                _harvestState.value = HarvestState.LocationRetrieved(location)
            } catch (e: Exception) {
                _harvestState.value = HarvestState.Error("Failed to get location: ${e.message}")
            }
        }
    }
    
    fun saveHarvest(
        date: Long,
        location: Location,
        quantity: Float,
        imageUris: List<Uri>,
        notes: String?
    ) {
        viewModelScope.launch {
            _harvestState.value = HarvestState.Loading
            
            try {
                // Validation
                if (quantity <= 0) {
                    _harvestState.value = HarvestState.Error("Quantity must be greater than 0")
                    return@launch
                }
                
                // Upload images
                val imageUrls = imageUploader.uploadImages(imageUris)
                
                // Create harvest object
                val harvest = Harvest(
                    id = UUID.randomUUID().toString(),
                    userId = getCurrentUserId(),
                    date = date,
                    location = location,
                    quantity = quantity,
                    imageUrls = imageUrls,
                    notes = notes,
                    createdAt = System.currentTimeMillis()
                )
                
                // Save to repository (handles both local and remote)
                harvestRepository.saveHarvest(harvest)
                
                _harvestState.value = HarvestState.Success(harvest)
                
            } catch (e: Exception) {
                _harvestState.value = HarvestState.Error("Failed to save harvest: ${e.message}")
                Crashlytics.recordException(e)
            }
        }
    }
    
    fun getAISuggestions(imageUris: List<Uri>) {
        viewModelScope.launch {
            _harvestState.value = HarvestState.Loading
            
            try {
                if (imageUris.isEmpty()) {
                    _harvestState.value = HarvestState.Error("Please add at least one image")
                    return@launch
                }
                
                // Analyze with Gemini
                val result = geminiRepository.analyzeHoneyQuality(imageUris.first())
                
                _harvestState.value = HarvestState.AIAnalysisComplete(result)
                
                // Log analytics
                FirebaseAnalytics.logEvent("ai_analysis_completed") {
                    param("grade", result.grade)
                    param("confidence", result.confidence.toLong())
                }
                
            } catch (e: Exception) {
                _harvestState.value = HarvestState.Error("AI analysis failed: ${e.message}")
                Crashlytics.recordException(e)
            }
        }
    }
}

sealed class HarvestState {
    object Idle : HarvestState()
    object Loading : HarvestState()
    data class LocationRetrieved(val location: Location) : HarvestState()
    data class Success(val harvest: Harvest) : HarvestState()
    data class AIAnalysisComplete(val result: GradingResult) : HarvestState()
    data class Error(val message: String) : HarvestState()
}
```

---

### SAMPLE: Harvest Log Screen (Compose)

```kotlin
@Composable
fun HarvestLogScreen(
    viewModel: HarvestLogViewModel = hiltViewModel(),
    onNavigateBack: () -> Unit,
    onNavigateToGrading: (List<String>) -> Unit
) {
    val harvestState by viewModel.harvestState.observeAsState(HarvestState.Idle)
    val location by viewModel.location.observeAsState()
    
    var selectedDate by remember { mutableStateOf(System.currentTimeMillis()) }
    var quantity by remember { mutableStateOf("") }
    var notes by remember { mutableStateOf("") }
    var imageUris by remember { mutableStateOf<List<Uri>>(emptyList()) }
    var showDatePicker by remember { mutableStateOf(false) }
    
    val imagePickerLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.GetMultipleContents()
    ) { uris ->
        imageUris = uris.take(3) // Max 3 images
    }
    
    val cameraLauncher = rememberLauncherForActivityResult(
        contract = ActivityResultContracts.TakePicture()
    ) { success ->
        if (success) {
            // Add captured image to list
        }
    }
    
    Scaffold(
        topBar = {
            TopAppBar(
                title = { Text(stringResource(R.string.log_harvest)) },
                navigationIcon = {
                    IconButton(onClick = onNavigateBack) {
                        Icon(Icons.Default.ArrowBack, contentDescription = "Back")
                    }
                }
            )
        }
    ) { padding ->
        Column(
            modifier = Modifier
                .fillMaxSize()
                .padding(padding)
                .verticalScroll(rememberScrollState())
                .padding(16.dp),
            verticalArrangement = Arrangement.spacedBy(16.dp)
        ) {
            // Date Picker
            OutlinedCard(
                modifier = Modifier.fillMaxWidth(),
                onClick = { showDatePicker = true }
            ) {
                Row(
                    modifier = Modifier.padding(16.dp),
                    verticalAlignment = Alignment.CenterVertically
                ) {
                    Icon(Icons.Default.DateRange, contentDescription = null)
                    Spacer(modifier = Modifier.width(16.dp))
                    Column {
                        Text(
                            text = stringResource(R.string.date),
                            style = MaterialTheme.typography.labelMedium
                        )
                        Text(
                            text = formatDate(selectedDate),
                            style = MaterialTheme.typography.bodyLarge
                        )
                    }
                }
            }
            
            // Location Section
            OutlinedCard(modifier = Modifier.fillMaxWidth()) {
                Column(modifier = Modifier.padding(16.dp)) {
                    Row(
                        modifier = Modifier.fillMaxWidth(),
                        horizontalArrangement = Arrangement.SpaceBetween,
                        verticalAlignment = Alignment.CenterVertically
                    ) {
                        Text(
                            text = stringResource(R.string.location),
                            style = MaterialTheme.typography.labelMedium
                        )
                        Button(onClick = { viewModel.getCurrentLocation() }) {
                            Icon(Icons.Default.MyLocation, contentDescription = null)
                            Spacer(modifier = Modifier.width(8.dp))
                            Text(stringResource(R.string.detect))
                        }
                    }
                    
                    location?.let {
                        Spacer(modifier = Modifier.height(8.dp))
                        Text(
                            text = "${it.latitude}, ${it.longitude}",
                            style = MaterialTheme.typography.bodyMedium
                        )
                        Text(
                            text = it.address ?: "",
                            style = MaterialTheme.typography.bodySmall
                        )
                    }
                }
            }
            
            // Quantity Input
            OutlinedTextField(
                value = quantity,
                onValueChange = { quantity = it },
                label = { Text(stringResource(R.string.quantity_kg)) },
                keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Decimal),
                modifier = Modifier.fillMaxWidth(),
                leadingIcon = {
                    Icon(Icons.Default.Scale, contentDescription = null)
                }
            )
            
            // Image Upload Section
            Text(
                text = stringResource(R.string.add_photos),
                style = MaterialTheme.typography.labelLarge
            )
            
            Row(
                modifier = Modifier.fillMaxWidth(),
                horizontalArrangement = Arrangement.spacedBy(8.dp)
            ) {
                Button(
                    onClick = { /* Launch camera */ },
                    modifier = Modifier.weight(1f)
                ) {
                    Icon(Icons.Default.Camera, contentDescription = null)
                    Spacer(modifier = Modifier.width(8.dp))
                    Text(stringResource(R.string.camera))
                }
                
                OutlinedButton(
                    onClick = { imagePickerLauncher.launch("image/*") },
                    modifier = Modifier.weight(1f)
                ) {
                    Icon(Icons.Default.PhotoLibrary, contentDescription = null)
                    Spacer(modifier = Modifier.width(8.dp))
                    Text(stringResource(R.string.gallery))
                }
            }
            
            // Image Previews
            if (imageUris.isNotEmpty()) {
                LazyRow(
                    horizontalArrangement = Arrangement.spacedBy(8.dp)
                ) {
                    items(imageUris) { uri ->
                        AsyncImage(
                            model = uri,
                            contentDescription = null,
                            modifier = Modifier
                                .size(100.dp)
                                .clip(RoundedCornerShape(8.dp))
                        )
                    }
                }
            }
            
            // Notes
            OutlinedTextField(
                value = notes,
                onValueChange = { notes = it },
                label = { Text(stringResource(R.string.notes_optional)) },
                modifier = Modifier
                    .fillMaxWidth()
                    .height(120.dp),
                maxLines = 5
            )
            
            Spacer(modifier = Modifier.weight(1f))
            
            // Action Buttons
            Button(
                onClick = {
                    location?.let { loc ->
                        viewModel.saveHarvest(
                            date = selectedDate,
                            location = loc,
                            quantity = quantity.toFloatOrNull() ?: 0f,
                            imageUris = imageUris,
                            notes = notes.ifBlank { null }
                        )
                    }
                },
                modifier = Modifier.fillMaxWidth(),
                enabled = quantity.toFloatOrNull() ?: 0f > 0 && location != null
            ) {
                Icon(Icons.Default.Save, contentDescription = null)
                Spacer(modifier = Modifier.width(8.dp))
                Text(stringResource(R.string.save_harvest))
            }
            
            if (imageUris.isNotEmpty()) {
                OutlinedButton(
                    onClick = { viewModel.getAISuggestions(imageUris) },
                    modifier = Modifier.fillMaxWidth()
                ) {
                    Icon(Icons.Default.AutoAwesome, contentDescription = null)
                    Spacer(modifier = Modifier.width(8.dp))
                    Text(stringResource(R.string.get_ai_suggestions))
                }
            }
        }
    }
    
    // Handle state changes
    when (val state = harvestState) {
        is HarvestState.Loading -> {
            LoadingDialog()
        }
        is HarvestState.Success -> {
            LaunchedEffect(Unit) {
                // Show success message
                onNavigateBack()
            }
        }
        is HarvestState.AIAnalysisComplete -> {
            LaunchedEffect(Unit) {
                onNavigateToGrading(state.result.imageUrls)
            }
        }
        is HarvestState.Error -> {
            ErrorDialog(message = state.message)
        }
        else -> {}
    }
}
```

---

## 14. LOCALIZATION (KANNADA SUPPORT)

### String Resources

**res/values/strings.xml (English)**
```xml
<resources>
    <string name="app_name">Jenu-Gumpu</string>
    <string name="tagline">Honey Producer\'s Collective</string>
    
    <!-- Authentication -->
    <string name="login">Login</string>
    <string name="register">Register</string>
    <string name="phone_number">Phone Number</string>
    <string name="send_otp">Send OTP</string>
    <string name="verify_otp">Verify OTP</string>
    
    <!-- Dashboard -->
    <string name="welcome">Welcome, %s</string>
    <string name="log_harvest">Log Harvest</string>
    <string name="grade_honey">Grade Honey</string>
    <string name="check_prices">Check Prices</string>
    <string name="create_batch">Create Batch</string>
    <string name="collective_stock">Collective Stock</string>
    <string name="calculate_profit">Calculate Profit</string>
    <string name="ai_helper">AI Helper</string>
    
    <!-- Harvest Log -->
    <string name="date">Date</string>
    <string name="location">Location</string>
    <string name="detect">Detect</string>
    <string name="quantity_kg">Quantity (kg)</string>
    <string name="add_photos">Add Photos</string>
    <string name="camera">Camera</string>
    <string name="gallery">Gallery</string>
    <string name="notes_optional">Notes (Optional)</string>
    <string name="save_harvest">Save Harvest</string>
    <string name="get_ai_suggestions">Get AI Suggestions</string>
    
    <!-- Grading -->
    <string name="honey_grading">Honey Grading</string>
    <string name="color">Color</string>
    <string name="clarity">Clarity</string>
    <string name="texture">Texture</string>
    <string name="analyze_with_ai">Analyze with AI</string>
    <string name="grade">Grade</string>
    <string name="confidence">Confidence</string>
    <string name="suggestions">Suggestions</string>
    
    <!-- Pricing -->
    <string name="price_monitoring">Price Monitoring</string>
    <string name="local_market">Local Market</string>
    <string name="city_retail">City Retail</string>
    <string name="wholesale">Wholesale</string>
    <string name="last_updated">Last Updated</string>
    <string name="refresh">Refresh</string>
    
    <!-- Batch -->
    <string name="batch_creation">Batch Creation</string>
    <string name="batch_number">Batch Number</string>
    <string name="select_harvests">Select Harvests</string>
    <string name="batch_summary">Batch Summary</string>
    <string name="total_quantity">Total Quantity</string>
    <string name="average_grade">Average Grade</string>
    <string name="generate_label">Generate Label</string>
    
    <!-- Collective -->
    <string name="group_stock">Group Stock</string>
    <string name="total_members">Total Members</string>
    <string name="my_contribution">My Contribution</string>
    <string name="bargaining_power">Bargaining Power</string>
    
    <!-- Calculator -->
    <string name="profit_calculator">Profit Calculator</string>
    <string name="cost_per_kg">Cost per kg</string>
    <string name="selling_price">Selling Price</string>
    <string name="calculate">Calculate</string>
    <string name="gross_profit">Gross Profit</string>
    <string name="profit_margin">Profit Margin</string>
    <string name="roi">ROI</string>
    
    <!-- Profile -->
    <string name="profile">Profile</string>
    <string name="edit_profile">Edit Profile</string>
    <string name="my_group">My Group</string>
    <string name="language">Language</string>
    <string name="notifications">Notifications</string>
    <string name="help_support">Help & Support</string>
    <string name="logout">Logout</string>
    
    <!-- Common -->
    <string name="save">Save</string>
    <string name="cancel">Cancel</string>
    <string name="delete">Delete</string>
    <string name="edit">Edit</string>
    <string name="share">Share</string>
    <string name="back">Back</string>
    <string name="next">Next</string>
    <string name="done">Done</string>
    <string name="loading">Loading...</string>
    <string name="error">Error</string>
    <string name="success">Success</string>
</resources>
```

**res/values-kn/strings.xml (Kannada)**
```xml
<resources>
    <string name="app_name">ಜೇನು-ಗುಂಪು</string>
    <string name="tagline">ಜೇನು ಉತ್ಪಾದಕರ ಸಮೂಹ</string>
    
    <!-- Authentication -->
    <string name="login">ಲಾಗಿನ್</string>
    <string name="register">ನೋಂದಣಿ</string>
    <string name="phone_number">ದೂರವಾಣಿ ಸಂಖ್ಯೆ</string>
    <string name="send_otp">OTP ಕಳುಹಿಸಿ</string>
    <string name="verify_otp">OTP ಪರಿಶೀಲಿಸಿ</string>
    
    <!-- Dashboard -->
    <string name="welcome">ಸ್ವಾಗತ, %s</string>
    <string name="log_harvest">ಸಂಗ್ರಹ ದಾಖಲಿಸಿ</string>
    <string name="grade_honey">ಜೇನು ದರ್ಜೆ</string>
    <string name="check_prices">ಬೆಲೆ ಪರಿಶೀಲಿಸಿ</string>
    <string name="create_batch">ಬ್ಯಾಚ್ ರಚಿಸಿ</string>
    <string name="collective_stock">ಸಾಮೂಹಿಕ ಸ್ಟಾಕ್</string>
    <string name="calculate_profit">ಲಾಭ ಲೆಕ್ಕಾಚಾರ</string>
    <string name="ai_helper">AI ಸಹಾಯಕ</string>
    
    <!-- Harvest Log -->
    <string name="date">ದಿನಾಂಕ</string>
    <string name="location">ಸ್ಥಳ</string>
    <string name="detect">ಗುರುತಿಸಿ</string>
    <string name="quantity_kg">ಪ್ರಮಾಣ (ಕೆಜಿ)</string>
    <string name="add_photos">ಫೋಟೋಗಳನ್ನು ಸೇರಿಸಿ</string>
    <string name="camera">ಕ್ಯಾಮೆರಾ</string>
    <string name="gallery">ಗ್ಯಾಲರಿ</string>
    <string name="notes_optional">ಟಿಪ್ಪಣಿಗಳು (ಐಚ್ಛಿಕ)</string>
    <string name="save_harvest">ಸಂಗ್ರಹ ಉಳಿಸಿ</string>
    <string name="get_ai_suggestions">AI ಸಲಹೆಗಳನ್ನು ಪಡೆಯಿರಿ</string>
    
    <!-- Grading -->
    <string name="honey_grading">ಜೇನು ದರ್ಜೆ</string>
    <string name="color">ಬಣ್ಣ</string>
    <string name="clarity">ಸ್ಪಷ್ಟತೆ</string>
    <string name="texture">ವಿನ್ಯಾಸ</string>
    <string name="analyze_with_ai">AI ನೊಂದಿಗೆ ವಿಶ್ಲೇಷಿಸಿ</string>
    <string name="grade">ದರ್ಜೆ</string>
    <string name="confidence">ವಿಶ್ವಾಸ</string>
    <string name="suggestions">ಸಲಹೆಗಳು</string>
    
    <!-- Pricing -->
    <string name="price_monitoring">ಬೆಲೆ ಮೇಲ್ವಿಚಾರಣೆ</string>
    <string name="local_market">ಸ್ಥಳೀಯ ಮಾರುಕಟ್ಟೆ</string>
    <string name="city_retail">ನಗರ ಚಿಲ್ಲರೆ</string>
    <string name="wholesale">ಸಗಟು</string>
    <string name="last_updated">ಕೊನೆಯ ನವೀಕರಣ</string>
    <string name="refresh">ರಿಫ್ರೆಶ್</string>
    
    <!-- Batch -->
    <string name="batch_creation">ಬ್ಯಾಚ್ ರಚನೆ</string>
    <string name="batch_number">ಬ್ಯಾಚ್ ಸಂಖ್ಯೆ</string>
    <string name="select_harvests">ಸಂಗ್ರಹಗಳನ್ನು ಆಯ್ಕೆಮಾಡಿ</string>
    <string name="batch_summary">ಬ್ಯಾಚ್ ಸಾರಾಂಶ</string>
    <string name="total_quantity">ಒಟ್ಟು ಪ್ರಮಾಣ</string>
    <string name="average_grade">ಸರಾಸರಿ ದರ್ಜೆ</string>
    <string name="generate_label">ಲೇಬಲ್ ರಚಿಸಿ</string>
    
    <!-- Collective -->
    <string name="group_stock">ಗುಂಪು ಸ್ಟಾಕ್</string>
    <string name="total_members">ಒಟ್ಟು ಸದಸ್ಯರು</string>
    <string name="my_contribution">ನನ್ನ ಕೊಡುಗೆ</string>
    <string name="bargaining_power">ಚೌಕಾಸಿ ಶಕ್ತಿ</string>
    
    <!-- Calculator -->
    <string name="profit_calculator">ಲಾಭ ಕ್ಯಾಲ್ಕುಲೇಟರ್</string>
    <string name="cost_per_kg">ಪ್ರತಿ ಕೆಜಿ ವೆಚ್ಚ</string>
    <string name="selling_price">ಮಾರಾಟ ಬೆಲೆ</string>
    <string name="calculate">ಲೆಕ್ಕಾಚಾರ ಮಾಡಿ</string>
    <string name="gross_profit">ಒಟ್ಟು ಲಾಭ</string>
    <string name="profit_margin">ಲಾಭ ಅಂಚು</string>
    <string name="roi">ROI</string>
    
    <!-- Profile -->
    <string name="profile">ಪ್ರೊಫೈಲ್</string>
    <string name="edit_profile">ಪ್ರೊಫೈಲ್ ಸಂಪಾದಿಸಿ</string>
    <string name="my_group">ನನ್ನ ಗುಂಪು</string>
    <string name="language">ಭಾಷೆ</string>
    <string name="notifications">ಅಧಿಸೂಚನೆಗಳು</string>
    <string name="help_support">ಸಹಾಯ ಮತ್ತು ಬೆಂಬಲ</string>
    <string name="logout">ಲಾಗ್ಔಟ್</string>
    
    <!-- Common -->
    <string name="save">ಉಳಿಸಿ</string>
    <string name="cancel">ರದ್ದುಮಾಡಿ</string>
    <string name="delete">ಅಳಿಸಿ</string>
    <string name="edit">ಸಂಪಾದಿಸಿ</string>
    <string name="share">ಹಂಚಿಕೊಳ್ಳಿ</string>
    <string name="back">ಹಿಂದೆ</string>
    <string name="next">ಮುಂದೆ</string>
    <string name="done">ಮುಗಿದಿದೆ</string>
    <string name="loading">ಲೋಡ್ ಆಗುತ್ತಿದೆ...</string>
    <string name="error">ದೋಷ</string>
    <string name="success">ಯಶಸ್ಸು</string>
</resources>
```

---

## 15. FINAL HANDOFF CHECKLIST

### ✅ READY FOR DEVELOPMENT

**Project Setup:**
- [ ] Android Studio Arctic Fox or later installed
- [ ] Kotlin 1.9+ configured
- [ ] Firebase project created
- [ ] Google Gemini API key obtained
- [ ] Git repository initialized

**Architecture:**
- [ ] MVVM structure defined
- [ ] Package structure created
- [ ] Navigation graph outlined
- [ ] Database schema designed

**UI/UX:**
- [ ] All 12 screens defined
- [ ] Screen transitions specified
- [ ] Material Design components selected
- [ ] Kannada translations prepared

**Backend:**
- [ ] Firebase collections structured
- [ ] Room database entities defined
- [ ] API endpoints documented
- [ ] GenAI integration planned

**Testing:**
- [ ] Test cases documented
- [ ] Testing strategy defined

**Documentation:**
- [ ] SOP complete
- [ ] Code samples provided
- [ ] Deployment guide ready

---

## 🚀 READY TO START CODING!

**This SOP provides everything needed to build the Jenu-Gumpu app:**

1. **Complete screen specifications** with UI components, logic, and navigation
2. **Detailed architecture** with MVVM, Room DB, and Firebase
3. **GenAI integration** with Gemini API for grading, pricing, and chat
4. **Code samples** for ViewModels, Screens, and API calls
5. **Localization support** with English and Kannada strings
6. **Testing requirements** with unit, integration, and UI tests
7. **Deployment guidelines** with build configuration and release checklist

**Next Steps:**
1. Set up Android project with provided dependencies
2. Implement screens phase by phase (Foundation → Core → AI → Polish)
3. Test thoroughly
4. Deploy to beta
5. Launch!

**Let's build an app that empowers honey producers! 🐝🍯**