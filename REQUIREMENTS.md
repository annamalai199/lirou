# 📋 LIROU — Requirements Document
## AI-Powered Safety Navigation with Automatic Emergency Response

---

## 1. Functional Requirements

### 1.1 Safe Route Navigation (Layer 1)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-1.1 | System shall calculate routes optimized for safety, not speed | Must Have |
| FR-1.2 | System shall score each route segment using 8 safety metrics | Must Have |
| FR-1.3 | System shall present multiple route options with safety scores | Must Have |
| FR-1.4 | System shall provide turn-by-turn navigation on the selected route | Must Have |
| FR-1.5 | System shall adjust routing based on time of day (day vs night) | Must Have |
| FR-1.6 | System shall display a visual safety indicator (color-coded) for each route | Should Have |
| FR-1.7 | System shall re-route dynamically if user deviates from safe path | Should Have |
| FR-1.8 | System shall allow users to submit crowdsourced safety reports | Could Have |

### 1.2 Emergency Incident Detection (Layer 2)

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-2.1 | System shall continuously monitor phone sensors (accelerometer, gyroscope) during navigation | Must Have |
| FR-2.2 | System shall detect abnormal motion patterns indicating a potential incident | Must Have |
| FR-2.3 | System shall provide a cancellation countdown (10 seconds) before triggering alerts | Must Have |
| FR-2.4 | System shall automatically record 1–2 minutes of video and audio upon incident detection | Must Have |
| FR-2.5 | System shall upload captured evidence to encrypted cloud storage (Amazon S3) | Must Have |
| FR-2.6 | System shall send emergency alerts to pre-configured contacts via SMS/push | Must Have |
| FR-2.7 | System shall initiate an automated voice call to 911 with GPS coordinates | Should Have |
| FR-2.8 | System shall log the incident with timestamp, GPS location, and severity level in DynamoDB | Must Have |
| FR-2.9 | System shall continue live location tracking after alert is triggered | Should Have |
| FR-2.10 | System shall support a silent alert mode (no sound/vibration) | Could Have |

### 1.3 User Management

| ID | Requirement | Priority |
|----|-------------|----------|
| FR-3.1 | System shall allow users to register and authenticate securely | Must Have |
| FR-3.2 | System shall allow users to configure up to 3 emergency contacts | Must Have |
| FR-3.3 | System shall allow users to adjust incident detection sensitivity | Should Have |
| FR-3.4 | System shall allow users to toggle the emergency detection system on/off | Must Have |
| FR-3.5 | System shall store user profiles and preferences in DynamoDB | Must Have |

---

## 2. Non-Functional Requirements

### 2.1 Performance

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-1.1 | Route calculation response time | < 3 seconds |
| NFR-1.2 | Emergency alert delivery (end-to-end) | < 5 seconds |
| NFR-1.3 | Incident detection latency (sensor event → alert trigger) | < 3 seconds |
| NFR-1.4 | Video upload initiation after incident detection | < 2 seconds |
| NFR-1.5 | API Gateway response time (p95) | < 500 ms |

### 2.2 Scalability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-2.1 | Support concurrent active users | 10,000+ |
| NFR-2.2 | Handle route calculation requests | 10M requests/month |
| NFR-2.3 | Auto-scale Lambda functions based on demand | On-demand + provisioned concurrency |
| NFR-2.4 | DynamoDB throughput scaling | On-demand capacity mode |

### 2.3 Reliability & Availability

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-3.1 | System uptime SLA | 99.9% |
| NFR-3.2 | Emergency alert pipeline uptime | 99.99% |
| NFR-3.3 | Evidence upload retry on network failure | Automatic with exponential backoff |
| NFR-3.4 | Data durability (S3 evidence storage) | 99.999999999% (11 nines) |
| NFR-3.5 | DynamoDB backup frequency | Daily point-in-time recovery |

### 2.4 Security

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-4.1 | Encryption at rest for all stored data | AES-256 via AWS KMS |
| NFR-4.2 | Encryption in transit | TLS 1.2+ |
| NFR-4.3 | Evidence access control | IAM least privilege + audit trail |
| NFR-4.4 | API authentication | Amazon Cognito / API keys |
| NFR-4.5 | Credential management | AWS Secrets Manager |
| NFR-4.6 | Evidence access audit logging | AWS CloudTrail |
| NFR-4.7 | Compliance considerations | HIPAA/GDPR design principles |

### 2.5 Cost

| ID | Requirement | Target |
|----|-------------|--------|
| NFR-5.1 | Monthly infrastructure cost (10K users) | < $200 |
| NFR-5.2 | Per-user annual cost | < $0.25 |
| NFR-5.3 | Storage optimization | S3 Intelligent-Tiering for aging evidence |
| NFR-5.4 | Compute optimization | Serverless (pay-per-use) |

---

## 3. Data Requirements

### 3.1 Safety Metrics Data Sources

| Metric | Source | Update Frequency | Storage |
|--------|--------|------------------|---------|
| Road Classification | OpenStreetMap API | Weekly | DynamoDB |
| Street Lighting | OpenStreetMap + Government Data | Weekly | S3 + DynamoDB |
| Police Proximity | Google Places API | Daily | DynamoDB (cached) |
| Hospital Access | Google Places API | Daily | DynamoDB (cached) |
| Business Density | Google Places API | Daily | DynamoDB (cached) |
| Foot Traffic | Google Popular Times | Hourly (peak), Daily (off-peak) | Lambda cache |
| Crime Heatmap | Local Police Open Data Portals | Weekly | S3 |
| Time of Day | System Clock | Real-time | Lambda calculation |

### 3.2 Data Models

**User Profile (DynamoDB)**
| Attribute | Type | Description |
|-----------|------|-------------|
| userId | String (PK) | Unique user identifier |
| email | String | User email |
| emergencyContacts | List | Up to 3 contacts (name, phone, relation) |
| detectionSensitivity | String | low / medium / high |
| silentModeEnabled | Boolean | Silent alert preference |
| createdAt | String (ISO 8601) | Account creation timestamp |

**Safety Score (DynamoDB)**
| Attribute | Type | Description |
|-----------|------|-------------|
| segmentId | String (PK) | Road segment identifier |
| geoHash | String (SK) | Geohash for spatial query |
| overallScore | Number | Composite safety score (0–100) |
| metrics | Map | Individual scores per metric |
| lastUpdated | String (ISO 8601) | Score calculation timestamp |

**Incident Log (DynamoDB)**
| Attribute | Type | Description |
|-----------|------|-------------|
| incidentId | String (PK) | Unique incident identifier |
| userId | String (GSI) | User who triggered the incident |
| timestamp | String (SK) | ISO 8601 incident time |
| location | Map | { lat, lng, accuracy } |
| severityLevel | String | low / medium / high / critical |
| evidenceS3Key | String | S3 key for video/audio evidence |
| alertsSent | List | Recipients and delivery status |
| status | String | active / resolved / false_alarm |

---

## 4. AWS Service Requirements

### 4.1 Compute

| Service | Configuration | Justification |
|---------|--------------|---------------|
| AWS Lambda (Route Engine) | Python 3.12, 512 MB memory, 30s timeout | Route calculation with safety scoring |
| AWS Lambda (Emergency Orchestrator) | Node.js 20, 256 MB memory, 15s timeout, provisioned concurrency | Low-latency emergency alert dispatch |
| AWS Lambda (ML Inference) | Python 3.12, 1024 MB memory, 10s timeout | Sensor data anomaly detection |
| AWS Lambda (ETL) | Python 3.12, 512 MB memory, 300s timeout | Crime data ingestion from external APIs |

### 4.2 Storage & Database

| Service | Configuration | Justification |
|---------|--------------|---------------|
| Amazon DynamoDB | On-demand capacity, point-in-time recovery enabled | User profiles, safety scores, incident logs |
| Amazon S3 (Evidence) | Versioning enabled, SSE-KMS encryption, lifecycle policy | Tamper-proof encrypted evidence storage |
| Amazon S3 (Data) | Intelligent-Tiering | Crime data, map cache, ML model artifacts |

### 4.3 Networking & API

| Service | Configuration | Justification |
|---------|--------------|---------------|
| Amazon API Gateway (REST) | Regional endpoint, caching enabled, throttling | Route requests, user management |
| Amazon API Gateway (WebSocket) | Regional endpoint | Real-time location updates during incidents |
| Amazon CloudFront | Edge caching for static assets | Low-latency map tile delivery |

### 4.4 AI/ML

| Service | Configuration | Justification |
|---------|--------------|---------------|
| Amazon SageMaker | Training job (ml.m5.xlarge) | Train incident detection model on accelerometer data |
| Amazon Rekognition | Video analysis API | Object/scene detection in evidence footage |
| Amazon Comprehend | Sentiment analysis API | Analyze user-submitted safety reports |

### 4.5 Messaging & Alerts

| Service | Configuration | Justification |
|---------|--------------|---------------|
| Amazon SNS | SMS + push notification topics | Emergency alert fan-out to contacts |
| Amazon Connect | Outbound voice call flow | Automated 911 call with GPS coordinates |

### 4.6 Orchestration & Scheduling

| Service | Configuration | Justification |
|---------|--------------|---------------|
| AWS Step Functions | Standard workflow | Emergency response multi-step orchestration |
| Amazon EventBridge | Scheduled rules (cron) | Periodic crime data refresh, business hours updates |

### 4.7 Monitoring & Security

| Service | Configuration | Justification |
|---------|--------------|---------------|
| Amazon CloudWatch | Logs, metrics, alarms | Lambda monitoring, error tracking, incident alerts |
| AWS CloudTrail | All management + data events for S3 evidence bucket | Audit trail for evidence access (legal compliance) |
| AWS KMS | Customer-managed key | Encryption for evidence in S3 and DynamoDB |
| AWS Secrets Manager | Automatic rotation | External API keys (Google Places, OpenStreetMap) |
| Amazon Cognito | User pool + identity pool | User authentication and API authorization |

### 4.8 Infrastructure as Code

| Service | Configuration | Justification |
|---------|--------------|---------------|
| AWS SAM / CloudFormation | Template-based deployment | Reproducible, version-controlled infrastructure |

---

## 5. External API Requirements

| API | Usage | Rate Limit Consideration |
|-----|-------|--------------------------|
| OpenStreetMap Overpass API | Road classification, street lighting data | Batch queries, cache results in DynamoDB |
| Google Places API | Police stations, hospitals, businesses, foot traffic | API key quota management via Secrets Manager |
| Google Popular Times | Pedestrian foot traffic estimation | Cached in Lambda, refreshed hourly |
| Local Police Open Data APIs | Historical crime data by location | Weekly ETL via EventBridge-triggered Lambda |
| Amazon Location Service | Geocoding, reverse geocoding, base route calculation | Native AWS integration |

---

## 6. Mobile App Requirements

### 6.1 Platform Support

| Platform | Minimum Version | Framework |
|----------|----------------|-----------|
| iOS | 15.0+ | React Native |
| Android | API Level 26 (8.0+) | React Native |

### 6.2 Device Permissions

| Permission | Purpose | Required |
|------------|---------|----------|
| Location (Always) | Real-time GPS for navigation and incident tracking | Yes |
| Camera | Video evidence capture on incident detection | Yes |
| Microphone | Audio evidence capture on incident detection | Yes |
| Motion/Accelerometer | Incident detection via motion analysis | Yes |
| Push Notifications | Receive safety alerts and incident updates | Yes |
| Background Execution | Continue monitoring and recording when app is backgrounded | Yes |

### 6.3 Offline Capabilities

| Feature | Offline Behavior |
|---------|-----------------|
| Navigation | Cached route continues; no re-routing | 
| Incident Detection | Sensor monitoring continues locally |
| Evidence Capture | Records locally, uploads when connection restored |
| Emergency Alerts | Queued, sent immediately on reconnection |

---

## 7. Acceptance Criteria (Hackathon MVP)

| # | Criteria | Validation Method |
|---|---------|-------------------|
| AC-1 | User can enter a destination and receive a safety-scored route | Manual test |
| AC-2 | Route avoids segments with low safety scores (< 40/100) | Compare with Google Maps route |
| AC-3 | Safety score incorporates at least 5 of 8 metrics | Metric breakdown visible in UI |
| AC-4 | Simulated phone shake triggers incident detection | Device shake test |
| AC-5 | Emergency alert (SNS) delivered within 5 seconds of detection | Timestamp comparison |
| AC-6 | Video evidence uploaded to S3 with KMS encryption | S3 console verification |
| AC-7 | Incident logged in DynamoDB with GPS coordinates and timestamp | DynamoDB console query |
| AC-8 | Cancellation countdown prevents false alarm when dismissed | Manual cancel test |
| AC-9 | All infrastructure deployed via AWS SAM/CloudFormation | Template validation |
| AC-10 | End-to-end demo completes in under 3 minutes | Timed run-through |

---

## 8. Constraints & Assumptions

### Constraints
- All backend services must run on AWS (hackathon requirement)
- Budget limited to AWS Free Tier + hackathon credits
- MVP must be demonstrable within a 3-minute demo window
- Mobile app must function on both iOS and Android

### Assumptions
- Users have a smartphone with GPS, accelerometer, gyroscope, and camera
- Users have an active internet connection during navigation (offline mode is best-effort)
- Crime data is available via public open data portals for the demo region
- Google Places API quota is sufficient for demo-scale usage
- Emergency contacts have valid phone numbers capable of receiving SMS

---

## 9. Risk Assessment

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| High false positive rate on incident detection | Users lose trust, emergency fatigue | Medium | Tunable sensitivity, 10-second cancel window, ML model refinement |
| Google Places API rate limits exceeded | Safety metrics incomplete | Low | Aggressive caching in DynamoDB, batch queries |
| Lambda cold starts delay emergency alerts | Alert delivery > 5 seconds | Medium | Provisioned concurrency on Emergency Orchestrator Lambda |
| Poor cellular connectivity during incident | Evidence upload fails | Medium | Local recording buffer, retry on reconnection |
| Crime data unavailable for demo region | Incomplete safety scoring | Low | Pre-load demo region data in S3, fallback to synthetic data |
| Sensor access denied by user | Incident detection non-functional | Low | Clear onboarding flow explaining permission necessity |

---

*Document Version: 1.0*  
*Last Updated: February 2026*  
*Project: Lirou — Navigate Safely. Protected Always.*
