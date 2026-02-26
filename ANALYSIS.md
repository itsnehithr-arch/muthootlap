# Muthoot LAP Track Application - Analysis Document

## Document Information
- **Project Name**: LAP app track
- **Date**: 12-02-2026
- **Sponsor**: Jignesh Vora
- **Department**: Digital - Business Performance
- **IT Coordinator**: Vidhya Kavishree

---

## 1. Application Overview

### Purpose
The Muthoot LAP Track Application is designed to digitize and automate the property site verification process for Loan Against Property (LAP) applicants. By using this dedicated APK, Muthoot Finance aims to:

- Capture precise GPS coordinates of the property
- Expedite the Simplified Fast Track Processing (SFTP)
- Reduce manual verification Turn-Around-Time (TAT)

### Target Users
- LAP applicants who need property verification
- RCU (Risk Control Unit) for monitoring and fraud detection

---

## 2. User Journey Analysis

### Screen Flow
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│  Verification   │───▶│   GPS Access    │───▶│Applicant Details│
│    (Screen 1)   │    │   (Screen 2)    │    │   (Screen 3)    │
└─────────────────┘    └─────────────────┘    └────────┬────────┘
                                                       │
                              ┌───────────────────────┘
                              ▼
                       ┌─────────────────┐
                       │  Terms & Cond.  │
                       │ (Bottom Sheet)  │
                       └────────┬────────┘
                                │
                                ▼
                       ┌─────────────────┐
                       │  Success Screen │
                       │   (Screen 5)    │
                       └─────────────────┘
```

### Screen Details

#### Screen 1: Welcome & Branding
- **Purpose**: Initial entry point for applicants
- **Key Elements**:
  - Muthoot branding with logo
  - Shield icon (security symbol)
  - Welcome message
  - "Start" button to begin process
- **Text**: "To expedite your application, we need to verify your property location"

#### Screen 2: Permission Request
- **Purpose**: Explain and request location permissions
- **Key Elements**:
  - GPS icon
  - Permission explanation
  - "Allow Access" button
- **Text**: "Allow 'All the time' access to finalize property verification for your application."
- **Permissions Required**:
  - `ACCESS_FINE_LOCATION`
  - `ACCESS_BACKGROUND_LOCATION`

#### Screen 3: Secure Login / Applicant Details
- **Purpose**: Collect applicant information and verify identity
- **Input Fields**:
  | Field | Type | Validation |
  |-------|------|------------|
  | First Name | Text | Required |
  | Last Name | Text | Required |
  | City | Dropdown | Required (59 options) |
  | Mobile Number | Number | 10 digits, Required |
  | OTP | Password | 6 digits, Required |
- **Additional Elements**:
  - T&C checkbox with hyperlink
  - "VERIFY" button

#### Screen 4: Terms & Conditions (Bottom Sheet)
- **Purpose**: Display detailed terms with user acceptance
- **Features**:
  - Slide-up animation from bottom
  - Scrollable content
  - Numbered sections
  - "I ACCEPT" button
- **Sections**:
  1. Purpose of Tracking
  2. Data Collection
  3. Location Tracking Schedule
  4. Data Security
  5. Data Usage
  6. User Consent
  7. Withdrawal of Consent

#### Screen 5: Submission Success
- **Purpose**: Confirm successful application submission
- **Key Elements**:
  - Green success icon
  - "Thank You!" message
  - Success message about LAP application
  - Next steps information
  - "Done" button

---

## 3. Data Requirements

### Data Capture (Per Verification)
| Data Type | Description |
|-----------|-------------|
| Geolocation | Exact Latitude and Longitude |
| Google API Response | Raw JSON from Fused Location Provider |
| Timestamp | Server-side ISO 8601 timestamp |
| Metadata | First Name, Last Name, Mobile, Device ID |

### Storage
- **Target**: Google BigQuery (BQ)
- **Retention**: As per business requirements

### Analytics
- **Tool**: Looker
- **Dashboard**: RCU monitoring for verification trends and fraud detection

---

## 4. Background Location Tracking

### Tracking Schedule
| Time | Purpose |
|------|---------|
| 7:00 AM | Morning verification |
| 9:00 AM | Mid-morning check |
| 12:00 PM | Noon verification |
| 2:00 PM | Afternoon check |
| 7:00 PM | Evening verification |
| 9:00 PM | Night check |

### Duration
- Maximum: 60 days from application date

### Foreground Service Notification
- **Channel Name**: Location Verification Service
- **Priority**: Low
- **Title**: 📍 Property Verification in Progress
- **Body**: "Muthoot LAP-Track is securely verifying your location. Please keep your GPS on."
- **Behavior**: Non-dismissible while active, opens app on click

---

## 5. Business Rules

| Rule ID | Logic Name | Description |
|---------|------------|-------------|
| BR-01 | One-Device Policy | Session tied to authenticated UID from Screen 2 |
| BR-02 | OTP Masking | OTP fields use password type (security) |
| BR-03 | Mobile Visibility | Mobile number unmasked for typo correction |

---

## 6. Error Handling Matrix

### Authentication Failures
| Scenario | User Message | System Action |
|----------|--------------|---------------|
| Incorrect OTP | "Invalid Details: The OTP entered does not match our records. Please verify and re-enter." | Clear OTP inputs; Increment error counter |
| Max OTP Attempts (3) | "Authentication Failed: Maximum attempts reached. Please click 'Resend Code' to try with a new OTP." | Lock "Verify" button for 60 seconds; Reset counter on new OTP |

### GPS & Connectivity Errors
| Scenario | User Message | System Action |
|----------|--------------|---------------|
| Permissions Denied | "Permission Required: This app cannot function without location access. Please enable it in Settings." | Redirect to App Settings |
| Network Timeout | "Connection Lost: We are unable to reach the server. Your location will be synced once back online." | Cache locally; Sync via background worker |

---

## 7. Technical Requirements

### Google Location API
- **Provider**: Google Fused Location Provider API
- **Accuracy Target**: < 10 meters

### City List (59 Cities)

#### Gujarat (8)
Mehsana, Kapadvanj, Vijapur, Nadiad, Ahmedabad City, Talod, Sanand, Vastral

#### Andhra Pradesh (5)
Guntur, Eluru, Vijaywada, Tenali, Nandigama

#### Karnataka (9)
Narsapura Kolar, Bidadi, Chikkaballapura, Dobaspet, Bangalore, Doddaballapur, Anekal Hosur

#### Madhya Pradesh (5)
Ujjain, Indore, Pithampur, Dewas, Dhar

#### Rajasthan (5)
Jaipur, Reengus, Phagi, Shahpura, Jobner

#### Maharashtra (14)
Pune, Karegoan, Talegaon Dabhade, Manchar, Pimpri Chichwad, Katol, Umred, Bhandara, Nagpur, Ramtek, Badlapur, Panvel, Virar, Mumbai

#### Tamil Nadu (10)
Chennai, Chengalapptu, Kanchipuram, Thiruvullar, Coimbatore, Pollachi, Kunnathur, Paladam, Tiruppur, Mettupalayam

#### Other (3)
Shahpur, Kedgaon, Renwal, Kelzar, Gudurru

---

## 8. UI/UX Design Analysis

### Color Scheme
| Element | Color | Hex Code |
|---------|-------|----------|
| Primary Brand | Muthoot Red | #D32F2F |
| Success | Green | #4CAF50 |
| GPS/Info | Blue | #1976D2 |
| Background | White | #FFFFFF |
| Text Primary | Black 87% | #212121 |
| Text Secondary | Grey | #757575 |
| Input Background | Grey 50 | #FAFAFA |

### Typography
- Font Family: Roboto
- Title: 22-28px, Bold
- Body: 13-14px, Regular
- Labels: 11-12px, SemiBold, Uppercase

### Common Elements
- **Header**: Muthoot Logo + Notification + Profile icons
- **Footer**: Secure SFTP Protocol badge
- **Buttons**: Rounded (25px radius), Full width, 50px height
- **Input Fields**: Rounded (8px), Grey background, Red focus border

---

## 9. Implementation Notes

### Flutter Dependencies
```yaml
permission_handler: ^11.1.0    # Location permissions
geolocator: ^10.1.0            # GPS services
device_info_plus: ^9.1.2       # Device info
shared_preferences: ^2.2.2     # Local storage
```

### Platform Configuration

#### Android (AndroidManifest.xml)
```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_LOCATION"/>
```

#### iOS (Info.plist)
```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>Location access for property verification</string>
<key>NSLocationAlwaysUsageDescription</key>
<string>Background location for LAP verification</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>Location access for property verification</string>
```

---

## 10. Security Considerations

1. **Data Transmission**: SFTP Protocol
2. **Data Storage**: Encrypted in Google BigQuery
3. **Session Management**: Device-bound authentication
4. **OTP Security**: Masked input, 3-attempt limit
5. **Location Privacy**: User consent required, T&C acceptance

---

## 11. Future Enhancements

1. Integration with backend APIs for real OTP verification
2. Push notifications for tracking updates
3. In-app chat support for applicants
4. Multi-language support
5. Offline mode with sync capability
6. Biometric authentication

---

**Document Version**: 1.0
**Last Updated**: 12-02-2026
**Status**: Implementation Complete
