# Background Red Zone Monitoring

This project now includes:
- foreground red-zone monitoring
- functional login/profile setup with local storage
- timer-based SOS verification that works when app is closed

## Login + Local Storage

- Main login screen now supports:
  - Tourist / Local role selection
  - Name and phone input
  - Secret color selection (Red / Blue / Green)
- Profile is stored in `SharedPreferences` via `UserSafetyStore`.
- Stored fields:
  - `name`, `phone`, `userType`, `correctColor`, `failureCount`

## SOS Timer Verification

### Added Files

- `app/src/main/java/com/example/geofencing/UserSafetyStore.kt`
  - Local persistence for profile and failure count
- `app/src/main/java/com/example/geofencing/SosScheduler.kt`
  - Schedules/cancels SOS timer via `AlarmManager`
- `app/src/main/java/com/example/geofencing/SosAlarmReceiver.kt`
  - Receives alarm and opens SOS screen
- `app/src/main/java/com/example/geofencing/SosActivity.kt`
  - Full-screen SOS verification UI

### Timer Behavior

- User can choose intervals:
  - 60 seconds
  - 10 minutes
  - 20 minutes
  - 2 hours
- Timer works when app is backgrounded/closed using `AlarmManager` + `BroadcastReceiver`.

### SOS Screen Behavior

- Full-screen screen opens on timer trigger.
- Buttons shown: Red, Blue, Green.
- Any button click always shows:
  - `You chose correct option`
- Internal logic:
  - Wrong choice -> increment failure count
  - No response in 30 seconds -> increment failure count
- Escalation:
  - If failure count >= 2 -> logs emergency escalation + shows toast

## Red Zone Monitoring (Current)

- `RedZone` zones are hardcoded in `RedZoneConfig`:
  - Dharavi: `19.0443, 72.8553`, radius `1000m`
  - Bandra: `19.0596, 72.8295`, radius `1000m`
- Marker-driven entry detection is used for alerting.
- Foreground service notification:
  - `Safety monitoring active`
- Red zone alert notification:
  - `⚠️ Safety Alert`
  - `You are in a high risk area`

## Manifest Declarations

- Permissions include:
  - `ACCESS_FINE_LOCATION`
  - `ACCESS_COARSE_LOCATION`
  - `ACCESS_BACKGROUND_LOCATION`
  - `FOREGROUND_SERVICE`
  - `FOREGROUND_SERVICE_LOCATION`
  - `POST_NOTIFICATIONS`
- Components added:
  - `.RedZoneMonitorService`
  - `.SosAlarmReceiver`
  - `.SosActivity`

## Quick Validation

1. Launch app and create profile from login screen.
2. Open SOS Timer screen and start any interval.
3. Close app and wait for timer completion.
4. Confirm SOS full-screen opens.
5. Tap wrong color twice (or timeout twice) and verify escalation log/toast.
6. Open map and verify red zones and marker-based detection still work.
