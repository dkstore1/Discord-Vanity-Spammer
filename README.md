# Discord Vanity Checker - Changelog

## Latest Update - Enhanced Features

### 🎯 NEW: Target Sniper Mode for Cooldown Vanities
- **Feature**: Monitor specific vanities on cooldown (e.g., "in 3 hours", "in 9 hours")
- **Usage**: Enter the vanity in "Target Vanity" mode
- **Behavior**: 
  - Continuously monitors the vanity status
  - Detects when it's on cooldown/banned
  - Sends webhook notification with 30-day countdown
  - **Instantly claims** the moment it becomes available
  - No delay between detection and claiming
- **Perfect for**: Grace period vanities that show countdown timers
- **Status Updates**: Shows progress every 100 checks while monitoring

### 1. Multiple Guild ID Support with Automatic Rotation
- **Feature**: Support for multiple Discord server IDs
- **Behavior**: When a claim is successful, the first guild ID is automatically removed and the system switches to the next one
- **Configuration**: Use `guild_ids` array in `config.json` instead of single `guild_id`
- **Example**:
  ```json
  {
    "guild_ids": ["123456789", "987654321", "555555555"]
  }
  ```
- **Automatic Saving**: The `config.json` is automatically updated when guild IDs are rotated

### 2. Professional Ban Verification System
- **Enhanced Detection**: Improved ban status checking with proper error code verification
- **Immediate Claim**: When a previously banned vanity becomes available, the system immediately attempts to claim it
- **Smart Logic**: Distinguishes between truly banned vanities and available ones more accurately

### 3. Separate Webhook Notifications
Now you can receive different webhook notifications for each case:

#### a) Claim Success Webhook (`webhook_claim_success`)
- Triggered when a vanity is successfully claimed
- Shows: Vanity name, Guild ID used, Total checked, Success timestamp
- Color: Green (65280)

#### b) Claim Failed Webhook (`webhook_claim_failed`)
- Triggered when a claim attempt fails
- Shows: Vanity name, Guild ID attempted, Failure reason
- Color: Red (16711680)

#### c) Banned Status Webhook (`webhook_banned`)
- Triggered when a vanity is detected as banned with 30-day notice
- Shows: Vanity name, Estimated availability time (30 days), Status
- Color: Orange (16776960)
- **Includes countdown**: Uses Discord's timestamp format `<t:timestamp:R>` for relative time

### 4. Configuration Template
Your `config.json` should now look like this:
```json
{
    "webhook_url": "YOUR_GENERAL_WEBHOOK",
    "webhook_claim_success": "YOUR_SUCCESS_WEBHOOK",
    "webhook_claim_failed": "YOUR_FAILED_WEBHOOK",
    "webhook_banned": "YOUR_BANNED_WEBHOOK",
    "thumbnail_url": "",
    "user_token": "YOUR_DISCORD_TOKEN",
    "user_password": "YOUR_PASSWORD_FOR_2FA",
    "guild_ids": ["GUILD_ID_1", "GUILD_ID_2", "GUILD_ID_3"],
    "threads_count": 2
}
```

**Note**: If you leave the specific webhooks empty, they will fall back to `webhook_url`

### 5. Immediate Claim on Unban
- **Feature**: Continuous monitoring of banned vanities
- **Behavior**: As soon as a banned vanity becomes available, the system immediately attempts to claim it
- **Webhook**: Automatically sends the appropriate webhook (success or failed) based on the claim result

### 6. Enhanced Ban Status Checking
- **Better Verification**: More accurate detection of ban status using Discord error codes
- **Code 10006**: Properly identifies "Unknown Invite" as available vanity
- **Fallback Logic**: Multiple verification methods to ensure accuracy

## How to Use

1. **Configure Multiple Guild IDs**:
   - Edit `config.json`
   - Add multiple guild IDs in the `guild_ids` array
   - Or enter comma-separated IDs when prompted

2. **Set Up Webhooks**:
   - Create separate Discord webhooks for each notification type
   - Add them to `config.json` under their respective fields
   - Or use a single `webhook_url` for all notifications

3. **Run the Tool**:
   - The tool will automatically use the first guild ID
   - On successful claim, it rotates to the next ID
   - Banned vanities are continuously monitored for availability

## Technical Details

### Guild ID Rotation
- Thread-safe implementation with locks
- Automatically saves to `config.json` after rotation
- Supports both legacy `guild_id` and new `guild_ids` format

### Webhook System
- Separate functions for each notification type
- Fallback to main webhook if specific webhook not configured
- Includes timestamps and detailed information

### Ban Detection
- Checks HTTP 404 with error code 10006
- Multiple verification methods (JSON parsing + text search)
- Immediate claim attempt when available

## Migration Guide

If you're upgrading from an older version:

1. **Backup** your current `config.json`
2. Update `config.json` structure:
   - Change `"guild_id": "123"` to `"guild_ids": ["123"]`
   - Add new webhook fields (optional)
3. Run the tool - it will work with both old and new formats

## Notes

- The system continues to monitor banned vanities every cycle
- Webhook notifications are sent asynchronously (non-blocking)
- Guild ID rotation happens immediately after successful claim
- All file operations are thread-safe with proper locking
