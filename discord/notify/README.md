# discord/notify

Sends a Discord DM with the job outcome (✅ success / ❌ failure / ⚪ cancelled) and a link to the run.

```yaml
      - name: Notify Discord DM
        if: always()
        uses: DarkWalker19/github-actions/discord/notify@v1
        with:
          status: ${{ job.status }}
```

Put it as the last step of the job. `if: always()` makes it run on failure too.

## Credentials
The bot token and DM channel ID are resolved in this order:

1. **Environment variables** `DISCORD_BOT_TOKEN` and `DISCORD_DM_CHANNEL_ID`, e.g. from GitHub secrets:
   ```yaml
         - name: Notify Discord DM
           if: always()
           uses: DarkWalker19/github-actions/discord/notify@v1
           env:
             DISCORD_BOT_TOKEN: ${{ secrets.DISCORD_BOT_TOKEN }}
             DISCORD_DM_CHANNEL_ID: ${{ secrets.DISCORD_DM_CHANNEL_ID }}
           with:
             status: ${{ job.status }}
   ```
   Variables set on the runner itself work too.
2. **Files** at `bot-token-file` and `channel-id-file`, by default the Docker secrets `/run/secrets/discord_bot_token` and `/run/secrets/discord_dm_channel_id`. To read them from elsewhere, override the paths:
   ```yaml
         - name: Notify Discord DM
           if: always()
           uses: DarkWalker19/github-actions/discord/notify@v1
           with:
             status: ${{ job.status }}
             bot-token-file: /run/secrets/my_bot_token
             channel-id-file: /etc/discord/channel_id
   ```

## Inputs
| Input | Default | Description |
|---|---|---|
| `status` | (required) | Job status to report; pass `job.status` |
| `bot-token-file` | `/run/secrets/discord_bot_token` | File holding the bot token, used when `DISCORD_BOT_TOKEN` is not set |
| `channel-id-file` | `/run/secrets/discord_dm_channel_id` | File holding the DM channel ID, used when `DISCORD_DM_CHANNEL_ID` is not set |

## Requirements
- `bash`, `jq` and `curl` on the runner.
- The bot must share a server with the recipient, otherwise Discord answers `50278 no mutual guilds`.

If no credentials are found or Discord rejects the message, the step only logs a warning and never fails the job.
