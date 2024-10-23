# Eight Sleep API Documentation

This documentation describes the functions available for interacting with the EightSleep API. These functions are written in TypeScript and use `fetchWithAuth` for API requests, which automatically handles authentication tokens. The base URLs for API requests are as follows:

- **CLIENT_API_URL**: `https://client-api.8slp.net/v1`
- **APP_API_URL**: `https://app-api.8slp.net/`
- **AUTH_URL**: `https://auth-api.8slp.net/v1/tokens`

## Authentication

### `authenticate(email: string, password: string): Promise<Token>`
Authenticates the user with the given email and password, returning an access token.

- **URL**: `https://auth-api.8slp.net/v1/tokens`
- **Method**: `POST`
- **Body**:
  - `client_id`: Known client ID.
  - `client_secret`: Known client secret.
  - `grant_type`: `"password"`.
  - `username`: User email.
  - `password`: User password.

### `obtainFreshAccessToken(refreshToken: string, existingUserId: string): Promise<Token>`
Refreshes the access token using the provided refresh token.

- **URL**: `https://auth-api.8slp.net/v1/tokens`
- **Method**: `POST`
- **Body**:
  - `client_id`: Known client ID.
  - `client_secret`: Known client secret.
  - `grant_type`: `"refresh_token"`.
  - `refresh_token`: User's refresh token.

## User Profile

### `getUserProfile(token: Token): Promise<UserProfile>`
Fetches the user profile information.

- **URL**: `https://client-api.8slp.net/v1/users/me`
- **Method**: `GET`
- **Headers**: Authorization Bearer token.

## Trend Data

### `getTrendData(token: Token, userId: string, startDate: string, endDate: string, timezone: string): Promise<TrendData[]>`
Fetches the trend data for a given user between specific dates.

- **URL**: `https://client-api.8slp.net/v1/users/${userId}/trends`
- **Method**: `GET`
- **Params**:
  - `tz`: User's timezone.
  - `from`: Start date.
  - `to`: End date.
  - `include-main`: `"false"`.
  - `include-all-sessions`: `"false"`.
  - `model-version`: `"v2"`.

## Interval Data

### `getIntervalsData(token: Token, userId: string): Promise<IntervalData[]>`
Fetches the interval data for a user.

- **URL**: `https://client-api.8slp.net/v1/users/${userId}/intervals`
- **Method**: `GET`

## Routines

### `getRoutinesData(token: Token, userId: string): Promise<RoutineData>`
Fetches routine data (like next alarm) for a user.

- **URL**: `https://app-api.8slp.net/v2/users/${userId}/routines`
- **Method**: `GET`

### `alarmSnooze(token: Token, userId: string, alarmId: string, snoozeMinutes: number): Promise<void>`
Snoozes a user's alarm for the specified number of minutes.

- **URL**: `https://app-api.8slp.net/v1/users/${userId}/routines`
- **Method**: `PUT`
- **Body**:
  - `alarm`: { `alarmId`, `snoozeForMinutes` }.

### `alarmStop(token: Token, userId: string, alarmId: string): Promise<void>`
Stops a user's alarm.

- **URL**: `https://app-api.8slp.net/v1/users/${userId}/routines`
- **Method**: `PUT`
- **Body**:
  - `alarm`: { `alarmId`, `stopped: true` }.

### `alarmDismiss(token: Token, userId: string, alarmId: string): Promise<void>`
Dismisses a user's alarm.

- **URL**: `https://app-api.8slp.net/v1/users/${userId}/routines`
- **Method**: `PUT`
- **Body**:
  - `alarm`: { `alarmId`, `dismissed: true` }.

## Heating Status

### `getCurrentHeatingStatus(token: Token): Promise<HeatingStatus>`
Fetches the current heating status for the user based on their profile and device data.

- **URL**: Requires calling `getUserProfile` and fetching device data with `getDeviceData`.
- **Method**: Depends on user side (left or right) and device data structure.

## Bed State

### `getBedStateType(token: Token, userId: string): Promise<BedStateType>`
Fetches the current bed state type for a user.

- **URL**: `https://app-api.8slp.net/v1/users/${userId}/temperature`
- **Method**: `GET`

### `setBedSide(token: Token, userId: string, deviceId: string, side: Side): Promise<void>`
Sets the user's bed side.

- **URL**: `https://client-api.8slp.net/v1/users/${userId}/current-device`
- **Method**: `PUT`
- **Body**:
  - `id`: Device ID.
  - `side`: `"left"` or `"right"`.

## Away Mode

### `setAwayMode(token: Token, userId: string, action: AwayModeAction): Promise<void>`
Sets away mode for a user.

- **URL**: `https://app-api.8slp.net/v1/users/${userId}/away-mode`
- **Method**: `PUT`
- **Body**:
  - `awayPeriod`: { `action`: timestamp }.

## Priming

### `primePod(token: Token, deviceId: string, userId: string): Promise<void>`
Primes a user's pod (device).

- **URL**: `https://app-api.8slp.net/v1/devices/${deviceId}/priming/tasks`
- **Method**: `POST`
- **Body**:
  - `notifications`: { `users`: [userId], `meta`: "rePriming" }.
