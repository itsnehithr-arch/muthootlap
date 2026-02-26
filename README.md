# Muthoot LAP Track Application

A Flutter application for Loan Against Property (LAP) applicant location verification for Muthoot Finance.

## Overview

This application digitizes and automates the property site verification process for LAP applicants. It captures precise GPS coordinates to expedite the Simplified Fast Track Processing (SFTP) and reduce manual verification TAT.

## Features

### Screens

1. **Verification Screen** - Welcome screen with Muthoot branding and verification initiation
2. **GPS Access Screen** - Location permission request with "All the time" access
3. **Applicant Details Screen** - Form with:
   - First Name & Last Name
   - City Selection (59 cities)
   - Mobile Number (10-digit)
   - OTP Verification (6-digit)
   - Terms & Conditions acceptance
4. **Terms & Conditions Bottom Sheet** - Detailed T&C with slide-up animation
5. **Success Screen** - Confirmation with success message

### Key Features

- **Location Permission Handling**: Requests both foreground and background location permissions
- **Form Validation**: Comprehensive validation for all input fields
- **OTP Verification**: Mock OTP verification with error handling (3 attempts max)
- **T&C Bottom Sheet**: Animated slide-up modal with full terms
- **Secure Footer**: SFTP protocol indicator on all screens
- **Error Handling**: User-friendly error messages for all scenarios

## Technical Details

### Dependencies

```yaml
permission_handler: ^11.1.0    # Location permissions
geolocator: ^10.1.0            # GPS location services
device_info_plus: ^9.1.2       # Device information
shared_preferences: ^2.2.2     # Local storage
flutter_svg: ^2.0.9            # SVG support
```

### Project Structure

```
lib/
├── main.dart                      # App entry point
├── models/
│   └── applicant_model.dart       # Data models & city list
├── screens/
│   ├── verification_screen.dart   # Welcome screen
│   ├── gps_access_screen.dart     # Permission request
│   ├── applicant_details_screen.dart  # Form screen
│   ├── terms_bottom_sheet.dart    # T&C modal
│   └── success_screen.dart        # Success confirmation
└── widgets/
    └── custom_widgets.dart        # Reusable UI components
```

## Screenshots

The app follows the exact UI design from the provided mockups:
- Red (#D32F2F) as primary brand color
- Clean white background
- Muthoot branding with logo
- Consistent header and footer across screens

## Getting Started

### Prerequisites

- Flutter SDK >= 3.0.0
- Android Studio / VS Code
- Android SDK / Xcode for iOS

### Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd muthoot_lap_app
```

2. Install dependencies:
```bash
flutter pub get
```

3. Run the app:
```bash
flutter run
```

### Building APK

```bash
flutter build apk --release
```

## Configuration

### Android Permissions

Add to `android/app/src/main/AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"/>
<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE"/>
<uses-permission android:name="android.permission.FOREGROUND_SERVICE_LOCATION"/>
```

### iOS Permissions

Add to `ios/Runner/Info.plist`:

```xml
<key>NSLocationWhenInUseUsageDescription</key>
<string>This app needs location access to verify your property location.</string>
<key>NSLocationAlwaysUsageDescription</key>
<string>This app needs background location access for property verification.</string>
<key>NSLocationAlwaysAndWhenInUseUsageDescription</key>
<string>This app needs location access to verify your property location for LAP application.</string>
```

## Business Rules

- **BR-01**: One-Device Policy - Session tied to authenticated UID
- **BR-02**: OTP Masking - Input fields use password type
- **BR-03**: Mobile Visibility - Mobile number remains unmasked during input

## Error Handling

| Scenario | User Message | System Action |
|----------|--------------|---------------|
| Incorrect OTP | "Invalid Details: The OTP entered does not match our records." | Clear OTP, increment counter |
| Max OTP Attempts (3) | "Authentication Failed: Maximum attempts reached." | Lock verify button for 60s |
| Permissions Denied | "Permission Required: Please enable it in Settings." | Redirect to App Settings |
| Network Timeout | "Connection Lost: Unable to reach the server." | Cache locally, sync later |

## City List

The app includes 59 cities across India:
- Gujarat: Mehsana, Kapadvanj, Vijapur, Nadiad, Ahmedabad City, Talod, Sanand, Vastral
- Andhra Pradesh: Guntur, Eluru, Vijaywada, Tenali, Nandigama
- Karnataka: Narsapura Kolar, Bidadi, Chikkaballapura, Dobaspet, Bangalore, Doddaballapur, Anekal Hosur
- Madhya Pradesh: Ujjain, Indore, Pithampur, Dewas, Dhar
- Rajasthan: Jaipur, Reengus, Phagi, Shahpura, Jobner
- Maharashtra: Pune, Karegoan, Talegaon Dabhade, Manchar, Pimpri Chichwad, Katol, Umred, Bhandara, Nagpur, Ramtek, Badlapur, Panvel, Virar, Mumbai
- Tamil Nadu: Chennai, Chengalapptu, Kanchipuram, Thiruvullar, Coimbatore, Pollachi, Kunnathur, Paladam, Tiruppur, Mettupalayam
- Other: Shahpur, Kedgaon, Renwal, Kelzar, Gudurru

## License

© 2026 Muthoot Finance. All rights reserved.
