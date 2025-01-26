# Jitsi Integration - Core Development Guide

A guide for implementing core Jitsi Meet integration features with Janssen IAM using Flutter.

## Table of Contents
- [Development Setup](#development-setup)
- [Project Structure](#project-structure)
- [Core Components](#core-components)
- [Basic Integration](#basic-integration)
- [Error Handling](#error-handling)
- [State Management](#state-management)

## Development Setup

### 1. Environment Configuration
```dart
/// Environment configuration management
class JitsiDevConfig {
  static const environment = String.fromEnvironment(
    'ENVIRONMENT',
    defaultValue: 'development'
  );

  static bool get isDevelopment => environment == 'development';
  static bool get isProduction => environment == 'production';

  static String get jitsiServerUrl => isDevelopment
    ? 'http://localhost:8000'
    : 'https://meet.your.domain';

  static String get janssenServerUrl => isDevelopment
    ? 'http://localhost:8080'
    : 'https://janssen.your.domain';
}
```

### 2. Dependencies
```yaml
# pubspec.yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^1.1.0
  provider: ^6.1.1
  jwt_decoder: ^2.0.1
  logging: ^1.2.0
  
dev_dependencies:
  flutter_test:
    sdk: flutter
  mockito: ^5.4.3
  build_runner: ^2.4.6
  flutter_lints: ^2.0.0
```

## Project Structure
```
lib/
├── jitsi/
│   ├── api/              # API client implementations
│   ├── models/           # Data models
│   ├── services/         # Business logic services
│   ├── widgets/          # UI components
│   └── utils/           # Utility functions
├── config/
│   ├── jitsi_config.dart # Configuration management
│   └── env_config.dart   # Environment settings
├── features/
│   ├── meeting/          # Meeting management
│   ├── room/             # Room configuration
│   └── recording/        # Recording features
└── shared/
    ├── components/       # Shared UI components
    └── utils/           # Shared utilities
```

## Core Components

### 1. Meeting Service
```dart
/// Core meeting service implementation
class MeetingService {
  final JitsiApiClient _apiClient;
  final TokenService _tokenService;

  Future<Meeting> createMeeting(MeetingConfig config) async {
    try {
      final token = await _tokenService.getToken();
      final response = await _apiClient.createMeeting(
        config,
        token: token,
      );
      return Meeting.fromJson(response);
    } catch (e) {
      throw MeetingException('Failed to create meeting: $e');
    }
  }

  Future<void> joinMeeting(String meetingId) async {
    try {
      final token = await _tokenService.getToken();
      await _apiClient.joinMeeting(
        meetingId,
        token: token,
      );
    } catch (e) {
      throw MeetingException('Failed to join meeting: $e');
    }
  }

  Future<void> endMeeting(String meetingId) async {
    try {
      final token = await _tokenService.getToken();
      await _apiClient.endMeeting(
        meetingId,
        token: token,
      );
    } catch (e) {
      throw MeetingException('Failed to end meeting: $e');
    }
  }
}
```

### 2. Room Controller
```dart
/// Room management implementation
class RoomController {
  final String roomId;
  final RoomConfig config;
  final RoomService _roomService;

  Future<void> updateConfiguration(RoomConfig newConfig) async {
    try {
      await _roomService.updateRoom(
        roomId,
        newConfig,
      );
    } catch (e) {
      throw RoomException('Failed to update room: $e');
    }
  }

  Future<void> setLobbyEnabled(bool enabled) async {
    try {
      await _roomService.updateLobbySettings(
        roomId,
        enabled: enabled,
      );
    } catch (e) {
      throw RoomException('Failed to update lobby settings: $e');
    }
  }

  Future<void> setPassword(String? password) async {
    try {
      await _roomService.updatePassword(
        roomId,
        password,
      );
    } catch (e) {
      throw RoomException('Failed to update password: $e');
    }
  }
}
```

### 3. Participant Manager
```dart
/// Participant management implementation
class ParticipantManager {
  final String meetingId;
  final ParticipantService _participantService;

  Future<void> admitParticipant(String participantId) async {
    try {
      await _participantService.admitParticipant(
        meetingId,
        participantId,
      );
    } catch (e) {
      throw ParticipantException('Failed to admit participant: $e');
    }
  }

  Future<void> removeParticipant(String participantId) async {
    try {
      await _participantService.removeParticipant(
        meetingId,
        participantId,
      );
    } catch (e) {
      throw ParticipantException('Failed to remove participant: $e');
    }
  }

  Future<void> muteParticipant(String participantId) async {
    try {
      await _participantService.muteParticipant(
        meetingId,
        participantId,
      );
    } catch (e) {
      throw ParticipantException('Failed to mute participant: $e');
    }
  }
}
```

## Basic Integration

### 1. Meeting Widget
```dart
/// Basic meeting widget implementation
class JitsiMeetingWidget extends StatefulWidget {
  final String meetingId;
  final MeetingConfig config;

  @override
  State<JitsiMeetingWidget> createState() => _JitsiMeetingWidgetState();
}

class _JitsiMeetingWidgetState extends State<JitsiMeetingWidget> {
  late final MeetingController _controller;

  @override
  void initState() {
    super.initState();
    _controller = MeetingController(
      meetingId: widget.meetingId,
      config: widget.config,
    );
  }

  @override
  Widget build(BuildContext context) {
    return Stack(
      children: [
        MeetingView(controller: _controller),
        ControlPanel(
          onMicToggle: _controller.toggleMic,
          onCameraToggle: _controller.toggleCamera,
          onEndMeeting: _controller.endMeeting,
        ),
      ],
    );
  }
}
```

### 2. Control Panel
```dart
/// Meeting controls implementation
class ControlPanel extends StatelessWidget {
  final VoidCallback onMicToggle;
  final VoidCallback onCameraToggle;
  final VoidCallback onEndMeeting;

  @override
  Widget build(BuildContext context) {
    return Container(
      padding: EdgeInsets.all(8),
      child: Row(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          IconButton(
            icon: Icon(Icons.mic),
            onPressed: onMicToggle,
          ),
          IconButton(
            icon: Icon(Icons.videocam),
            onPressed: onCameraToggle,
          ),
          IconButton(
            icon: Icon(Icons.call_end),
            onPressed: onEndMeeting,
          ),
        ],
      ),
    );
  }
}
```

## Error Handling

### 1. Exception Types
```dart
/// Custom exception implementations
class MeetingException implements Exception {
  final String message;
  final dynamic cause;

  MeetingException(this.message, [this.cause]);

  @override
  String toString() => 'MeetingException: $message${cause != null ? ' ($cause)' : ''}';
}

class RoomException implements Exception {
  final String message;
  final dynamic cause;

  RoomException(this.message, [this.cause]);

  @override
  String toString() => 'RoomException: $message${cause != null ? ' ($cause)' : ''}';
}
```

### 2. Error Handler
```dart
/// Error handling utility
class ErrorHandler {
  final ErrorLogger _logger;
  final NotificationService _notifications;

  Future<T> handleError<T>(Future<T> Function() operation) async {
    try {
      return await operation();
    } on MeetingException catch (e) {
      _logger.logError('Meeting operation failed', e);
      _notifications.showError(e.message);
      rethrow;
    } on RoomException catch (e) {
      _logger.logError('Room operation failed', e);
      _notifications.showError(e.message);
      rethrow;
    } catch (e) {
      _logger.logError('Unexpected error', e);
      _notifications.showError('An unexpected error occurred');
      rethrow;
    }
  }
}
```

## State Management

### 1. Meeting State
```dart
/// Meeting state management
class MeetingState extends ChangeNotifier {
  Meeting? _currentMeeting;
  List<Participant> _participants = [];
  bool _isMuted = false;
  bool _isCameraOff = false;

  Meeting? get currentMeeting => _currentMeeting;
  List<Participant> get participants => List.unmodifiable(_participants);
  bool get isMuted => _isMuted;
  bool get isCameraOff => _isCameraOff;

  void updateMeeting(Meeting meeting) {
    _currentMeeting = meeting;
    notifyListeners();
  }

  void toggleMic() {
    _isMuted = !_isMuted;
    notifyListeners();
  }

  void toggleCamera() {
    _isCameraOff = !_isCameraOff;
    notifyListeners();
  }
}
```

### 2. Provider Setup
```dart
/// Provider configuration
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(
          create: (_) => MeetingState(),
        ),
        ChangeNotifierProvider(
          create: (_) => RoomState(),
        ),
        Provider(
          create: (_) => MeetingService(
            apiClient: JitsiApiClient(),
            tokenService: TokenService(),
          ),
        ),
      ],
      child: JitsiApp(),
    ),
  );
}
```

## Next Steps
- See [Performance & Optimization Guide](jitsi-performance.md) for advanced optimization techniques
- See [Testing & Quality Guide](jitsi-testing.md) for comprehensive testing strategies
- See [API Integration Guide](jitsi-api.md) for detailed API documentation

