# 🛡️ LIROU
## Navigate Safely. Protected Always.

**AI-Powered Safety Navigation with Automatic Emergency Response**

> *Would you save 3 minutes if it meant walking through a dark alley alone at night?*

---

## 🎯 THE PROBLEM

Standard GPS apps optimize for **speed**, not **safety**. They route users through:
- ❌ Dark, poorly-lit streets
- ❌ High-crime areas
- ❌ Isolated alleys with no help nearby
- ❌ Routes with no nearby emergency services

**For women, elderly, and vulnerable users, this isn't just inconvenient—it's dangerous.**

---

## ✨ THE SOLUTION

**Lirou** provides **dual-layer protection**:

### **Layer 1: Smart Prevention** 🗺️
AI-driven routing that avoids dangerous areas using 8 real-time safety metrics

### **Layer 2: Automatic Response** 🚨  
Incident detection that alerts first responders and captures evidence automatically

---

## 🏗️ AWS ARCHITECTURE

```
┌─────────────────────────────────────────────────────────────┐
│                        USER (Mobile App)                     │
│                         React Native                         │
└──────────────────────────┬──────────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────────┐
│                    AWS API GATEWAY                           │
│              (REST API + WebSocket for real-time)            │
└──────────────────────────┬──────────────────────────────────┘
                           │
        ┌──────────────────┴──────────────────┐
        ▼                                      ▼
┌──────────────────┐                  ┌──────────────────┐
│  AWS LAMBDA      │                  │  AWS LAMBDA      │
│  Route Engine    │                  │  Emergency Alert │
│  (Python/Node)   │                  │  Orchestrator    │
└────────┬─────────┘                  └────────┬─────────┘
         │                                     │
         ▼                                     ▼
┌──────────────────┐                  ┌──────────────────┐
│  AMAZON DYNAMODB │                  │  AWS SNS         │
│  Safety Scores   │                  │  Alert Dispatch  │
│  User Profiles   │                  │  (SMS/Push/Call) │
└──────────────────┘                  └──────────────────┘
         │
         ▼
┌──────────────────┐
│  AMAZON S3       │◄─────────────────┐
│  Crime Data      │                  │
│  Map Cache       │                  │
└──────────────────┘                  │
                                      │
┌─────────────────────────────────────┴───────────────────┐
│           INCIDENT DETECTION PIPELINE                    │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  │
│  │AWS KINESIS   │→ │AWS LAMBDA    │→ │AMAZON S3     │  │
│  │Video Stream  │  │ML Inference  │  │Evidence Store│  │
│  └──────────────┘  └──────────────┘  └──────────────┘  │
└──────────────────────────────────────────────────────────┘
         │
         ▼
┌──────────────────┐
│ AMAZON REKOGNITION│
│ Video Analysis    │
│ (Future: Face ID) │
└──────────────────┘
```

---

## 🔧 AWS SERVICES USED

### **Core Infrastructure**
| Service | Purpose |
|---------|---------|
| **Amazon API Gateway** | REST API endpoints + WebSocket for real-time navigation |
| **AWS Lambda** | Serverless route calculation, safety scoring, emergency alerts |
| **Amazon DynamoDB** | User profiles, safety scores, incident logs (NoSQL, fast) |
| **Amazon S3** | Crime data storage, map tiles cache, video/audio evidence |

### **Emergency Response System**
| Service | Purpose |
|---------|---------|
| **Amazon Kinesis Video Streams** | Real-time video ingestion during incidents |
| **Amazon SNS** | Multi-channel emergency alerts (SMS, push, email, voice) |
| **Amazon Connect** | Automated voice calls to 911/emergency contacts |
| **AWS Step Functions** | Emergency response workflow orchestration |

### **AI/ML & Data Processing**
| Service | Purpose |
|---------|---------|
| **Amazon SageMaker** | Train incident detection ML model (accelerometer patterns) |
| **Amazon Rekognition** | Video analysis, object detection in evidence footage |
| **AWS Lambda (ML Inference)** | Real-time anomaly detection on device sensor data |
| **Amazon Comprehend** | Sentiment analysis on user safety reports |

### **Monitoring & Security**
| Service | Purpose |
|---------|---------|
| **Amazon CloudWatch** | Real-time monitoring, logging, incident detection alerts |
| **AWS CloudTrail** | Audit trail for compliance (who accessed evidence) |
| **AWS KMS** | Encryption keys for video/audio evidence |
| **AWS Secrets Manager** | API keys, database credentials |

### **Data Sources Integration**
| Service | Purpose |
|---------|---------|
| **Amazon Location Service** | Geocoding, reverse geocoding, route calculation |
| **AWS Lambda** | ETL for crime data from government APIs |
| **Amazon EventBridge** | Schedule periodic data updates (crime stats, business hours) |

---

## 📊 THE 8 SAFETY METRICS

| # | Metric | Data Source | AWS Service |
|---|--------|-------------|-------------|
| 1️⃣ | **Road Classification** | OpenStreetMap | Lambda + DynamoDB |
| 2️⃣ | **Street Lighting** | OpenStreetMap + Gov Data | S3 + Lambda |
| 3️⃣ | **Police Proximity** | Google Places API | Location Service |
| 4️⃣ | **Hospital Access** | Google Places API | Location Service |
| 5️⃣ | **Business Density** | Google Places API | Lambda + DynamoDB |
| 6️⃣ | **Foot Traffic** | Google Popular Times | Lambda (cached) |
| 7️⃣ | **Crime Heatmap** | Local Police Open Data | S3 + Lambda |
| 8️⃣ | **Time of Day** | System Clock | Lambda calculation |

---

## 🚨 EMERGENCY INCIDENT FLOW

### **Detection → Alert → Response (< 5 seconds)**

```
Phone Sensors (Accelerometer + Gyroscope)
        ↓
Device ML Model detects anomaly
        ↓
Streams video to Amazon Kinesis Video Streams
        ↓
AWS Lambda triggers emergency protocol
        ↓
Parallel Actions:
├─→ Amazon S3: Store video/audio evidence (encrypted)
├─→ Amazon SNS: Alert emergency contacts (SMS/Push)
├─→ Amazon Connect: Auto-call 911 with GPS
├─→ DynamoDB: Log incident with timestamp
└─→ CloudWatch: Trigger monitoring alerts
        ↓
Step Functions: Orchestrate ongoing response
        ↓
Real-time location updates via WebSocket (API Gateway)
```

**Key Features:**
- ✅ **3-second response time** (Lambda provisioned concurrency)
- ✅ **Evidence preserved** even if phone is destroyed
- ✅ **Encrypted storage** (AWS KMS)
- ✅ **Audit trail** (CloudTrail for legal compliance)

---

## 🎨 FEATURES

### **Prevention (Layer 1)**
🗺️ **Smart Routing** - AI calculates safest path, not fastest  
💡 **Safety Score** - Visual indicator for each route option  
🕐 **Time-Aware** - Different routes for day vs night  
👥 **Community Data** - Crowdsourced safety reports  

### **Protection (Layer 2)**
🚨 **Auto-Detection** - No button press needed  
📹 **Evidence Capture** - 1-2 min video + audio to cloud  
📍 **GPS Lock** - Precise incident location  
⚡ **Instant Alerts** - First responders + emergency contacts  
☁️ **Tamper-Proof** - Cloud backup survives phone damage  

---

## 💻 TECH STACK

**Frontend:** React Native (iOS + Android)  
**Backend:** AWS Lambda (Python 3.12) + Node.js 20  
**Database:** Amazon DynamoDB (NoSQL)  
**Storage:** Amazon S3 (Standard + Intelligent-Tiering)  
**API:** Amazon API Gateway (REST + WebSocket)  
**ML:** Amazon SageMaker (training) + Lambda (inference)  
**Video:** Amazon Kinesis Video Streams  
**Notifications:** Amazon SNS + Amazon Connect  
**IaC:** AWS SAM / CloudFormation  

**Why AWS?**
- ✅ **Serverless** - Pay only for active users, scales automatically
- ✅ **Global** - CloudFront CDN for low-latency worldwide
- ✅ **Reliable** - 99.99% SLA for emergency response
- ✅ **Secure** - Designed with HIPAA/GDPR compliance considerations

---

## 📈 COST OPTIMIZATION (AWS)

**Estimated Monthly Cost for 10,000 Active Users:**

| Service | Monthly Cost | Optimization |
|---------|--------------|--------------|
| Lambda (10M requests) | $20 | Provisioned concurrency |
| DynamoDB (on-demand) | $50 | Auto-scaling |
| S3 (1TB storage) | $23 | Intelligent-Tiering |
| API Gateway | $35 | Caching enabled |
| Kinesis (incidents only) | $10 | Event-driven activation |
| SNS/Connect | $20 | Emergency-only |
| **TOTAL** | **~$158/month** | |

**Per User:** ~$0.016/month (~$0.19/year)

---

## 🎯 DEMO FLOW (3 Minutes)

### **Minute 1: The Problem** (30 sec)
Show Google Maps routing through dark alley vs Lirou's safe route

### **Minute 2: Smart Routing** (60 sec)
- Enter destination
- Display 3 route options with safety scores
- Highlight: well-lit streets, police stations, active businesses
- Compare: Lirou (11 min, safe) vs Google (8 min, dangerous)

### **Minute 3: Emergency Response** (90 sec)
- Simulate phone shake/drop
- Show 3-second countdown
- Demonstrate:
  - ✅ Video recording starts
  - ✅ Upload to S3
  - ✅ SNS alert sent
  - ✅ GPS captured
  - ✅ Mock 911 call via Connect
- Show responder view with evidence link

**Closing:** *"3 seconds. That's how fast help arrives with Lirou."*

---

## 📊 SUCCESS METRICS

**Hackathon Goals:**
- ✅ Route 100% safer than Google Maps (avg +25% safety score)
- ✅ Incident detection accuracy >95%
- ✅ Emergency alert delivery <5 seconds
- ✅ All 8 metrics integrated and functional

**Post-Launch Impact:**
- 🎯 10,000 users in first 6 months
- 🎯 50% reduction in safety incidents
- 🎯 3-5 min faster emergency response time
- 🎯 90% user "felt safer" rating

---

## 🌟 WHY LIROU STANDS OUT

### **Technical Excellence**
✅ Fully serverless AWS architecture  
✅ Real-time ML inference on edge + cloud  
✅ Multi-source data integration (8 metrics)  
✅ Sub-5-second emergency response pipeline  

### **Social Impact**
✅ Addresses real safety crisis for vulnerable populations  
✅ Empowers millions to travel confidently  
✅ Evidence capture increases conviction rates  
✅ Data-driven urban safety improvements  

### **Market Viability**
✅ Scalable AWS infrastructure  
✅ Low per-user cost (~$0.19/year)  
✅ Clear monetization path (B2B enterprise, insurance partnerships)  
✅ Global expansion potential  

### **Innovation**
✅ First to combine preventive routing + automatic emergency response  
✅ AI-driven safety scoring (not just crime data)  
✅ Tamper-proof evidence preservation  
✅ No competitor offers both layers  

---

## 🏆 AWS BEST PRACTICES IMPLEMENTED

✅ **Well-Architected Framework (All 6 Pillars):**
- **Operational Excellence:** CloudWatch monitoring, AWS SAM for IaC
- **Security:** KMS encryption, IAM least privilege, Secrets Manager
- **Reliability:** Multi-AZ deployment, DynamoDB backups, retry policies
- **Performance Efficiency:** Lambda provisioned concurrency, API Gateway caching
- **Cost Optimization:** S3 Intelligent-Tiering, on-demand DynamoDB
- **Sustainability:** Serverless reduces carbon footprint, intelligent data tiering, edge inference reduces cloud compute

---

<div align="center">

## 🛡️ **LIROU**
### Navigate Safely. Protected Always.

*Built with AWS | Powered by AI | Driven by Impact*

**Prevention Through Intelligence. Protection Through Technology.**

</div>

---

**AWS Services Count:** 15+ services integrated  
**Lines of Code:** ~5,000 (backend + frontend)  
**Development Time:** 48 hours  
**Lives Protected:** ∞
